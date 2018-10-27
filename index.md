# iOS

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

Why? iOS needs to go deeper into the view hierarchy to figure out what color to render.

How? Use `Debug\Color Blended Layers` option to locate non-opaque views.

## 2017-07-12

### Never block main thread

Why? UI rendering (*UIKit*) works in main thread.

How? All heavy lifting (time consuming) should be moved out of main thread, including:

- Disk I/O
- Networking, e.g., API calls
- Large computations
- Use GCD, and/or Operation
