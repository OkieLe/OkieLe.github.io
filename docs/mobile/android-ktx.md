---
layout: post
title: Android KTX
parent: Mobile
nav_order: 3
date: 2022-05-06
---

# Android KTX

## OVERVIEW

[What](https://developer.android.com/kotlin/ktx)

- A set of Kotlin extensions
- Extensions to Jetpack, Android platform and other APIs

Including

- Extension functions
- Extension properties
- Lambdas
- Named parameters
- Parameter default values
- Coroutines

A sample

```other
sharedPreferences
    .edit()  // create an Editor
    .putBoolean("key", value)
    .apply() // write to disk asynchronously
```

```other
// Commit a new value asynchronously
sharedPreferences.edit { putBoolean("key", value) }

// Commit a new value synchronously
sharedPreferences.edit(commit = true) { putBoolean("key", value) }
```

### MODULES

#### Android Platform

- Core KTX

```other
implementation "androidx.core:core-ktx:x.x.x"
```

- [androidx.core.animation](https://developer.android.com/reference/kotlin/androidx/core/animation/package-summary)

```other
// Add a listener for animator
ValueAnimator.ofFloat(0f, 1f)
	.apply {
		duration = ValueAnimator.DURATION_INFINITE
		repeatMode = ValueAnimator.REVERSE
	}.let {
		it.doOnStart {
			// Action on start
		}

		it.doOnRepeat {
			// Action on each repeat
		}
		it.start()
	}
```

- [androidx.core.content](https://developer.android.com/reference/kotlin/androidx/core/content/package-summary)

```other
class CustomView @JvmOverloads constructor(
	context: Context,
	attributeSet: AttributeSet? = null,
	defStyleAttr: Int = 0,
	defStyleRes: Int = 0
) : View(context, attributeSet, defStyleAttr, defStyleRes) {

	init {
		context.withStyledAttributes(attributeSet, R.styleable.CustomView, defStyleAttr,
			defStyleRes) {
			use {
				val label = getStringOrThrow(R.styleable.CustomView_label)
				setLabel(label)
			}
		}
	}

	fun setLabel(label: String) {}
}
```

- [androidx.core.content.res](https://developer.android.com/reference/kotlin/androidx/core/content/res/package-summary)
- [androidx.core.database](https://developer.android.com/reference/kotlin/androidx/core/database/package-summary)
- [androidx.core.database.sqlite](https://developer.android.com/reference/kotlin/androidx/core/database/sqlite/package-summary)
- [androidx.core.graphics](https://developer.android.com/reference/kotlin/androidx/core/graphics/package-summary)

```other
Bitmap.get()
Bitmap.contains()
Bitmap.set()
Bitmap.scale()
```

```other
Canvas.withClip()
Canvas.withRotation()
```

- [androidx.core.graphics.drawable](https://developer.android.com/reference/kotlin/androidx/core/graphics/drawable/package-summary)

```other
Bitmap.toAdaptiveIcon()
Bitmap.toDrawable(resources: Resources)
Bitmap.toIcon()

// Create a ColorDrawable from this color value.
Int.toDrawable()
Color.toDrawable()

Uri.toIcon()
```

- [androidx.core.location](https://developer.android.com/reference/kotlin/androidx/core/location/package-summary)
- [androidx.core.net](https://developer.android.com/reference/kotlin/androidx/core/net/package-summary)

```other
Uri.toFile()

File.toUri()

String.toUri()
```

- [androidx.core.os](https://developer.android.com/reference/kotlin/androidx/core/os/package-summary)
- [androidx.core.text](https://developer.android.com/reference/kotlin/androidx/core/text/package-summary)
- [androidx.core.transition](https://developer.android.com/reference/kotlin/androidx/core/transition/package-summary)
- [androidx.core.util](https://developer.android.com/reference/kotlin/androidx/core/util/package-summary)
- [androidx.core.view](https://developer.android.com/reference/kotlin/androidx/core/view/package-summary)

```other
View.doOnAttach {}
View.doOnLayout {}
View.setPadding()

ViewGroup.contains(view: View)
ViewGroup.isEmpty()
ViewGroup.forEach {}
ViewGroup.children()
```

- [androidx.core.widget](https://developer.android.com/reference/kotlin/androidx/core/widget/package-summary)

#### Jetpack

- Activity KTX

```other
implementation "androidx.activity:activity-ktx:x.x.x"
```

```other
// Add a OnBackPressedCallback in Activity/Fragment
onBackPressedDispatcher.addCallback(owner = this, enabled = true) {
	// Action when back pressed
}
```

```other
// in a class extending ComponentActivity
val viewModel by viewModels<MyViewModel>()
```

- Collection KTX

```other
implementation "androidx.collection:collection-ktx:x.x.x"
```

```other
// Combine 2 ArraySets into 1.
val combinedArraySet = arraySetOf(1, 2, 3) + arraySetOf(4, 5, 6)

// Combine with numbers to create a new sets.
val newArraySet = combinedArraySet + 7 + 8
```

- Fragment KTX

```other
implementation "androidx.fragment:fragment-ktx:x.x.x"
```

```other
fragmentManager().commit {
	addToBackStack("...")
	setCustomAnimations(
		R.anim.enter_anim,
		R.anim.exit_anim)
	add(fragment, "...")
}
```

```other
// Get a reference to the ViewModel scoped to this Fragment
val viewModel by viewModels<MyViewModel>()

// Get a reference to the ViewModel scoped to its Activity
val viewModel by activityViewModels<MyViewModel>()
```

```other
// Find a Fragment associated with a View.
customView.findFragment<CustomFragment>()
```

- Lifecycle KTX

```other
implementation "androidx.lifecycle:lifecycle-runtime-ktx:x.x.x"
```

```other
class MyFragment: Fragment() {
	override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
		super.onViewCreated(view, savedInstanceState)
		viewLifecycleOwner.lifecycleScope.launch {
			val params = TextViewCompat.getTextMetricsParams(textView)
			val precomputedText = withContext(Dispatchers.Default) {
				PrecomputedTextCompat.create(longTextContent, params)
			}
			TextViewCompat.setPrecomputedText(textView, precomputedText)
		}
	}
}
```

- LiveData KTX

```other
implementation "androidx.lifecycle:lifecycle-livedata-core-ktx:x.x.x"
implementation "androidx.lifecycle:lifecycle-livedata-ktx:x.x.x"
```

```other
val user: LiveData<User> = liveData {
	val data = database.loadUser() // loadUser is a suspend function.
	emit(data)
}
```

- Reactive Streams KTX

```other
implementation "androidx.lifecycle:lifecycle-reactivestreams-ktx:x.x.x"
```

```other
val fun getUsersLiveData() : LiveData<List<User>> {
	val users: Flowable<List<User>> = dao.findUsers()
	return LiveDataReactiveStreams.fromPublisher(users)
}
```

```other
LiveData<T>.asFlow()

Flow<T>.asLiveData(context: CoroutineContext = EmptyCoroutineContext, timeoutInMs: Long = DEFAULT_TIMEOUT)
```

```other
LifecycleOwner.whenCreated {}
LifecycleOwner.whenResumed {}
LifecycleOwner.whenStarted {}
Lifecycle.whenStateAtLeast() {}
Lifecycle.coroutineScope()
LifecycleOwner.lifecycleScope()
```

- Navigation KTX

```other
implementation "androidx.navigation:navigation-runtime-ktx:x.x.x"
implementation "androidx.navigation:navigation-fragment-ktx:x.x.x"
implementation "androidx.navigation:navigation-ui-ktx:x.x.x"
```

```other
class MyDestination : Fragment() {
	// Type-safe arguments are accessed from the bundle.
	val args by navArgs<MyDestinationArgs>()
	...

	override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
		view.findViewById<Button>(R.id.next)
			.setOnClickListener {
				// Fragment extension added to retrieve a NavController from
				// any destination.
				findNavController().navigate(R.id [http://R.id].action_to_next_destination)
			}
	}

	...
}
```

- Room KTX

```other
implementation "androidx.room [http://androidx.room]:room-ktx:x.x.x"
```

```other
@Query("SELECT * FROM Users")
suspend fun getUsers(): List<User>

@Query("SELECT * FROM Users")
fun getUsers(): Flow<List<User>>
```

```other
RoomDatabase.withTransaction {
}
```

- SQLite KTX

```other
implementation "androidx.sqlite:sqlite-ktx:x.x.x"
```

```other
db.transaction {
	// insert data
}
```

- ViewModel KTX

```other
implementation "androidx.lifecycle:lifecycle-viewmodel-ktx:x.x.x"
```

```other
class MainViewModel : ViewModel() {
	// Make a network request without blocking the UI thread
	private fun makeNetworkRequest() {
		// launch a coroutine in viewModelScope
		viewModelScope.launch {
			remoteApi.slowFetch()
			...
		}
	}

	// No need to override onCleared()
}
```

- WorkManager KTX

```other
implementation "androidx.work [http://androidx.work]:work-runtime-ktx:x.x.x"
```

```other
class CoroutineDownloadWorker(context: Context, params: WorkerParameters)
	: CoroutineWorker(context, params) {
	override suspend fun doWork(): Result = coroutineScope {
		val jobs = (0 until 100).map {
			async {
				downloadSynchronously("https://www.google.com [https://www.google.com]")
			}
		}
		// awaitAll will throw an exception if a download fails, which
		// CoroutineWorker will treat as a failure
		jobs.awaitAll()
		Result.success()
	}
}
```

```other
// Inside of a coroutine...
// Run async operation and suspend until completed.
WorkManager.getInstance()
	.beginWith(longWorkRequest)
	.enqueue().await()
// Resume after work completes...
```

#### Others

- Firebase KTX
- Google Maps Platform KTX
- Play Core KTX

## PRACTICE

- Default parameters
- Properties for convenience
- Wrap more logics
- Bulk actions(chained → lambda)
- Help to release resource
- For flow or coroutines

