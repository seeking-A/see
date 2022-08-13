# Android 界面编程

## 布局管理器

布局管理器继承了```View```，可以作为普通```UI```使用。包括```LinearLayout```|```TableLayout```、```FrameLayout```、```ConstrainLayout```、```AbsoluteLayout```、```Relativelayout```、```GridLayout``` ，其中```AbsoluteLayout```很难兼容不同屏幕大小和分辨率问题，已经过时；```Relativelayout```、```GridLayout``` 在```Android9.0```标注为不推荐使用，推荐使用```ConstrainLayout```替代。

### LinearLayout

| xml属性                | 相关方法            | 说明                                                         |
| ---------------------- | ------------------- | ------------------------------------------------------------ |
| android:gravity        | setGravity(int)     | 设置布局内组件的对齐方式，包括top、bottom、left、right、center_vertical、center_horizontal、fill_horizoontal、fill、center、clip_vertical、clip_horizontal，同时支持组合方式，如left\|center_vertical |
| android:orientation    | setOrientation(int) | 设置布局内组件的排列方式，包括horizontal（水平）、vertical（垂直） |
| android:layout_gravity |                     | 指定该子元素在LinearLayout中的对其方式                       |
| android:layout_weight  |                     | 指定该子元素在LinearLayout中所占的权重                       |



### TableLayout

| xml属性                 | 相关方法                        | 说明                                                   |
| ----------------------- | ------------------------------- | ------------------------------------------------------ |
| android:collapseColumns | setColumnCollapsed(int,boolean) | 设置需要被隐藏列的序列号，多个序列号之间使用逗号分隔   |
| android:shrinkColumns   | setShrinAllColumns(boolean)     | 设置允许被收缩的列的列序号，多个列序号之间使用逗号分隔 |
| android:stretchColumns  | setStretchAllColumns(boolean)   | 设置允许被拉伸的列的列序号，多个列序号之间用逗号分隔   |



### FrameLayout

| xml属性                    | 相关方法                       | 说明                                   |
| -------------------------- | ------------------------------ | -------------------------------------- |
| android:foregroundGravity  | setForegroundGravity(int)      | 定义绘制前景图像的gravity属性          |
| android:measureAllChildren | setMeasureAllChildren(boolean) | 设置该布局计算大小时是否考虑所有子组件 |



### ConstrainLayout

| xml属性                              | 说明                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| android:layout_constraintXxx_toXxxOf | 设置组件与父容器对齐，包括```Start```、```End```、```Top```、```Button```等值，也可以是```parent```和其他组件```id``` |
| android:layout_marginXxx             | 设置该组件与参照组件中的间距，包括```Top```、```Bottom```等值 |
| android:layout_constraintXxx_bias    | 设置该组件在参照组件中的百分比，可以是```Horizontal```和```Vertical``` |



## TextView及其子类

### TextView

| xml属性             | 相关方法                                      | 说明                                                         |
| ------------------- | --------------------------------------------- | ------------------------------------------------------------ |
| android:autoText    | setKeyListener(keyListener)                   | 控制是否将```URL```、```E-mail```地址等链接自动转换为可单击的链接 |
| android:capitalize  | setKeyListener(keyListener)                   | 控制是否将用户输入文本转换为大小写字母                       |
| android:editable    |                                               | 设置该文本是否允许编辑                                       |
| android:gravity     | setGravity(int)                               | 设置文本框内文本的对齐方式                                   |
| android:hint        | setHint(int)                                  | 设置该文本框的默认提示内容                                   |
| android:intputType  | setRawInputType(int)                          | 指定该文本框类型                                             |
| android:maxLength   | setFilters(InputFilter)                       | 设置该文本框的最大字符                                       |
| android:passwd      | setTransformationMethod(TransformationMethod) | 设置该文本框为密码输入框                                     |
| android:phoneNumber | setKeyListener(KeyListener)                   | 设置该文本框只能输入电话号码                                 |
| android:singleLine  | setTransformationMethod(TransformationMethod) | 设置该文本框为单行模式                                       |
| android:text        | setText(CharSequence)                         | 设置文本框内的内容                                           |



### EditText

```EditText```与```TextView```公用绝大部分```XML```属性和方法，区别在于```EditText```与```TextView```最大的区别在于：```EditText```可以接收用户输入。

