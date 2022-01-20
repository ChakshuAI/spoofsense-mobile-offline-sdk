# SpoofSense Passive Liveness Android SDK (Offline)

## Overview
SpoofSense Passive Liveness SDK let's you authenticate the liveness of your users on edge. 

## Prerequisites
Before integrating the SDK please contact your SpoofSense to obtain your artifactory_user, artifactory_password, artifactory_context_url.

## SDK Compatibility
The SDK supports all Android flavours above 25 (Android 7.1 Nougat)

## Table Of Content
- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [SDK Compatibility](#sdk-compatibility)
- [Integration Steps](#integration-steps)
    1. [Give required permissions in your application](#1-give-required-permissions-in-your-application)
    2. [Add JFrog Credentials and URL](#2-add-jfrog-credentials-and-url)
    3. [Add JFrog dependency to the application](#3-add-jfrog-dependency-to-the-application)
    4. [Implement JFrog dependency to the application](#4-implement-jfrog-dependency-to-the-application)
    5. [Authenticate connection to JFrog Artifactory](#5-authenticate-connection-to-jfrog-artifactory)
    6. [Add required implementation for SpoofSense SDK](#6-Add-required-implementation-for-spoofsense-sdk)
    
- [Usage](#usage)
    1. [Import SpoofSense functionalities](#1-import-spoofsense-functionalities)
    2. [Implement the callback function](#2-implement-the-callback-function)
    3. [Initialize SpoofSense SDK](#3-initialize-spoofsense-sdk)
    4. [Load Model](#4-load-model)
    5. [Load Image Bitmap](#5-load-image-bitmap)
    6. [Fetch results from SpoofSenseResult callback](#6-fetch-results-from-spoofsenseresult-callback)
  
- [Contact Us](#contact-us)


## Integration Steps

### 1. Give required permissions in your application
#### In AndroidManifest.xml, add these lines:

```java
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"></uses-permission>
```

### 2. Add JFrog Credentials and URL	
#### In gradle.properties, add these lines:

```java
artifactory_user=your_user_name
artifactory_password=your_password
artifactory_context_url=</URL/>
```


### 3. Add JFrog dependency to the application
#### In Project build.gradle, add JFrog dependency under dependencies tag.

```java
dependencies {
   . . . 
   classpath "org.jfrog.buildinfo:build-info-extractor-gradle:4+" 
   . . .  
}
```


### 4. Implement JFrog dependency to the application
#### In Project build.gradle, initialize the jfrog artifactory in the allprojects tag.

```java
allprojects {
   apply plugin: "com.jfrog.artifactory"
   . . .
}
```


### 5. Authenticate connection to JFrog Artifactory
#### In Project build.gradle, add maven credentials under repositories under allprojects tag.

```java
allprojects {
   . . .
   repositories {
       . . .
       maven { // Add these lines for authentication
           url "${artifactory_context_url}"
           credentials {
               username = "${artifactory_user}"
               password = "${artifactory_password}"
           }
       }
   }
   . . .
}
```


### 6. Add required implementation for SpoofSense SDK
#### In app build.gradle, add the following implementations under dependencies tag.

```java
dependencies {
   . . .
   implementation 'org.pytorch:pytorch_android:1.8.0'
   implementation 'org.pytorch:pytorch_android_torchvision:1.8.0'
   implementation 'com.chakshu.SpoofSenseSDK:app:v0.2.3'
   . . .
}
```

## Usage

### 1. Import SpoofSense functionalities
#### Import *SpoofSenseInterface* and *SpoofSenseResult* fetching callback to the application. In MainActivity.java, add the following imports.

```java
import com.chakshu.androidsdk.SpoofSenseInterface;
import com.chakshu.androidsdk.SpoofSenseResult;
```

### 2. Implement the callback function
#### In MainActivity.java, implement *SpoofSenseResult* fetching callback in the MainActivity class.

```java
public class MainActivity extends AppCompatActivity implements SpoofSenseResult {
   . . .
}
```

### 3. Initialize SpoofSense SDK
#### In MainActivity.java, instantiate *SpoofSenseInterface* class. This initializes SpoofSense SDK.

```java
SpoofSenseInterface spoofSenseInterface = new SpoofSenseInterface();
```

### 4. Load Model
#### In MainActivity.java, load the model by calling the *LoadModel* method. The method takes the Application Context as a parameter and returns an integer value.

```java
int a = spoofSenseInterface.LoadModel(getApplicationContext());
```

### 5. Load Image Bitmap
#### In MainActivity.java, the Image Bitmap can be loaded by using the *LoadImage* method. This method takes as parameters the Application Context, 
#### Activity, callback reference, bitmap and rect bounding box. *Rect* should be used for the bounding box and the method should be called only if the 
#### model is loaded successfully.

Rect refers to the cropping coordinates required to crop the face from the image. Rect has 4 attributes, namely Rect.top, Rect.bottom, Rect.left, Rect.right

```java
private Rect bb_box;
. . .

if(a == 1000){ // check model load is successful
   spoofSenseInterface.LoadImage(getApplicationContext(), this, this, decodedByte, bb_box);
}
```

### 6. Fetch results from SpoofSenseResult callback
#### As soon as the LoadImage method is called, the result from the SpoofSense SDK can be fetched in the method SSResult. 
#### The parameters to the method are Top predicted labels, Top predicted probabilities and the message. The results can be utilised as per need.

```java
@Override
public void SSResult(String[] topKClassNames, float[] topKScores, String errorMsg) {  // Fetch results from SpoofSenseResult CallBack
   
   . . .

}
```

The parameters to the method are:
- **topKClassNames:**	string array of class labels with the most probable label followed by the other. 

- **topKScores:**	float array of probabilities for classes with the probability of the most probable class followed by the probability of the other.

- **errorMsg:**	string message to indicate the status of execution. Following are the different messages provided:

  - ‘’Success’’: This indicates that the pipeline was executed successfully.
  - ‘’Minimum face check Failed”: This indicates that the cropped Bitmap height and width is too small. The height of the cropped Bitmap should be greater than 150 and the width of the cropped Bitmap should be greater than 150.
  - “Bitmap or Rect empty”: This indicates that the Bitmap or Rect is either empty or NULL.
  - “Error during image analysis”: This indicates that the PyTorch image analysis functionality failed.

NOTE: The parameters name can be changed if needed.


## Contact Us
Please send us a mail at [founders@spoofsense.com](mailto:founders@spoofsense.com)

