---
title: "Switching between multiple Java versions (macOS)"
date: "2025-01-22"
tags:
  - java
  - shell
  - mac
---

On macOS, `java_home` gives you the path for a given JDK version. Wire that into env vars and aliases and you can switch JVMs from the shell without touching system settings.

Drop this into your `~/.zshrc` or `~/.bash_profile`:

```bash
# Java
export JAVA8=`/usr/libexec/java_home -v 1.8`
export JAVA11=`/usr/libexec/java_home -v 11`
export JAVA17=`/usr/libexec/java_home -v 17`
export JAVA21=`/usr/libexec/java_home -v 21`

# default version of java
export JAVA_HOME=$JAVA21

# aliases for dynamic JVM switching
alias java8="export JAVA_HOME=\$JAVA8"
alias java11="export JAVA_HOME=\$JAVA11"
alias java17="export JAVA_HOME=\$JAVA17"
alias java21="export JAVA_HOME=\$JAVA21"
```

**Usage:** Open a new terminal (or `source ~/.zshrc`). Default is Java 21. Run `java8`, `java11`, `java17`, or `java21` to switch for that session. `java -version` and any tool that reads `JAVA_HOME` will use the active one.

Only include the versions you actually have installed—`java_home` will error if the JDK isn’t there.
