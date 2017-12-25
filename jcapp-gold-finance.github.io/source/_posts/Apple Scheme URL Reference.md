title: About Apple URL Schemes 翻译一篇

type: iOS
---

> Interpreter：戴奕




Apple 官方文档：《Apple Scheme URL Reference》翻译一篇。文虽短但里面还是有些有意思的东西。

<!-- more -->

This document describes several URL schemes that are supported by system apps on iOS, macOS, and watchOS 2 and later. Native iOS apps and web apps running in Safari on any platform can use these schemes to integrate with system apps and provide a more seamless experience for the user. For example, if your iOS app displays telephone numbers, you could use an appropriate URL to launch the Phone app whenever someone taps one of those numbers. Similarly, clicking an iTunes link launches the iTunes app and plays the song specified in the link. When a user clicks a link, what happens depends on the platform and the installed system apps.

***

本文档介绍了在 iOS、macOS 和 watchOS2 及以上版本的 watchOS 中所支持的几种系统 URL scheme。在任何平台上运行的 iOS 原生 app 或者 Safari 中的 web app 都可以使用这些 scheme 来提供一种无缝连接的用户体验。打个比方，如果你的 iOS app 显示了一串电话号码，你可以在用户点击电话号码的时候调用适当的URL来启动“电话”这个系统 app 来打电话。同样的，在点击一个 iTunes 链接的时候启动 iTunes app 并播放链接中指定的歌曲。当一个用户点击链接时，具体发生什么事情取决于当前操作系统和系统app。



This document describes those schemes that require special attributes or special formatting in order to be understood by the associated system app. As a result, this document does not describe all URL schemes supported on different Apple platforms.

***

这个文档的目的是帮助读者理解那些必须使用特殊格式才能和系统 app 关联的 scheme。所以，这个文档并没有把所有 Apple 平台上支持的 URL scheme 都列举出来。



## At a Glance

You should read this document if you want to launch a system app from your iOS or macOS app, or from your web app running in Safari. This document contains both Cocoa Touch sample code—using the `openURL:options:completionHandler:` method of the shared `UIApplication` object to open URLs—and HTML samples.

***

如果你想在 iOS app、macOS app 以及 Safari 的 web app 中启动系统 app，那么应该先读该文档。这个文档包含了 Cocoa Touch 中的例子——使用 `UIApplication` 单例中的 `openURL:options:completionHandler:` 方法，以及HTML中的例子。



### Composing Items Using Mail

Use the `mailto` scheme to open the Mail app and populate a new email with information.

***

使用 `mailto` scheme 打开邮件 app 并使用附带的信息生成新的邮件。



#### Mail Links

The `mailto` scheme is used to launch the Mail app and open the email compose sheet. When specifying a `mailto` URL, you must provide the target email address. The following examples show strings formatted for Safari and for native apps.

***

`mailto` scheme 用来启动 邮件 app 并打开邮件编写页面。当指定一个 `mailto` URL的时候，你必须提供目标邮件的地址。以下的例子里列举了 Safari 和 原生 app 中的 scheme 格式：



- HTML link:

`<a href="mailto:frank@wwdcdemo.example.com">John Frank</a>`

- Native app URL string:

`mailto:frank@wwdcdemo.example.com`



You can also include a subject field, a message, and multiple recipients in the To, Cc, and Bcc fields. (In iOS, the `from` attribute is ignored.) The following example shows a `mailto`URL that includes several different attributes:

***

你还可以在scheme中包含主题、邮件内容、多个收件人、多个抄送以及密送人。以下例子中展示了一个包含多种不同属性的 `mailto` scheme：



```
mailto:foo@example.com?cc=bar@example.com&subject=Greetings%20from%20Cupertino!&body=Wish%20you%20were%20here!
```



