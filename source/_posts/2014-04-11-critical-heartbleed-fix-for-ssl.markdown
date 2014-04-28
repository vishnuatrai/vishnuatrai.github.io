---
layout: post
title: "Critical Heartbleed fix for SSL"
date: 2014-04-11 15:30:33 +0530
comments: true
categories: 
  OpenSSL
  Valunability

---

OpenSSL heartbleed bug allows hackers to untraceably read server traffic and some server memory. This implementation mistake leads to the leak of memory information from the server to the client and from the client to the server. <!--more--> 


For my Ruby on Rails application deployed on AWS, the minimal steps I took to upgrade fixed OpenSSL version given below - 

  1) Update OpenSSL to 1.0.1g

    download source from here http://www.openssl.org/source/openssl-1.0.1g.tar.gz
    tar -zxf openssl-1.0.1g.tar.gz 
    cd openssl-1.0.1g
    ./config
    make
    make test
    make install

    openssl version
    
    if this will show older version then do below steps
    
    ln -s /usr/local/ssl/bin/openssl /usr/bin/openssl

  2) Recompile/reinstall ruby with new openssl version

  3) Recompile/reinstall libriaries or gems those are related to openssl

  4) Reboot the server

  5) Regenerate new private key and csr to generate new SSL certificate

  6) Change server access keys and passwords

  7) Change API keys, passwords, tokens

  8) Cahnge session secret key for cookie based session store

  9) Restart the web and app servers

  10) You can ask your application users to change their password



