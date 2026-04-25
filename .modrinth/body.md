# PostgreSQL JDBC

The official PostgreSQL JDBC driver ([pgjdbc](https://github.com/pgjdbc/pgjdbc)) repackaged as a Bukkit/Spigot/Paper plugin and a Fabric/Forge/NeoForge mod. One universal jar covers every supported loader and MC version.

The mod does nothing on its own — it exists so plugins or mods that want to talk to a PostgreSQL database don't each have to bundle their own copy of the driver.

## What's in the jar

`org.postgresql:postgresql:42.7.5` plus minimal loader stubs for Spigot, Forge 1.12, Forge 1.13–1.16, Forge 1.17–1.20, NeoForge 1.21+, and Fabric. The driver classes stay at their canonical `org.postgresql.*` paths — no relocation — so consumers find them with plain `Class.forName("org.postgresql.Driver")`. `META-INF/services/java.sql.Driver` is preserved so the driver auto-registers with `DriverManager`.

## Compatibility

| Loader | MC versions | Notes |
|---|---|---|
| Bukkit / Spigot / Paper / Folia / Purpur | 1.8 → current | drop into `plugins/` |
| Fabric | 1.16.1 → current | needs Fabric Loader 0.14+ |
| Forge | 1.12 → 1.20 | universal jar, no Mixins |
| NeoForge | 1.21 → current | drop into `mods/` |

Java 8+ required (pgjdbc 42.x baseline).

## Using it from a plugin or mod

Declare pgjdbc as `compileOnly` against the same version this jar ships:

```kotlin
compileOnly("org.postgresql:postgresql:42.7.5")
```

Probe at startup. Fall through gracefully if the operator hasn't installed this:

```java
try {
    Class.forName("org.postgresql.Driver");
} catch (ClassNotFoundException e) {
    getLogger().warning("PostgreSQL backend disabled — install minecraft-postgresql-jdbc");
    return;
}
try (Connection c = DriverManager.getConnection(url, user, password)) {
    // ...
}
```

On Paper 1.17+ each plugin's classloader is isolated. Add this mod to your `plugin.yml` `softdepend` so the driver classes are visible to your plugin:

```yaml
softdepend: [minecraft-postgresql-jdbc]
```

Fabric and NeoForge unify all mods on one classloader, so no equivalent declaration is needed there.

## Versioning

The jar version tracks pgjdbc one-to-one. `42.7.5+2026-04-25` ships pgjdbc 42.7.5; the suffix is the build date. A scheduled GitHub Action checks Maven Central daily — when pgjdbc cuts a new release, it opens an auto-merge PR and the Modrinth release goes out automatically.

## License

The driver is BSD-2-Clause (PostgreSQL Global Development Group). The repackage adds no functional changes and inherits that license. Full text in [`LICENSE`](https://github.com/Axionize/minecraft-postgresql-jdbc/blob/main/LICENSE).

---

Issues, source: [GitHub](https://github.com/Axionize/minecraft-postgresql-jdbc).