```EditText```组件还派生出了两个子类：

1. ```AutoCompleteTextView```：带有自动完成功能的```EditText```
2. ```ExtraEditText```：不是```UI```组件而是```EditText```的底层服务类，负责提供全屏输入法支持。



### Button

| xml属性            | 方法                              | 说明            |
| ------------------ | --------------------------------- | --------------- |
| android:onclick    | setClickListener(OnClickListener) | 添加onclick事件 |
| android:background | setBackground(Drawable)           | 设置按钮背景    |



### RadioButton和CheckBox

```RadioButton```、```CheckBox```、```ToggleButton```、```Switch```都继承了```Button```，因此都可以使用```Button```支持的属性和方法。```RadioButton```和```CheckBox```都可额外指定一个```android:checked```属性，用于指定组件是否被选中。不同之处在于，一组```RadioButton```只能选中其中一个，因此```RadioButton```通常需要与```RadioGroup```一起使用，用于定义一组单选按钮。



### ToggleButton和Switch

**ToggleButton**

| xml属性               | 说明                         |
| --------------------- | ---------------------------- |
| android:disabledAlpha | 设置禁用指示器的透明度       |
| android:textOff       | 设置该按钮关闭状态的显示文本 |
| android:textOn        | 设置该按钮打开状态的显示文本 |

**Switch**

| xml属性                               | 说明                                                         |
| ------------------------------------- | ------------------------------------------------------------ |
| android:layout_showText(boolean)      | 设置是否绘制开关文本                                         |
| android:layout_setSwitchMinWidth(int) | 设置该组件与参照组件中的间距，包括```Top```、```Button```等值 |
| android:textOff                       | 设置该按钮关闭状态的显示文本                                 |
| android:textOn                        | 设置该按钮打开状态的显示文本                                 |



### AnalogClock和TextClock

```TextClock```继承了```TextView```，与```TextView```不同的是，为```TextClock```设置```android:text```属性没有什么作用。```TextClock```显示数字时钟，可显示当前秒数，```AnalogClock```显示模拟时钟，不会显示当前秒数。

**TextClock**

| xml属性              | 相关方法                      | 说明                         |
| -------------------- | ----------------------------- | ---------------------------- |
| android:format12Hour | setFormat12Hour(CharSequence) | 设置禁用指示器的透明度       |
| android:textOff      | setFormat12Hour(CharSequence) | 设置该按钮关闭状态的显示文本 |
| android:textOn       | setTimeZone(String)           | 设置该按钮打开状态的显示文本 |

**AnalogClock**

| xml属性           | 说明                           |
| ----------------- | ------------------------------ |
| android:dial      | 设置该模拟时钟表盘使用的图片   |
| android:hand_hour | 设置该按模拟时钟时针使用的图片 |
| android:hand_time | 设置该模拟时钟分针使用的图片   |



### Chronometer

| xml属性           | 说明                           |
| ----------------- | ------------------------------ |
| android:format    | 设置该计时器的事件显示格式     |
| android:countDown | 设置该计时器是否从开始重新计时 |

| 常用方法                                                     | 说明                                             |
| ------------------------------------------------------------ | ------------------------------------------------ |
| setBase                                                      | 设置计时器的起始时间                             |
| setFormate                                                   | 设置计时器的事件显示格式                         |
| start()                                                      | 启动计时器开始计时                               |
| stop()                                                       | 关闭计时器停止计时                               |
| setOnChronometer(Chronometer.OnChronometerTickListener listener) | 为计时器绑定监听事件，当计时器改变时触发该监听器 |



## ImageView及其子类

### ImageView

