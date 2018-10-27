# iOS

## 2018-07-21

### Load image: `imageWithContentsOfFile` vs. `imageNamed`

Use `imageWithContentsOfFile` only when you are sure it's used once since iOS won't cache it; otherwise, use `imageNamed`.

## 2018-06-05

### What slows down app launch

- api calls
- disk I/O
- data preparation

## 2018-02-14

### When/what/how to cache

*When?* Read a lot, but no/min updates.

*What?* Images, API call results, computational results (e.g., row heights).

*How?* Use NSCache, networking frameworks that supports caching, set cache policy (e.g., `NSURLRequest.CachePolicy.returnCacheDataElseLoad`).

## 2018-01-22

### Swift Singleton

[Nice article](https://krakendev.io/blog/the-right-way-to-write-a-singleton) says it all.

```swift
class MySingleton {
    static let sharedInstance = MySingleton()
    private init() {}
}
```


## 2018-01-18

### Match `UIImage` size to `UIImageView`

*Why?* Scaling images on the fly impact performance.

*How?* If you have no control over the incoming image size, then scale the image outside of the main thread before displaying.

## 2017-12-29

### Make use of the `reuseIdentifier`

For:

- `UITableViewCell`s
- `UICollectionViewCell`s
- `UITableViewHeaderFooterView`s

If not reuse, it will create one each time a cell is displayed which impacts scrolling performance.

Remember to reuse

- headers and footers
- supplementary views

## 2017-12-24

### Set views as Opaque, if possible

Opaque means no transparency defined. If not set, it will impact performance, especially the animated UIs.

*Why?* iOS needs to go deeper into the view hierarchy to figure out what color to render.

*How?* Use `Debug\Color Blended Layers` option to locate non-opaque views.

## 2017-07-12

### Never block main thread

*Why?* UI rendering (*UIKit*) works in main thread.

*How?* All heavy lifting (time consuming) should be moved out of main thread, including:

- Disk I/O
- Networking, e.g., API calls
- Large computations
- Use GCD, and/or Operation

## 2017-02-15

### Don't keep instantiating heavy objects

*What?* Classes like NSDateFormatter, NSCalendar are heavy to instantiate.

*How?* Create *singleton* (and it should be thread-safe if the singleton is created properly.)

## 2016-10-10

### Persitent local storage options

- `NSUserDefaults` - tiny piece of info
- `JSON` files, `NSCoding` - large one time data, when used, needs to first load from disk to memory. And entire payload has to be loaded before be used.
- `SQLite`, `Core Data` - large queryable data

## 2016-07-05

### `UIScrollView` performance

Load the subviews in the scroll view only when they need to be displayed. Similar to how `UITableView` works.

## 2016-05-28

### Background: `colorWithPatternImage` vs. `UIImageView`

For small image repeated/tiled, use `colorWithPatternImage`
For large full size image, use `UIImageView`

## 2016-05-02

### What slows down table scroll

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
