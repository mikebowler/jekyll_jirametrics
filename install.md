---
layout: page
permalink: /install/
title: Installation / Upgrade instructions
---

{: .important }
Version 3.0 now requires a newer version of ruby. See the [changelog]({% link changes.md %}) for details.

JiraMetrics should run on any Ruby runtime that supports version **3.4** or higher. Instructions below are for CRuby, which is also known as MRI, and for JRuby, which runs on the Java Virtual Machine (JVM).

Unless you have a reason to want to run on the JVM, I'd recommend using CRuby.

## CRuby (formerly called MRI Ruby)

### Install

{: .tip }
If you're on a mac, you may have noticed that Ruby is already installed on your machine. The bad news is that it's likely too old to be useful. Run `ruby -v` and if the version is less than 3.4 then you'll still need to install a newer version.

1. Install CRuby itself with [the instructions here](https://www.ruby-lang.org/en/downloads/). If you're using Windows, there is a one-click installer. If you're on almost any other platform, your local package manager will have a way to do that. In my case, I'm using a mac so it's as simple as `brew install ruby`
2. Once Ruby is installed, you can install JiraMetrics with this command.
```
gem install jirametrics
```
3. You can then invoke `jirametrics` directly from the terminal.
4. Optional: If you want to use the dependency report then you'll want to install [graphviz](https://graphviz.org/download/)

### Upgrade

When it's already installed, you can upgrade to the lastest with `gem install jirametrics` again

## JRuby

How you run the commands in this section depends on how JRuby is installed:

- **As a separate binary** installed alongside another Ruby: prefix each command with `jruby -S` (as shown below) so it runs under JRuby instead of your default Ruby.
- **As your active Ruby**, for example through a version manager like [rvm](https://rvm.io), [rbenv](https://github.com/rbenv/rbenv), or [asdf](https://asdf-vm.com): `ruby`, `gem`, and `jirametrics` already point at JRuby, so drop the `jruby -S` prefix and run the commands exactly as in the CRuby instructions above.

### Install

1. Install [JRuby](https://www.jruby.org/download) 10 or higher.
2. Set the JAVA_OPTS environment variable as shown here. If you don't do that then the tool will hang when it tries to generate the dependency report.
```
export JAVA_OPTS="--add-opens java.base/sun.nio.ch=ALL-UNNAMED --add-opens java.base/java.io=ALL-UNNAMED"
```
3. Install `jirametrics` with...
```
jruby -S gem install jirametrics
```
4. You will now invoke the tool as `jruby -S jirametrics` (or just `jirametrics` when JRuby is your active Ruby) instead of the plain `jirametrics` the rest of the instructions use. Everything else is the same.
5. Optional: If you want to use the dependency report then you'll want to install [graphviz](https://graphviz.org/download/)

### Upgrade

When it's already installed, you can upgrade to the lastest with `jruby -S gem install jirametrics` again

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

### Docker

What if you want to deploy this as a docker image? The short answer is that we don't provide any direct support for this but there is another project that does. [Check it out.](https://github.com/magicdude4eva/docker-jirametrics) 