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
	        implementation 'com.github.TrullyAI:TrullySDK:TAG'
}
```


## Configure SDK

### Initialize

For production environments use `Environment.RELEASE`.
 

| Parameter | Description |
| -------- | ------- |
| `packageContext` | Is the context of your Application/Activity. |

```java
  private fun initialize() {
        val config: TrullyConfig = TrullyConfig(Environment.DEBUG)
        TrullySdk.init(packageContext,config)
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