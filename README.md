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
    implementation("androidx.appcompat:appcompat:1.4.2")
    implementation("com.google.android.material:material:1.6.1")

    //This are the dependencies that makes our SDK
    implementation("com.github.TrullyAI:FaceCore:5.2.232")
    implementation("com.github.TrullyAI:DocumentReaderFullAuth:6.9.9555")
    implementation("com.github.TrullyAI:TrullyKotlinSDK:0.0.14")
}
```

#### Groovy DSL

```groovy
dependencies {
    //if you're using JetPack make sure to add this two dependencies otherwise you'll get resource missing error
    implementation 'androidx.appcompat:appcompat:1.4.2'
    implementation 'com.google.android.material:material:1.6.1'

    //This are the dependencies that makes our SDK
    implementation 'com.github.TrullyAI:FaceCore:5.2.232'
    implementation 'com.github.TrullyAI:DocumentReaderFullAuth:6.9.9555'
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

Add TrullyResultListener to the activity that will launch the SDK and implement
its members so you can have access to the process data.

| Method          | Description                                        |
| --------------- | -------------------------------------------------- |
| `onTrack`       | Catch the step changing of the operation.          |
| `onTrackDetail` | Catch the user's interaction during the operation. |
| `onResult`      | Catch the results of the operation.                |
| `onError`       | Catch the errors of the operation.                 |

```java
class MainActivity : AppCompatActivity(), TrullyResultListener {

    override fun onTrack(trackStep: TrackStep) {
        Log.d("onTrack", trackStep.toString())
    }

    override fun onTrackDetail(trackDetail: TrackDetail) {
        Log.d("onTrackDetail", trackDetail.toString())
    }

    override fun onResult(response: TrullyResponse) {
        Log.d("onresult", response.toString())
    }

    override fun onError(errorData: ErrorData) {
        Log.d("onError", errorData.toString())
    }
}
```

### Listeners data structure

Here you'll found the structures of the data received in each listener function

#### onTrack

This listener function will be called every time the user starts a new step on
the process. There are five different steps. When a new step is started, you'll
receive the data from the previous completed step.

| Key            | Description                                  |
| -------------- | -------------------------------------------- |
| `step`         | Name of the previous completed step.         |
| `userID`       | The userID you passed during initialization. |
| `started_on`   | UTC timezone. Step starting time.            |
| `completed_on` | UTC timezone. Step completion time.          |

#### Example

```java
    override fun onTrack(trackStep: TrackStep) {
        Log.d("onTrack", trackStep.toString())
    }
```

##### Steps Table

| Name                  | Description                                       |
| --------------------- | ------------------------------------------------- |
| `form_start`          | User is currently scanning the document.          |
| `form_document`       | Process is currently trying to obtained location. |
| `form_location`       | User is currently completing liveness process.    |
| `form_selfie`         | User reach the final step of the operation.       |
| `form_decision_maker` | Operation has the Decision Maker respond.         |

#### onTrackDetail

This listener function will be called when the user take some actions during the
operation. The actions that will trigger it are the ones related to the data
needed for the Decision Maker.

| Key         | Description                                   |
| ----------- | --------------------------------------------- |
| `userID`    | The userID you passed during initialization.  |
| `action`    | Name of the action that trigger the function. |
| `timestamp` | UTC timezone. When the function was trigger.  |

##### Actions Table

| Name                                     | Description                                                            |
| ---------------------------------------- | ---------------------------------------------------------------------- |
| `LEGAL_DOCUMENTATION_ACCEPTED`           | User accept Privacy Policy and Terms and Conditions.                   |
| `LEGAL_DOCUMENTATION_DECLINED`           | User declined Privacy Policy and Terms and Conditions.                 |
| `LOAD_NEXT_PAGE`                         | Operation start new page.                                              |
| `NO_CAMERA_AVAILABLE`                    | The operation couldn't start camera.                                   |
| `FRONT_IMAGE_OBTAINED`                   | Operation has document front.                                          |
| `BACK_IMAGE_OBTAINED`                    | Operation has document back.                                           |
| `DOCUMENT_ANALYSIS_RETRY`                | Operation needs to re start document analysis.                         |
| `DOCUMENT_ANALYSIS_COMPLETE`             | Operation has analyzed front and back image.                           |
| `LOCATION_OBTAINED`                      | Operation has user's location.                                         |
| `LOCATION_SKIPPED`                       | Operation has continued without user's location.                       |
| `LIVENESS_RECOGNITION_PROCESS_STARTED`   | Operation has start liveness analysis.                                 |
| `LIVENESS_RECOGNITION_PROCESS_RETRY`     | Operation needs to re start liveness analysis.                         |
| `LIVENESS_RECOGNITION_PROCESS_COMPLETED` | Operation has analyzed liveness status.                                |
| `DATA_SEND_TO_DECISION_MAKER`            | Operation has sended data collected to Decision Maker. Awaiting result |
| `END_KYC_PROCESS`                        | Operation has Decision Maker result.                                   |

#### Example

```java
    override fun onTrackDetail(trackDetail: TrackDetail) {
        Log.d("onTrackDetail", trackDetail.toString())
    }
```

### onResult

This listener function will be called when the operation gets the Decision Maker
result. Go to [<b>Reading Results</b>](#add-it-next) section to get more
information

### onError

This listener function will be called in case of an error during the operation.

| Key         | Description                                     |
| ----------- | ----------------------------------------------- |
| `process`   | Which part of the process trigger the function. |
| `message`   | Error message                                   |
| `userID`    | The userID you passed during initialization.    |
| `timestamp` | UTC timezone. When the function was trigger.    |

##### Process Table

| Name                           | Description                                          |
| ------------------------------ | ---------------------------------------------------- |
| `SAVING_TRACK_STEP`            | HTTP error when saving track data.                   |
| `OBTAINING_IP`                 | HTTP error when getting ip data.                     |
| `INITIALIZING_DOCUMENT_READER` | Process error during document reader initialization. |
| `READING_DOCUMENT`             | Process error analyzing document.                    |
| `GETTING_LIVENESS`             | Process error analyzing liveness.                    |
| `OBTAINING_DM_RESPONSE`        | HTTP error when getting Decision Maker response.     |

#### Example

```java
    override fun onError(errorData: ErrorData) {
        Log.d("onError", errorData.toString())
    }
```

### Configure and initialize

#### init function parameters

| Parameter        | Description                                                       |
| ---------------- | ----------------------------------------------------------------- |
| `packageContext` | Is the context of your Application/Activity.                      |
| `apiKey`         | You're client API_KEY. The SDK won't work without it.             |
| `config`         | Config object will pass the environment and the styles to the SDK |

#### config object

| Parameter     | Description                                                                                             |
| ------------- | ------------------------------------------------------------------------------------------------------- |
| `environment` | Environment.DEBUG for development. Environment.RELEASE for production. Mandatory                        |
| `userID`      | Will allow you to link the process to an ID generate by you for better track of each process. Mandatory |
| `styles`      | Styles object that will allow you to config color, logo and texts. Optional                             |

#### To configure texts use the uiTexts object

| Value     | Description                                                                       |
| --------- | --------------------------------------------------------------------------------- |
| `docType` | What type of document the user need to complete de process. One of the Texts enum |

#### Texts enums

| Parameter      | Value                   |
| -------------- | ----------------------- |
| `INE_PASSPORT` | INE o Pasaporte vigente |
| `INE`          | INE vigente             |
| `PASSPORT`     | Pasaporte vigente       |

#### Changing styles

Optionally you can change colors and logo. These are the default values

##### Colors

| Key                        | Description                                           | Value     |
| -------------------------- | ----------------------------------------------------- | --------- |
| `primaryColor`             | Will change statusBar, button, icons and links color. | #475FFF   |
| `disabledColor`            | Will change button color when legal is not accepted.  | #D6A0FF   |
| `cameraScreenStrokeActive` | Liveness active pointer color.                        | #475FFF75 |
| `cameraScreenStrokeNormal` | Liveness pointer color.                               | #475FFF50 |
| `cameraScreenSectorActive` | Liveness active background pointer color.             | #475FFF75 |
| `cameraScreenSectorTarget` | Liveness background pointer color.                    | #475FFF50 |

##### Logo

We are using the url to show you the images. Please, make sure you upload the
images to your project and pass the corresponding drawable to the styles object

| Key    | Value                                                                 |
| ------ | --------------------------------------------------------------------- |
| `logo` | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/logo.png |

```java
  private fun initialize() {
    val styles: TrullyStyles = TrullyStyles()

    styles.primaryColor = ai.trully.sdk.R.color.primary
    styles.disabledColor = ai.trully.sdk.R.color.disabledColor
    styles.logo = ai.trully.sdk.R.drawable.logo

    styles.cameraStyle.cameraScreenSectorTarget = ai.trully.sdk.R.color.primary75
    styles.cameraStyle.cameraScreenSectorActive = ai.trully.sdk.R.color.primary50
    styles.cameraStyle.cameraScreenStrokeActive = ai.trully.sdk.R.color.primary75
    styles.cameraStyle.cameraScreenStrokeNormal = ai.trully.sdk.R.color.primary50

    styles.uiTexts.docType = Texts.INE

    //Set SDK configuration
    val config = TrullyConfig(environment =  Environment.DEBUG, userID = "YOUR_ID_FOR_THE_PROCESS", style = styles)
    //* For production environments use `Environment.RELEASE`.
    //* We recommend using named arguments so the order doesn't matter. If you're not using them, this example shows the order you should pass the arguments.

    //Initialize SDK
    TrullySdk.init(packageContext = this, apiKey = "YOUR_API_KEY", config = config)

    //Run SDK
    TrullySdk.start(packageContext = this, listener = this)
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

    override fun onResult(response: TrullyResponse) {
        Log.d("onResult", response.toString())
    }

    override fun onTrack(trackStep: TrackStep) {
        Log.d("onTrack", trackStep.toString())
    }

    override fun onTrackDetail(trackDetail: TrackDetail) {
        Log.d("onTrackDetail", trackDetail.toString())
    }

    override fun onError(errorData: ErrorData) {
        Log.d("onError", errorData.toString())
    }


    private fun initialize() {
        val styles: TrullyStyles = TrullyStyles()

        styles.primaryColor = ai.trully.sdk.R.color.primary
        styles.logo = ai.trully.sdk.R.drawable.logo

        styles.cameraStyle.cameraScreenSectorTarget = ai.trully.sdk.R.color.primary_50
        styles.cameraStyle.cameraScreenStrokeNormal = ai.trully.sdk.R.color.primary_50
        styles.cameraStyle.cameraScreenSectorActive = ai.trully.sdk.R.color.primary_75
        styles.cameraStyle.cameraScreenStrokeActive = ai.trully.sdk.R.color.primary_75

        val config = TrullyConfig(environment =  Environment.DEBUG, userID = "YOUR_ID_FOR_THE_PROCESS", style = styles, clarityKey = "YOUR_CLARITY_KEY")

        TrullySdk.init(packageContext = this, apiKey = "YOUR_API_KEY", config = config)
    }

    private fun onTap() {
        TrullySdk.start(packageContext = this, listener = this)
    }
}
```

## <a id="results"></a>Reading Results

### Decision Maker Response

You'll find more details in
[Decision Maker API Docs](https://trully.readme.io/reference/decisionmakerpredict)

| Object          | Description                                                             |
| --------------- | ----------------------------------------------------------------------- |
| `decisionMaker` | Contains the complete Decision Maker response.                          |
| `images`        | Contains the Base64 string of the document images and the selfie image. |
| `shortResponse` | Contains the basic data of the Decision Maker response.                 |

#### shortResponse Object

| key            | Description                                                                             |
| -------------- | --------------------------------------------------------------------------------------- |
| `userID`       | userID you passed during initialization. Link to request_id.                            |
| `request_id`   | Decision Maker result id. Link to userID.                                               |
| `label`        | Decision Maker result label.                                                            |
|                | No Threat - low risk user. Review - medium risk user. Potential Threat - high risk user |
| `reason`       | Decision Maker result reasons.                                                          |
| `request_date` | UTC timestamp.                                                                          |

#### images Object

| key                       | Description               |
| ------------------------- | ------------------------- |
| `selfieStr`               | Selfie.                   |
| `documentStr`             | Cropped document front.   |
| `documentBackStr`         | Cropped document back.    |
| `documentCompleteStr`     | Uncropped document front. |
| `documentBackCompleteStr` | Uncropped document back.  |

```java
    override fun onResult(response: TrullyResponse) {
        //Complete Decision Maker response
        Log.d("TRULLY_SDK", response.decisionMaker.toString())

        //Short response
        Log.d("TRULLY_SDK", response.shortResponse?.userID.toString())
        Log.d("TRULLY_SDK", response.shortResponse?.request_id.toString())
        Log.d("TRULLY_SDK", response.shortResponse?.label.toString())
        Log.d("TRULLY_SDK", response.shortResponse?.reason.toString())
        Log.d("TRULLY_SDK", response.shortResponse?.request_date.toString())

        //Images - base64 string
        Log.d("TRULLY_SDK", response.images?.selfieStr.toString())
        Log.d("TRULLY_SDK", response.images?.documentStr.toString())
        Log.d("TRULLY_SDK", response.images?.documentBackStr.toString())
        Log.d("TRULLY_SDK", response.images?.documentCompleteStr.toString())
        Log.d("TRULLY_SDK", response.images?.documentBackCompleteStr.toString())
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
    implementation("com.github.TrullyAI:TrullyKotlinSDK:0.0.14")
}
```

##### Groovy DSL

```groovy
dependencies {
    //android.play so we can use SplitCompat
    implementation 'com.google.android.play:core-ktx:1.8.1'

    //SDK dependencies without DocumentReaderFullAuth and FaceCore
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

    override fun onResult(response: TrullyResponse) {
        Log.i("response", "finish")
    }

    override fun onTrack(trackStep: TrackStep) {
        Log.d("onTrack", trackStep.toString())
    }

    override fun onTrackDetail(trackDetail: TrackDetail) {
        Log.d("onTrackDetail", trackDetail.toString())
    }

    override fun onError(errorData: ErrorData) {
        Log.d("onError", errorData.toString())
    }

    private fun initialize() {
        val config = TrullyConfig(environment =  Environment.DEBUG,  clarityKey = "lqy0z96k5z", style = styles, userID = "un-id")

        TrullySdk.init(this, "YOUR_API_KEY", config)
        TrullySdk.start(this, this)
    }
}
```
