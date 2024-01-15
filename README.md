# TrullySDK

## Add TrullySDK repository and dependencies

### 1.- Add jitpack as repository store in `settings.gradle`

#### Kotlin DSL

```groovy
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)

    repositories {
        google()
        mavenCentral()

        maven {
            url = uri("https://jitpack.io")
        }
    }
}
```

#### Groovy DSL

```groovy
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)

    repositories {
        google()
        mavenCentral()

        maven {
            url 'https://jitpack.io'
        }
    }
}
```

### 2.- Add dependencies to the App level `build.gradle`

#### Kotlin DSL

```groovy
dependencies {
    //if you're using JetPack make sure to add this two dependencies otherwise you'll get resource missing error
    implementation("androidx.appcompat:appcompat:1.6.1")
    implementation("com.google.android.material:material:1.10.0")

    //We use viewbinding. Add this if you don't already have it in your project
    implementation("androidx.databinding:viewbinding:8.1.4")

    //We use retrofit to handle http requests. Add this if you don't already have it in your project
    implementation("com.squareup.retrofit2:retrofit:2.9.0")
    implementation("com.squareup.retrofit2:converter-gson:2.9.0")
    implementation("com.squareup.okhttp3:logging-interceptor:4.9.3")

    //This are the dependencies that makes our SDK
    implementation("com.github.TrullyAI:FaceAPI:5.2.2715")
    implementation("com.github.TrullyAI:FaceCore:5.2.232")
    implementation("com.github.TrullyAI:CommonAPI:6.9.1398")
    implementation("com.github.TrullyAI:DocumentReaderFullAuth:6.9.9555")
    implementation("com.github.TrullyAI:DocumentReaderAPI:6.9.9406")
    implementation("com.github.TrullyAI:TrullyKotlinSDK:0.0.14")
}
```

#### Groovy DSL

```groovy
dependencies {
    //if you're using JetPack make sure to add this two dependencies otherwise you'll get resource missing error
    implementation 'androidx.appcompat:appcompat:1.6.1'
    implementation 'com.google.android.material:material:1.10.0'

    //We use viewbinding. Add this if you don't already have it in your project
    implementation 'androidx.databinding:viewbinding:8.1.4'

    //We use retrofit to handle http requests. Add this if you don't already have it in your project
    implementation 'com.squareup.retrofit2:retrofit:2.9.0'
    implementation 'com.squareup.retrofit2:converter-gson:2.9.0'
    implementation 'com.squareup.okhttp3:logging-interceptor:4.9.3'

    //This are the dependencies that makes our SDK
    implementation 'com.github.TrullyAI:FaceAPI:5.2.2715'
    implementation 'com.github.TrullyAI:FaceCore:5.2.232'
    implementation 'com.github.TrullyAI:CommonAPI:6.9.1398'
    implementation 'com.github.TrullyAI:DocumentReaderFullAuth:6.9.9555'
    implementation 'com.github.TrullyAI:DocumentReaderAPI:6.9.9406'
    implementation 'com.github.TrullyAI:TrullyKotlinSDK:0.0.14'
}
```

### 3.- Add permission in manifest

```xml
    <uses-feature android:name="android.hardware.camera" android:required="true" />

    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.CAMERA" />
```

## Add it to you're project

### Listener

At the end of the process the SDK will trigger an HTTP Request. Add
TrullyResultListener to the activity that will launch the SDK and implement its
members so you can have access to the response.

| Method     | Description                         |
| ---------- | ----------------------------------- |
| `onResult` | Catch the results of the operation. |
| `onError`  | Catch the errors of the operation.  |

```java
class MainActivity : AppCompatActivity(), TrullyResultListener {

    override fun onResult(response: AppData) {
        TODO("On completed logic")
        //AppData.decisionMaker -> The result of the process. You'll find more details in https://trully.readme.io/docs/decision-maker-1
        //AppData.images - base64 for the different images obtained during the process
            //AppData.images?.documentBackStr - Will return the back image of the document
            //AppData.images?.documentStr - Will return the front image of the document
            //AppData.images?.selfieStr - Will return the selfie
    }

    override fun onError() {
        TODO("Error logic")
    }
}
```

### Configure and initialize

| Parameter        | Description                                                          |
| ---------------- | -------------------------------------------------------------------- |
| `packageContext` | Is the context of your Application/Activity.                         |
| `apiKey`         | You're client API_KEY. The SDK won't work without it                 |
| `styles`         | Styles object that will allow you to config color and logo. Optional |
| `config`         | Config object will pass the environment and the styles to the SDK    |

