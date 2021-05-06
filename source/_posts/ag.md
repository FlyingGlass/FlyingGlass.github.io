---
title: ag安装
date: 2018-02-26 19:16:21
categories: 
- [安装配置]
---

#### Installing ag on CentOS

- ### Prerequistes

  + libpcre
  + liblzma

  ### Download, build and install

  ```bash
  sudo yum install -y pcre-devel
  sudo yum install xz-devel
  
  cd /usr/local/src && sudo git clone https://github.com/ggreer/the_silver_searcher.git
  
  cd the_silver_searcher
  sudo ./build.sh
  sudo make install
  
  which ag
  ```