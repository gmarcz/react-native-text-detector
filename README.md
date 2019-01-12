# react-native-text-detector

## Getting started

`$ npm install git+https://github.com/gmarcz/react-native-text-detector.git --save`
or
`$ yarn add git+https://github.com/gmarcz/react-native-text-detector.git`

### Manual installation

#### iOS

Please create project on firebase console, Download a GoogleService-Info.plist file from [Firebase console](https://console.firebase.google.com/?pli=1) and include it in your app.

##### Using Pods (Recommended)
1. In `<your_project>/ios` create Podfile file `pod init`
2. Add following in `ios/Podfile` 
```ruby
    pod 'yoga', :path => '../node_modules/react-native/ReactCommon/yoga'
    pod 'React', :path => '../node_modules/react-native'
    pod 'RNTextDetector', path: '../node_modules/react-native-text-detector/ios'
```
3. Run following from project's root directory
```bash
    pod update && pod install
```
4. Use `<your_project>.xcworkspace` to run your app
5. In Xcode include GoogleService-Info.plist in your app
6. Configure Firebase, edit `ios/<your_project>/AppDelegate.m` import Firebase `@import Firebase;` and configure the library `[FIRApp configure];`
```bash
    ...
    #import <React/RCTRootView.h>
    @import Firebase;
    ...
    
    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
  [FIRApp configure];

  NSURL *jsCodeLocation;
  ...
```

##### Direct Linking
1.  In XCode, in the project navigator, right click `Libraries` ➜ `Add Files to [your project's name]`
2.  Go to `node_modules` ➜ `react-native-text-detector` and add `RNTextDetector.xcodeproj`
3.  In XCode, in the project navigator, select your project. Add `libRNTextDetector.a` to your project's `Build Phases` ➜ `Link Binary With Libraries`
4.  Run your project (`Cmd+R`)<

#### Android

1.  Open up `android/app/src/main/java/[...]/MainApplication.java`

- Add `import com.fetchsky.RNTextDetector.RNTextDetectorPackage;` to the imports at the top of the file
- Add `new RNTextDetectorPackage()` to the list returned by the `getPackages()` method

2.  Append the following lines to `android/settings.gradle`:
    ```
    include ':react-native-text-detector'
    project(':react-native-text-detector').projectDir = new File(rootProject.projectDir, 	'../node_modules/react-native-text-detector/android')
    ```
3.  Insert the following lines inside the dependencies block in `android/app/build.gradle`:

    ```
    ...
    dependencies {
        implementation 'com.google.firebase:firebase-core:16.0.1'
        implementation 'com.google.firebase:firebase-ml-vision:17.0.0'

        implementation (project(':react-native-text-detector')) {
            exclude group: 'com.google.firebase'
        }
    }

    // Place this line at the end of file

    apply plugin: 'com.google.gms.google-services'

    // Work around for onesignal-gradle-plugin compatibility
    com.google.gms.googleservices.GoogleServicesPlugin.config.disableVersionCheck = true
    ```

4.  Insert the following lines inside the dependencies block in `android/build.gradle`:

    ```
    buildscript {
        repositories {
            google()
            ...
        }
        dependencies {
            classpath 'com.android.tools.build:gradle:3.0.1'
            classpath 'com.google.gms:google-services:4.0.1' // google-services plugin
        }
    }
    ```

## Example usage with react-native-camera (ios)

1.  Install react-native-camera

`$ npm install --save react-native-camera`
or
`$ yarn add react-native-camera`

2.  Link the library

`$ react-native link react-native-camera`

3. In Xcode add permission for camera `Info.plist`

```
<key>NSCameraUsageDescription</key>
<string>Your message to user when the camera is accessed for the first time</string>

<key>NSMicrophoneUsageDescription</key>
<string>Your message to user when the microphone is accessed for the first time</string>
```

```javascript
/**
 *
 * This Example uses react-native-camera for getting image
 *
 */

import React from 'react';
import { StyleSheet, Text, View, TouchableOpacity } from 'react-native';
import { RNCamera } from 'react-native-camera';
import RNTextDetector from "react-native-text-detector";

export class TextDetectionComponent extends PureComponent {

  takePicture = async () => {
    if (this.camera) {
        try {
          const options = {
            quality: 0.8,
            skipProcessing: true,
          };
          const { uri } = await this.camera.takePictureAsync(options);
          const visionResp = await RNTextDetector.detectFromUri(uri);
          console.log('visionResp', visionResp);
        } catch (e) {
          console.warn(e);
        }
    }
  };

  render() {
    return (
      <View style={styles.container}>
        <RNCamera
            ref={comp => { this.camera = comp; }}
            style = {styles.preview}
            type={RNCamera.Constants.Type.back}
            flashMode={RNCamera.Constants.FlashMode.on}
            permissionDialogTitle={'Permission to use camera'}
            permissionDialogMessage={'We need your permission to use your camera phone'}
        />
        <View style={{flex: 0, flexDirection: 'row', justifyContent: 'center',}}>
        <TouchableOpacity
            onPress={this.takePicture}
            style = {styles.capture}
        >
            <Text style={{fontSize: 14}}> SNAP </Text>
        </TouchableOpacity>
        </View>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: 'column',
    backgroundColor: 'black'
  },
  preview: {
    flex: 1,
    justifyContent: 'flex-end',
    alignItems: 'center'
  },
  capture: {
    flex: 0,
    backgroundColor: '#fff',
    borderRadius: 5,
    padding: 15,
    paddingHorizontal: 20,
    alignSelf: 'center',
    margin: 20
  }
});
```
