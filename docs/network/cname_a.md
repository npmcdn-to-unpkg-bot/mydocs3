# CNAME, A记录等

## 域名解析

域名解析就是域名到IP地址的转换过程。IP地址是网路上标识您站点的数字地址，为了简单好记，采用域名来代替ip地址标识站点地址。域名的解析工作由DNS服务器完成。

## A记录

A记录是用来指定主机名（或域名）对应的IP地址记录。用户可以将该域名下的网站服务器指向到自己的web server上。同时也可以设置您域名的二级域名。

## MX记录

MX记录邮件路由记录，用户可以将该域名下的邮件服务器指向到自己的mail server上，然后即可自行操控所有的邮箱设置。您只需在线填写您服务器的IP地址，即可将您域名下的邮件全部转到您自己设定相应的邮件服务器上。

## CNAME记录

CNAME记录，即：`别名记录`。这种记录允许您将多个名字映射到同一台计算机。 
通常用于同时提供WWW和MAIL服务的计算机。

例如，有一台计算机名为`host.mydomain.com`（A记录）。 它同时提供WWW和MAIL服务，
为了便于用户访问服务。可以为该计算机设置两个别名（CNAME）：WWW和MAIL。 

这两个别名的全称就是`www.mydomain.com`和`mail.mydomain.com`。实际上他们都指向`host.mydomain.com`。

## TTL值

TTL值全称是`生存时间（Time To Live)`，简单的说它表示DNS记录在DNS服务器上缓存时间。

## 例子

使用dig命令，查看域名`258i.com`的域名解析信息：

    $ dig 258i.com

    ; <<>> DiG 9.8.3-P1 <<>> 258i.com
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 3020
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 3, AUTHORITY: 2, ADDITIONAL: 21

    ;; QUESTION SECTION:
    ;258i.com.          IN  A

    ;; ANSWER SECTION:
    258i.com.       900 IN  CNAME   hdm0571.gotoip1.com.
    hdm0571.gotoip1.com.    900 IN  CNAME   hkip190.800cdn.com.
    hkip190.800cdn.com. 600 IN  A   113.10.166.190

    ;; AUTHORITY SECTION:
    800cdn.com.     394 IN  NS  ns2.dnsv5.com.
    800cdn.com.     394 IN  NS  ns1.dnsv5.com.

    ;; ADDITIONAL SECTION:
    ns1.dnsv5.com.      93389   IN  A   221.204.186.11
    ns1.dnsv5.com.      93389   IN  A   112.90.143.36
    ns1.dnsv5.com.      93389   IN  A   115.236.151.143
    ns1.dnsv5.com.      93389   IN  A   115.236.151.180
    ns1.dnsv5.com.      93389   IN  A   119.28.48.224
    ns1.dnsv5.com.      93389   IN  A   119.167.195.9
    ns1.dnsv5.com.      93389   IN  A   125.39.213.166
    ns1.dnsv5.com.      93389   IN  A   125.39.213.190
    ns1.dnsv5.com.      93389   IN  A   182.140.167.191
    ns1.dnsv5.com.      93389   IN  A   183.60.57.192
    ns1.dnsv5.com.      93389   IN  A   183.60.59.217
    ns2.dnsv5.com.      96378   IN  A   183.60.59.218
    ns2.dnsv5.com.      96378   IN  A   115.236.151.142
    ns2.dnsv5.com.      96378   IN  A   115.236.151.190
    ns2.dnsv5.com.      96378   IN  A   115.236.151.191
    ns2.dnsv5.com.      96378   IN  A   119.28.48.222
    ns2.dnsv5.com.      96378   IN  A   122.225.217.194
    ns2.dnsv5.com.      96378   IN  A   180.153.10.169
    ns2.dnsv5.com.      96378   IN  A   180.153.10.191
    ns2.dnsv5.com.      96378   IN  A   180.153.162.153
    ns2.dnsv5.com.      96378   IN  A   183.60.57.191

    ;; Query time: 128 msec
    ;; SERVER: 172.22.1.253#53(172.22.1.253)
    ;; WHEN: Fri Nov 21 12:39:33 2014
    ;; MSG SIZE  rcvd: 479
