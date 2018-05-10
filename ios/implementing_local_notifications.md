# Implementing Local Notifications in iOS 10

### Requirements
* Physical Device will be helpful but not necessary

### Implementing Local Notifications in iOS 10
For implementing user local notifications there are three things to keep in mind
* 1) Notification Request
* 2) Notification Center
* 3) Notifications Delegate

Lets discuss these:
##### 1) Notification Request
```swift
// Swift
let request = UNNotificationRequest(identifier: identifier,
                                            content: content, trigger: trigger)
```
The notification request contains  three things
###### Identifier
This is identity for every notification it should be different, this can be used to update the notification after some time too.
###### Content
This is notification content, title, subtitle, body, user info etc.
```swift
// Swift
var userInfo : [String : String] = [:]
userInfo["imageName"] = imageName

let content = UNMutableNotificationContent()
        content.sound = UNNotificationSound.default()
        content.title = title
        content.subtitle = subTitle
        content.body = body
        content.userInfo = userInfo
```
###### Triggers
This is the information when iOS should display the notifications.
There are three types of triggers
* Time interval
```swift
// Swift
let trigger = UNTimeIntervalNotificationTrigger(timeInterval: 300,
              repeats: false)
```
* Calendar 
```swift
// Swift
let date = Date(timeIntervalSinceNow: 3600)
let triggerDate = Calendar.current.dateComponents([.year,.month,.day,.hour,.minute,.second,], from: date) 
let trigger = UNCalendarNotificationTrigger(dateMatching: triggerDate,
              repeats: false)
```
* Location 
```swift
// Swift
let trigger = UNLocationNotificationTrigger(triggerWithRegion:region, repeats:false)
```
##### 2) Notification Center
To let iOS schedule the notification, we need to add the notification request via notification center.
```swift
//swift
let center = UNUserNotificationCenter.current()
center.add(request, withCompletionHandler: { (error) in
            if let error = error {
                // Something went wrong
                print( "\(UserNotificationUtils.TAG) : error: \(error.localizedDescription)" )
            }
        })
```
##### 3) Notifications Delegate
The notification delegate is used to recieve events like notification recieved, notification clicked etc. Notification delegate is set to notification center delegate property
```swift
//swift
let center = UNUserNotificationCenter.current()
center.delegate = self
```
and implement the delegate functions:

```swift
extension AppDelegate : UNUserNotificationCenterDelegate {
    func userNotificationCenter(_ center: UNUserNotificationCenter, didReceive response: UNNotificationResponse, withCompletionHandler completionHandler: @escaping () -> Void) {
        
        // Determine the user action
        switch response.actionIdentifier {
        case UNNotificationDefaultActionIdentifier:
            print("Open Notification")
        default:
            print("Unknown action")
        }
        
        completionHandler()
        
    }
    
    func userNotificationCenter(_ center: UNUserNotificationCenter, willPresent notification: UNNotification, withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        print("Notification Recieved")
        completionHandler([.alert, .sound])
    }
}
```

### Full Source Code  
```swift
import UserNotifications
class UserNotificationUtils  {
    
    static let TAG = "UserNotificationUtils"
    
    static func scheduleNotification(date : Date, title : String, subTitle : String,  body : String, imageName : String,  delegate : UNUserNotificationCenterDelegate){
        
        var userInfo : [String : String] = [:]
        
        userInfo["imageName"] = imageName
        
        // schedule user notification
        let content = UNMutableNotificationContent()
        content.sound = UNNotificationSound.default()
        content.title = title
        content.subtitle = subTitle
        content.body = body
        content.userInfo = userInfo
        
        
        let center = UNUserNotificationCenter.current()
        center.delegate = delegate
        
        let triggerDate = Calendar.current.dateComponents([.year,.month,.day,.hour,.minute,.second,], from: date)
        
        let trigger = UNCalendarNotificationTrigger(dateMatching: triggerDate,
                                                    repeats: false)
        
    
        let identifier = UUID().uuidString
        
        let request = UNNotificationRequest(identifier: identifier,
                                            content: content, trigger: trigger)
        center.add(request, withCompletionHandler: { (error) in
            if let error = error {
                // Something went wrong
                print( "\(UserNotificationUtils.TAG) : error: \(error.localizedDescription)" )
            }
        })
        
    }
    
}
```
The prefered way to write notification scheduling code is in AppDelegate because it can be accessed from any view controller.

So In App Delegate,
```
extension MyAppDelegate : UNUserNotificationCenterDelegate {
    
    func userNotificationCenter(_ center: UNUserNotificationCenter, didReceive response: UNNotificationResponse, withCompletionHandler completionHandler: @escaping () -> Void) {
        
        // Determine the user action
        switch response.actionIdentifier {
        case UNNotificationDefaultActionIdentifier:
            print("Open Notification")
            
            if(visibleViewController is SomeListViewController){

            }
            else if(visibleViewController is SomeDetailViewController){

            } else {  // notification opened outside the app
                let userInfo = response.notification.request.content.userInfo
                openNotification(title: response.notification.request.content.subtitle, imageName: userInfo["imageName"] as! String)
            }
            
        default:
            print("Unknown action")
        }
        
        completionHandler()
        
    }
    
    func userNotificationCenter(_ center: UNUserNotificationCenter, willPresent notification: UNNotification, withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        
        completionHandler([.alert, .sound])
    }
```
To open the view controller via AppDelegate, we need visible visibleViewController
```
//MARK:- Visible View Controller
extension MyAppDelegate {
    var visibleViewController: UIViewController? {
        return getVisibleViewController(nil)
    }
    
    func getVisibleViewController(_ rootViewController: UIViewController?) -> UIViewController? {
        
        var rootVC = rootViewController
        if rootVC == nil {
            rootVC = UIApplication.shared.keyWindow?.rootViewController
        }
        
        if rootVC?.presentedViewController == nil {
            return rootVC
        }
        
        if let presented = rootVC?.presentedViewController {
            if presented.isKind(of: UINavigationController.self) {
                let navigationController = presented as! UINavigationController
                return navigationController.viewControllers.last!
            }
            
            if presented.isKind(of: UITabBarController.self) {
                let tabBarController = presented as! UITabBarController
                return tabBarController.selectedViewController!
            }
            
            return getVisibleViewController(presented)
        }
        return nil
    }
}
```

#### How Schedule notification via AppDelegate
Steps:
* First write a function scheduleNotification in AppDelegate
```swift
func scheduleNotification(date : Date, subTitle : String, body : String, imageName : String){
        UserNotificationUtils.scheduleNotification(date: date, title: "16:8 Eats", subTitle: subTitle , body: body, imageName: imageName, delegate: self)
    }
```

* Now call this function via any view controller using the following code

```swift
let appDelegate = UIApplication.shared.delegate as? MyAppDelegate
appDelegate?.scheduleNotification(date: Date(), subTitle: "My Notification", body: "Notification Body", imageName: "abc.jpg")

```


### Notes
* The prefered way to schedule notification is in AppDelegate.

### Resources:
* https://useyourloaf.com/blog/local-notifications-with-ios-10/