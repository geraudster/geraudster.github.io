---
layout: post
title: Installation Gitblit sur Ubuntu
tags: gitblit ubuntu
comments: true
tracking: true
share: true 
---

# Configuration

    useraddd gitblit
    ${GITBLIT_HOME}/install-service-ubuntu.sh
    chown -R gitblit:gitblit ${GITBLIT_HOME}
    service gitblit start

    openssl req -batch -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/nginx.key -out /etc/nginx/ssl/nginx.crt


# Liens

* Config proxy nginx: http://jettyapplicationserver.blogspot.fr/2013/11/proxy-pass-gitblit-and-nexus-in-nginx.html
* Config proxy Gitblit: http://gitblit.com/setup.html
* Problème HTTP 413: https://www.sudocodesystems.com/?p=98
*