| xml属性                     | 相关方法                            | 说明                                                         |
| --------------------------- | ----------------------------------- | ------------------------------------------------------------ |
| android:adjustViewBound     | setAdjustViewBound(boolean)         | 设置```ImageView```是否调整自己的边界来保持所显示的图片长宽比 |
| android:baseline            | setBaseline(int)                    | 设置基线在该组件内的偏移量                                   |
| android:baselineAlignBotton | setBaselineAlignBotton(boolean)     | 如果属性值为```true```，组件会根据其底边基线对其             |
| android:cropTopPadding      | setCropTopadding(boolean)           | 如果属性值为```true```，组件将会被裁剪到保留该```ImageView```的```padding``` |
| android:maxHeight           | setMaxHeight(int)                   | 设置```ImageView```的最大高度                                |
| android:maxWidth            | setMaxWidth(int)                    | 设置```ImageView```的最大宽度                                |
| android:scaleType           | setScaleType(ImageView.ScaleType)   | 设置图片的缩放方式                                           |
| android:src                 | setImageResource(int)               | 设置```ImageView```所显示的```Drawable```对象的```ID```      |
| android:tint                | setImageTintList(ColorStateList)    | 设置对该组件显示的图片重新着色                               |
| android:tintMode            | setImageTintMode(ProterDuffer.Mode) | 设置对该组件显示的图片着色模式                               |

为了控制 ```ImageView```显示的图片，```ImageView```提供了以下方法：

```java
//使用Bitmap位图设置该ImageView显示的图片
setImageBitmap(Bitmap bm);
//使用Drawable对象设置该ImageView显示的图片
setImageDrawable(Drawable drawable);
//使用资源图片ID设置该ImageView显示的图片
setImageResource(int resId);
//使用图片URL设置该ImageView显示的图片
setImageURI(Uri uri);
```



### FloatingActionButton

### ImageView

| xml属性            | 说明                                                 |
| ------------------ | ---------------------------------------------------- |
| app:fabSize        | 指定按钮大小，支持```normal```，```mini```两个属性值 |
| app:backgroundTint | 设置按钮的填充色。若不指定该属性，则支持默认填充色   |
| app:rippleColor    | 指定悬浮按钮被点击时的波纹颜色                       |

> tips：```FlatingActionButton```位于```support```库中，必须先添加后才能使用。在```build.gradle```文件中添加：```implementation 'com.android.support:design:28.0.0'```



## AdapterView及其子类

 AdapterView 具有以下特征：

1. ```AdapterView``` 继承了 ```ViewGroup```，其本质是容器；
2. ```AdapterView```可以包括多个"列表项"，并将多个"列表项"以合适的形式显示出来；
3. ```AdapterView ```显示的多个"列表项"由```Adapter```提供，通过```setAdapterView(Adapter)```方法设置```Adapter```即可。

### Adapter接口及其实现类

```Adapter```常用实现类如下：

ArrayAdapter：简单易用的 Adapter,通常用于将数组或List集合的多个值包装成多个列表项；

```java
//定义一个数组
String[] arr1 =  new String[]{"孙悟空","猪八戒","沙悟净","牛魔王"};
//将数组包装为ArrayAdapter
ArrayAdapter adapter1 = new ArrayAdapter(MainActivity.this,R.layout.array_item,arr1);
```

SimpleAdapter：并不简单，功能强大的 Adapter，可用于将 List 集合的多个对象包装成多个列表项；

```java
List<Map<String,Object>> listItems = new ArrayList<>();
        for (int i = 0; i < names.length; i++) {
            Map<String,Object> listItem = new HashMap<>();
            listItem.put("header",imageIds[i]);
            listItem.put("personName",names[i]);
            listItem.put("desc",descs[i]);
            listItems.add(listItem);
        }

        //创建一个SimpleAdapter
        /**
         * 第四个参数:string[]类型参数,确定获取Map<String,?>对象中哪些key对应的value来生成列表项;
         * 第五个参数:int[]类型参数,决定填充哪些组件
         * */
        SimpleAdapter simpleAdapter = new SimpleAdapter(this,listItems,R.layout.simple_item,
                new String[]{"personName","header","desc"},new int[]{R.id.name,R.id.header,R.id.desc});
```

SimpleCursorAdapeter：与 SimpleAdapter 基本相似，只是用于通过 Cursor 包装提供的数据。

BaseAdapter：通常用于被扩展，通过 BaseAdapter 可以对各项列表项进行最大限度的定制。



### AutoCompleteTextView

```AutoCompleteTextView```从```EditText```派生出来，实际上也是一个文本，但比普通文本框多了一个功能，当输入一定字符时，自动完成文本框会显示一个菜单供用户选择，当用户选择某个菜单后，```AutoCompleteTextView```会自动填写该文本框。

