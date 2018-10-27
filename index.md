# 2018-10-27
## Make use of the `reuseIdentifier`
For the followings:
- `UITableViewCell`s
- `UICollectionViewCell`s
- `UITableViewHeaderFooterView`s

If not reuse, it will create one each time a cell is displayed which impacts scrolling performance. 

Remember to reuse 
- headers and footers
- supplementary views