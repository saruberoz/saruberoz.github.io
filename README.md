# saruberoz.github.io
===================

This is my personal homepage + blog where I can learn from others and share stuff to everyone.


To start locally:

1. Set the following variables in your `config.yml` file:
`url: http://localhost:4000`
2. Then run `jekyll server --watch`

To add new pages

1. Do `rake new_page`
2. Then on `_config.yml`
```
links:
  - title: <new page title>
    url: <page url>
    external: true/false
```

To add new post

1. Do `rake new_post`


This site is powered by [Jekyll](http://jekyllrb.com/) and using [So Simple Theme](https://github.com/mmistakes/so-simple-theme)
