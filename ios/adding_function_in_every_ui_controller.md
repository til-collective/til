# Adding a function in base class using Protocols and Extensions

Let’s say there are many places in an app where a pop up alert is needed. We love the DRY principle and don’t want to copy and paste the alert creation code everywhere. We can resolve this problem using a protocol and an extension.

First, create a protocol

```swift
protocol AlertPresentable {
   func presentAlert(message: String)
}
```
Then, an extension with a default implementation:
```swift
extension AlertPresentable {
 
   func presentAlert(message: String) {
     let alert = UIAlertController(title: “Alert”, message: message,    preferredStyle: .alert)
     alert.addAction(UIAlertAction(title: “OK”, style: .default,  handler: nil))
   }
}
```
Ok, the presentAlert method only creates the alert, but doesn’t present anything. We need a view controller reference for that. Should we pass it through an argument into the method? Meh, it’s not a good idea. Let’s use the Where clause!
```swift
extension AlertPresentable where Self : UIViewController {
 
 func presentAlert(message: String) {
    let alert = UIAlertController(title: “Alert”, message: message,  preferredStyle: .alert)
    alert.addAction(UIAlertAction(title: “OK”, style: .default, handler: nil))
    self.present(alert, animated: true, completion: nil)
  }
}
```
What’s happened here? We have added a special requirement to our protocol. Only UIViewController’s can conform to it. Thanks to this, we can call UIViewController methods inside the presentAlert method. This allows us to display the alert.

Let’s go further:

```swift
extension UIViewController : AlertPresentable {}
```
Now, all UIViewControllers have a new ability:

![alt text](https://cdn-images-1.medium.com/max/800/0*MyYI753o2PGwEks8. "presentAlert Function in Every UIViewController")



### Resources:
* https://blog.indoorway.com/swift-v-kotlin-the-differences-that-matter-50b2d393f526