# [deployphp/deployer](https://github.com/deployphp/deployer)

A deployment tool written in PHP with support for popular frameworks out of the box <https://deployer.org>

## 安装

```sh
# 通过 Phar 存档
curl -LO https://deployer.org/deployer.phar
mv deployer.phar /usr/local/bin/dep
chmod +x /usr/local/bin/dep

# 通过 composer
composer global require deployer/deployer -vvv

# 通过github
git clone https://github.com/deployphp/deployer.git
php ./build

dep --version
```

## 使用

```sh
# 目标服务机
sudo adduser deployer
sudo usermod -aG www-data deployer
su deployer
echo "umask 022" >> ~/.bashrc
exit

vim /etc/sudoers # 在最后加入 deployer ALL=(ALL) NOPASSWD: ALL
sudo chown deployer:www-data /var/www/html
sudo chmod g+s /var/www/html

ssh-keygen -t rsa -b 4096 -C "deployer"
cat ~/.ssh/id_rsa.pub # 添加到代码库公钥中

# 本地机操作
ssh-keygen -t rsa -b 4096 -f  ~/.ssh/deployerkey
ssh-copy-id -i  ~/.ssh/deployerkey.pub deployer@123.45.67.89
ssh-copy-id -i  ~/.ssh/deployerkey.pub deployer@123.45.67.89 # 测试免密钥登录


## 项目目录操作
dep init # 配置 deployer.php 文件

dep deploy -vvv # 准备 hook 文件 -> 在项目上添加一个 Webhook 并设置 hook 的网址
```

配置文件：dep deploy debug | production
```php
<?php

namespace Deployer;

require 'recipe/laravel.php';

// Project name
set('application', 'xxx');

// Project repository
set('repository', 'git@github.com:tianyong90/xxx.git');

// [Optional] Allocate tty for git clone. Default value is false.
set('git_tty', true);

// Shared files/dirs between deploys
add('shared_files', []);
add('shared_dirs', []);

// Writable dirs by web server
add('writable_dirs', []);

// 保存最近五次部署，这样的话回滚最多也只能回滚到前 5 个版本
set('keep_releases', 5);

// 实践证明，这样能减少一些不必要的麻烦,如出现权限相关的问题，也可将此项设置为 true 后尝试
set('writable_use_sudo', false);

// 生产用的主机
host('172.16.1.1')
    ->stage('production')
    ->user('root')
    ->port(22)
    ->set('branch', 'master') // 最新的主分支部署到生产机
    ->set('deploy_path', '/data/wwwroot/xxx')
    ->identityFile('/home/vagrant/.ssh/id_rsa')
    ->forwardAgent(true)
    ->multiplexing(true)
    ->set('http_user', 'www') // 这个与 nginx 里的配置一致
    ->addSshOption('UserKnownHostsFile', '/dev/null')
    ->addSshOption('StrictHostKeyChecking', 'no');

// 测试用的主机
host('172.16.3.2')
    ->stage('debug')
    ->user('root')
    ->port(22)
    ->set('branch', 'develop') // 一般是把 develop 分支弄到测试机测试，没问题再合并
    ->set('deploy_path', '/data/wwwroot/xxx')
    ->identityFile('/home/vagrant/.ssh/id_rsa')
    ->forwardAgent(true)
    ->multiplexing(true)
    ->set('http_user', 'www')
    ->addSshOption('UserKnownHostsFile', '/dev/null')
    ->addSshOption('StrictHostKeyChecking', 'no');

// 自定义任务：重置 opcache 缓存
task('opcache_reset', function () {
    run('{{bin/php}} -r \'opcache_reset();\'');
});

// 自定义任务：重启 php-fpm 服务
task('php-fpm:restart', function () {
    run('systemctl restart php-fpm.service');
});

// 自定义任务：supervisor reload
task('supervisor:reload', function () {
    run('sudo supervisorctl reload');
});

// 自定义任务：部署成功了用 bearychat 发消息给大佬和自己
task('send_message', function () {
    run('{{bin/php}} {{release_path}}/artisan deployed');
});

// 自定义任务：缓存路由，recipe/laravel.php 默认的流程里没有这个，所以加上，息看需要
after('artisan:config:cache', 'artisan:route:cache');

// 执行自定义任务，注意时间点是 current 已经成功链向新部署的目录之后
after('deploy:symlink', 'php-fpm:restart');
after('deploy:symlink', 'supervisor:reload');

// 部署成功后重置 opcache 缓存
after('deploy:symlink', 'opcache_reset');

// 部署成功后调用 laravel 命令行发送通知
after('success', 'send_message');

// [Optional] if deploy fails automatically unlock.
after('deploy:failed', 'deploy:unlock');
```
## 部署结构

* current - 它是指向一个具体的版本的软链接，你的 nginx 配置中 root 应该指向它，比如 laravel 项目的话 root 就指向：/var/www/demo-app/current/public
* releases - 部署的历史版本文件夹，里面可能有很多个最近部署的版本，可以根据你的配置来设置保留多少个版本，建议 5 个。保留版本可以让我们在上线出问题时使用 dep rollback 快速回滚项目到上一个版本。
* shared - 共享文件夹，它的作用就是存储我们项目中版本间共享的文件

## 工具

* [spinnaker/spinnaker](https://github.com/spinnaker/spinnaker):Spinnaker is an open source, multi-cloud continuous delivery platform for releasing software changes with high velocity and confidence. http://www.spinnaker.io/