```java
  private fun initialize() {
    //Optionally change color and logo
    val styles: TrullyStyles = TrullyStyles()

    styles.primaryColor = ai.trully.sdk.R.color.primary
    styles.logo = ai.trully.sdk.R.drawable.logo

    styles.cameraStyle.cameraScreenSectorTarget = ai.trully.sdk.R.color.primary
    styles.cameraStyle.cameraScreenSectorActive = ai.trully.sdk.R.color.primary
    styles.cameraStyle.cameraScreenStrokeActive = ai.trully.sdk.R.color.primary
    styles.cameraStyle.cameraScreenStrokeNormal = ai.trully.sdk.R.color.primary

    //Set SDK configuration
    val config = TrullyConfig(Environment.DEBUG, styles) //For production environments use `Environment.RELEASE`.

    //Initialize SDK
    TrullySdk.init(packageContext, apiKey, config)
}
```

### Launch

To start the SDK you'll need to call the `start` method.

| Parameter        | Description                                               |
| ---------------- | --------------------------------------------------------- |
| `packageContext` | Is the context of your Application/Activity.              |
| `listener`       | Is the TrullyResultListener of your Application/activity. |

```java
    TrullySdk.start(packageContext, listener)
```

### Full Example

```java
class MainActivity : AppCompatActivity(), TrullyResultListener {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        findViewById<Button>(R.id.launchTrully)
            .setOnClickListener {
                initialize()
                onTap()
            }
    }

    override fun onResult(response: AppData) {
        TODO("On completed logic")
    }


    override fun onError() {
        TODO("Error logic")
    }


    private fun initialize() {
        val styles: TrullyStyles = TrullyStyles()

        styles.primaryColor = ai.trully.sdk.R.color.primary
        styles.logo = ai.trully.sdk.R.drawable.logo

        styles.cameraStyle.cameraScreenSectorTarget = ai.trully.sdk.R.color.primary
        styles.cameraStyle.cameraScreenSectorActive = ai.trully.sdk.R.color.primary
        styles.cameraStyle.cameraScreenStrokeActive = ai.trully.sdk.R.color.primary
        styles.cameraStyle.cameraScreenStrokeNormal = ai.trully.sdk.R.color.primary

        val config = TrullyConfig(Environment.DEBUG, styles)

        TrullySdk.init(this, "YOUR_API_KEY", config)
    }

    private fun onTap() {
        TrullySdk.start(this, this)
    }
}
```

## Reading Results

### Decision Maker Response

```java
class MainActivity : AppCompatActivity(), TrullyResultListener {
    override fun onResult(response: AppData) {
        //Trully Decision Data
        Log.d("TRULLY_SDK", response.decisionMaker?.data?.request_id.toString())
        Log.d("TRULLY_SDK", response.decisionMaker?.data?.label.toString())
        Log.d("TRULLY_SDK", response.decisionMaker?.data?.reason.toString())

        //Images - base64 string
        Log.d("TRULLY_SDK", response.images?.documentStr.toString())
        Log.d("TRULLY_SDK", response.images?.documentBackStr.toString())
        Log.d("TRULLY_SDK", response.images?.selfieStr.toString())
    }
}
```

## Shrinking App

To reduce the App download size you can implement Dynamic Feature Modules to
generate an on-demand installation of **DocumentReaderFullAuth** and
**FaceCore**

⚠️ The .aab size will not be reduced but Google Play will manage the files so
the download size will be reduced

⚠️ Make sure to download the Dynamic Feature Module before initializing
TrullySDK

### 1.- Create a new Dynamic Feature Module

You can create a new Dynamic Feature Module with Android Studio using **File >
New > New Module** this will open a window. Select Dynamic Feature and choose a
name for your module then click next. The next window will ask to create a
Module Title and to choose how you would like to include the module. We
recommend to choose **Do not include module at install-time (on-demand only)**
because it will let you decide when you want to start the download. This action
will generate some changes to your app

#### `settings.gradle`

```groovy
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)

    repositories {
        google()
        mavenCentral()

        maven {
            url = uri("https://jitpack.io")
        }
    }
}

rootProject.name = "Your Application Name"
include(":app")
include(":your_module_name")
```

#### App level `build.gradle`

```groovy
android {
    //...other configurations
    dynamicFeatures += ":your_module_name"
}
```

### 2.- Add android.play to the App level `build.gradle`

#### Kotlin DSL

```groovy
dependencies {
    implementation("com.google.android.play:core-ktx:1.8.1")
}
```

#### Groovy DSL

```groovy
dependencies {
    implementation 'com.google.android.play:core-ktx:1.8.1'
}
```

### 3.- Add split android.play SplitCompatApplication to the App manifest

```xml
    <application
        android:name="com.google.android.play.core.splitcompat.SplitCompatApplication"
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.MyApplication"
        tools:targetApi="31">
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
```

### 4.- Move DocumentReaderFullAuth and FaceCore dependencies to your new module `build.gradle`

#### App level `build.gradle` dependencies

##### Kotlin DSL