| xml属性                          | 相关方法                           | 说明                                                 |
| -------------------------------- | ---------------------------------- | ---------------------------------------------------- |
| android:completionHint           | setCompletionHint(CharSequence)    | 设置下拉菜单中的提示标题                             |
| android:completionHintView       |                                    | 设置下拉菜单中的提示标题的视图                       |
| android:completionThreshold      | setThreshold(int)                  | 设置用户至少输入几个字符才显示提示                   |
| android:dropDownAnchor           | setDropDownAnchor(int)             | 设置下拉菜单的定位锚点，默认为```TextView```本身     |
| android:dropDownHeight           | setDropDownHeigh(int)              | 设置下拉菜单的高度                                   |
| android:dropDownHorizontalOffset |                                    | 设置下拉菜单与文本框之间的水平位移，默认与文本框对齐 |
| android:dropDownVerticalOffset   |                                    | 设置下拉菜单与文本框之间的垂直间距，默认紧跟文本框   |
| android:dropDownWidth            | setDropDownWidth(int)              | 设置下拉框的宽度                                     |
| android:popupBackground          | setDropDownBackgroundResource(int) | 设置下拉菜单的背景                                   |

```AutoCompleteTextView```还派生出一个子类：```MultiAutoCompleteTextView```，其允许输入多个提示项并以分隔符分割，通过```setTokenizer()```方法来设置分隔符。



### ExpandableListView

```ExpandabaleListView```是```ListView```的子类，其不同在于```ExpandableListView```所显示的列表项是由```ExpandableListAdapter```提供。

| xml属性                   | 说明                                                         |
| ------------------------- | ------------------------------------------------------------ |
| android:childDivider      | 指定各组内各子列表项之间的分隔条                             |
| android:childIndicator    | 显示在子列表旁的Drawable对象                                 |
| android:childIndicatorXxx | 设置每个子列表项的Xxx（Start、End、Right、Left）边界符，该属性为任意长度 |
| android:indecatorXxx      | 设置每个组列表项的Xxx（Start、End、Right、Left）边界符，该属性为任意长度 |
| android:groupIndicator    | 设置每个组列表项旁边的Drawable对象                           |

实现```ExpandableListAdapter```有如下三种常用方式：

1. 扩展```BaseExpandableListAdapter```实现```ExpandableListAdapter```；
2. 使用```SimpleExpandableListAdapter```将两个```List```集合包装为```ExpandableListAdapter```；
3. 使用```SimpleCursorTreeAdapter```将```Cursor```中的数据包装成```SimpleCursorTreeAdapter```；



### AdapterViewFlipper

```AdapterViewFlipper```继承了`AdapterViewAnimator`，它会显示`Adapter`提供的多个`View`组件，但每次只能显示一个组件。程序可以通过`showPrevious()` 和`showNext()`方法控制该组件的显示上一个组件、下一个组件。```AdapterViewFlipper```可以在多个```View```切换过程使用渐隐渐现的动画效果。

```AdapterViewAnimator```支持的```XML```属性：

| xml属性                  | 说明                                             |
| ------------------------ | ------------------------------------------------ |
| android:animateFirstView | 设置显示该组件的第一个View时是否使用动画         |
| android:inAnimation      | 设置组件显示时的动画                             |
| android:loopViews        | 设置循环到最后一个组件后是否自动跳转至第一个组件 |
| android:outAnimation     | 设置组件隐藏时使用的动画                         |

```AdapterViewFlipper```额外支持的```XML```属性：

| xml属性                      | 说明                       |
| ---------------------------- | -------------------------- |
| android:startFlipper         | 设置显示该组件是否自动播放 |
| android:setFlipInterval(int) | 设置自动播放的时间间隔     |



### StackView

`StackView`是`AdapterViewAnimator`的子类，用于显示`Adapter`提供的一系列`View`，`StackView`将以堆叠方式显示多个列表项。

为控制`StackView`显示的`View`组件，`StackView`提供了如下两种控制方式：

1. 拖动方式
2. 通过调用`showNext()`、`showPreviout()`控制显示下一个、上一个组件。



### RecyclerView

