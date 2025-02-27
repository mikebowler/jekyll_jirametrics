---
layout: page
permalink: /install/
title: Installation
---
JiraMetrics should run on any Ruby runtime that supports version 3.0 or higher. Instructions below are for CRuby, which is also known as MRI, and for JRuby, which runs on the Java Virtual Machine (JVM).

Unless you have a reason to want to run on the JVM, I'd recommend using CRuby.

## CRuby (formerly called MRI Ruby)

{: .tip }
If you're on a mac, you may have noticed that Ruby is already installed on your machine. The bad news is that it's likely too old to be useful. Run `ruby -v` and if the version is less than 3.0 then you'll still need to install a newer version.

1. Install CRuby itself with [the instructions here](https://www.ruby-lang.org/en/downloads/). If you're using Windows, there is a one-click installer. If you're on almost any other platform, your local package manager will have a way to do that. In my case, I'm using a mac so it's as simple as `brew install ruby`
2. Once Ruby is installed, you can install JiraMetrics with this command.
```
gem install jirametrics
```
3. You can then invoke `jirametrics` directly from the terminal.
4. Optional: If you want to use the dependency report then you'll want to install [graphviz](https://graphviz.org/download/)

## JRuby

1. Install [JRuby](https://www.jruby.org) 9.4 or higher. In my case that's as simple as `brew install jruby`. If you aren't using `brew` then see the [JRuby download instructions](https://www.jruby.org/download).
2. Set the JAVA_OPTS environment variable as shown here. If you don't do that then the tool will hang when it tries to generate the dependency report.
```
export JAVA_OPTS="--add-opens java.base/sun.nio.ch=ALL-UNNAMED --add-opens java.base/java.io=ALL-UNNAMED"
```
3. Install `jirametrics` with...
```
jruby -S gem install jirametrics
```
4. You will now invoke the tool with `jruby -S jirametrics` instead of just `jirametrics` as the rest of the instructions say. Everything else is the same.
5. Optional: If you want to use the dependency report then you'll want to install [graphviz](https://graphviz.org/download/)

## System package managers

### pkgsrc

For platforms with binary packages available:
```
sudo pkgin -y install 'ruby*-jirametrics'
```

Or for any platform, from source, inside a pkgsrc checkout:
```
cd devel/ruby-jirametrics
bmake install
```
