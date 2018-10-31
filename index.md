# iOS

## Steps to performance tuning

##### 2018-09-16

Notes from [Practical Approaches to Great App Performance](https://developer.apple.com/videos/play/wwdc2018/407/)

- measure existing performance, set up baseline.
- re-measure performance, compare, document, share.
- focus on total impact (vs. just an area that are not used by many users, not used frequently)
- reproduce -> profile -> modify -> repeat
- good to have automated performance tests to avoid regressions over time (the thousand performance paper cuts)
- important to understand user usage pattern, so that target is clear and optimizing on the most valuable areas.
- break large task into smaller steps and write unit tests for each step. That helps pinpoint issue.
- integration tests are measured from UX perspective, covering not just the task, but all other areas that work together.
- always start with integration tests, so that you know what area to start the tuning.
- use `Time Profiler` to get performance profile.
- if not debugging threads related, better not to group the trace by thread. To disable it: `Call Tree` -> uncheck `Separate by Thread`.
- how to remove *noise*, or focus on the *signal*
  - focus on one thread at a time. To do that: select only the thread that cost the most in the `Track Filter` panel
  - focus on one message at a time. To do that: select the suspecious in the `Heaviest back trace` panel
  - remove recursions. To do that: `Call Tree` -> check `Flatten recursion`.
  - drill down (bring up to the top level). To do that: right click on the trace -> `Focus on subtree`
  - hide all *objc* related messages in call tree (bubble those "cost of doing business" up to the parent callers). To do that: right click -> `Charge 'libobjc.A.dylib' to callers`
  - hide all small "contributors". Say for a 2-sec sample range, filter out only trace that > 20ms, that means only focusing on contributors that cost >1%. To do that: `Call trace constraints` -> 20 for min.
- even the slowness happens in system lib, there is possibilities to optimize
  - the data you passed into the system lib to operate
  - how many times you are calling this system method
  - system method is calling back into your code via delegates
- key-value observer (KVO) (i.e., "update UI whenever model changes") in a loop could impact performance
- instant app launch means? 500ms to 600ms (that's how long it takes the zoom animation from the home screen)
- do initialization, such as DB init, in the background thread, to improve launch time.
- as little work as possible in the initializers
- load data on screen synchronously, off-screen data async.
- strive for **constant** time.

## Load image: `imageWithContentsOfFile` vs. `imageNamed`

###### 2018-07-21

Use `imageWithContentsOfFile` only when you are sure it's used once since iOS won't cache it; otherwise, use `imageNamed`.

## Access levels

###### 2018-07-20

- `open/public`: cross-module
- `internal`: the default, within same module
- `fileprivate`: within same file
- `private`: within same enclosing declaration and extensions

## Unregistering NSNotificationCenter observers is optional in iOS 9+

###### 2018-06-27

See Apple [release note](https://developer.apple.com/library/archive/releasenotes/Foundation/RN-FoundationOlderNotes/index.html#10_11NotificationCenter)

## Live Rendering

###### 2018-06-17

Notes from [IBInspectable / IBDesignable](https://nshipster.com/ibinspectable-ibdesignable/)

`user-defined runtime attributes`: allows updating attribute value in Xcode

`IBInspectable`: easlier editable in Xcode

Can add inspectable property to existing classes via `extension`.

`IBDesignable`: WYSIWYG.

Use `prepareForInterfaceBuilder()` to provide dummy data at design time. It's not run in shipping code.

## Escaping closure

###### 2018-06-09

A clusure that is

- passed in to a function as parameter
- called after the function has returned

Closures are non-escaping by default in Swift 3+.

Use `@escaping` to mark it as escaping.

## What slows down app launch

###### 2018-06-05

- api calls
- disk I/O
- data preparation

## `let` vs. read-only (computed)

###### 2018-05-25

- `let` means constant, and cannot be set after initialized.
- read-only also means cannot be set but since it is computated so it could return different value at different times.

## 2 Swift examples of Decorator Design Pattern

###### 2018-04-09

`Extension` and `Delegation`

## When/what/how to cache

###### 2018-02-14

*When?* Read a lot, but no/min updates.

*What?* Images, API call results, computational results (e.g., row heights).

*How?* Use NSCache, networking frameworks that supports caching, set cache policy (e.g., `NSURLRequest.CachePolicy.returnCacheDataElseLoad`).

## When to use `unowned` variable

###### 2018-02-06

Only when you want to use a `weak` variable but are sure it will never be `nil` once it has been set during initialization.

## Capture list/value

###### 2018-01-25

Notes from [Capturing Values In Swift Closures](https://marcosantadev.com/capturing-values-swift-closures/)

Within a closure, referring to variables outside of th closure, strong references are created.

```swift
class MyClass {
    let a = 1
    let myClosure = {
        print(self.a)
    }
    myClosure() // 1
    a = 2
    myClosure() // 2
}
```

If we don't want the strong ref, we can specify `weak`.

```swift
class MyClass {
    let a = 1
    let myClosure = { [weak self] in
        guard let strongSelf = self else { return }
        print(strongSelf.a)
    }
    myClosure() // 1
    a = 2
    myClosure() // 1    <-- here's the difference
}
```

## Swift Singleton

###### 2018-01-22

[Nice article](https://krakendev.io/blog/the-right-way-to-write-a-singleton) says it all.

```swift
class MySingleton {
    static let sharedInstance = MySingleton()
    private init() {}
}
```

## Match `UIImage` size to `UIImageView`

###### 2018-01-18

*Why?* Scaling images on the fly impact performance.

*How?* If you have no control over the incoming image size, then scale the image outside of the main thread before displaying.

## Make use of the `reuseIdentifier`

###### 2017-12-29

For:

- `UITableViewCell`s
- `UICollectionViewCell`s
- `UITableViewHeaderFooterView`s

If not reuse, it will create one each time a cell is displayed which impacts scrolling performance.

Remember to reuse

- headers and footers
- supplementary views

## Set views as Opaque, if possible

###### 2017-12-24

Opaque means no transparency defined. If not set, it will impact performance, especially the animated UIs.

*Why?* iOS needs to go deeper into the view hierarchy to figure out what color to render.

*How?* Use `Debug\Color Blended Layers` option to locate non-opaque views.

## NSError

###### 2017-12-21

Notes from [NSError](https://nshipster.com/nserror/)

- `domain`: the subsystem
- `code`: error code within that subsystem
- `userInfo`: a dictionary, contains all other details

Also available: `localizedDescription`

## KVO

###### 2017-07-31

Notify objects when changes to properties of other objects. Very similar to willSet and didSet, but they can be defined outside of the type defination.

```swift
observation = observe(
            \.objectToObserve.myDate,
            options: [.old, .new]
        ) { object, change in
            print("myDate changed from: \(change.oldValue!), updated to: \(change.newValue!)")
        }
```

## Never block main thread

###### 2017-07-12

*Why?* UI rendering (*UIKit*) works in main thread.

*How?* All heavy lifting (time consuming) should be moved out of main thread, including:

- Disk I/O
- Networking, e.g., API calls
- Large computations
- Use GCD, and/or Operation

## Responder chain

###### 2017-03-18

UIControl actions will send events to a chain of responders, if the 1st one doesn't implement the action, then it goes deeper to the 2nd one, till it's handled or no more responders in the chain.

## Dynamic dispatch

###### 2017-02-18

*What?* Since **override** is support by Swift, it has to be determined at runtime what methods/properties to call, so an indirect call cannot be avoid.

When performance is important, to minimize and help compiler optimization, use `private`, `final` access levels to declare methods/properties.

## Don't keep instantiating heavy objects

###### 2017-02-15

*What?* Classes like NSDateFormatter, NSCalendar are heavy to instantiate.

*How?* Create *singleton* (and it should be thread-safe if the singleton is created properly.)

*Tips:* If possible, use UNIX epoch (an Int) to represent a date. That speeds up date object creation (vs. creation by parsing date formatted string.)

## bounds vs. frame

###### 2016-12-09

- bounds: uses its own coordinate system, used to place views in itself.
- frame: uses parent view's coordinate system, used to place it in parent view.

## Persitent local storage options

###### 2016-10-10

- `NSUserDefaults` - tiny piece of info
- `JSON` files, `NSCoding` - large one time data, when used, needs to first load from disk to memory. And entire payload has to be loaded before be used.
- `SQLite`, `Core Data` - large queryable data

## When to use `weak` variable

###### 2016-07-14

Object A holds a reference to Object B while Object B also holds a reference to Object A, then we have a retain cycle here. To break it, one of the object should be defined as `weak`. So the ARC can eventually count down to zero then release both objects.

## `UIScrollView` performance

###### 2016-07-05

Load the subviews in the scroll view only when they need to be displayed. Similar to how `UITableView` works.

## Background: `colorWithPatternImage` vs. `UIImageView`

###### 2016-05-28

For small image repeated/tiled, use `colorWithPatternImage`
For large full size image, use `UIImageView`

## What slows down table scroll

###### 2016-05-02

- cell creation
- transparency
- gradient
- shadow
- off-screen drawing
- image scaling
- row height calculation
- api call
- data transformation
- deep subviews
