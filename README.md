Website
=======

Introduction
------------
Blog for xbay users.

Install
-------

    npm install -g hexo-cli
    git clone --recursive git@github.com:xbay/website.git
    cd website
    npm install

Add a blog
----------
Add a markdown file under source/_posts
write this befor your article

    title: AMD 和 CommonJS -- 1. 基本概念
    date: 2015-12-31 12:03:15
    author: [mac.meng]
    categories: [tech,intermediate]
    tags: [javascript,AMD,CommonJS]
    ---

Preview
-------

    hexo generate
    hexo server

Deploy
------

    hexo clean
    hexo deploy
