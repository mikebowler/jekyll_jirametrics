---
layout: page
permalink: /jruby/
---
This page is for people who have chosen to run `jirametrics` on a Java VM. If you want to run it on native ruby then go back to the [main instructions](https://github.com/mikebowler/jirametrics/wiki).

1. Install [JRuby](https://www.jruby.org) 9.4 or higher. In my case that's as simple as `brew install jruby`. If you aren't using `brew` then see the JRuby docs.
2. Set the JAVA_OPTS environment variable as shown here. If you don't do that then the tool will hang when it tries to generate the dependency report.
```
export JAVA_OPTS="--add-opens java.base/sun.nio.ch=ALL-UNNAMED --add-opens java.base/java.io=ALL-UNNAMED"
```
3. Install `jirametrics` with...
```
jruby -S gem install jirametrics
```
4. You will now invoke the tool with `jruby -S jirametrics` instead of just `jirametrics` as the rest of the instructions say. Everything else is the same.
5. You're done. Now back to the [main instructions](https://github.com/mikebowler/jirametrics/wiki).
