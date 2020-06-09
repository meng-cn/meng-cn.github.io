Blog
==== 

New post
------------
```
$ hexo new [layout] <title>
```

New page
------------
```
$ cd site-dir
$ hexo new page <page-name>
```

Config
------------
- project config: _config.yml
- theme config: themes/theme-name/_config.yml

Test
------------
```
$ hexo server
```

Questions
------------
* 使用 next 主题后，Travis CI 部署后网站无法访问

    ```
    $ rm themes/next/.git
    $ git rm --cached themes/next/
    $ git add directory
    $ git commit
    $ git push
    ```
    原因可能是 git clone next-repo themes/next 方式接入的 next 主题，提交时只是以 sub-module 存在，没有真正增加文件到项目中，Travis CI 不能正常集成部署这种依赖。

Related Links
------------
- [Hexo](https://hexo.io/zh-cn/docs/)
- [Travis CI](https://travis-ci.com/)
- [Next Theme](http://theme-next.iissnan.com/)
- [Valine](https://valine.js.org/)
- [LeanCloud](https://leancloud.cn/)