开发者在编写`BaseExpandableListAdapter`的子类时，需要对`getChildView()`、`getGroupView()`方法进行优化，否则`ListView`运行时可能不太流畅，这种优化很大程度上依赖于开发者的技巧。此外，`ListView`通常不支持水平滚动，因此`RecyclerView`用于替代`ListView`。

改进后的`RcyclerView`需要使用`RecyclerView.Adapter`，改进后的`RecyclerView.Adapter`只需要实现三个方法：

```java
//用于创建爱你列表项组件，该方法创建的组件会被自动缓存
onCreateViewHolder(ViewGroup viewGroup, int position);
//负责为列表项组件绑定数据，每次组件重新显示时会重新执行该方法
onBindViewHolder(ViewHolder viewHolder, int position);
//返回值决定包含多少个列表项
getItemCount();
```

`RecyclerView.Adapter`大致提供了以下方法来控制对列表项的修改：

```java
//当position位置数据发生改变时，通知Adapter更新界面
notifyItemChanged(int position);
//当position位置数据发生改变时，通知Adapter从positionStart开始更新itemCount数量的列表项
notifyItemRangeChanged(int positionStart,int itemCount);
//当方法指定在position位置执行动作
notifyItemInserted(int positon);
//当方法指定列表项从fromPosition移动到toPosition
notifyItemMoved(int fromPosition,int toPosition);
//执行插入多个列表项
notifyItemRangeInserted(int position,int itemCount);
//执行插入多个列表项
notifyItemRemoved(int position);
//执行删除多个列表项
notifyItemRangeRemoved(int positionStart,int itemCount);
```



## ProgressBar及其子类

### ProgressBar

`Android`支持多种风格的进度条，通过`style`属性为`ProgressBar`指定风格，该属性支持以下值：

```xml
<!--水平进度条-->
@android:style/Widget.ProgressBar.Horizontal;
<!--普通环形进度条-->
@android:style/Widget.ProgressBar.Inverse;
<!--大环形进度条-->
@android:style/Widget.ProgressBar.Large;
<!--大环形进度条-->
@android:style/Widget.ProgressBar.Large.Inverse;
<!--小环形进度条-->
@android:style/Widget.ProgressBar.Small;
<!--小环形进度条-->
@android:style/Widget.ProgressBar.Small.Inverse;
```

| xml属性                      | 说明                                     |
| ---------------------------- | ---------------------------------------- |
| android:max                  | 设置进度条的最大值                       |
| android:progress             | 设置进度条已完成进度值                   |
| android:progressDrawable     | 设置进度条的轨道对应的Drawable对象       |
| android:indeteminate         | 设置为true，设置进度条不精确显示进度     |
| android:indeteminateDrawable | 设置绘制不显示进度的进度条的Drawable对象 |
| android:indeteminateDuration | 设置不精确显示进度的持续时间             |

`ProgressBar`提供了以下方法来操作进度：

1. `setProgress(int)`：设置进度的完成百分比；
2. `incrementProgress(int)`：设置及进度条的增减。



### SeekBar

拖动条`SeekBar`继承了`ProgressBar`，允许用户改变其外观，改变滑块外观通过以下属性设置：

| xml属性          | 说明                                         |
| ---------------- | -------------------------------------------- |
| android:thumb    | 指定一个Drawable对象，将其作为自定义滑块     |
| android:tickMark | 指定一个Drawable对象，将其作为自定义刻度图标 |

为了使程序响应拖动条滑块位置改变，可以绑定一个`onSeekBarChangeListener`监听器。



### RatingBar

星级评分条`RatingBar`与拖动条`SeekBar`区别在于`RatingBar`使用星星来表示进度。

| xml属性             | 说明                                           |
| ------------------- | ---------------------------------------------- |
| android:isindicator | 设置评分条是否允许用户改变（true为不允许改变） |
| android:numStarts   | 设置评分条总共有多少星级                       |
| android:ratings     | 设置评分条默认星级                             |
| android:stepSize    | 设置每次需要改变多少个星级                     |



## ViewAnimator及其子类

`ViewAnimator`主要功能是添加动画效果，常用`XML`属性如下：

| xml属性                  | 说明                                                 |
| ------------------------ | ---------------------------------------------------- |
| android:animateFirstView | 设置`ViewAnimator`显示第一个`View`组件时是否使用动画 |
| android:numStarts        | 设置`ViewAnimator`显示组件时所使用的动画             |
| android:ratings          | 设置`ViewAnimator`隐藏组件时所使用的动画             |

