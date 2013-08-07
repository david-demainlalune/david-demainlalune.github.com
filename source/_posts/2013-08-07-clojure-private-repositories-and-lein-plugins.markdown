---
layout: post
title: "clojure private repositories and lein plugins"
date: 2013-08-07 16:07
comments: true
categories: 
---

A quick post for the puzzled me in the future. 

I am writing a simple leiningen plugin which given a fully qualified module name generates the corresponding source and test files. I am thinking that having a test file automatically generated for me will guilt-trip me into actually writing them. 

Given the nature of the task, I would like this plugin to be a system wide command. I found out I could do this by specifying a user profile in ~/.lein/profiles.clj

``` clojure

; ~/.lein/profiles.clj

{:user {:plugins [[lein-gen-src "0.1.0-SNAPSHOT"]]}}

```

This works... if you actually publish the plugin to clojars. This is not my case. The plugin is being developed locally. So how can I make this work?

(A word of warning, we are entering cargo cult territory for me: the magical land of java maven and pom files. In other words there is probably a better way of doing this.)

In ~/.lein/profiles.clj you can specify a :deploy-repositories attribute as being internal and pointing to a directory of static files. 


``` clojure

; ~/.lein/profiles.clj

{:user {:plugins [[lein-gen-src "0.1.0-SNAPSHOT"]]
	    :deploy-repositories [["internal" "file:///I:/clojure/internal_repositories"]]}}
```

The "internal_repositories" folder is then filled with the files produced by the "lein install" command (a .jar and a pom.xml). And lo and behold, it works. 

Of course, now I have to write a new task to copy the built plugin over to the "internal_repositories" when new versions are available.