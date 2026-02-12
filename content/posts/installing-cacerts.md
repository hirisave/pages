---
title: "Installing CA certs into the JVM truststore"
date: "2025-02-12"
tags:
  - java
  - ssl
  - certificates
---

When you're hitting an internal API over HTTPS and the JVM doesn't trust the corp CA, you get the usual `PKIX path building failed` or `unable to find valid certification path` errors. Fix: add the server's cert (or the CA that signed it) into Java's cacerts.

**One-liner** (replace paths and alias as needed):

```bash
sudo "$JAVA_HOME/bin/keytool" -importcert -alias "api.corp.intranet" -file "/Users/user/Downloads/api.corp.intranet.cer" -keystore "$JAVA_HOME/lib/security/cacerts" -storepass changeit -noprompt
```

**What to change:**

- **`-alias`** — Any label you'll recognize (e.g. the hostname). Must be unique in the keystore.
- **`-file`** — Path to the `.cer` (or `.crt`) you got from the server or your security team.
- **`-keystore`** — Default truststore is `$JAVA_HOME/lib/security/cacerts`. Leave as-is unless you use a custom one.
- **`-storepass`** — Default password for the stock cacerts is `changeit`. Yes, really.

**Why `sudo`?** The default cacerts is under `$JAVA_HOME/lib/security/`, which is usually root-owned. One-time add, then your JVM trusts that cert.

After this, restart the app (or JVM) so it reloads the truststore. No more PKIX errors for that host.
