# RainbowBridge
A native bridge that using WKScriptMessageHandler to expose native function to JavaScript

Supported Api:
* [scanMetadata](#scanmetadata) - Scan specified type of metadata using camera
* [playVibration](#playvibration) - Play vibration
* [authenticateTouchId](#authenticatetouchid) - Authenticate with Touch Id

## Requirements:
* ios >= 8.0
* swift 2

## Install with cocoapods
* PodFile
```
platform :ios, '8.0'
use_frameworks!

pod 'RainbowBridge'
```

## Usage
```swift
import RainbowBridge

let webView = RainbowBridge.initWithFrame(self.view.frame)
yourView.addSubview(webView)

let url = NSURL(string: "http://www.github.com")
let req = NSURLRequest(URL:url!)
webView.loadRequest(req)
```

in Javascript
```javascript
// define callback if needed
window.rainbowBridge = {
  callbacks: {
    'onPlayVibration': function(returnedValue) {
      console.log('Vibration played = ', returnedValue); // Vibration played = true
    }
  },
  executeCallback: function(id, returnedValue) {
    this.callbacks[id] && this.callbacks[id](returnedValue);
  }
};

// a helper to stringify JSON object
function createBridgeObjectString(name, id) {
  var obj = {
    wrappedApiName: name,
    callbackId: id
  };


  return JSON.stringify(obj);
}

// create and send the stringified json object to swift
var data = createBridgeObjectString('playVibration', 'onPlayVibration');
window.webkit.messageHandlers.rainbowBridge.postMessage(data);
```

## Supported Api:

### scanMetadata ###
[ref](https://developer.apple.com/library/ios/documentation/AVFoundation/Reference/AVMetadataMachineReadableCodeObject_Class/#//apple_ref/occ/instp/AVMetadataMachineReadableCodeObject/stringValue)
Scan and detect one-dimensional or two-dimensional barcode.
This will return a callback with detected data as argument

```javascript
{
  wrappedApiName: 'scanMetadata',
  metadataTypes: ['org.iso.QRCode', 'org.gs1.EAN-13'] // array of types to be detected
}
```
MetadataTypes:
```
face
org.iso.Aztec
org.iso.Code128
org.iso.Code39
org.iso.Code39Mod43 
com.intermec.Code93
org.iso.DataMatrix
org.gs1.EAN-13
org.gs1.EAN-8
org.ansi.Interleaved2of5
org.gs1.ITF14
org.iso.PDF417
org.iso.QRCode
org.gs1.UPC-E
```

### playVibration ###
[ref](https://developer.apple.com/library/ios/documentation/AudioToolbox/Reference/SystemSoundServicesReference/#//apple_ref/c/func/AudioServicesPlayAlertSound) -
Plays a system sound as an alert.
```javascript
{
  wrappedApiName: 'playVibration'
}
```

### authenticateTouchId ###
[ref](https://developer.apple.com/library/prerelease/ios/documentation/LocalAuthentication/Reference/LAContext_Class/index.html#//apple_ref/occ/instm/LAContext) -
Request the user to authenticate themselves using personal information such as a fingerprint registered with Touch ID.
```javascript
{
  wrappedApiName: 'authenticateTouchId',
  reason: 'Verify with your finger'
}
```
