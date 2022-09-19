---
layout: post
title: View Binding
parent: Mobile
nav_order: 1
date: 2022-01-06
---

# View Binding

View Binding是android gradle plugin提供的一项编译特性，可以让开发人员更轻松地编写可与视图交互的代码。

在模块中启用视图绑定之后，系统会为该模块中的每个 XML 布局文件生成一个绑定类。绑定类的实例包含对在相应布局中*具有 ID* 的所有视图的直接引用。

在大多数情况下，视图绑定可以替代 `findViewById`，也可以避免自己写View类型转换导致的各种问题。

> 以下代码基于Android Studio Arctic Fox and AGP 7.0+

## 1. 基本使用

### 1.1 打开功能

在模块的build.gradle中添加一下代码
```gradle
android {
    ...
    buildFeatures {
        viewBinding true
    }
    ...
}
```

### 1.2 使用


#### 添加布局

```xml
<!-- layout_main.xml -->
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:id="@+id/text_hello"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello, world!"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

#### 在代码中使用

##### a. Activity

```kotlin
class MainAcitivty : AppCompatActivity() {
    private lateinit var binding: LayoutMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        binding = LayoutMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        binding.textHello.text = "Hello, view binding!"
    }
}
```

##### b. Fragment

```kotlin
class MainFragment : Fragment() {
    private lateinit var binding: LayoutMainBinding

    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?
    ): View {
        binding = LayoutMainBinding.inflate(inflater, container, false)
        binding.textHello.text = "Hello, view binding!"
        return binding.root
    }
}
```

## 2. 主要接口/类

### 2.1 ViewBinding

接口，所有生成的Binding类都会实现此接口，该接口只有一个方法`getRoot()`，通过此方法可以获取到binding类对应布局的根View
```Java
@NonNull
View getRoot();
```

### 2.2 ***Binding

打开viewBinding功能的模块中每一个layout资源都会生成对应的`***Binding`类，`***`是根据布局文件名字自动转换为的驼峰命名。

- 生成文件路径：`build/generated/data_binding_base_class_source_out`
- 其中包含布局中所有具有 ID 的视图的直接引用，如`binding.textHello`
- 静态方法`inflate`，可能有多个重载
- 静态方法`bind`

### 2.3 inflate()

```Java
@NonNull
public static LayoutMainBinding inflate(@NonNull LayoutInflater inflater)

@NonNull
public static LayoutMainBinding inflate(@NonNull LayoutInflater inflater, @Nullable ViewGroup parent, boolean attachToParent)
```

- 这个方法用于直接从xml生成View，内部实现是先`LayourInflater.inflate`生成View，再调用`bind`方法创建`LayoutMainBinding`
- 一般情况下布局会生成上面的两个重载方法，可根据传参需求选择

### 2.4 bind()

```Java
@NonNull
public static LayoutMainBinding bind(@NonNull View rootView)
```

- 这个方法用于把`inflate`出来的View绑定到`LayoutMainBinding`，构造一个`LayoutMainBinding`对象，完成`findViewById`
- 部分场景下，可以通过别的非View Binding方式inflate出View，然后通过`bind`完成`findViewById`的过程

## 3. 特殊tag

Android官方提供了[复用布局](https://developer.android.com/training/improving-layouts/reusing-layouts)的方式。可以高效地重复使用完整的布局，使用 `<include/>` 和 `<merge/>` 标记在当前布局中嵌入其他布局。

`<include/>`可以和其他普通布局一块使用，也可以和 `<merge/>` 一起使用，下面依次说明。

### 3.1 include和View/ViewGroup

如下面的布局被include到`layout_main.xml`中
```xml
<!-- layout_inside.xml -->
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content">

    <ImageView
        android:id="@+id/inside_image"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:src="@android:drawable/ic_menu_agenda"
        android:contentDescription="Agenda" />
</FrameLayout>
```

```xml
<!-- layout_main.xml -->
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:id="@+id/text_hello"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello, world!"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <include layout="@layout/layout_inside"
        android:id="@+id/layout_inside_root"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:layout_constraintTop_toBottomOf="@id/text_hello"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"/>
</androidx.constraintlayout.widget.ConstraintLayout>
```

可以通过以下方式访问子布局中的ImageView
```kotlin
class LayoutMainFragment: Fragment() {
    private lateinit var binding: LayoutMainBinding
    private lateinit var insideBinding: LayoutInsideBinding

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        binding = LayoutMainBinding.inflate(inflater, container, false)
        insideBinding = binding.layoutInsideRoot
        insideBinding.insideImage.setImageResource(android.R.drawable.ic_menu_camera)
        return binding.root
    }
}
```

> 注意的是要给include定义一个ID，否则无法生成binding中的`layoutInsideRoot`
> 如果未给include定义ID，则无法获取`LayoutInsideBinding`对象，只能自己通过`findViewById`获取子布局对象

### 3.2 include和merge

如下面的布局被include到`layout_main.xml`中
```xml
<!-- layout_merge.xml -->
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    tools:showIn="@layout/layout_main">

    <ImageView
        android:id="@+id/inside_image"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:src="@android:drawable/ic_menu_agenda"
        android:contentDescription="Agenda" />
</merge>
```

```xml
<!-- layout_main.xml -->
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:id="@+id/text_hello"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello, world!"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <FrameLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:layout_constraintTop_toBottomOf="@id/text_hello"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent">

        <include layout="@layout/layout_merge"
            android:layout_gravity="center"
            android:layout_height="wrap_content"
            android:layout_width="wrap_content" />
    </FrameLayout>
</androidx.constraintlayout.widget.ConstraintLayout>
```

可以通过以下方式访问子布局中的ImageView
```kotlin
class LayoutMainFragment: Fragment() {
    private lateinit var binding: LayoutMainBinding
    private lateinit var mergeBinding: LayoutMergeBinding

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        binding = LayoutMainBinding.inflate(inflater, container, false)
        mergeBinding = LayoutMergeBinding.bind(binding.root)
        mergeBinding.insideImage.setImageResource(android.R.drawable.ic_menu_camera)
        return binding.root
    }
}
```

布局根节点为`merge`时，转换为`***Binding`时，inflate方法定义如下，即强制父布局不能为空。
```Java
@NonNull
public static ItemTileMetadataViewBinding inflate(@NonNull LayoutInflater inflater, @NonNull ViewGroup parent)
```

- `<include/>` 和 `<merge/>` 在inflate时都不会产生对应的View对象，所以两个一起使用时，加上ID也无法自动生成对应Binding对象

## 4. 其他Tips

- View Binding对继承不友好，比如“子类提供layout资源ID，父类负责inflate”，设计时需要提前考虑
- 单独使用`bind()`的场景，View重建后一定要重新bind，保持获取到的View都是最新的
