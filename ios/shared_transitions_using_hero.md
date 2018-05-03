# Implementing Shared Transitions Using Hero

### Requirements
* Physical Device will helpful but not necessary

### What is shared transition?
Traditionally transitions between different two view controllers involved enter and exit transitions that animated entire view hierarchies independent to each other. Example of such transitions are a fade transition, slide transition or the newly introduced explode transition.
![alt text](https://cdn-images-1.medium.com/max/800/1*YPDis2g8BiKKzkeR2dUgcQ.gif "Examples of Enter/Exit Transitions")
However, many times, there are elements common to both view controllers and providing the ability to transition these shared elements separately emphasizes continuity between transitions and breaks activity boundaries as the user navigates the app.
The nature of this transition forces the human eye to focus on the content and its representation in the new view controller instead of the actual view controller frame sliding or fading which makes the experience a lot more seamless. These transitions are called shared transitions
![alt text](https://2.bp.blogspot.com/-SYxpgdCZYdQ/WoYW8x3m0II/AAAAAAAAFEs/m4ZE9gpGwsE1n1SopuBkvbg9_IGzGqgpgCLcBGAs/s1600/intro.gif "Example of Shared Transition")

The above example is for android, but same can be implemented in iOS too.
### Implementing Shared Transitions Using Hero
##### Steps: 
* Install the hero pod, https://github.com/lkzhao/Hero
```
pod "Hero"
```
To make shared transitions between two view controllers, the following are the steps:
* Enable hero in both view controllers in first view controller when transitioning and in second in viewDidLoad()
```swift
controller.hero.isEnabled = true
```
* Assign same heroId for shared elements in both view controllers
```swift
ivDetail.hero.id = "image"
```
* Call replaceViewController from Hero Library instead of pushViewController or presentViewController
```swift
self.hero.replaceViewController(with: controller)
```
### Full Example Source Code
##### ArticleListViewController:
-----------
```swift
//
//  ArticlesListViewController.swift
//  
//
//  Created by Sheeraz Ahmed Memon on 03/05/2018.
//  Copyright © 2018 iSystematic LLC. All rights reserved.
//

import UIKit
import Hero

class ArticlesListViewController: UITableViewController {
    
    fileprivate let items = ["image1", "image2", "image3"]
    
    override func viewDidLoad() {
        super.viewDidLoad()
        tableView.delegate = self
        tableView.dataSource = self
    }

}

//MARK:- Table View Settings....
extension ArticlesListViewController {
    
    public override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return items.count
    }
    
    public override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        
        let cell = tableView.dequeueReusableCell(withIdentifier: "ArticleTableViewCell") as! ArticleTableViewCell
        
        cell.articleImageView.image = UIImage(named: items[indexPath.row].0)
        
        cell.articleImageView.hero.id = "image"
        
        return cell
    }
    
    override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        let controller = UIStoryboard(name: "HeroApproach", bundle: nil).instantiateViewController(withIdentifier: "ArticleDemoDetailViewController") as! ArticleDemoDetailViewController
        controller.imageName = items[indexPath.row]
        controller.hero.isEnabled = true
        self.hero.replaceViewController(with: controller)
    }
}

```
##### ArticleDemoDetailViewController:
-----------
```swift
class ArticleDemoDetailViewController: UIViewController {
    
    @IBOutlet weak var ivDetail : UIImageView!
    
    var imageName : String?

    override func viewDidLoad() {
        super.viewDidLoad()
        
        self.hero.isEnabled = true
        ivDetail.hero.id = "image"
        
        if(imageName != nil){
            ivDetail.image = UIImage(named: imageName!)
        }
    }

}
```


### Resources
* https://medium.com/the-code-community/hello-x-cool-swift-3-transition-framework-ddada37db819
* https://guides.codepath.com/android/shared-element-activity-transition