### ViewSwitcher

`ViewSwitcher`代表了视图切换组件，继承于`FrameLayout`，可以将多个`View`层叠在一起，每次只显示一个组件。使用`ViewFactory`为其创建`View`，通过 `setFactory(ViewSwitcher.ViewFactory)`方法为之设置`ViewFactory`。

```java
switcher.setFactory(() -> {
    // 加载R.layout.slidelistview组件，实际上就是一个GridView组件
    return inflater.inflate(R.layout.slidelistview, null);
});
```



### ImageSwithcer

`ImageView`继承了`ViewFactory`，并重写了`showNext()`、`showPrevious()`方法，相比于`ViewSwitcher`使用更加简单。使用`ImageView`只需要以下两个步骤：

1. 为`ImageView`提供一个`ViewFactory`且生成的`View`必须是`ImageView`；
2. 切换图片时调用`ImageView`的`setImageDrawable(Drawable drawable)`、`setImageResource(int resId)`和`setImageURI(Uri uri)`方法更换图片。

### ViewFlipper

`ViewFlipper`组件继承了`ViewAnimator`，可调用`addView(View view)`添加多个组件。`ViewFlipper`与`AdapterViewFlipper`有较大的相似性，区别在于`ViewFlipper`需要通过`addView(View view)`添加多个`View`，而`AdapterView`则只需要传入一个`Adapter`。



## 杂项组件

### Toast

`Toast`是非常方便的提示消息框，使用步骤：

1. 调用`Toast`构造器或`makeText()`静态方法创建`Toast`对象；
2. 调用`Toast`方法来设置消息对其方式、页边距；
3. 调用`Toast`的`show()`方法将其显示出来。

### CalendarView

| xml属性                             | 相关方法                        | 说明                                                       |
| ----------------------------------- | ------------------------------- | ---------------------------------------------------------- |
| android:dateTextAppearance          | setDateTextAppearance(int)      | 设置该日历视图的日期文字的样式                             |
| android:firstDayOfWeek              | setFirstDayOfWeek(int)          | 设置每周第一天，允许设置周一到周日任意一天作为每周第一天   |
| android:focusedMonthDateColor       | setFocusedMonthDateColor(int)   | 设置获得焦点的月份的日期文字的颜色                         |
| android:unfocusedMonthDateColor     | setUnFocusedMonthDateColor(int) | 设置未获得焦点的月份的日期文字的颜色                       |
| android:maxDate                     | setMaxDate(long)                | 设置该日历组件支持的最大日期，以`mm/dd/yy`格式指定最大日期 |
| android:minDate                     | setMinDate(long)                | 设置该日历组件支持的最小日期，以`mm/dd/yy`格式指定最小日期 |
| android:selectedWeekBackgroundColor | setWeekBackgroundColor(int)     | 设置被选中的背景颜色                                       |
| android:showWeekNumber              | setShowWeekNumber(boolean)      | 设置是否显示第几周                                         |
| android:weekDayTextAppearance       | setWeekDayTextAppearance(int)   | 设置星期几的文字样式                                       |
| android:weekNumberColor             | setWeekNumberColor(int)         | 设置周编号的颜色                                           |
| android:weekSeparatorLineColor      | setWeekSeparatorLineColor(int)  | 设置周分割线的颜色                                         |

通过调用该组件的`setOnDateChangeListener()`方法来添加事件监听器。



### DatePicker和TimerPicker

