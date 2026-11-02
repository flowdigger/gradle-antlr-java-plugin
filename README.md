# Gradle ANTLR Java Plugin

![Conformance](https://img.shields.io/badge/Conformance-Check--All%20Passing-brightgreen)

[![Build](https://github.com/flowdigger/gradle-antlr-java-plugin/actions/workflows/ci.yml/badge.svg)](https://github.com/flowdigger/gradle-antlr-java-plugin/actions/workflows/ci.yml)
[![Release](https://github.com/flowdigger/gradle-antlr-java-plugin/actions/workflows/release.yml/badge.svg)](https://github.com/flowdigger/gradle-antlr-java-plugin/actions/workflows/release.yml)
[![Coverage CI](https://github.com/flowdigger/gradle-antlr-java-plugin/actions/workflows/coverage.yml/badge.svg)](https://github.com/flowdigger/gradle-antlr-java-plugin/actions/workflows/coverage.yml)
[![CodeQL](https://github.com/flowdigger/gradle-antlr-java-plugin/actions/workflows/codeql.yml/badge.svg)](https://github.com/flowdigger/gradle-antlr-java-plugin/actions/workflows/codeql.yml)
[![Dependency Check](https://github.com/flowdigger/gradle-antlr-java-plugin/actions/workflows/dependency-check.yml/badge.svg)](https://github.com/flowdigger/gradle-antlr-java-plugin/actions/workflows/dependency-check.yml)
[![SpotBugs Security](https://github.com/flowdigger/gradle-antlr-java-plugin/actions/workflows/spotbugs-security.yml/badge.svg)](https://github.com/flowdigger/gradle-antlr-java-plugin/actions/workflows/spotbugs-security.yml)
[![Dependabot](https://img.shields.io/badge/dependabot-enabled-025E8C?logo=dependabot)](https://github.com/flowdigger/gradle-antlr-java-plugin/security/dependabot)
[![Coverage](https://codecov.io/gh/flowdigger/gradle-antlr-java-plugin/graph/badge.svg?branch=main)](https://codecov.io/gh/flowdigger/gradle-antlr-java-plugin?branch=main)
[![Maven Central](https://img.shields.io/maven-central/v/org.flowdigger.gradle/gradle-antlr-java-plugin.svg)](https://search.maven.org/artifact/org.flowdigger.gradle/gradle-antlr-java-plugin)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Java](https://img.shields.io/badge/java-21+-green.svg)](https://www.oracle.com/java/)
[![Gradle](https://img.shields.io/badge/gradle-9.5+-blue.svg)](https://gradle.org/)

`gradle-antlr-java-plugin` provides preconfigured XML AST conversion support for Java sources using ANTLR Java grammars.

Grammar coverage includes finalized Java 21 pattern syntax, including record patterns and pattern matching for `switch` (preview syntax excluded).

## Position in XIR spine

- **Problem solved**: convert Java code into consistent structural AST artifacts.
- **Relation to XIR**: observation adapter on top of `gradle-antlr-plugin` for Java grammar domain.
- **Why it matters**: standardizes source evidence used later in derivation and knowledge applications.

```text
Structured Knowledge -> XIR -> Observation -> Derivation -> Knowledge -> Applications
```

## Install

```groovy
plugins {
    id 'org.flowdigger.gradle.antlr.java' version '0.1.4'
}
```

## What Plugin Adds

- `XmlAstJavaGradleTask` task type
- `javaXmlAst` pre-registered task
- Runtime classpath and `classes` dependency wiring when `java` plugin present

Default task conventions:

- `grammar = java`
- `parserClassName = org.flowdigger.parsers.JavaParser`
- `lexerClassName = org.flowdigger.parsers.JavaLexer`
- `startRule = compilationunit`
- `includes = ['**/*.java']`

## Quick Start

```groovy
plugins {
    id 'java'
    id 'org.flowdigger.gradle.antlr.java'
}

tasks.named('javaXmlAst', org.flowdigger.gradle.antlr.XmlAstJavaGradleTask) {
    sourceDirectory.set(layout.projectDirectory.dir('src/main/java'))
    destinationDirectory.set(layout.buildDirectory.dir('xmlast-java'))
    targetExtension.set('.xml')
    continueOnError.set(true)
}
```

Run:

```bash
./gradlew javaXmlAst
```

S-expression output variant:

```groovy
tasks.named('javaXmlAst', org.flowdigger.gradle.antlr.XmlAstJavaGradleTask) {
    targetExtension.set('.xir')
    outputFormat.set('beautified')
}
```

## Testing and Coverage

- Java grammar sources:
  - `src/main/antlr/org/flowdigger/parsers/JavaLexer.g4`
  - `src/main/antlr/org/flowdigger/parsers/JavaParser.g4`
- Example corpus copied from `antlr/grammars-v4`:
  - `src/test/resources/examples`

Commands:

```bash
./gradlew clean check
./gradlew coverage
./gradlew allSecurityChecks
```

Coverage gate enforced in `check`: **60% line coverage minimum**.

## Release to Maven Central

Set credentials and signing material:

```bash
export ORG_GRADLE_PROJECT_mavenCentralUsername=...
export ORG_GRADLE_PROJECT_mavenCentralPassword=...
export ORG_GRADLE_PROJECT_signingKey=...
export ORG_GRADLE_PROJECT_signingPassword=...
export ORG_GRADLE_PROJECT_signingKeyId=...
```

Build signed Central bundle:

```bash
./gradlew clean packageCentralBundle
```

## CI and Code Scans

- `.github/workflows/ci.yml` -> build + tests + SpotBugs + OWASP Dependency-Check
- `.github/workflows/coverage.yml` -> JaCoCo + Codecov upload
- `.github/workflows/codeql.yml` -> GitHub CodeQL analysis
- `.github/workflows/release.yml` -> Maven Central bundle + Central Publisher upload
- `.github/workflows/test-on-push.yml` -> fast test validation

## Troubleshooting

- `ClassNotFoundException` for parser/lexer:
  - Ensure generated sources run (`generateParserSources`)
  - Ensure `classes` completed before task execution
- No output files:
  - Check `sourceDirectory` and `includes`
  - Use `force = true` for full rerun
- Parse entry failures:
  - Confirm parser start rule exists (`compilationunit`)
