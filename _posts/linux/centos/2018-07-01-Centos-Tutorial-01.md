---
layout: post
title: Centos Tutorial 01 ( Centos 概述 ) 
category: centos
tags: [centos]
---

[samuel@VM_0_16_centos ~]$ curl -L https://get.rvm.io | bash -s stable

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   194  100   194    0     0    361      0 --:--:-- --:--:-- --:--:--   361
100 24535  100 24535    0     0   7025      0  0:00:03  0:00:03 --:--:--  6915
Downloading https://github.com/rvm/rvm/archive/1.29.9.tar.gz
Downloading https://github.com/rvm/rvm/releases/download/1.29.9/1.29.9.tar.gz.asc
gpg: Signature made Wed 10 Jul 2019 04:31:02 PM CST using RSA key ID 39499BDB
gpg: Can't check signature: No public key
GPG signature verification failed for '/usr/local/rvm/archives/rvm-1.29.9.tgz' - 'https://github.com/rvm/rvm/releases/download/1.29.9/1.29.9.tar.gz.asc'! Try to install GPG v2 and then fetch the public key:

    gpg2 --keyserver hkp://pool.sks-keyservers.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB

or if it fails:

    command curl -sSL https://rvm.io/mpapis.asc | gpg2 --import -
    command curl -sSL https://rvm.io/pkuczynski.asc | gpg2 --import -

In case of further problems with validation please refer to https://rvm.io/rvm/security





gpg2 --keyserver hkp://pool.sks-keyservers.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB

[root@VM_0_16_centos ~]# curl -L get.rvm.io | bash -s stable

rvm -v

rvm list known

[root@VM_0_16_centos ~]# rvm install 2.6



Your user account isn't allowed to install to the system RubyGems.


[samuel@VM_0_16_centos test]$ bundle install --path vendor/bundle
