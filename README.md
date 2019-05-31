# WPF_Arduino

C# 课程实验4

![](https://raw.githubusercontent.com/8qwe24657913/WPF_Arduino/master/Images/screenshot.png)

## 功能概述

 	1. 通过滑动条控制灯的亮度，并为每个 `slider` 添加了独立的指示器
 	2. 每秒钟拉取一次亮度和温度信息，显示实时数据并将历史数据绘制成图表显示
 	3. 实时显示并更新原始数据值，方便使用者观察
 	4. 可使用 log 功能将发送和接收的数据导出到本地文件中，以便后续使用

## 实现细节

\1. 原要求为使用rgb三色显示，但灯共有5个，能表示的信息量不足，故使用5个分别的圆来替代，其中红色圆的代码如下（其它四个圆类似）

```xaml
<Ellipse x:Name="redEllipse"
        Fill="Red"
        HorizontalAlignment="Left"
        Height="21"
        Margin="29,114,0,0"
        Stroke="Black"
        VerticalAlignment="Top"
        Width="21"/>
```

当我们调节滑块的位置时，实际上调节的是圆的透明度，越不透明表示灯越亮

```C#
private void LightSlider_ValueChanged(object sender, RoutedPropertyChangedEventArgs<double> e) {
    var slider = sender as Slider;
    ellipses[lights.IndexOf(slider)].Fill.Opacity = (255 - slider.Value) / 256;
}
```

\2. 为了在发送/接收数据时使最新数据显示出来，需要将 ListView 滚动到最底部，而 ListView 并未直接提供该方法，可通过如下代码实现此功能：

```C#
private void ScrollToEnd(ListView listView) {
    var lvap = new ListViewAutomationPeer(listView);
    var svap = lvap.GetPattern(PatternInterface.Scroll) as ScrollViewerAutomationPeer;
    if (svap == null) return;
    var scroll = svap.Owner as ScrollViewer;
    if (scroll == null) return;
    scroll.ScrollToEnd()
}
```

\3. 在写入 log 时需要进行大量字符串的拼接，我在这里使用了 `StringBuilder` 以避免反复分配内存造成的时间消耗，并且把拼接字符串的工作分散到了每次收发信息的时候，减少了用户在试图保存大量 log 时的等待时间

\4. 需要注意的是，`DataReceivedHandler` 执行时并不在主线程，而将数据写入到与控件进行了数据绑定的对象上的操作要求必须在主线程执行，所以需要使用 `Dispatcher.BeginInvoke()` 来避免问题的发生

## 项目特色

☑ 功能丰富

☑ 易于操作

☑ 美观大方

☑ 实用性强

## 代码总量

约 600 行

## 工作时间

约两夜

## 结论

​	通过这次实验，我掌握了WPF的图形界面APP开发方法，学会了数据绑定和控件的事件机制，学会了使用 SerialPort 串口类，能够使用 MIDI 协议实现 PC 与 Arduino 之间的信息交换，还了解了不同数据类型在内存中的大/小端序存储方式，可以实现 MIDI 协议的正确编/解码，受益良多