| xml属性                                | 相关方法               | 说明                                                         |
| -------------------------------------- | ---------------------- | ------------------------------------------------------------ |
| android:calendarTextColor              |                        | 设置日历列表的文本颜色                                       |
| android:calendarViewShown              |                        | 设置日历选择器是否显示`CalendarView`组件                     |
| android:datePickerMode                 |                        | 设置日期选择模式，该属性支持`CalendarView`和`Spinner`两种模式 |
| android:dateOfWeekBackground           |                        | 设置该日历视图的每周上方的页眉的颜色                         |
| android:dateOfWeekTextAppearance       |                        | 设置该日历视图的每周上方的页眉的文本外观                     |
| android:startYear                      |                        | 设置该日历视图允许选择的第一年                               |
| android:endYear                        |                        | 设置该日历视图允许选择的最后一年                             |
| android:firstDayOfWeek                 | setFirstDayOfWeek(int) | 设置每周第一天，允许设置周一到周日任意一天作为每周第一天     |
| android:headerBackground               |                        | 设置选中日期的背景色                                         |
| android:headerDayOfMonthTextAppearance |                        | 设置选中日期中代表日的背景色                                 |
| android:headerMonthTextAppearance      |                        | 设置选中日期中代表月的背景色                                 |
| android:headerYearTextAppearance       |                        | 设置选中日期中代表年的背景色                                 |
| android:maxDate                        |                        | 设置该日历组件支持的最大日期，以`mm/dd/yy`格式指定最大日期   |
| android:minDate                        |                        | 设置该日历组件支持的最小日期，以`mm/dd/yy`格式指定最小日期   |
| android:spinnerShown                   |                        | 设置该日期选择器是否显示`Spinner`日期选择组件                |

通过调用该组件的`setOnDateChangeListener()`方法来添加事件监听器。



### NumberPicker

数值选择器用于让用户输入，用户可以通过键盘输入数值，也可通过拖动来选择数值。该组件有常用的以下方法：

```java
//设置组件支持的最小值
setMinValue(int vlaue);
//设置组件支持的最大值
setMaxValue(int value);
//设置组件支持的当前值
setValue(int value);
```

通过调用该组件的`setOnValueChangeListener()`方法来添加事件监听器。



## 对话框

### AlertDialog

`AlertDialog`支持四个区域：图标区、标题区、内容区、按钮区

创建一个对话框步骤：

1. 创建 `AlertDialog.Builder`对象；
2. 调用 `AlertDialog.Builder`对象的`setTitle()`或`setCustomtitle()`方法设置标题；
3. 调用 `AlertDialog.Builder`对象的`setIcon()`方法设置图标；
4. 调用 `AlertDialog.Builder`对象的相关方法设置对话框内容；
5. 调用 `AlertDialog.Builder`对象的`setPositiveButton()`、`setNegativeButton()`、`setNeutralButton()`方法添加多个按钮；
6. 调用 `AlertDialog.Builder`对象的`create()`方法创建`AlertDialog`对象，再调用`AlertDialog`对象的`show()`方法显示该对话框。

`AlertDialog`提供了以下6种方法来显示指定对话框内容：

```java
//显示简单文本
setMessage();
//显示简单列表
setItems();
//显示单选列表项
setSingleChoiceItems();
//显示多选列表项
setMutilsChoiceItems();
//显示自定义列表项
setAdapter();
//显示自定义View
setView();
```



### PopupWindow

使用步骤：

1. 调用 `PopupWindow` 的构造器创建 `PopupWindow`;
2. 调用`PopupWindow`的`showAsDropDown(View viw)`以下拉组件方式显示或调用`showAtLocation()`方法在特定位置显示。



### DatePickerDialog和TimePickerDialog

设置监听器核心代码：

```java
Calendar calendar = Calendar.getInstance();
                new DatePickerDialog(this,(dp,year,month,dayOfMonth)->{
                    birthEd.setText(year+"年"+month+"月"+dayOfMonth+"日");
                    },calendar.get(Calendar.YEAR),
                    calendar.get(Calendar.MONTH),
                    calendar.get(Calendar.DAY_OF_MONTH)).show();
```



### ProgressDialog

```java
//进度条不显示进度值
setIndeterminater(boolean indeterminate);
//设置进度条最大值
setMax(int max);
//设置显示消息
setMessage(CharSequence message);
//设置进度条的进度值
setProgress(int value);
//设置进度条风格
setProgressStyle(int style);
```



## 菜单

### 菜单和SubMenu

`Android`的菜单具有以下特征：

1. 选项菜单(Option Menu)：选项菜单不支持勾选标记，只显示菜单标题；
2. 子菜单（SubMenu)：不支持菜单图标，不支持嵌套子菜单；
3. 上下文菜单（ContextMenu)：不支持菜单快捷键和图标。

