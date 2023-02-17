+++
title = "Install Older and Newer PHP version on AWS EC2"
date = "2023-02-17T13:15:05+0000"
authors = ["kn"]
cover = "posts/ec2-aws-php-differversion-installation/php.png"
tags = ["php", "aws", "centOS"]
keywords = ["php", "aws"]
description = "Install Older And Newer PHP version on AWS ec2"
showFullContent = false
+++

## To install older or newer version of PHP with AWS utility

```bash
amazon-linux-extras enable php7.1
amazon-linux-extras install php7.1
```

## Install PHP extension
```bash
yum update
yum install php-cli php-pdo php-fpm php-json php-mysqlnd
```