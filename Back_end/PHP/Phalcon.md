# Phalcon Framework

## Add extension phalcon

### Ubuntu

```sh
curl -s https://packagecloud.io/install/repositories/phalcon/stable/script.deb.sh | sudo bash
sudo apt-get install php5.6-phalcon
sudo service php5.6-fpm start

sudo apt-get install php7.0-phalcon
```

### Mac

```sh
brew install php71-phalcon
```

## Phalcon Developer Tools

```sh
git clone git://github.com/phalcon/phalcon-devtools.git
cd phalcon-devtools/
./phalcon.sh
ln -s ~/phalcon-devtools/phalcon.php /usr/bin/phalcon
chmod ugo+x /usr/bin/phalcon
```

## 仓库

- [phalcon/cphalcon](https://github.com/phalcon/cphalcon):High performance, full-stack PHP framework delivered as a C extension. <https://phalconphp.com>
- [Phalcon中文文档](http://docs.iphalcon.cn/)
- [documentation](https://docs.phalconphp.com/en/3.2)