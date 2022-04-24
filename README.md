# AndroLua-Docs

## 快速入门

FusionApp 是使用 Lua 语法编写并可以使用 Android API 的轻型脚本编程工具，可以用于快速编写 Android 应用。
项目默认创建了 `main.lua`，并自带了以下代码

```lua
require "import"
import "android.widget.*"
import "android.view.*"
```

`require "import"` 是导入 `import` 模块，该模块集成了很多导入工具函数，可以大幅度减轻写代码负担。

`import "android.widget.*"` 是导入 Java 包。
这里导入了 Android 的 `widget` 和 `view` 两个包。

导入包后使用类是很容易的，新建类实例的方式和调用Lua的函数一样。
比如新建一个 `TextView`：
`text = TextView(activity)`,
其中的 `activity` 表示当前活动的 `Context`

同理可新建按钮： `btn = MaterialButton(activity)`

给视图设置属性也非常简单，如下：
`btn.text = "按钮" --按钮文字设为了 “按钮” `

添加视图点击事件：
```lua
btn.onClick = function(view)
  print(view)
end
```
函数参数 `view` 指的是视图本身。

安卓的视图需要添加到布局才能显示，一般常用的是 `LinearLayout`：
`layout = LinearLayout(activity)`

之后用 `addView` 方法添加之前创建的按钮视图：
`layout.addView(btn)`

最后调用 `activity` 的 `setContentView` 方法显示内容：
`activity.setContentView(layout)`



## 导入模块

`require "import"` 是导入 `import` 模块，可以简化写代码的难度。

目前程序还内置 `canvas` `cjson` `md5` `socket` `xml` 等模块。

一般模块导入形式：
`local http = require "http"`
这样导入的模块是局部变量

而导入 `import` 模块后也可以使用
`import "xml"`
的形式，将其导入为全局变量



## 导入 Java 类

在使用 Java 类之前需要导入相应的包或者类

可以用 `包名.*` 的形式导入整个包：
`import "android.widget.*"`

或者也可以用完整的类名导入类：
`import "android.widget.Button"`

导入内部类：
`import "android.view.View_OnClickListener"`

而在导入类后可以直接使用内部类
`View.OnClickListener`

包名和类名都必须用引号包围！

导入的类为全局变量，你也可以使用：
`local View = import "android.view.View"`
的形式将其保存为局部变量，以解决类名冲突问题。



## 创建布局与组件

Android 使用布局与视图管理和显示界面。
布局负责管理视图如何显示，如 `LinearLayout` 可以线性排列视图，`FrameLayout` 则要求自行位置。
视图则显示具体内容，如 `TextView` 可以向用户展示文字内容，`MaterialButton`（按钮）可以响应用户点击事件。

创建一个线性布局：
`layout = LinearLayout(activity)`

创建一个按钮视图：
`button = MaterialButton(activity)`

将按钮添加到布局：
`layout.addView(button)`

将刚才的内容设置为活动内容视图：
`activity.setContentView(layout)`

`activity` 指的是当前窗口的 `Context` 对象，如果你习惯也可以使用 `this` 来替代：
`button = Button(this)`



## 使用 Java 方法（Method）

`button.setText("按钮")` 中就使用了 `setText` 方法。

Java 的 `get***` 方法没有参数时，与 `set***` 方法只有一个参数时可以简写，如下：
```lua 
button.Text = "按钮" --这行代码等同于 button.setText("按钮")
t = button.Text      --这行代码等同于 t = button.getText()
```



## 使用事件监听

创建事件处理函数：
```lua
function click()
  print("点击")
end
```
把函数添加到事件接口：
`listener = OnClickListener{onClick = click}`

把接口注册到组件：
`button.setOnClickListener(listener)`

你也可以使用匿名函数：
```lua
button.setOnClickListener(OnClickListene{
  onClick = function()
    print("点击")
  end
})
```

`on***` 事件也可以简写为
```lua
button.onClick = function()
  print("点击")
end
```


## 使用布局表

使用布局表前须导入你在布局表内使用了的所有的类和 `loadlayout` 模块（FA 会自动帮你默认导入）。

布局表格式：
```lua
layout = {
  控件类名,
  id=控件ID,
  属性=值,
  {
    子控件类名,
    id=控件名称,
    属性=值,
  }
}
```

例如：
```lua
layout = {
  LinearLayout, --视图类名
  id="linear", --视图ID，可以在 loadlayout 后直接使用
  orientation="vertical", --属性与值
  {
    TextView, --子视图类名
    text="hello AndroLua+", --属性与值
    layout_width="fill", --布局属性
  },
}
```

使用 `loadlayout` 函数来解析布局表生成布局并设置为活动视图：
`activity.setContentView(loadlayout(layout))`

也可以简化为：
`activity.setContentView(layout)`

如果使用单独文件布局（比如有个 `layout.aly` 布局文件）也可以简写为：
`activity.setContentView("layout")`

布局表支持大部分安卓控件属性，
与安卓XML布局文件的不同点：

`id` 表示在 Lua 中变量的名称，而不是安卓的可以 `findById` 的数字 `id`；

`ImageView` 的 `src` 属性是当前目录图片名称或网络图片；

`layout_width` 与 `layout_height` 的值支持 `match` 与 `wrap` 简写；

`onClick` 值为 `Lua函数` 或 `Java onClick 接口` 或他们的全局变量名称；

`background` 支持背景图片，背景色与 `LuaDrawable` 自绘制背景，背景图片参数为是当前目录图片名称或网络图片，颜色同 `backgroundColor` 属性，自绘制背景参数为绘制函数或绘制函数的全局变量名称；

控件背景色使用 `backgroundColor` 设置，值为 “十六进制颜色值”（如 `0xffffffff`）；

尺寸单位支持 dp、sp、px、in、mm、%w、%h。
