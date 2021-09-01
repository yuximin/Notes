# UITableView性能优化

关于 UITableView 的性能优化，可以从以下几点着手。

## 1. cell 复用

UITableView 创建之后，最多只能显示有限多个 cell 内容到当前屏幕。对于数据量较多的 UITableView，当我们滑动视图时，位于末端的数据才会得以展示，同时部分内容会超出屏幕而被隐藏。对于超出屏幕的 cell，我们可以将其缓存起来，在需要使用的时候，优先使用缓存中的cell，缓存中没有可用 cell 时，再选择新建。

UITableView 提供以下两种方法用于 cell 复用。

```objective-c
- (nullable __kindof UITableViewCell *)dequeueReusableCellWithIdentifier:(NSString *)identifier;

- (__kindof UITableViewCell *)dequeueReusableCellWithIdentifier:(NSString *)identifier forIndexPath:(NSIndexPath *)indexPath API_AVAILABLE(ios(6.0));
```

对于 `dequeueReusableCellWithIdentifier:` 方法，通过该方法可以获取当前缓存区内可复用的 cell。如果当前缓存区中不存在可复用的 cell，则需要手动新建一个 UITableViewCell 类型的对象。**注意，新建 cell 时，`initWithStyle:reuseIdentifier:` 方法中传入的 `CellIdentifier` 需与 `dequeueReusableCellWithIdentifier:` 中的 ID 保持一致。** 具体代码如下：

```objective-c
static NSString *CellIdentifier = @"xxx";
UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:CellIdentifier];
if (cell == nil) {
    cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:CellIdentifier];
}
```

对于 `dequeueReusableCellWithIdentifier:forIndexPath:` 方法，需要在创建 UITableView 的时候，事先注册指定类型的 UITableViewCell 。因为通过该方法获取复用的 Cell 时，如果获取到的复用 Cell 为 nil，则会新建一个对应于注册类型的 UITableViewCell。

## 2. 提前计算并缓存行高

UITableView 每次滚动时，都会调用下面两个代理方法，来获取 cell 的行高和内容。

```objc
/// 行高
- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath;

/// cell内容
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath;
```

对于 cell 行高是可变，则需要每次在 `tableView:heightForRowAtIndexPath:` 方法中计算出当前 cell 的高度。当我们快速来回滑动 UITableView 时，则会频繁调用重复计算，增加性能损耗。所以，针对这种情况，我们可以视线计算好高度并保存在 cell 对应的数据模型中，避免重复运算。

## 3. 使用不透明图片

不透明的视图可以极大地提高渲染的效率。因此，如非必要，可以将 tableViewCell 及其子视图的 opaque 属性设置为 YES（默认值）。

对于 cell 背景色，它的 alpha 值应该为1，即不要设置 clearColor。图像的 alpha 值也应该为1，或者在画图时设置为不透明。

## 4. 异步绘制

当我们大量添加控件时，对资源的开销比较大，我们可以选择利用底层Api手动绘制，并将绘制部分的代码放在异步线程中，提高效率。

## 5. image创建方式

对于重复使用的图片使用 `imageNamed:` 来创建。对于不需要重复使用的，使用 `imageWithContentsOfFile:` 来创建。