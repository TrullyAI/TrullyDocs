# TrullySDK

TrullyWebSDK is an identity validation component designed to be integrated into
your decision-making process.

## Table of Contents

- [Add TrullySDK repository and dependencies](#add-trullysdk-repository-and-dependencies)
  - [1.- Add jitpack as repository store in `settings.gradle`](#1-add-jitpack-as-repository-store-in-settingsgradle)
    - [Kotlin DSL](#kotlin-dsl)
    - [Groovy DSL](#groovy-dsl)
  - [2.- Check compileSdk and minSdk](#2-check-compilesdk-and-minsdk)
    - [Kotlin DSL](#kotlin-dsl-1)
    - [Groovy DSL](#groovy-dsl-1)
  - [3.- Add dependencies](#3-add-dependencies)
    - [Without libraries system. Add the dependencies directly to the App level `build.gradle`](#without-libraries-system-add-the-dependencies-directly-to-the-app-level-buildgradle)
      - [Kotlin DSL](#kotlin-dsl-2)
      - [Groovy DSL](#groovy-dsl-2)
    - [With libraries system.](#with-libraries-system)
      - [1.- Add the dependencies info to the `libs.versions.toml` file](#1-add-the-dependencies-info-to-the-libsversionstoml-file)
      - [2.- Add the library dependencies to your App level `build.gradle`](#2-add-the-library-dependencies-to-your-app-level-buildgradle)
  - [4.- Check manifest permissions](#4-check-manifest-permissions)
  - [5.- Replace theme](#5-replace-theme)
- [Add it to you're project](#add-it-to-youre-project)
  - [Add Listeners](#add-listeners)
    - [Example](#example)
    - [Configure back event](#configure-back-event)
    - [Listeners data structure](#listeners-data-structure)
      - [onTrack](#ontrack)
        - [Example](#example-1)
        - [Steps Table](#steps-table)
      - [onTrackDetail](#ontrackdetail)
        - [Example](#example-2)
        - [Actions Table](#actions-table)
      - [onResult](#onresult)
      - [onError](#onerror)
        - [Example](#example-3)
        - [Process Table](#process-table)
  - [Configure SDK](#configure-sdk)
    - [Example](#example-4)
  - [Launch SDK](#launch-sdk)
    - [Example](#example-5)
  - [Complete Example with default styles](#complete-example-with-default-styles)
  - [Personalization](#personalization)
    - [Example](#example-6)
    - [Combine with other Decision Maker analysis](#combine-with-other-decision-maker-analysis)
      - [Example](#example-7)
    - [Changing styles](#changing-styles)
      - [Colors](#colors)
        - [Example](#example-8)
      - [Images](#images)
        - [Example](#example-9)
  - [Full Example](#full-example)
- [Reading Results](#results)
  - [Decision Maker Response](#decision-maker-response)
    - [Example](#example-10)
  - [Receiving data with webhook](#receiving-data-with-webhook)
    - [Example](#example-11)
- [How to know the app size](#how-to-know-the-app-size)
  - [1.- Configure the desired architecture build on your app level `build.gradle`](#1-configure-the-desired-architecture-build-on-your-app-level-buildgradle)
    - [Kotlin DSL](#kotlin-dsl-3)
    - [Groovy DSL](#groovy-dsl-3)
  - [2.- Generate a signed App Bundle](#2-generate-a-signed-app-bundle)

#### ⚠️ Before start using the TrullySdk component we need to set some data on our server. Please contact us to ask for this set up. <br> We are going to need the following information

|               | Description                        | Example          |
| ------------- | ---------------------------------- | ---------------- |
| `packageName` | You're app namespace/applicationId | com.trully.unico |

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

        maven {
            url = uri("https://maven-sdk.unico.run/sdk-mobile")
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

        maven {
            url 'https://maven-sdk.unico.run/sdk-mobile'
        }
    }
}
```

### 2.- Check compileSdk and minSdk

Our module requires a compileSdk of, at least, 34 and a minSdk of, at least, 24.
Make sure your `build.gradle` has the correct versioning

#### Kotlin DSL

```groovy
android {
    compileSdk = 34

    defaultConfig {
        minSdk = 24
    }
}
```

#### Groovy DSL

```groovy
android {
    compileSdk 34

    defaultConfig {
        minSdk 24
    }
}
```

### 3.- Add dependencies

#### Without libraries system. Add the dependencies directly to the App level `build.gradle`

###### Kotlin DSL

```groovy
dependencies {
    implementation("com.github.TrullyAI:TrullyKotlinSDK:latest") //change latest for the version number
}
```

###### Groovy DSL

```groovy
dependencies {
    implementation 'com.github.TrullyAI:TrullyKotlinSDK:latest' //change latest for the version number
}
```

#### With libraries system.

##### 1.- Add the dependencies info to the `libs.versions.toml` file

```groovy
[versions]
trully = "latest" //change latest for the version number

[libraries]
trully-sdk = { group = "com.github.TrullyAI", name = "TrullyKotlinSDK", version.ref = "trully" }
```

##### 2.- Add the library dependencies to your App level `build.gradle`

```groovy
dependencies {
    implementation(libs.trully.sdk)
}
```

### 4.- Check manifest permissions

```xml
    <uses-feature android:name="android.hardware.camera" android:required="true" />

    // If you're app doesn't need FINE_LOCATION, make sure to add this line so you remove our declaration from the final AndroidManifest.xml
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" tools:node="remove" />
```

### 5.- Replace theme

If you have a custom app theme declared in the manifest file:

```xml
    <application
	...
        android:theme="@style/Theme.your_theme"
        ....
        tools:replace="android:theme" - Add this line to replace the themes and avoid compile configs
	... >
```

#### ⚠️

It is possible that some other theme related issues appear during compilation.
Please, make sure to read the Logcat an add the corresponding data to the
tools:replace.

## Add it to you're project

### Add Listeners

Add TrullyListeners to the activity that will launch the SDK and implement its
members so you can have access to the process data.

| Method             | Description                                                                              |
| ------------------ | ---------------------------------------------------------------------------------------- |
| `onTrack`          | Catch the step changing of the operation.                                                |
| `onTrackDetail`    | Catch the user's interaction during the operation.                                       |
| `onResult`         | Catch the results of the operation.                                                      |
| `onError`          | Catch the errors of the operation.                                                       |
| `onLeaveFromStart` | Optional. Set an action for the onBackPressed event for the initial view of the process. |

##### Example

```java
class MainActivity : AppCompatActivity(), TrullyListeners {

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

#### Configure back event

To start the process you need to pass the packageContext. This is because our
SDK runs on top of that context, which means if you run it from an empty
Activity and the user goes back while on the first view of the SDK it will be
left with an empty view. Same goes for fragments. <br/> You can control this by
adding the optional onLeaveFromStart listener so you can decide where the user
should be redirected

##### Example

```java
    class MainActivity : AppCompatActivity(), TrullyListeners {

    override fun onLeaveFromStart() {
        Log.d("onLeaveFromStart", "Custom back action when the user back to the previous screen from the TrullySDK")
    }
}
```

#### Listeners data structure

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

##### Example

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

##### Example

```java
    override fun onTrackDetail(trackDetail: TrackDetail) {
        Log.d("onTrackDetail", trackDetail.toString())
    }
```

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
| `LOCATION_RETRY`                         | Operation blocked due to location permissions denied. Retry operation  |
| `LOCATION_SKIPPED`                       | Operation has continued without user's location.                       |
| `LIVENESS_RECOGNITION_PROCESS_STARTED`   | Operation has start liveness analysis.                                 |
| `LIVENESS_RECOGNITION_PROCESS_COMPLETED` | Operation has analyzed liveness status.                                |
| `DATA_SEND_TO_DECISION_MAKER`            | Operation has sended data collected to Decision Maker. Awaiting result |
| `END_KYC_PROCESS`                        | Operation has Decision Maker result.                                   |

#### onResult

This listener function will be called when the operation gets the Decision Maker
result. Go to [<b>Reading Results</b>](#results) section to get more information

#### onError

This listener function will be called in case of an error during the operation.

| Key         | Description                                     |
| ----------- | ----------------------------------------------- |
| `process`   | Which part of the process trigger the function. |
| `message`   | Error message                                   |
| `userID`    | The userID you passed during initialization.    |
| `timestamp` | UTC timezone. When the function was trigger.    |

##### Example

```java
    override fun onError(errorData: ErrorData) {
        Log.d("onError", errorData.toString())
    }
```

##### Process Table

| Name                           | Description                                          |
| ------------------------------ | ---------------------------------------------------- |
| `SAVING_TRACK_STEP`            | HTTP error when saving track data.                   |
| `OBTAINING_IP`                 | HTTP error when getting ip data.                     |
| `INITIALIZING_DOCUMENT_READER` | Process error during document reader initialization. |
| `READING_DOCUMENT`             | Process error analyzing document.                    |
| `GETTING_LOCATION`             | Process error obtaining location.                    |
| `GETTING_LIVENESS`             | Process error analyzing liveness.                    |
| `OBTAINING_DM_RESPONSE`        | HTTP error when getting Decision Maker response.     |

### Configure SDK

To configure the SDK you'll need to call the `init` method.

| Parameter        | Description                                                                                            |
| ---------------- | ------------------------------------------------------------------------------------------------------ |
| `packageContext` | Is the context of your Application/Activity.                                                           |
| `apiKey`         | You're client API_KEY. The SDK won't work without it.                                                  |
| `config`         | Config object will pass the environment and the styles to the SDK. [<b>See more</b>](#personalization) |

##### Example

```java
    val config = TrullyConfig(environment = Environment.DEBUG, userID = "YOUR_ID_FOR_THE_PROCESS")
    //* For production environments use `Environment.RELEASE`. Required
    //* userID will identify the user completing the process. Required

    TrullySdk.init(apiKey = "YOUR_API_KEY", config = config)
```

### Launch SDK

To start the SDK you'll need to call the `start` method.

| Parameter        | Description                                          |
| ---------------- | ---------------------------------------------------- |
| `packageContext` | Is the context of your Application/Activity.         |
| `listener`       | Is the TrullyListeners of your Application/activity. |

##### Example

```java
    TrullySdk.start(packageContext = this, listener = this)
```

### Complete Example with default styles

```java
import ai.trully.sdk.TrullySdk
import ai.trully.sdk.configurations.TrullyConfig
import ai.trully.sdk.models.Environment
import ai.trully.sdk.models.ErrorData
import ai.trully.sdk.models.TrackDetail
import ai.trully.sdk.models.TrackStep
import ai.trully.sdk.models.TrullyResponse
import ai.trully.sdk.protocols.listeners.TrullyListeners

class MainActivity : AppCompatActivity(), TrullyListeners {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        initialize()
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
        //Set SDK configuration
        val config = TrullyConfig(environment = Environment.DEBUG, userID = "YOUR_ID_FOR_THE_PROCESS")
        //* For production environments use `Environment.RELEASE`. Required
        //* userID will identify the user completing the process. Required

        //Initialize SDK
        TrullySdk.init(apiKey = "YOUR_API_KEY", config = config)

        //Run SDK
        TrullySdk.start(packageContext = this, listener = this)
    }
}
```

### Personalization

The config object will allow to configure environment execution and change the
styles. Also, for the process to work you need to pass a userID. The config
object will let you do that.

| Parameter     | Description                                                                                            |
| ------------- | ------------------------------------------------------------------------------------------------------ |
| `environment` | Environment.DEBUG for development. Environment.RELEASE for production. Required                        |
| `userID`      | Will allow you to link the process to an ID generate by you for better track of each process. Required |
| `tag`         | The tag from the process. Automatically generated.                                                     |
|               | This is used to link every image analysis run by this SDK                                              |
| `styles`      | Styles object that will allow you to config color, logo and texts. Optional                            |

##### Example

```java
  private fun initialize() {
    val styles: TrullyStyles = TrullyStyles()

    //Set SDK configuration
    val config = TrullyConfig(environment = Environment.DEBUG, userID = "YOUR_ID_FOR_THE_PROCESS", style = styles)
    //* For production environments use `Environment.RELEASE`.
    //* We recommend using named arguments so the order doesn't matter. If you're not using them, this example shows the order you should pass the arguments.

    //Initialize SDK
    TrullySdk.init(apiKey = "YOUR_API_KEY", config = config)

    //Run SDK
    TrullySdk.start(packageContext = this, listener = this)
}
```

#### Combine with other Decision Maker analysis

TrullySdk helps you to get a candidate data (by default you'll be collecting
document image, location and selfie) so our Decision Maker can analyze it. But
our Decision Maker could be used to analyze different data points and you may
want to combine every Decision Maker response you get for every data point
(learn more about Decision Maker on the
[API Docs](https://trully.readme.io/reference/decisionmakerpredict)). You can
join the response you get from the TrullySdk with others Decision Maker
responses passing the request_id in the config object.

| Key          | Description                                                          |
| ------------ | -------------------------------------------------------------------- |
| `request_id` | String. The id generated by the Decision Maker for any analysis run. |

##### Example

```java
  private fun initialize() {
    val styles: TrullyStyles = TrullyStyles()

    //Set SDK configuration
    val config = TrullyConfig(environment = Environment.DEBUG, userID = "YOUR_ID_FOR_THE_PROCESS", request_id = "PREV_DM_REQUEST_ID")
    //* For production environments use `Environment.RELEASE`.
    //* We recommend using named arguments so the order doesn't matter. If you're not using them, this example shows the order you should pass the arguments.

    //Initialize SDK
    TrullySdk.init(apiKey = "YOUR_API_KEY", config = config)

    //Run SDK
    TrullySdk.start(packageContext = this, listener = this)
}
```

#### Changing styles

Optionally you can change colors and images.

##### Colors

| Key               | Description                                           | Value   |
| ----------------- | ----------------------------------------------------- | ------- |
| `primaryColor`    | Will change statusBar, button, icons and links color. | #475FFF |
| `disabledColor`   | Will change button color when legal is not accepted.  | #D6A0FF |
| `backgroundColor` | Will change app background color.                     | #FFFFFF |

###### Example

```java
private fun initialize() {
    val styles: TrullyStyles = TrullyStyles()

    styles.primaryColor = ai.trully.sdk.R.color.primary
    styles.disabledColor = ai.trully.sdk.R.color.disabled
    styles.backgroundColor = ai.trully.sdk.R.color.background

    //Set SDK configuration
    val config = TrullyConfig(environment = Environment.DEBUG, userID = "YOUR_ID_FOR_THE_PROCESS", style = styles)
    //* For production environments use `Environment.RELEASE`.
    //* We recommend using named arguments so the order doesn't matter. If you're not using them, this example shows the order you should pass the arguments.

    //Initialize SDK
    TrullySdk.init(apiKey = "YOUR_API_KEY", config = config)

    //Run SDK
    TrullySdk.start(packageContext = this, listener = this)
}
```

##### Images

We are using the url to show you the images. Please, make sure you upload the
images to your project and pass the corresponding drawable to the styles object

| Key           | Value                                                                                        |
| ------------- | -------------------------------------------------------------------------------------------- |
| `logo`        | https://trully-api-documentation.s3.us-east-1.amazonaws.com/trully-sdk/logo-trully-unico.svg |
| `IDIcon`      | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/ID-1.svg                        |
| `selfieIcon`  | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/Video-1.svg                     |
| `IDImage`     | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/ID2-1.svg                       |
| `permissions` | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/modalcamera-andriod.svg         |
| `light`       | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/luzIcon.svg                     |
| `cross`       | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/retirarElementosIcon.svg        |
| `showId`      | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/pruebavida.svg                  |
| `check`       | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/icon-check.svg                  |
| `faceTimeout` | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/rostrofail.svg                  |
| `noLocation`  | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/pin-1.svg                       |
| `noCamera`    | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/cameraDenied-1.svg              |
| `error`       | https://trully-api-documentation.s3.amazonaws.com/trully-sdk/timeout.svg                     |

###### Example

```java
private fun initialize() {
    val styles: TrullyStyles = TrullyStyles()

    styles.logo = ai.trully.sdk.R.drawable.logo
    styles.IDIcon = ai.trully.sdk.R.drawable.ine
    styles.selfieIcon = ai.trully.sdk.R.drawable.selfie
    styles.IdImage = ai.trully.sdk.R.drawable.id
    styles.permission = ai.trully.sdk.R.drawable.modalandroid_camara
    styles.light = ai.trully.truedeepfakedetection.R.drawable.light
    styles.cross = ai.trully.truedeepfakedetection.R.drawable.cross
    styles.showId = ai.trully.sdk.R.drawable.pruebavida
    styles.check = ai.trully.sdk.R.drawable.check_circulo
    styles.faceTimeout = ai.trully.truedeepfakedetection.R.drawable.rostrofail
    styles.noLocation = ai.trully.sdk.R.drawable.ubicacion
    styles.noCamera = ai.trully.sdk.R.drawable.camara
    styles.errorIcon = ai.trully.sdk.R.drawable.timeouticon

    //Set SDK configuration
    val config = TrullyConfig(environment = Environment.DEBUG, userID = "YOUR_ID_FOR_THE_PROCESS", style = styles)
    //* For production environments use `Environment.RELEASE`.
    //* We recommend using named arguments so the order doesn't matter. If you're not using them, this example shows the order you should pass the arguments.

    //Initialize SDK
    TrullySdk.init(apiKey = "YOUR_API_KEY", config = config)

    //Run SDK
    TrullySdk.start(packageContext = this, listener = this)
}
```

### Full Example

```java
import ai.trully.sdk.TrullySdk
import ai.trully.sdk.configurations.TrullyConfig
import ai.trully.sdk.configurations.TrullyStyles
import ai.trully.sdk.models.Environment
import ai.trully.sdk.models.ErrorData
import ai.trully.sdk.models.TrackDetail
import ai.trully.sdk.models.TrackStep
import ai.trully.sdk.models.TrullyResponse
import ai.trully.sdk.protocols.listeners.TrullyListeners

class MainActivity : AppCompatActivity(), TrullyListeners {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        initialize()
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

	styles.uiTexts.docType = Texts.PASSPORT

        styles.primaryColor = ai.trully.sdk.R.color.primary
        styles.disabledColor = ai.trully.sdk.R.color.disabled
        styles.backgroundColor = ai.trully.sdk.R.color.background

        styles.logo = ai.trully.sdk.R.drawable.logo
        styles.IDIcon = ai.trully.sdk.R.drawable.ine
    	styles.selfieIcon = ai.trully.sdk.R.drawable.selfie
    	styles.IdImage = ai.trully.sdk.R.drawable.id
    	styles.permission = ai.trully.sdk.R.drawable.modalandroid_camara
    	styles.light = ai.trully.truedeepfakedetection.R.drawable.light
    	styles.cross = ai.trully.truedeepfakedetection.R.drawable.cross
    	styles.showId = ai.trully.sdk.R.drawable.pruebavida
    	styles.check = ai.trully.sdk.R.drawable.check_circulo
    	styles.faceTimeout = ai.trully.truedeepfakedetection.R.drawable.rostrofail
    	styles.noLocation = ai.trully.sdk.R.drawable.ubicacion
    	styles.noCamera = ai.trully.sdk.R.drawable.camara
    	styles.errorIcon = ai.trully.sdk.R.drawable.timeouticon

        val config = TrullyConfig(environment = Environment.DEBUG, userID = "YOUR_ID_FOR_THE_PROCESS", style = styles)

        //Initialize SDK
        TrullySdk.init(apiKey = "YOUR_API_KEY", config = config)

        //Run SDK
        TrullySdk.start(packageContext = this, listener = this)
    }
}
```

## <a id="results"></a>Reading Results

### Decision Maker Response

You'll find more details in
[Decision Maker API Docs](https://docs.trully.ai/reference/post_v1-decision-maker-predict)

| Key                       | Description                                                                                                                                                      |
| ------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `tag`                     | String. The tag from the process. Automatically generated when you didn't pass one through configuration prop                                                    |
| `user_id`                 | String. The userID you passed with the TrullyConfig object                                                                                                       |
| `raw_data`                | Object containing the unprocessed data from the Decision Maker. You can learn more about [here](https://docs.trully.ai/reference/post_v1-decision-maker-predict) |
| `ip`                      | String. Device IP captured on the SDK process (Only available after v4.2.2)                                                                                      |
| `location`\*              | Object containing the latitude and longitude captured on the SDK process (Only available after v4.2.2)                                                           |
| `calculated_rfc`          | String. The RFC calculated from the document. Could be null (Only available after v4.4.0)                                                                        |
| `label`                   | String. The label generate by the Decision Maker for the user who has completed the process                                                                      |
|                           | No Threat - low risk user. Review - medium risk user. Potential Threat - high risk                                                                               |
| `reason`                  | Array. Contains the reasons behind the decision                                                                                                                  |
| `request_id`              | String. The ID generated bay the Decision Maker for the process.                                                                                                 |
| `image`                   | Base64 string. Selfie                                                                                                                                            |
| `document_image`          | Base64 string. Document front cropped                                                                                                                            |
| `document_image_complete` | Base64 string. Document front uncropped                                                                                                                          |
| `document_back`           | Base64 string. Document back cropped                                                                                                                             |
| `document_back_complete`  | Base64 string. Document back uncropped                                                                                                                           |

\* In order to make the process as smooth as possible for the user, after the
permission is granted, the SDK will wait up to five seconds to retrieve
location. If it is not possible, the SDK will let the user continue. Before the
data is sent to the Decision Maker, it will try again to retrieve the location.
The precess will never stop the user after they granted location permission.
This means, it its possible to complete a process without location data.

#### Example

```java
    override fun onResult(response: TrullyResponse) {
        //Complete Decision Maker response
        Log.d("TRULLY_SDK", response.raw_data.toString())

        //Short response
        Log.d("TRULLY_SDK", response.user_id.toString())
        Log.d("TRULLY_SDK", response.request_id.toString())
        Log.d("TRULLY_SDK", response.label.toString())
        Log.d("TRULLY_SDK", response.reason.toString())

        //Images - base64 string
        Log.d("TRULLY_SDK", response.image.toString())
        Log.d("TRULLY_SDK", response.document_image.toString())
        Log.d("TRULLY_SDK", response.document_image_complete.toString())
        Log.d("TRULLY_SDK", response.document_back.toString())
        Log.d("TRULLY_SDK", response.document_back_complete.toString())

        // IP (Only available after v4.2.2)
        Log.d("TRULLY_SDK", response.ip.toString())

        // Location (Only available after v4.2.2)
        Log.d("TRULLY_SDK", response.location?.lat.toString())
        Log.d("TRULLY_SDK", response.location?.lng.toString())

        // Calculated RFC (Only available after v4.4.0)
        Log.d("TRULLY_SDK", response.calculated_rfc.toString())
    }
```

### Receiving data with webhook

If you prefer it is possible to set a webhook url to receive the Decision Maker
response. To do it, add webhook parameter to the TrullyConfig object

#### Example

```java
  private fun initialize() {

    //Set SDK configuration
    val config = TrullyConfig(environment = Environment.DEBUG, userID = "YOUR_ID_FOR_THE_PROCESS", webhook = "YOUR_WEBHOOK_URL")
    //* For production environments use `Environment.RELEASE`.
    //* We recommend using named arguments so the order doesn't matter. If you're not using them, this example shows the order you should pass the arguments.

    //Initialize SDK
    TrullySdk.init(apiKey = "YOUR_API_KEY", config = config)

    //Run SDK
    TrullySdk.start(packageContext = this, listener = this)
}
```

## How to know the app size

The size that you are seeing is not the size that your end users will get. The
size that you can see includes support for four architectures (arm64-v8a,
armeabi-v7a, x86, and x86_64). But one device has one architecture, not four.
You can check the size of the app for one architecture creating an .aab for a
specific architecture by following these instructions

### 1.- Configure the desired architecture build on your app level `build.gradle`

#### Kotlin DSL

```groovy
android {
    defaultConfig {
        ...
        ndk {
            abiFilters 'arm64-v8a'
        }
    }
}
```

#### Groovy DSL

```groovy
android {
    defaultConfig {
        ...
        ndk {
            abiFilters("arm64-v8a")
        }
    }
}
```

### 2.- Generate a signed App Bundle

In the Menu of Android Studio: Build -> Generate Signed Bundle / APK. Then
choose the Android App Bundle option. Follow the assistance instructions and the
result should be a single architecture bundle with the size your app will take
on a user device

⚠️ Make sure to delete these configuration before creating the final package
