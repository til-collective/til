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

### Getting OneSignalIds and saving to parse
Often times we want to store one signal id to server in our case parse. That OneSignalId will be used for sending push notification from parse server or device to device for chat message notifications. Kindly note that a user can logout from the app any time he wants, so this user should not be able to get push notification again. To acomplish this we need to clear the onesignal id when ever the user logouts from our app.
```swift
//
//  OneSignalUtils.swift
//
//
//  Created by Sheeraz Ahmed Memon on 30/04/2018.
//  Copyright © 2018 Ahsan Misbah. All rights reserved.
//

import Foundation
import OneSignal
import ParseUI
import RxSwift
public class OneSignalUtils : NSObject {
    
    static let TAG = "OneSignalUtils"
    
    override init() {
        
    }
    
    public func updateOneSignalIdForCurrentUser(completionHandler: @escaping (Bool) -> ()){
        
        //getPermissionSubscriptionState
        let status: OSPermissionSubscriptionState = OneSignal.getPermissionSubscriptionState()
        let hasPrompted = status.permissionStatus.hasPrompted
        let userStatus = status.permissionStatus.status
        let isSubscribed = status.subscriptionStatus.subscribed
        let userSubscriptionSetting = status.subscriptionStatus.userSubscriptionSetting
        let userID = status.subscriptionStatus.userId
        let pushToken = status.subscriptionStatus.pushToken
        
        if userID != nil  {
            if(PFUser.current() != nil){
                let user = PFUser.current() as! RTUser
                user.oneSignalId = userID
                user.saveInBackground(block: { (result, error) in
                    
                    _ = self.deleteDuplicateOneSignalIdsCurrUserObservable(userId: user.objectId!, oneSignalId: user.oneSignalId)
                        .observeOn(MainScheduler())
                        .subscribeOn(ConcurrentDispatchQueueScheduler(qos: .background))
                        .subscribe(onNext: { (result) in
                            print("\(OneSignalUtils.TAG) : onNext()")
                            
                            completionHandler(true)
                            
                            }, onError: { (err) in
                                print("\(OneSignalUtils.TAG) : error: \(err)")
                                
                            }, onCompleted: {
                                print("\(OneSignalUtils.TAG) : onCompleted()")
                                
                        } )
                    
                    
                })
            }
        }
    }
    
    
    private func deleteDuplicateOneSignalIdsCurrUserObservable(userId : String, oneSignalId : String) -> Observable<Bool> {
        
        return Observable.create { observer in
            
            print("\(OneSignalUtils.TAG) : deleteDuplicateOneSignalIdsCurrUserObservable()")
            
            var parms : [String : Any] = [:]
            parms["userId"] = userId
            parms["oneSignalId"] = oneSignalId
        
            do {
                
                _ = try PFCloud.callFunction("checkDuplicateOneSignalId", withParameters: parms)
                
                observer.onNext(true)
                observer.onCompleted()
                
            } catch {
                observer.onError(error)
            }
            
            return Disposables.create()
        }
    }
}

```
### Remove Duplicate One Signal Ids Cloud Function
```js
Parse.Cloud.define("checkDuplicateOneSignalId", function(request, response) {

    console.log('Started Checking One Signal Id');

    var observerdUserId = request.params.userId;
    var observedOneSignalId = request.params.oneSignalId;

    // var testOneSignalId = 'bcf07e40-aff4-4533-adba-8c9be1baae55';
    // var testUserId = 'HxP0F1e4dX';

    var Table = Parse.Object.extend(Parse.User);
    var query = new Parse.Query(Table);
    var userOneSignalId = '';
    var userObjectId = '';

    query.equalTo('oneSignalId', observedOneSignalId);

    query.find().then(function(users) {
        console.log('Users found');
        console.log(users);


        users.forEach(function(user) {
            userOneSignalId = user.get('oneSignalId');
            userObjectId = user.id;

            console.log('One Signal Id ', userOneSignalId);
            console.log('User id ', userObjectId);

            if (userOneSignalId === observedOneSignalId && userObjectId !== observerdUserId) {
                user.set('oneSignalId', '');
                user.save(null, { useMasterKey: true }).then(function() {
                    console.log('User OneSignalId destroyed');
                }).catch(function(error) {
                    console.log('Error');
                    console.log(error);
                })
            }

        });

        response.success('Checking Completed');

    }).catch(function(error) {
        console.log('Error');
        console.log(error);
    })


});
```


### Resources:
* https://www.youtube.com/watch?v=tjuv3g_ZDEU
* https://www.youtube.com/watch?v=yKFM4xD2KHA
* https://documentation.onesignal.com/docs/ios-sdk-setup