# win10 uwp dataGrid


 
 看到国内一个大神写的：https://github.com/zmtzawqlp/UWP-master/commits/master 
 
 本文有大量都是抄袭他的代码

##表格控件

我们先要知道我说的是哪个？

其实DataGrid就是表格控件，本文就是告诉大家如何做一个UWP 表格控件

一开始我是改ListView，ListView有个问题，就是你设置他的宽度实际是很小，这个如何做？

其实简单UWP ListView宽度过小，可以通过

```
                <ListView.ItemContainerStyle>
                    <Style TargetType="ListViewItem">
                        <Setter Property="HorizontalContentAlignment"
                                Value="Stretch"></Setter>
                    </Style>
                </ListView.ItemContainerStyle>

```

我们这个问题还可以做ListView对齐，ListBox内容对齐，ListBox宽度过小的解决

这样我们手动写表格，手动写表格宽度不好做，因为我们需要都是固定宽度

##
 
 
 国外 https://liftcodeplay.com/2015/10/24/datagrid-alternatives-in-uwp/
 
 需要钱：https://www.syncfusion.com/products/uwp/sfdatagrid
 
 <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="知识共享许可协议" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />本作品采用<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">知识共享署名-非商业性使用-相同方式共享 4.0 国际许可协议</a>进行许可。欢迎转载、使用、重新发布，但务必保留文章署名[林德熙](http://blog.csdn.net/lindexi_gd)(包含链接:http://blog.csdn.net/lindexi_gd )，不得用于商业目的，基于本文修改后的作品务必以相同的许可发布。如有任何疑问，请与我[联系](mailto:lindexi_gd@163.com)。