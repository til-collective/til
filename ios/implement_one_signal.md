# Implement Push Notifications Using One Signal

### Requirements
* Physical Device to test one signal
* Paid Apple developer account

### Setup One Signal
#### Steps:
   * Create an account at onesignal.com
   * Create your app at onesignal.com with name MyApp.
   * Generate Certificates for Push Notifications (both debug and release)at developer.apple.com and provide them at onesignal.com. For more help for Push Notifications read step 2 : APNS Prerequisites Certs at https://www.pubnub.com/docs/ios-objective-c/mobile-gateway-sdk-v4#APNS_Prerequisites.

### Implementing One Signal
#### Steps:
* Open PodFile and add the following pod 
```
target 'YourApp-IOS' do
pod 'OneSignal', '>= 2.6.2', '< 3.0'
end
```
* and install the pod using pod install
* The following function is used for initializing OneSignal
```swift
func initOneSignal(withLaunchingOptions launchOptions: [UIApplicationLaunchOptionsKey : Any]? = nil ){
        let onesignalInitSettings = [kOSSettingsKeyAutoPrompt: false]
        
        OneSignal.initWithLaunchOptions(launchOptions, appId: oneSignalAppId , handleNotificationReceived: { [weak self] (notification) in
            
            print("\(self?.TAG ?? "") didRecievePushNotification")
            
        }, handleNotificationAction: { (notificationResult) in
            
        }, settings: onesignalInitSettings)

        OneSignal.inFocusDisplayType = OSNotificationDisplayType.notification;

        // Recommend moving the below line to prompt for push after informing the user about
        //   how your app will use them.
        OneSignal.promptForPushNotifications(userResponse: { accepted in
            print("User accepted notifications: \(accepted)")
        })
    }
```
* call this function in AppDelegate class
```swift
self.initOneSignal(withLaunchingOptions: launchOptions)
```

### Testing One Signal
* See the following link: https://documentation.onesignal.com/docs/testing-mobile-push-notifications


### Resources:
* https://www.youtube.com/watch?v=tjuv3g_ZDEU
* https://www.youtube.com/watch?v=yKFM4xD2KHA
* https://documentation.onesignal.com/docs/ios-sdk-setup