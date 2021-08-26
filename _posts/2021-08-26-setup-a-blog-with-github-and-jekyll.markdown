---
layout: post
title: Setup a blog with github and jekyll
categories: misc
tags: [misc]
---

## Setup blog locally
Before publish it to github, we need test it locally, it's useful as we can preview the content before post it.
### Install prerequisites
* sudo apt-get install ruby-full build-essential zlib1g-dev
* echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
* echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
* echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
* source ~/.bashrc

### Install jekyll and bundler
* gem install jekyll bundler

### Download jekyll template
* git clone git@github.com:jeffreytse/jekyll-theme-yat.git
> This is what my blog use.

### Build the site and make it available on a local server
* bundle exec jekyll serve

if you want to change port, please add -P, e.g.
* bundle exec jekyll serve -P 8080

### Browse to http://localhost:4000

## Add to github
It's easy, please google to setup github.io and push all source code of this template to related repository.

## Change gitment to utterances
gitment is included in this template by default, but it's not maintained by the owner anymore and it's can not work now. so I change it to utterances.

1.Create a new repository
As utterances is based on github's issue system, so we need to create a new repository to store issues.
e.g. You can create a repository named blog_comment.

2.Install [utterances app][2] on the repo.

3.change _config.yml, add bellowing:

    utterance:
      enable: "true"

4.create a new file _includes/extensions/comments/utterance.html
```javascript
      <script src="https://utteranc.es/client.js"
            repo="successcw/blog_comment"
            issue-term="pathname"
            theme="github-light"
            crossorigin="anonymous"
            async>
      </script>
```

5.modify _layouts/post.html

```
{% raw %}

@@ -69,6 +69,10 @@ sidebar:
       {%- include extensions/comments/gitment.html -%}
       {%- endif -%}

+      {%- if site.utterance.enable -%}
+      {%- include extensions/comments/utterance.html -%}
+      {%- endif -%}

{% endraw %}
```
## Reference
1.[Jekyll][1]

2.[Utterances][3]


  [1]: https://jekyllrb.com/docs/
  [2]: https://github.com/apps/utterances
  [3]: https://utteranc.es/?installation_id=19082772&setup_action=install
