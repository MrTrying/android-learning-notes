# Android中的复制&粘贴
最近开发过程中有使用到复制/粘帖功能，也在其中遇到了一些问题，就顺势学习一下复制粘贴相关的知识。
## 一、前言
Android提供了一个强大的剪切板框架（以至于在复制某些内容粘帖到我的项目中的时候出现的格式问题的BUG）用于复制和粘帖。同时支持简单和复杂的数据类型，简单的文本数据直接存储在剪贴板中，而复杂的数据存储为一个引用，即粘贴应用程序解析为内容提供者（这里涉及到ContentProvider）。
## 二、框架&使用
![框架图](https://raw.githubusercontent.com/MrTrying/android-learning-notes/master/_pic/copy_paste_framework.png)
> 要复制数据，应用程序将`ClipData`对象放在`ClipboardManager`全局剪贴板上。它`ClipData`包含一个或多个`ClipData.Item`对象和一个 `ClipDescription`对象。要粘贴数据，应用程序会从中`ClipData`获取其MIME类型`ClipDescription`，并从`ClipData.Item`或从内容提供者 获取数据`ClipData.Item`。


可以看出`Android`剪贴板框架主要涉及到`ClipboardManager`、`ClipData`、`ClipData.Item`、`ClipDescription`这四个类，下面详细说明
1. `ClipboardManager`是系统全局的剪贴板对象，通过`context.getSystemService(CLIPBOARD_SERVICE)`获取。
2. `ClipData`，即`clip`（剪切）对象，在系统剪贴板里只存在一个，当另一个`clip`对象进来时，前一个`clip`对象会消失。
3. `ClipData.Item`，即 data item，它包含了文本、`Uri`或者`Intent`数据，一个`clip`对象可以包含一个或多个`Item`对象。通过 `addItem(ClipData.Item item)`可以实现往`clip`对象中添加`Item`。
	 - 文本：文本是直接放在 `clip` 对象中，然后放在剪贴板里；粘贴这个字符串的时候直接从剪贴板拿到这个对象，把字符串放入你的应用存储中。
	 - Uri：对于复杂数据的剪贴拷贝并不是直接将数据放入内存，而是通过 `Uri` 来实现，毕竟 `Uri` 的中文名叫：统一资源标识符。通过 `Uri` 能定位手机上所有资源，这当然能实现拷贝了，只不过需要做一些额外的处理工作。(对于 `Uri` 不是很理解，如有误，望指正~)
	 - `Intent`：复制的时候 `Intent` 会被直接放入 `clip` 对象，这相当于拷贝了一个快捷方式。
4. `ClipDescription` ，即 `clip metadata`，它包含了 `ClipData` 对象的 `metadata` 信息。可以通过 `getMimeType(int index)` 获取（一般 `index = 0`，有兴趣的可以去看下 `ClipData` 的源码）。`MimeType` 一般有以下四种类型：

```
    // 对应 ClipData.newHtmlText(label, text, htmlText) 的 MimeType
    public static final String MIMETYPE_TEXT_HTML = "text/html";
    // 对应 ClipData.newIntent(label, intent) 的 MimeType
    public static final String MIMETYPE_TEXT_INTENT = "text/vnd.android.intent";
    // 对应 ClipData newPlainText(label, text) 的 MimeType
    public static final String MIMETYPE_TEXT_PLAIN = "text/plain";
    // 对应 ClipData newPlainText(label, text) 的 MimeType
    public static final String MIMETYPE_TEXT_URILIST = "text/uri-list";
```
接下来看看简单的使用，以文本操作为例
```
    public void putTextIntoClip(Context context){
        ClipboardManager clipboardManager = (ClipboardManager) context.getSystemService(Context.CLIPBOARD_SERVICE);
        //创建ClipData对象
        ClipData clipData = ClipData.newPlainText("simple text copy", "Clipboard test.");
        //添加ClipData对象到剪切板中
        clipboardManager.setPrimaryClip(clipData);
    }
```
创建`ClipData`的方法还有另外四个：
```
	//创建一个包含 htmlText 的 ClipData
	//一般在浏览器中对网页进行拷贝的时候会调用此方法,其中 htmlText 是包含 HTML 标签的字符串
	public static ClipData newHtmlText(CharSequence label, CharSequence text, String htmlText)
	//创建一个包含 Intent 的 ClipData
	public static  ClipData newIntent(CharSequence label, Intent intent)
	//创建一个包含 Uri 的 ClipData，MimeType 会根据 Uri 进行修改
	public static  ClipData newUri(ContentResolver resolver, CharSequence label, Uri uri)
	//与 newUri 相对应，但是并不会根据 Uri 修改 MimeType
	public static  ClipData newRawUri(CharSequence label, Uri uri)
```
从剪切板中获取数据，同样以文本操作为例
```
    public void getTextFromClip(Context context){
        ClipboardManager clipboardManager = (ClipboardManager) context.getSystemService(Context.CLIPBOARD_SERVICE);
        //判断剪切版时候有内容
        if(!clipboardManager.hasPrimaryClip())
            return;
        ClipData clipData = clipboardManager.getPrimaryClip();
        //获取 ClipDescription
        ClipDescription clipDescription = clipboardManager.getPrimaryClipDescription();
        //获取 lable
        String lable = clipDescription.getLabel().toString();
        //获取 text
        String text = clipData.getItemAt(0).getText().toString();
    }
```

> 顺带说一下之前遇到的问题，我boss直接从网易新闻复制了内容，粘帖到我们自己的app中，之后文本的样式都不对，这是因为复制的内容是包含HTML标签的字符串，导致内容显示有问题，`String text = clipData.getItemAt(0).coerceToText(context).toString();`最后使用`coerceToText()`将剪贴板数据强制转换为文本解决问题。

## 三、官方建议

**Designing Effective Copy/Paste Functionality**
To design effective copy and paste functionality for your application, remember these points:

 - At any time, there is only one clip on the clipboard. A new copy operation by any application in the system overwrites the previous clip. Since the user may navigate away from your application and do a copy before returning, you can't assume that the clipboard contains the clip that the user previously copied in your application.

 - The intended purpose of multiple ClipData.Item objects per clip is to support copying and pasting of multiple selections rather than different forms of reference to a single selection. You usually want all of the ClipData.Item objects in a clip to have the same form, that is, they should all be simple text, content URI, or Intent, but not a mixture.

 - When you provide data, you can offer different MIME representations. Add the MIME types you support to the ClipDescription, and then implement the MIME types in your content provider.

 - When you get data from the clipboard, your application is responsible for checking the available MIME types and then deciding which one, if any, to use. Even if there is a clip on the clipboard and the user requests a paste, your application is not required to do the paste. You should do the paste if the MIME type is compatible. You may choose to coerce the data on the clipboard to text using coerceToText() if you choose. If your application supports more than one of the available MIME types, you can allow the user to choose which one to use.

为设计有效的复制和粘贴功能，请记住以下几点：
 - 任何时候，剪切板只有一个`clip`。系统中任何一个app的复制操作都会覆盖上一次操作。用于用户可以到导航你的app，在返回之前复制一次，你不能假设剪切板中包含用户在你的app中复制剪切的数据。
 - `clip`的多个`ClipData.Item`对象是支持复制和粘贴多个选择，而不是对单个选择的不同形式的引用。你通常希望ClipData.Item剪辑中的所有 对象具有相同的形式，即它们应该是简单的文本，内容URI或者Intent混合。
 - 当你提供数据是，你可以提供不同的MIME属性，添加MIME类型到`ClipDescription`中，然后在内容提供者中事项MIME类型。
 - 当你获取剪切板的数据时，你的app检查MIME类型的可用性然后确定使用的MIME类型。即使用户请求粘帖剪切板上的数据，你的app也不是需要粘帖的。如果MIME类型兼容你应该粘帖。你也可以选择用`coerceToText()`方法强制将数据转化为文本。如果你的app支持多种MIME类型，你可能允许用户选择使用MIME类型

参考文章：
https://developer.android.com/guide/topics/text/copy-paste.html#Provider

PS：文章不是很全面，翻译的也不是很好，请见谅






