---
title: Binding a personal blog on Github to a Godaddy domain name
date: 2023-09-27 12:03:40
categories:
  - Domain name
tags:
  - Domain name
  - Godaddy
  - Github
  - CNAME
  - DNS
description: How to bind a personal blog on Github to a Godaddy domain name
---

## I. Purchase a domain name

Just go to the Godaddy website [sg.godaddy.com/zh/](https://sg.godaddy.com/zh/) and buy it yourself.

## II. Configuring Github

### 1、New CNAME file

Create a new CNAME file in the sources directory of our Hexo project with our domain name in it.

```txt
xxxx.com
```

Redeploy the project afterward:

```txt
hexo g
hexo d
```

> If you are using hexo framework to build a blog and deploy it to Github Pages.
> Every time hexo g hexo d will push everything in the public folder of your blog directory to the Github Pages repository and overwrite the CNAME file. To solve this problem, **you can directly add the CNAME file to the source folder**, so that you don't have to worry about overwriting the CNAME file in the repository every time you push it. This way, you don't have to worry about the CNAME file being overwritten in the repository every time you push.

After that we can see this file in the root directory of the Github project on our website.

>There is another way to do this: on your website's Github project, click SettingsSettings, find Custom domain, fill in the domain name you applied for, and save it. You will also see this file in the root of your Github project, but when you deploy the project each time, the CNAME file will disappear, which is essentially the same as if you had created a new CNAME and placed it in the root of your local Github project, instead of in the source folder.

### 2. Add records in DNS

Add 3 records to your DNS configuration (at the domain name resolution provider, using dnspod as an example below)

|Host | Record types | Points to|
|----|----|----|
|@ | A | 192.168.1.1|
|www | CNAME | username.github.io|

>So people can access your website with or without www (in fact, the way of www, it will be resolved to xxxx.github.io first, and then according to the CNAME to xxx.com, that is, in the middle of the conversion is once).
>Above, we use the CNAME alias record, some people also use the A record, the value of the record is to write the github page inside the ip address, but sometimes the IP address will change, resulting in the final resolution is incorrect, so it is still recommended to use the CNAME alias record is better, it is not recommended to use the IP.
Such as:
>(1) first add a CNAME, the host record write @, after the record value write your xxxx.github.io
>(2) add another CNAME, the host record write www, followed by the record value is also username.github.io, replace username with your own Github username.

### 3、Modify DNS address in GoDaddy

(1) In the My Account drop-down menu in the upper right corner, click -> My Products:

(2) Click the DNS button after the domain name:

(3) Change the domain name servers to:

```txt
f1g1ns1.dnspod.net 
f1g1ns2.dnspod.net

```

(4) Wait for your DNS configuration to take effect:

Configuration of DNS does not take effect immediately, go back to your domain name after 1 minute to see if the configuration has been successful.

## III. References

- [How to bind your own domain name to github](https://www.zhihu.com/question/31377141)
- [How to Build a Standalone Blog - A Concise Github Pages & Hexo Tutorial](http://www.jianshu.com/p/05289a4bc8b2)
- [Building a Static Personal Blog via GitHub and GoDaddy](http://www.cnblogs.com/openxxs/p/5950598.html?utm_source=itdadao&utm_medium=referral)
