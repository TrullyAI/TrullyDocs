# TrullySDK


## Implementation

Add dependencies to the Android project.

### 1.- Add these two repositories in your
###  `settings.gradle`
 
```groovy
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
        maven { url 'https://jitpack.io' }
    }
}
```
### 2.- Add the dependencies in your
###  `build.gradle (App)`
```groovy
dependencies {
    implementation("com.squareup.retrofit2:retrofit:2.9.0")
    implementation("com.squareup.retrofit2:converter-gson:2.9.0")
    implementation("com.squareup.okhttp3:logging-interceptor:4.9.3")

    implementation 'com.github.TrullyAI:FaceAPI:5.2.2715'
    implementation 'com.github.TrullyAI:FaceCore:5.2.232'
    implementation 'com.github.TrullyAI:CommonAPI:6.9.1398'
    implementation 'com.github.TrullyAI:DocumentReaderFullAuth:6.9.9555'
    implementation 'com.github.TrullyAI:DocumentReaderAPI:6.9.9406'
}
```


## Configure SDK
Add permission in manifest

```xml

    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.CAMERA" />
```



### Initialize

For production environments use `Environment.RELEASE`.
 

| Parameter | Description |
| -------- | ------- |
| `packageContext` | Is the context of your Application/Activity. |

```java
  private fun initialize() {
        val config: TrullyConfig = TrullyConfig(Environment.DEBUG)
        TrullySdk.init(packageContext, apiKey, config)
    }
```

### Listener
Add TrullyResultListener inside the activity. And implement members.

| Method | Description |
| -------- | ------- |
| `onResult` | Catch the results of the operation. |
| `onError` | Catch the errors of the operation. |


```java
class MainActivity : AppCompatActivity(), TrullyResultListener {

    override fun onResult(response: AppData) {
        TODO("On completed logic")
    }

    override fun onError() {
        TODO("Error logic")
    }
}
```

### Launch

To start the SDK you only need to call the `start` method.

| Parameter | Description |
| -------- | ------- |
| `packageContext` | Is the context of your Application/Activity. |
| `listener` | Is the TrullyResultListener of your application/activity. |


```java
private fun onLauchTrully() {
        TrullySdk.start(packageContext, listener)
}
```