`Menu`接口下包括两个子接口：`SubMenu`、`ContextMenu`，通过重载方法`add()`方法添加菜单项，`addSubMenu()`方法添加子菜单。

使用步骤：

1. 重写`Activity`的`onCreateOptionMenu(Menu menu)`方法，调用`Menu`对象的方法来添加菜单项和子菜单；
2. 重写`Activity`的`onOptionsItemSelected(MenuItem menuItem)`方法响应菜单项的单击事件。

`Android`提供了使用`Java`代码和`XML`两种方式创建菜单，推荐使用`XML`方式创建菜单，菜单资源文件通常放到`\res\menu`目录下。

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
	<!-- 定义一组单选菜单项 -->
	<group android:checkableBehavior="single">
		<!-- 定义三个菜单项 -->
		<item
			android:id="@+id/red"
			android:title="@string/red_title"
			android:alphabeticShortcut="r"/>
		<item
			android:id="@+id/green"
			android:title="@string/green_title"
			android:alphabeticShortcut="g"/>
		<item
			android:id="@+id/blue"
			android:title="@string/blue_title"
			android:alphabeticShortcut="b"/>
	</group>
</menu>
```



`MenuItem`的常用方法：

```java
//设置菜单项是否被勾选
setCheckable(boolean checkbale);
//设置group里的所有菜单项是否可被勾选，若exclusive为true，表示它们是一组单选菜单项
setGroupCheckable(int group,boolean checkable,boolean exclusive);
//设置字母快捷键
setAlphabeticShortCut(char alphaChar);
//设置数字快捷键
setNumbericShortcut(char numericChar);
//同时设置两种快捷键
setShortcut(char numericChar,char alphaChar);
//关联Activity
setIntent(Intent itent);
```



### ContextMenu

使用步骤：

1. 重写`Activity`的`onCreateContextMenu(ContextMenu menu, View v, ContextMenu.ContextMenuInfo menuInfo)`方法，调用`Menu`对象的方法来添加菜单项和子菜单；
2. 调用`Activity`的`registerForContextMenu(View viw)`方法为`View`组件注册上下文菜单；
3. 重写`Activity`的`onContextItemSelected(MenuItem menuItem)`方法响应菜单项的单击事件。



### PopupMenu

创建弹出式菜的步骤：

1. 调用`PopupMenu(Context context,View anchor)`构造器创建下拉菜单，`anchor`代表要激发该弹出菜单的组件；；
2. 调用`MenuInflater`的`inflate()`方法将菜单资源填充到`PopupMenu`中
3. 调用`PopuMenu`的show()`方法显示弹出式菜单。



## ActionBar

`ActionBar`提供了以下功能：

1. 显示选项菜单
2. 使用程序图标作为返回`Home`主屏或显示成`Action Item`
3. 提供交互式`View`作为`Action View`
4. 提供基于`Tab`的导航方式，可用于切换多个`Fragment`
5. 提供基于下拉方式的导航方式

### 使用

`Android3.0`版本默认启动了`ActionBar`，如果希望关闭，则可设置该应用的主题为`Xxx.NoactionBar`。

```xml
<appliction
            android:theme="@android:style/Theme.Material.NoActionBar"
            android:label="@String/app_name">
</appliction>
```

可通过代码方式控制`ActionBar`的显示

```
//显示ActionBar
show();
//隐藏ActionBar
hide();
```

`ActionBar`可以将菜单显示成`Action Item`，通过`setShowAsAction(int actionEnum)`，该方法设置是否将菜单项显示在`ActionBar`上，作为`Action Item`。

方法支持以下参数：

| 参数                                | 说明                         |
| ----------------------------------- | ---------------------------- |
| SHOW_AS_ACTION_ALWAYS               | 总是显示                     |
| SHOW_AS_ACTION_COLLAPSE_ACTION_VIEW | 折叠成普通菜单项             |
| SHOW_AS_ACTION_IF_ROOM              | 有足够位置时才显示`MenuItem` |
| SHOW_AS_ACTION_NEVER                | 不显示在`ActionBar`上        |
| SHOW_AS_ACTION_WITH_TEXT            | 显示菜单项文本               |

在`XML`资源文件定义菜单：

```xml
<item
    android:showAsAction="always|withText">
        <menu></menu>
</item>
```
