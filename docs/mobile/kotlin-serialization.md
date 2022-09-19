---
layout: post
title: Kotlin serialization
parent: Mobile
nav_order: 2
date: 2022-04-06
---

# Kotlin serialization

### 简介

Serialization是kotlin原生的序列化库，包含gradle插件`org.jetbrains.kotlin.plugin.serialization`和运行时库，目前支持的[数据格式](https://github.com/Kotlin/kotlinx.serialization/blob/master/formats/README.md)有：

[[https://github.com/Kotlin/kotlinx.serialization/blob/master/formats/README.md](https://github.com/Kotlin/kotlinx.serialization/blob/master/formats/README.md))有：]

- JSON: kotlinx-serialization-json
- Protocol buffers: kotlinx-serialization-protobuf
- CBOR: kotlinx-serialization-cbor
- Properties: kotlinx-serialization-properties
- HOCON: kotlinx-serialization-hocon (only on JVM)

> 以上除了json都是实验性的，API随时可能会修改

> 还有非官方的数据格式支持，如[YAML](https://yaml.org/)，[Avro](https://avro.apache.org/)等

### JSON数据转换

#### 配置

> IDEA默认的kotlin插件包含了serialization插件，不需另外添加依赖

> 要求kotlin compiler 1.4.0+

0. #### 添加CLASSPATH

Groovy DSL:

```other
buildscript {

dependencies {

classpath "org.jetbrains.kotlin:kotlin-serialization:1.6.10"

}

}
```

1. #### 应用GRADLE插件

Kotlin DSL:

```other
plugins {
	kotlin("jvm") version "1.6.10"
	kotlin("plugin.serialization") version "1.6.10"
}
```

Groovy DSL:

```other
plugins {
	id 'org.jetbrains.kotlin.jvm' version '1.6.10'
	id 'org.jetbrains.kotlin.plugin.serialization' version '1.6.10'
}
```

2. #### 添加JSON依赖

Kotlin DSL:

```other
dependencies {
	implementation("org.jetbrains.kotlinx:kotlinx-serialization-json:1.3.2")
}
```

Groovy DSL:

```other
dependencies {
	implementation 'org.jetbrains.kotlinx:kotlinx-serialization-json:1.3.2'
}
```

#### 声明可序列化类型

```other
import kotlinx.serialization.Serializable

@Serializable
data class Data(val a: Int, val b: String = "")
```

- SerialName：指定属性序列化/反序列化时的名字
- Required：属性反序列化时时必须存在，或者有默认值
- Transient：序列化/反序列化时忽略属性
- 允许为null的属性，需要在构造方法提供默认值

#### 序列化

```other
import kotlinx.serialization.json.Json
import kotlinx.serialization.encodeToString

fun main() {
	val json = Json.encodeToString(Data(42, "str"))
	val dataList = listOf(Data(42, "str"), Data(12, "test"))
	val jsonList = Json.encodeToString(dataList)
}
```

#### 反序列化

```other
import kotlinx.serialization.json.Json
import kotlinx.serialization.decodeFromString

fun main() {
	val obj = Json.decodeFromString<Data>("""{"a":42, "b": "str"}""")
}
```

