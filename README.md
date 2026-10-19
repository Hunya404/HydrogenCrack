# Hydrogen 1.1.22 — 还原工程

从 `Hydrogen-1.1.22.jar` 反向还原的 Fabric 1.21.11 客户端模组 Gradle 工程。

## 目录

- `src/main/java` — 128 个 CFR 反编译源码（已从 intermediary 重映射到 Yarn named）
- `precompiled/classes-named.jar` — 589 个无法用 Java 表达的反编译类原始字节码（named 版）
- `src/main/resources` — 原 jar 资源（`fabric.mod.json`、`hydrogen.mixins.json`、`assets/`、内嵌 jar）
- `libs/` — 本地化的第三方依赖、Fabric API、Yarn mappings
- `.m2repo`（上级目录）— 离线 Maven 仓库
- `.guh`（上级目录）— 离线 Gradle 缓存

## 构建

```bat
set JAVA_HOME=C:\Users\Admin\.gradle\jdks\eclipse_adoptium-25-amd64-windows.2
set GRADLE_USER_HOME=Z:\Admin\Crak\.guh
gradle build --offline --no-daemon
```

产物：`build/libs/hydrogen-1.1.22.jar`（约 95.9 MB，1072 条目）。

## 关键实现

- Loom 1.17.21 + Gradle 9.5.0 + JDK 25（toolchain 目标 Java 21）
- `mappings "net.fabricmc:yarn:1.21.11+build.6:v2"`
- 预编译类以 `compileOnly` 参与编译，`jar` 任务用 `zipTree` 合并进产物
- Live2D 类与 `META-INF/jars/*.jar` 一起合入，保持原 jar 结构
- Loom `remapJar` 会把全部类统一重映射回 intermediary，与原始 mod 的发布格式一致

## 已知局限

- 444 个类因控制流平坦化只能保留字节码，无法还原为可读 Java
- `rebel.xml`（IDE 元数据）未保留
