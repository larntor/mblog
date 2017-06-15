---
layout: post
title:  win10 UWP 剪贴板 Clipboard 
category: uwp 
---

win10 UWP 剪贴板 Clipboard使用`Windows.ApplicationModel.DataTransfer.Clipboard`
<!--more-->

<div id="toc"></div>


## 设置文本

在UWP把字符串添加到剪贴板使用代码很少。

第一个创建 DataPackage，无聊添加图片还是什么都是使用 DataPackage
```csharp
 DataPackage dataPackage = new DataPackage();
```

然后把文件设置 DataPackage ，因为剪贴板只能设置 DataPackage ，所以即使添加字符串，也是使用 DataPackage

```csharp
            dataPackage.SetText("文本");
            Clipboard.SetContent(dp);
```

设置图片的方法也是相同。

但是 75351663 大神说，设置之前需要清空剪贴板，不然之前数据成为垃圾内存，我自己没有去试，但是加一句代码也没什么，建议设置之前清空。

## 获取文本

检查剪贴板包含文本

```csharp
DataPackageView con = Windows.ApplicationModel.DataTransfer.Clipboard.GetContent();
if (con.Contains(StandardDataFormats.Text))
```

```csharp
            DataPackageView con = Windows.ApplicationModel.DataTransfer.Clipboard.GetContent();
            string str = string.Empty;
            if (con.Contains(StandardDataFormats.Text))
            {
                str = await con.GetTextAsync();
            }
```

## 获取图片

```csharp
            if (con.Contains(StandardDataFormats.Bitmap))
            {
                RandomAccessStreamReference img = await con.GetBitmapAsync();
                var imgstream = await img.OpenReadAsync();
                BitmapImage bitmap = new BitmapImage();
                bitmap.SetSource(imgstream);

                Windows.UI.Xaml.Media.Imaging.WriteableBitmap src = new Windows.UI.Xaml.Media.Imaging.WriteableBitmap(bitmap.PixelWidth, bitmap.PixelHeight);
                src.SetSource(imgstream);

                Windows.Graphics.Imaging.BitmapDecoder decoder = await Windows.Graphics.Imaging.BitmapDecoder.CreateAsync(imgstream);
                Windows.Graphics.Imaging.PixelDataProvider pxprd = await decoder.GetPixelDataAsync(Windows.Graphics.Imaging.BitmapPixelFormat.Bgra8, Windows.Graphics.Imaging.BitmapAlphaMode.Straight, new Windows.Graphics.Imaging.BitmapTransform(), Windows.Graphics.Imaging.ExifOrientationMode.RespectExifOrientation, Windows.Graphics.Imaging.ColorManagementMode.DoNotColorManage);
                byte[] buffer = pxprd.DetachPixelData();

                str = "image";
                StorageFolder folder = await _folder.GetFolderAsync(str);

                StorageFile file = await folder.CreateFileAsync(DateTime.Now.Year.ToString() + DateTime.Now.Month.ToString() + DateTime.Now.Day.ToString() + DateTime.Now.Hour.ToString() + DateTime.Now.Minute.ToString() + ".png", CreationCollisionOption.GenerateUniqueName);

                using (var fileStream = await file.OpenAsync(FileAccessMode.ReadWrite))
                {
                    var encoder = await Windows.Graphics.Imaging.BitmapEncoder.CreateAsync(Windows.Graphics.Imaging.BitmapEncoder.PngEncoderId, fileStream);
                    encoder.SetPixelData(Windows.Graphics.Imaging.BitmapPixelFormat.Bgra8, Windows.Graphics.Imaging.BitmapAlphaMode.Straight, decoder.PixelWidth, decoder.PixelHeight, decoder.DpiX, decoder.DpiY, buffer);
                    await encoder.FlushAsync();
                }
            }
```

## 获取文件

```csharp
 if (con.Contains(StandardDataFormats.StorageItems))
            {
                var filelist = await con.GetStorageItemsAsync();
                foreach (StorageFile t in filelist)
                {
                    
                }
            }
```

IStorageItem 转 StorageFile

```csharp
                     if (t.IsOfType(StorageItemTypes.File))
                    {
                        StorageFile storageFile = storageItem as StorageFile;  
                    }
```

参考：

http://www.cnblogs.com/tcjiaan

http://www.cnblogs.com/chengxingliang/archive/2013/01/21/2857718.html

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="知识共享许可协议" style="border-width:0" src="https://licensebuttons.net/l/by-nc-sa/4.0/88x31.png" /></a><br />本作品采用<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">知识共享署名-非商业性使用-相同方式共享 4.0 国际许可协议</a>进行许可。欢迎转载、使用、重新发布，但务必保留文章署名[林德熙](http://blog.csdn.net/lindexi_gd)(包含链接:http://blog.csdn.net/lindexi_gd )，不得用于商业目的，基于本文修改后的作品务必以相同的许可发布。如有任何疑问，请与我[联系](mailto:lindexi_gd@163.com)。