For detailed information on the format of the `mailto` scheme, see [RFC 2368](http://www.ietf.org/rfc/rfc2368.txt).

***

有关 `mailto` scheme 的详细格式方案信息，请参阅 [RFC 2368](http://www.ietf.org/rfc/rfc2368.txt)。



> **iOS Note:** If the Mail app is not installed, clicking a `mailto` URL displays an appropriate warning message to the user.

***

> iOS 提醒：如果 邮件 app 没有安装，那么在点击 `mailto` URL 的时候会显示一个合适的警告给用户。





### Starting a Phone or FaceTime Conversation

Use the `tel` and `facetime` schemes to initiate telephone or video conversations.

***

使用 `tel` 和 `facetime` scheme 开始通话或发起一个视频会话。



#### Phone Links

The `tel` URL scheme is used to launch the Phone app on iOS devices and initiate dialing of the specified phone number. When a user taps a telephone link in a webpage, iOS displays an alert asking if the user really wants to dial the phone number and initiates dialing if the user accepts. When a user opens a URL with the `tel` scheme in a native app, iOS 10.3 and later displays an alert and requires user confirmation before dialing. (When this scenario occurs in versions of iOS prior to 10.3, iOS initiates dialing without further prompting the user and does not display an alert, although a native app can be configured to display its own alert.)

***

`tel` URL scheme 用来启动 iOS 上的 电话 app 并向指定的手机号码拨号。当用户点击一个网页上的手机号链接时，iOS系统会弹出一个对话框询问用户是否要拨打当前这个号码，如果用户接受的话，就会向这个手机号拨号。当一个用户在 原生 app 上打开一个 `tel` scheme 时，如果是 iOS10.3 以及更高的系统版本的话，在拨打之前也会弹出一个确认框。（当这种情况发生在10.3以下的系统中时，iOS 不会提示用户也不会显示一个对话框，而会直接启动 电话 app 并拨打，除非 app 中自行弹出一个对话框。）



FaceTime in macOS 10.10 and later can also use the `tel` URL scheme to launch the Phone app on an iOS device by using Handoff. This scenario works when FaceTime is configured to dial phone numbers (the default configuration) and the iOS device is connected to the same iCloud account as the Mac.

***

FaceTime 在 macOS 10.10 以及之后的系统中也可以使用 `tel` URL scheme 然后通过 Handoff 启动 iOS 设备上的 电话 app。这种情况只有在 FaceTime 配置为拨打电话号码（默认配置）并且 iOS 设备和 Mac 的 iCloud 账号为同一个账号时才会发生。（否则在 macOS 上使用 `tel` scheme 会直接打开 FaceTime app）。



You can specify phone links explicitly in both web and native iOS apps using the `tel` URL scheme. The following examples show the strings formatted for Safari and for a native app:

***

在 web 和 原生 iOS app 上你都可以使用 `tel` URL scheme。以下的例子展示了该 scheme 的规则：



* HTML link:


`<a href="tel:1-408-555-5555">1-408-555-5555</a>`

* Native app URL string:

`tel:1-408-555-5555`



To prevent users from maliciously redirecting phone calls or changing the behavior of a phone or account, the Phone app supports most, but not all, of the special characters in the `tel` scheme. Specifically, if a URL contains the `*` or `#` characters, the Phone app does not attempt to dial the corresponding phone number. If your app receives URL strings from the user or an unknown source, you should also make sure that any special characters that might not be appropriate in a URL are escaped properly. For native apps, use the `stringByAddingPercentEscapesUsingEncoding:` method of `NSString` to escape characters, which returns a properly escaped version of your original string.

***

为了避免用户拨打恶意重定向电话或者更改电话、账号的行为，电话 app 支持 `tel` scheme 中大部分但并非全部的特殊字符。具体来说，如果 URL 中包含 `*` 或者 `#` 字符，电话 app 不会尝试拨打相应的手机号码。如果你的 app 收到用户或者未知来源的 URL 字符串，你应该确保 URL 中的特殊字符能够被正确转义。在原生 app 中，使用`NSString`类中的 `stringByAddingPercentEscapesUsingEncoding:` 方法来转义，该方法会返回转义后的字符串。



In Safari on iOS, telephone number detection is on by default. However, if your webpage contains numbers that can be interpreted as phone numbers, but are not phone numbers, you can turn off telephone number detection. You might also turn off telephone number detection to prevent the DOM document from being modified when parsed by the browser. To turn off telephone number detection in Safari on iOS, use the `format-detection` meta tag as follows:

***

在 iOS 的 Safari 浏览器上，电话号码检测是默认开启的。但是如果你的网页上包含一些被误认为电话号码的数字，你可以关闭电话号码检测。为了防止浏览器解析DOM时篡改电话号码，你也可以关闭电话号码检测功能。使用 `format-detection` 标签来关闭 iOS 中的 Safari 电话检测功能，如下所示：



`<meta name = "format-detection" content = "telephone=no">`



Listing 2-1 shows a simple webpage in which automatic telephone number detection is off. When displayed in Safari on iOS, the `408-555-5555` telephone number does not appear as a link. However, the `1-408-555-5555` number does appear as a link because it is in a phone link.

***

2-1 清单展示了一个关闭电话检测的简单网页。当网页展示在 iOS 的 Safari 中时， `408-555-5555` 并不会展示成链接的样子。然而，`1-408-555-5555` 会被显示成一个链接因为它确实是一个超链接（标签）。



**Listing 2-1**  Turning telephone number detection off

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"
        "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en" lang="en" >
<head>
    <meta http-equiv="content-type" content="text/html; charset=utf-8">
    <title>Telephone Number Detection</title>
    <meta name = "viewport" content = "width=device-width">
     <!-- Turn off telephone number detection. -->
    <meta name = "format-detection" content = "telephone=no">
</head>
<body>
     <!-- Then use phone links to explicitly create a link. -->
     <p>A phone number: <a href="tel:1-408-555-5555">1-408-555-5555</a></p>
     <!-- Otherwise, numbers that look like phone numbers are not links. -->
     <p>Not a phone number: 408-555-5555</p>
</body>
</html>
```





> **iOS Note:** If the Phone app is not installed on the iOS device, opening a `tel` URL displays an appropriate warning message to the user.

------

> iOS 提醒：如果 电话 app 没有安装，那么在打开 `tel` URL 的时候会显示一个合适的警告给用户。



For more information about the `tel` URL scheme, see [RFC 2806](http://www.ietf.org/rfc/rfc2806.txt) and [RFC 2396](http://www.ietf.org/rfc/rfc2396.txt).

***

有关 `tel` URL scheme 的详细信息，请参阅 [RFC 2806](http://www.ietf.org/rfc/rfc2806.txt) 和 [RFC 2396](http://www.ietf.org/rfc/rfc2396.txt)。



#### FaceTime Links

The `facetime` URL scheme is used to initiate a FaceTime call to a specified user. You can use the phone number or email address of a user to initiate the call. When a user taps a FaceTime link in a webpage, iOS confirms that the user really wants to initiate a FaceTime call before proceeding. When an app opens a URL with the `facetime` scheme, iOS opens the FaceTime app and initiates the call without prompting the user. When opening FaceTime URLs on macOS, the system always prompts the user before initiating a call.

***

`faceTime` URL scheme 用来给指定用户发起 FaceTime 通话。你可以用户的使用电话号码或者邮件地址来发起。当用户点击一个网页上的 FaceTime 链接时， iOS 系统会在继续之前让用户进行确认。当一个 app 打开 `facetime` URL时， iOS 不会提示用户而是直接打开 FaceTime app。（实测iOS10以下不会提醒，iOS10及以上会弹出确认框）在 macOS 上打开 FaceTime URL 的时候，系统总是会在打开之前提醒用户。）



You can specify FaceTime links explicitly in both web and native iOS apps using the `facetime` URL scheme. The following examples show the strings formatted for Safari and for a native app:

***

在 web 和 原生 iOS app 上你都可以使用 `facetime` URL scheme 来制定一个 FaceTime 链接。以下的例子展示了该格式的字符串：



* HTML links for FaceTime video calls:

```html
<a href="facetime:14085551234">Connect using FaceTime</a>
<a href="facetime:user@example.com">Connect using FaceTime</a>
```

* HTML links for FaceTime audio calls (iOS only):

```html
<a href="facetime-audio:14085551234">Connect using FaceTime</a>
<a href="facetime-audio:user@example.com">Connect using FaceTime</a>
```

* Native app URL strings for FaceTime video calls:

```objective-c
facetime://14085551234
facetime://user@example.com
```

* Native app URL strings for FaceTime audio calls (iOS only):

```objective-c
facetime-audio://14085551234
facetime-audio://user@example.com
```



To prevent users from maliciously redirecting calls or changing the behavior of a phone or account, the FaceTime app supports most, but not all, of the special characters in the `facetime` schemes. Specifically, if a URL contains the `*` or `#` characters, the app ignores those characters when they are included after the phone number. If your app receives URL strings from the user or from an unknown source, use the `stringByAddingPercentEscapesUsingEncoding:` method of `NSString` to generate a properly escaped version of the original string before opening the URL.

***

为了避免用户拨打恶意重定向电话或者更改电话、账号的行为，FaceTime app 支持 `facetime` scheme 中大部分但并非全部的特殊字符。具体来说，如果 URL 中包含 `*` 或者 `#` 字符，并且当这些字符都在电话号码后面时，FaceTime app会忽略他们 。如果你的 app 收到用户或者未知来源的 URL 字符串，你可以使用`NSString`类中的 `stringByAddingPercentEscapesUsingEncoding:` 方法来转义后在进行打开。





> **iOS Note:** If the FaceTime app is not installed on the iOS device or Mac, opening a `facetime` URL displays an appropriate warning message to the user. Prior to iOS 7, the Phone app handled FaceTime calls.

------

> iOS 提醒：如果在 iOS 或者 Mac 设备上 FaceTime app 没有安装，那么在打开 `facetime` URL 的时候会显示一个合适的警告给用户。在 iOS7 之前则会由 电话 app 来处理这个 scheme。





### Specifying Text Messages

Use the `sms` scheme to compose a text message and specify a recipient.

***

使用 `sms` scheme 来编写短信并指定接收人。



#### SMS Links



> **iOS Note:** SMS text links are supported on iOS only.

------

> iOS 提醒：短信链接只支持 iOS 设备。



The `sms` scheme is used to launch the Messages app. The format for URLs of this type is “`sms:`*<phone>*”, where *<phone>* is an optional parameter that specifies the target phone number of the SMS message. This parameter can contain the digits 0 through 9 and the plus (`+`), hyphen (`-`), and period (`.`) characters. The URL string must not include any message text or other information.

***

`sms` scheme 用来启动 短信 app。这个 URL 的格式是  “`sms:`*<手机号>*”，*<手机号>*是一个可选参数，这是用来指定短信发送到谁的手机号上。这个参数里可以包含数字0~9，`+`，`-`，`.`。这个 URL 字符串中不能包含包括短信文本在内的其他信息。



The following examples show strings formatted for Safari and for native apps.

* HTML links:

```html
<a href="sms:">Launch Messages App</a>
<a href="sms:1-408-555-1212">New SMS Message</a>
```

* Native app URL strings:

```objective-c
sms:
sms:1-408-555-1212
```





### Opening Locations in Maps

Use specially formatted URLs to open the Maps app and display directions or locations.

***

用特定格式的 URL 来打开 地图 app 并显示指定方向或者坐标。



#### Map Links

The maps URL scheme is used to show geographical locations and to generate driving directions between two points. If your app includes address or location information, you can use map links to open that information in the Maps app in iOS or macOS.

***

地图 URL scheme 用来显示地理位置以及两个地点之间的导航。如果你的 app 包含地址或者位置信息，你可以使用 map 链接在 iOS、macOS 地图 app 上打开该地址或位置。



Unlike some schemes, map URLs do not start with a “maps” scheme identifier. Instead, map links are specified as regular `http` links and are opened either in Safari or the Maps app on the target platform.

***

map URL 不像其他一些 scheme 使用 "maps" 开头。取而代之的是使用常规的 `http` 链接，并在 Safari 或者其他平台的 地图 app 中打开。



Table 5-1 lists the supported parameters along with a brief description of each.

***

表格 5-1 列举了每个支持的参数以及其简要说明。



**Table 5-1**  Supported Apple Maps parameters

| Parameter | Meaning                                  | Values                                   |
| --------- | ---------------------------------------- | ---------------------------------------- |
| `t`       | The map type. If you don’t specify one of the documented values, the current map type is used. | `m` (standard view)`k` (satellite view)`h` (hybrid view)`r` (transit view) |
| `q`       | The query. This parameter is treated as if its value had been typed into the Maps search field by the user. Note that `q=*`is not supportedThe `q` parameter can also be used as a label if the location is explicitly defined in the `ll` or `address` parameters. | A URL-encoded string that describes the search object, such as “pizza,” or an address to be geocoded |
| `address` | The address. Using the address parameter simply displays the specified location, it does not perform a search for the location. | An address string that geolocation can understand. |
| `near`    | A hint used during search. If the `sll` parameter is missing or its value is incomplete, the value of `near` is used instead. | A comma-separated pair of floating point values that represent latitude and longitude (in that order). |
| `ll`      | The location around which the map should be centered.The `ll` parameter can also represent a pin location when you use the `q` parameter to specify a name. | A comma-separated pair of floating point values that represent latitude and longitude (in that order). |
| `z`       | The zoom level. You can use the `z` parameter only when you also use the `sll` parameter; in particular, you can’t use `z` in combination with the `spn` or `sspn` parameters. | A floating point value between `2` and `21`that defines the area around the center point that should be displayed. |
| `spn`     | The area around the center point, or *span*. The center point is specified by the `ll` parameter.You can’t use the `spn` parameter in combination with the `z` parameter. | A comma-separated pair of floating point values that represent latitude and longitude (in that order). |
| `saddr`   | The source address to be used as the starting point for directions.A complete directions request includes the `saddr`, `daddr`, and `dirflg` parameters, but only the `daddr` parameter is required. If you don’t specify a value for `saddr`, the starting point is “here.” | An address string that geolocation can understand. |
| `daddr`   | The destination address to be used as the destination point for directions.A complete directions request includes the `saddr`, `daddr`, and `dirflg` parameters, but only the `daddr` parameter is required. | An address string that geolocation can understand. |
| `dirflg`  | The transport type.A complete directions request includes the `saddr`, `daddr`, and `dirflg` parameters, but only the `daddr` parameter is required. If you don’t specify one of the documented transport type values, the `dirflg` parameter is ignored; if you don’t specify any value, Maps uses the user’s preferred transport type or the previous setting. | `d` (by car)`w` (by foot)`r` (by public transit) |
| `sll`     | The search location. You can specify the `sll` parameter by itself or in combination with the `q` parameter. For example, `http://maps.apple.com/?sll=50.894967,4.341626&z=10&t=s` is a valid query. | A comma-separated pair of floating point values that represent latitude and longitude (in that order). |
| `sspn`    | The screen span. Use the `sspn` parameter to specify a span around the search location specified by the `sll` parameter. | A comma-separated pair of floating point values that represent latitude and longitude (in that order). |

***

| Parameter | Meaning                                  | Values                                   |
| --------- | ---------------------------------------- | ---------------------------------------- |
| `t`       | 地图类型。如果你不指定文档中的任何类型，那么会使用地图当前的类型。        | `m` (标准视图)`k` (卫星视图)`h` (混合视图)`r` (交通视图) |
| `q`       | 查询参数。这个参数会被当成搜索值填入到地图的搜索框中。注意  `q=*` 是不支持的。如果位置由 `ll` 或者 `address` 参数明确地指定，那么 `q` 参数会被当做标签来对待。 | 经过URL编码的字符串，用来描述搜索对象，比如“披萨”，或者需要被地理编码的地点。 |
| `address` | 地址。使用地址参数只会简单显示地址的位置，并不会执行搜索该地址的操作。      | 一个可以识别的地址字符串。                            |
| `near`    | 搜索中的提示. 如果没有 `sll` 参数或者该参数的值不完整，那么将会使用 `near` 的值代替。 | 用逗号分隔的浮点数分别表示纬度和经度。                      |
| `ll`      | 表示地图中心点的坐标。当你使用 `q` 参数指定一个名字时，`ll` 参数会作为一个大头针放置在地图上进行展示。 | 用逗号分隔的浮点数分别表示纬度和经度。                      |
| `z`       | 缩放等级。`z` 参数只有在使用了 `sll` 参数后生效；一个比较特殊的地方是 `z` 不能和 `spn` 或者 `sspn` 参数共同使用。 | 用来显示地图中心点附近显示多大区域的值，必须介于 `2` 和 `21` 之间。  |
| `spn`     | 地图中心点附件的区域大小，或者说是*跨度*。中心点必须由 `ll` 参数进行指定。你不能同时使用 `z` 和 `span` 两个参数。 | 用逗号分隔的浮点数分别表示纬度和经度。                      |
| `saddr`   | 一个指定线路的起点位置。一个完整的线路包含 `saddr`，`daddr`，`dirflg` 3个参数构成，但是只有 `daddr` 参数是必须的。如果你不指定 `saddr` 参数，那么起始点就是当前位置。 | 一个可以识别的地址字符串。                            |
| `daddr`   | 一个指定线路的目标位置。一个完整的线路包含 `saddr`，`daddr`，`dirflg` 3个参数构成，但是只有 `daddr` 参数是必须的。 | 一个可以识别的地址字符串。                            |
| `dirflg`  | 交通方式。一个完整的线路包含 `saddr`，`daddr`，`dirflg` 3个参数构成，但是只有 `daddr` 参数是必须的。如果该参数是除了文档内指定的类型以外，该参数将会被忽略；如果你不指定该参数，那么交通方式将会使用用户之前指定的方式。 | `d`（驾车）`w`（步行）`r` (公交)                   |
| `sll`     | 搜索的位置。你可以自己指定 `sll` 参数，并且可以与 `q` 参数组合使用。例如， `http://maps.apple.com/?sll=50.894967,4.341626&z=10&t=s` 是一个合法查询URL。 | 用逗号分隔的浮点数分别表示纬度和经度。                      |
| `sspn`    | 一个屏幕上的经纬度跨度。当使用 `sll` 参数指定搜索的位置时，你可以使用 `sspn` 参数指定经纬度跨度。 | 用逗号分隔的浮点数分别表示纬度和经度。                      |



You can use the maps URL scheme to help the user:

- Perform a search
- Get directions to a location
- View a specific location

***

你可以使用地图 URL scheme 帮助用户完成下面这些操作：

* 搜索一个地点
* 给出前往指定地点的线路
* 查看一个指定的位置





To perform a search, supply a properly encoded URL string as the value of the `q` parameter. For example:

***

使用一个经过编码的正确的URL字符串作为 `q` 参数的值来执行搜索。举个例子：

`http://maps.apple.com/?q=Mexican+Restaurant`



To specify a location for search, you can supply a value for the `near` parameter, or combine the `sll` parameter with either the `z` or `sspn` parameters. You can also set the map type using the `t` parameter, as shown here:

***

在搜索指定位置时，你可以使用 `near` 参数，或者结合 `z` 或者 `sspn` 和 `sll` 参数共同使用。当然你也可以使用 `t` 参数来指定地图类型，比如这样：

`http://maps.apple.com/?q=Mexican+Restaurant&sll=50.894967,4.341626&z=10&t=s`



To provide navigation directions from one location to another, supply the start and destination addresses in the `saddr` and `daddr` parameters as shown below. You can also supply much more detail for the start and destination addresses than is shown here.

***

使用 `saddr` 和 `daddr` 参数来指定起始以及目的地位置，从而提供导航功能。你还可以提供比如下示例中更多的详细参数。

`http://maps.apple.com/?saddr=Cupertino&daddr=San+Francisco`



To specify a transport type, you can use the `dirflg` parameter as shown here:

***

如下所示，你可以使用 `dirflg` 参数指定交通方式：

`http://maps.apple.com/?saddr=San+Jose&daddr=San+Francisco&dirflg=r`



You can omit the start address when you want to provide directions “from here.” The following example shows a search from here that provides driving directions in a hybrid map.

***

当你想从当前位置开始查询线路时，你可以忽略起始位置参数。下面这个例子展示了在混合地图中使用驾车到达指定地点的线路。

`http://maps.apple.com/?daddr=San+Francisco&dirflg=d&t=h`



To display a specific location, use the `ll` parameter to center the map at the specified position as shown here:

***

要显示特定的位置，那么使用 `ll` 参数来指定地图中心点的坐标，如下所示：

`http://maps.apple.com/?ll=50.894967,4.341626`



Another way to display a location is to specify an address, such as:

***

另一种显示特定位置的方式是使用指定的地址，如下所示：

`http://maps.apple.com/?address=1,Infinite+Loop,Cupertino,California`



If you use both the `ll` and `address` parameters, `ll` takes precedence over `address`. If you include a name in the value of the `q` parameter, Maps tries to match the name at the specified location.

***

如果你同时使用了 `ll` 和 `addres` 参数，那么会优先使用 `ll` 参数。如果你的参数中包含了 `q` 参数，那么 地图 app 会尝试在指定位置处匹配这个名称。





### Opening Items in iTunes

Use specially formatted URLs to open iTunes and display items in the iTunes Music Store.

***

用特定格式的 URL 来打开 iTunes app 并显示 iTunes 音乐商店中的指定项目。



#### iTunes Links

The iTunes URL scheme is used to link to content on the iTunes Music Store. The iTunes URL format is complicated to construct, so you create it using an online tool called iTunes Link Maker. The tool allows you to select a country destination and media type, and then search by song, album, or artist. After you select the item you want to link to, it generates the corresponding URL.

***

iTunes URL scheme 用来链接到 iTunes 音乐商店。由于 iTunes URL 的格式比较复杂，所以你可以使用 iTunes Link Maker 这个在线工具来创建。通过该工具，你可以对国家、媒体类型进行筛选并搜索歌曲、专辑或艺术家。当你选择了项目后会生成相应的 URL 。



The following examples show the strings you would use in Safari and in a native iOS app to link to a song on the iTunes Music Store. The HTML example includes the complete link returned by the iTunes Link Maker tool, which includes a link to any appropriate artwork for the target link.

***

下面这个例子展示了一个在 Safari 和 原生 iOS app 中可以使用的 URL 字符串。 HTML 例子中就包含了一个使用  iTunes Link Maker 工具生产的指向合适图片的链接。



* HTML link;

```html
<a href="http://phobos.apple.com/WebObjects/MZStore.woa/wa/viewAlbum?i=156093464&id=156093462&s=143441">
<img height="15" width="61" alt="Randy Newman - Toy Story
- You&#39;ve Got a Friend In Me" src="http://ax.phobos.apple.com.edgesuite.net/images/
badgeitunes61x15dark.gif"></img>
</a>
```

* Native app URL string:


`http://phobos.apple.com/WebObjects/MZStore.woa/wa/viewAlbum?i=156093464&id=156093462&s=143441`



For more information on creating iTunes links, see [iTunes Link Maker FAQ](http://www.apple.com/itunes/linkmaker/faq/). That webpage contains a link to the iTunes Link Maker tool.

***

有关 `iTunes` 链接 的详细格式方案信息，请参阅  [iTunes Link Maker FAQ](http://www.apple.com/itunes/linkmaker/faq/)。该网站中包含了 iTunes Link Maker 工具的链接。





### Opening YouTube Videos

Use specially formatted URLs to open YouTube videos in Safari.

***

在 Safari 中使用指定格式的 URL 打开 YouTube 视频。



#### YouTube Links

The YouTube URL scheme is used to connect to the YouTube website to play the specified video. If your app links to YouTube content, you can use this scheme to play videos from your app.

***

YouTube URL scheme 用来打开 YouTube 网站并播放指定的视频。如果你的 app 有链接到 YouTube 的内容，那么你可以从 app 中跳转并播放视频。



Unlike some schemes, YouTube URLs do not start with a “youtube” scheme identifier. Instead, they are specified as regular `http` links but are targeted at the YouTube server. The following examples show the basic strings you would use in Safari and in an app to show a YouTube video. In each example, you would need to replace the `VIDEO_IDENTIFIER` value with the identifier of the video you wanted to display:

***

YouTube URL 并不像其他 scheme 一样以 "youtube" 开头。而是使用指向 YouTube 服务的常规 `http` 链接。以下例子中展示了在 Safari 和 app 中用来展示 YouTube 视频的基本字符串。在每个例子中你需要将 `VIDEO_IDENTIFIER` 替换成你想要播放的视频 id ：



* HTML links:

```html
<a href="http://www.youtube.com/watch?v=VIDEO_IDENTIFIER">Play Video</a>
<a href="http://www.youtube.com/v/VIDEO_IDENTIFIER">Play Video</a>
```

* Native app URL strings:

```objective-c
http://www.youtube.com/watch?v=VIDEO_IDENTIFIER
http://www.youtube.com/v/VIDEO_IDENTIFIER
```





> **iOS Note:** If the YouTube video cannot be viewed on the device, iOS displays an appropriate warning message to the user.

------

> iOS 提醒：如果在该 YouTube 视频无法再设备上观看，那么 iOS 系统会显示一个合适的警告给用户。

