# [symfony/symfony](https://github.com/symfony/symfony)

The Symfony PHP framework http://symfony.com. Fabien Potencier

## 安装

```sh
# get symfony cli
wget https://get.symfony.com/cli/installer -O - | bash # linux
curl -sS https://get.symfony.com/cli/installer | bash # mac

symfony new --full [--version=3.4] my_project
composer create-project symfony/skeleton  my-project "2.3.*" # framework-standard-edition

cd my-project
composer require server --dev

# cli-server
php bin/console server:run -vvv
php bin/console server:start 0.0.0.0:8000

./bin/phpunit

## nginx
server {
    server_name domain.tld www.domain.tld;
    root /var/www/project/public;

    location / {
        # try to serve file directly, fallback to index.php
        try_files $uri /index.php$is_args$args;
    }

    location ~ ^/index\.php(/|$) {
        fastcgi_pass unix:/var/run/php/php7.1-fpm.sock;
        fastcgi_split_path_info ^(.+\.php)(/.*)$;
        include fastcgi_params;

        # optionally set the value of the environment variables used in the application
        # fastcgi_param APP_ENV prod;
        # fastcgi_param APP_SECRET <app-secret-id>;
        # fastcgi_param DATABASE_URL "mysql://db_user:db_pass@host:3306/db_name";

        # When you are using symlinks to link the document root to the
        # current version of your application, you should pass the real
        # application path instead of the path to the symlink to PHP
        # FPM.
        # Otherwise, PHP's OPcache may not properly detect changes to
        # your PHP files (see https://github.com/zendtech/ZendOptimizerPlus/issues/126
        # for more information).
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        fastcgi_param DOCUMENT_ROOT $realpath_root;
        # Prevents URIs that include the front controller. This will 404:
        # http://domain.tld/index.php/some-path
        # Remove the internal directive to allow URIs like this
        internal;
    }

    # return 404 for all other php files not matching the front controller
    # this prevents access to other php files you don't want to be accessible.
    location ~ \.php$ {
        return 404;
    }

    error_log /var/log/nginx/project_error.log;
    access_log /var/log/nginx/project_access.log;
}
```


## Doctrine
Entity
Repository
Proxy:需要时才生成



## Annotation

annotation: 中间不能有空格
method #加use
嵌套

```
@Route("/hi/{name}",requirements={"name"="\d"})
```
router.yml
php app/console router:debug # 路由列表
php app/console router:match /hi/4 #路由匹配


Serveice
php app/console container:debug # 服务列表

## symfony Proxy with Varish


团队共享开发环境：域名映射 dns服务器



## controller

* request
* response




## twig

* 判断  {{}}
* 输出 {% %}
* 注释{# #}



## assettic bundle

* 静态资源软连接 `php app/console assets:install web --symlink --relative`

You must add HenryWebBundle to the assetic.bundle config to use the {% javascripts %} tag
app/config/config.yml里有个assetic配置段  HenryWebBundle

* 静态文件分离
* coffee 编译
* 压缩 `https://github.com/mishoo/UglifyJS2`
* 版本控制
* 资源合并：`php app/console assetic:dump  --env=prod --on-debug 生成静态文件以及合并`
    - 2.5 --fork :多线程生成

## View

* 页面管理:类的继承关系管理页面
* HTML5Boilerplate
    - http://www.initializr.com/

## 问题

symfony 3.4 与 PHP 7.3
Warning: "continue" targeting switch is equivalent to
  "break". Did you mean to use "continue 2"

## 扩展

* Debug bar
* [bundles 仓库](http://knpbundles.com/)
* [symfony/thanks](https://github.com/symfony/thanks):Give thanks (in the form of a GitHub ★) to your fellow PHP package maintainers (not limited to Symfony components)!
* [symfony/console](https://github.com/symfony/console):The Console component eases the creation of beautiful and testable command line interfaces. https://symfony.com/console
* [symfony/http-foundation](https://github.com/symfony/http-foundation):The HttpFoundation component defines an object-oriented layer for the HTTP specification. https://symfony.com/http-foundation
* [jonaswouters/XhprofBundle](https://github.com/jonaswouters/XhprofBundle):XHProf bundle for Symfony 2
* [EasyCorp/EasyAdminBundle](https://github.com/EasyCorp/EasyAdminBundle):The new (and simple) admin generator for Symfony applications.
* [symfony/dotenv](https://github.com/symfony/dotenv):Symfony Dotenv parses .env files to make environment variables stored in them accessible via getenv(), $_ENV, or $_SERVER. https://symfony.com/dotenv

## 参考

* [Documentation](https://symfony.com/doc/current/index.html)
* [symfony/symfony-docs](https://github.com/symfony/symfony-docs):The Symfony documentation https://symfony.com/doc
* [中文文档](http://symfonychina.com/doc/current/index.html)
* [symfonycasts](https://symfonycasts.com/)
* [洪大师带你解读Symfony 2框架](https://www.imooc.com/learn/244)
* [](https://github.com/symfony/symfony-standard)
* [](http://symfony.com/legacy/doc/jobeet/1_2/zh_CN/01?orm=Propel)
* [](http://www.newlifeclan.com/symfony/)
