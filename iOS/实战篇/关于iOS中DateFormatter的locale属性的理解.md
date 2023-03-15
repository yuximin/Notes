# 关于iOS中DateFormatter的locale属性的理解

DateFormatter的locale属性只会影响到时间的显示样式，不会对时间值本身造成影响。而真正决定最后展示的时间值的，是手机设置里面的时区。

设置路径为：`Settings-General-Date&Time-TimeZone`

即对于时间戳 `1678860178`，如果手机时区设置为 `Beijing`，则不论 DateFormatter 的 locale 设置为什么，最后得到的时间都是当前时间戳对应的北京时间，只是展示的时间格式不同而已。

下述代码运行的手机时区为 `New York`。

```swift
private func dateFormatterStringForDate(_ date: Date, localeIdentifier: String) {
    let dateFormatter = DateFormatter()
    dateFormatter.dateFormat = "EE,dd/MM,HH:mm:ss"
    let dateLocal = Locale(identifier: localeIdentifier)
    dateFormatter.locale = dateLocal
    print(localeIdentifier, dateLocal, dateFormatter.string(from: date))
}

let date = Date(timeIntervalSince1970: 1678860178)
dateFormatterStringForDate(date, localeIdentifier: "en")
dateFormatterStringForDate(date, localeIdentifier: "zh")
dateFormatterStringForDate(date, localeIdentifier: "ja")
dateFormatterStringForDate(date, localeIdentifier: "tr")
```

运行上述代码，输出日志如下：

```
en en (fixed) Wed,15/03,02:02:58
zh zh (fixed) 周三,15/03,02:02:58
ja ja (fixed) 水,15/03,02:02:58
tr tr (fixed) Çar,15/03,02:02:58
```