```groovy
dependencies {
    //android.play so we can use SplitCompat
    implementation("com.google.android.play:core-ktx:1.8.1")

    //SDK dependencies without DocumentReaderFullAuth and FaceCore
    implementation("androidx.databinding:viewbinding:8.1.4")
    implementation("com.squareup.retrofit2:retrofit:2.9.0")
    implementation("com.squareup.retrofit2:converter-gson:2.9.0")
    implementation("com.squareup.okhttp3:logging-interceptor:4.9.3")
    implementation("com.github.TrullyAI:FaceAPI:5.2.2715")
    implementation("com.github.TrullyAI:CommonAPI:6.9.1398")
    implementation("com.github.TrullyAI:DocumentReaderAPI:6.9.9406")
    implementation("com.github.TrullyAI:TrullyKotlinSDK:0.0.14")
}
```

##### Groovy DSL

```groovy
dependencies {
    implementation 'com.google.android.play:core-ktx:1.8.1'

    //SDK dependencies without DocumentReaderFullAuth and FaceCore
    implementation 'androidx.databinding:viewbinding:8.1.4'
    implementation 'com.squareup.retrofit2:retrofit:2.9.0'
    implementation 'com.squareup.retrofit2:converter-gson:2.9.0'
    implementation 'com.squareup.okhttp3:logging-interceptor:4.9.3'
    implementation 'com.github.TrullyAI:FaceAPI:5.2.2715'
    implementation 'com.github.TrullyAI:CommonAPI:6.9.1398'
    implementation 'com.github.TrullyAI:DocumentReaderAPI:6.9.9406'
    implementation 'com.github.TrullyAI:TrullyKotlinSDK:0.0.14'
}
```

#### Module level `build.gradle` dependencies

##### Kotlin DSL

```groovy
dependencies {
    implementation("com.github.TrullyAI:FaceCore:5.2.232")
    implementation("com.github.TrullyAI:DocumentReaderFullAuth:6.9.9555")
}
```

##### Groovy DSL

```groovy
dependencies {
    implementation 'com.github.TrullyAI:FaceCore:5.2.232'
    implementation 'com.github.TrullyAI:DocumentReaderFullAuth:6.9.9555'
}
```

### 5.- Configure an Activity to init the download

#### Full Example

```java
class MainActivity : AppCompatActivity(), TrullyResultListener, SplitInstallStateUpdatedListener {
    //variable to store the SplitInstallManager instance
    private lateinit var installManager: SplitInstallManager

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        //Generate SplitInstallManager instance
        installManager = SplitInstallManagerFactory.create(applicationContext)
        //Register Activity as listener
        installManager.registerListener(this)

        findViewById<Button>(R.id.launchTrully)
            .setOnClickListener {
                onTap()
            }
    }

    //Start download
    private fun onTap() {
        installManager.startInstall(
            SplitInstallRequest.newBuilder()
                .addModule("your_module_name")
                .build()
        )
    }

    //Delete all unnecessary data when Activity is destroy
    override fun onDestroy() {
        installManager.unregisterListener(this)
        installManager.deferredUninstall(listOf("your_module_name"))
        super.onDestroy()
    }

    //Configure the actions for the different states
    override fun onStateUpdate(state: SplitInstallSessionState) {
        when (state.status()) {
            //This state is required for every module larger than 10MB. You'll need it in this case
            SplitInstallSessionStatus.REQUIRES_USER_CONFIRMATION -> {
                installManager.startConfirmationDialogForResult(state, this, 123)
                Toast.makeText(this, "Request State", Toast.LENGTH_SHORT).show()
            }
            SplitInstallSessionStatus.INSTALLED -> {
                initialize()
                Toast.makeText(this, "Install State", Toast.LENGTH_SHORT).show()
            }
            SplitInstallSessionStatus.FAILED -> {
                Toast.makeText(this, "Failed State", Toast.LENGTH_SHORT).show()
            }

            SplitInstallSessionStatus.CANCELED -> {
                Toast.makeText(this, "Canceled State", Toast.LENGTH_SHORT).show()
            }

            SplitInstallSessionStatus.CANCELING -> {
                Toast.makeText(this, "Canceling State", Toast.LENGTH_SHORT).show()
            }

            SplitInstallSessionStatus.DOWNLOADED -> {
                Toast.makeText(this, "Downloaded State", Toast.LENGTH_SHORT).show()
            }

            SplitInstallSessionStatus.DOWNLOADING -> {
                Toast.makeText(this, "Downloading State", Toast.LENGTH_SHORT).show()
            }

            SplitInstallSessionStatus.INSTALLING -> {
                Toast.makeText(this, "Installing State", Toast.LENGTH_SHORT).show()
            }

            SplitInstallSessionStatus.PENDING -> {
                Toast.makeText(this, "Pending State", Toast.LENGTH_SHORT).show()
            }

            SplitInstallSessionStatus.UNKNOWN -> {
                Toast.makeText(this, "Unknown State", Toast.LENGTH_SHORT).show()
            }
        }
    }

    override fun onResult(response: AppData) {
        Log.i("response", "finish")
    }

    override fun onError() {
        Log.i("response", "error")
    }

    private fun initialize() {
        val config = TrullyConfig(Environment.DEBUG)

        TrullySdk.init(this, "YOUR_API_KEY", config)
        TrullySdk.start(this, this)
    }
}
```
