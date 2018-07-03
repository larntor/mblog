---
title: "C# 从零开始写 SharpDx 应用 初始化dx修改颜色"
author: lindexi
date: 2018-7-2 17:7:20 +0800
CreateTime: 2018-6-20 8:39:31 +0800
categories: C# D2D DirectX SharpDX Direct2D
---

本文来告诉大家如何在上一篇博客创建的窗口里面使用 Sharpdx 初始化，然后设置窗口颜色。

<!--more-->


<!-- csdn -->
<!-- 标签：C#,D2D,DirectX,SharpDX,Direct2D, -->
<div id="toc"></div>

本文是 SharpDX 系列博客，更多博客请点击[SharpDX 系列](https://lindexi.github.io/lindexi/post/sharpdx.html )

在[C# 控制台创建 Sharpdx 窗口](https://lindexi.oschina.io/lindexi/post/C-%E6%8E%A7%E5%88%B6%E5%8F%B0%E5%88%9B%E5%BB%BA-Sharpdx-%E7%AA%97%E5%8F%A3.html )已经创建了一个窗口，现在需要在这个窗口画一些图片

## 创建资源

第一步是需要添加一个方法 `InitializeDeviceResources` 用来初始化资源

这个方法就是写在`KikuSimairme`类里，关于这个类的代码在[C# 控制台创建 Sharpdx 窗口](https://lindexi.oschina.io/lindexi/post/C-%E6%8E%A7%E5%88%B6%E5%8F%B0%E5%88%9B%E5%BB%BA-Sharpdx-%E7%AA%97%E5%8F%A3.html )

```csharp

        // 其他被忽略的代码
        private void InitializeDeviceResources()
        {
        }
```

创建一个可以画出来的类需要先创建显示模式描述，通过显示描述创建交换链描述，交换链描述创建设备和交换链，通过交换链和设备可以创建可以画出来的类，在这个类就可以画出无聊的图形

### 模式描述

首先需要创建一个描述显示模式，请看下面代码

```csharp
using SharpDX.Direct3D;
using SharpDX.DXGI;

// 其他忽略的代码
    class KikuSimairme : IDisposable
// 其他忽略的代码
        private void InitializeDeviceResources()
        {
            ModeDescription backBufferDesc =
                new ModeDescription(Width, Height, new Rational(60, 1), Format.R8G8B8A8_UNorm);

        }
```

通过 ModeDescription 就可以描述，前两个参数是表示缓存的大小，在很多的情况，这个值都和显示的大小相同。

第三个参数就是表示刷新率，这里使用的就是 `60/1` 也就是 60hz 

最后一个参数设置的是像素格式，这里使用 8 位的 RGBA 格式，使用一个无符号的 32 位整数表示

更多关于 ModeDescription 请看 [DXGI_MODE_DESC](https://msdn.microsoft.com/en-us/library/windows/desktop/bb173064(v=vs.85).aspx )

这里的 backBufferDesc 是在描述后台缓冲区，后台缓冲区的作用是防止动画中出现闪烁，可以这样看，在用户看到一个白纸的时候，一边有个人在这个白纸上画东西，这时用户就会看到画东西的闪烁。如果有两张纸，给用户看一张纸，在另一张纸上画，画完就把这张纸给用户，把刚才的纸拿来画下一个动画。通过这个方式就可以减少用户看到闪烁。这个不给用户看到的纸就是后台缓冲区（back buffer）。

### 交换链描述

下面可以来创建交换链的描述，请使用这个代码

```csharp

// 其他忽略的代码
    class KikuSimairme : IDisposable
// 其他忽略的代码

        private void InitializeDeviceResources()
// 其他忽略的代码
            SwapChainDescription swapChainDesc = new SwapChainDescription()
            {
                ModeDescription = backBufferDesc,
                SampleDescription = new SampleDescription(1, 0),
                Usage = Usage.RenderTargetOutput,
                BufferCount = 1,
                OutputHandle = _renderForm.Handle,
                IsWindowed = true
            };
```

交换链的 ModeDescription 就是上面定义的 backBufferDesc 

多重采用 SampleDescription 用来优化图片，是一种用于采样和平衡渲染像素的创建亮丽色彩变化之间的平滑过渡的一种技术，这里设置等级 1 也就是关闭多重采样，需要传入两个参数一个是Count 指定每个像素的采样数量，一个是Quality指定希望得到的质量级别，参见[DXGI_SAMPLE_DESC structure](https://msdn.microsoft.com/en-us/library/windows/desktop/bb173072(v=vs.85).aspx )，在这里质量级别越高，占用的系统资源就越多。

Usage 设置 CPU 访问缓冲的权限，这里设置可以访问 RenderTarget 输出，请看 [DXGI_USAGE](https://msdn.microsoft.com/en-us/library/windows/desktop/bb173078(v=vs.85).aspx )

后缓冲数量 BufferCount 建议设置一个，设置一个就是双缓冲。两个缓冲区已经足够用了。

OutputHandle 获取渲染窗口句柄

IsWindowed 这个值设置是否希望是全屏，如果是 true 就是窗口。现在软件还没写好，所以这时全屏可能就无法退出，我就设置了全屏，本金鱼有两个屏幕，所以可以让软件退出

现在已经创建交换链，但是我里面很多设置没有告诉大家还有哪些可以设置

这里有很多都需要在微软官方才可以看到，因为本文是简单的博客，不会在本文介绍。

为什么需要设置交换链？因为在刚才已经说了防止用户看到闪烁需要使用两个缓冲，如何把前台缓冲区和后台缓冲区交换就需要用到交换链。

### 私有变量

下面可以创建私有变量

```csharp
using D3D11 = SharpDX.Direct3D11;

// 其他忽略的代码

    class KikuSimairme : IDisposable
// 其他忽略的代码

        private D3D11.DeviceContext _d3DDeviceContext;
        private SwapChain _swapChain;
        private D3D11.RenderTargetView _renderTargetView;
```

这里使用了 using 定义了 D3D11 ，这样可以区分一些类，如果有看到我之前的博客，会看到我在很多博客里都使用这个方式

渲染上下文 d3DDeviceContext 是一种描述设备如何绘制的渲染设备上下文。

创建的 RenderTargetView 是 渲染目标视图，在CPU把如何渲染写入到渲染目标视图中，它是一个2D纹理，写入 RenderTargetView 不会立刻渲染到屏幕，而是到管线的输出混合阶段，最后才到屏幕。

### 创建交换链

准备的代码已经写好，可以创建设备，创建了设备才可以画出

```csharp
using SharpDX.Direct3D;

// 其他忽略的代码
    class KikuSimairme : IDisposable
// 其他忽略的代码

        private void InitializeDeviceResources()
// 其他忽略的代码

            D3D11.Device.CreateWithSwapChain(DriverType.Hardware, D3D11.DeviceCreationFlags.None, swapChainDesc,
                out _d3DDevice, out _swapChain);
            _d3DDeviceContext = _d3DDevice.ImmediateContext;
```

第一个参数 DriverType.Hardware 表示希望使用 GPU 渲染，设置 驱动设备类型 可以设置硬件设备（hardware device）、参考设备（reference device）、软件驱动设备（software driver device）

 - 硬件设备（hardware device）是一个运行在显卡上的D3D设备，在所有设备中运行速度是最快的

 - 软件驱动设备（software driverdevice）是开发人员自己编写的用于Direct3D的渲染驱动软件

 - 参考设备（reference device）是用于没有可用的硬件支持时在CPU上进行渲染的设备

 - WARP设备（WARPdevice）是一种高效的CPU渲染设备，可以模拟现阶段所有的Direct3D特性

具体请看[Direct3D 11入门级知识介绍](https://blog.csdn.net/pizi0475/article/details/7786348 )

第二个参数选不使用特殊的方法，参见 [D3D11_CREATE_DEVICE_FLAG enumeration](https://msdn.microsoft.com/en-us/library/windows/desktop/ff476107(v=vs.85).aspx )

第三个参数是输入上面的交换链描述

最后的参数是输出设备和交换链，关于输出设备请看[Direct3D设备](https://blog.csdn.net/nightelve/article/details/6460477 )。

交换链在Direct3D中为一个设备渲染目标的集合。每一个设备都有至少一个交换链，而多个交换链能够被多个设备所创建。

有了交换链和设备可以在缓冲区画出图形，画图形需要使用`RenderTargetView`，为了在其他函数可以使用，这里需要把这个类写在私有变量

```csharp
        private D3D11.RenderTargetView _renderTargetView;

```

然后在 InitializeDeviceResources 使用下面代码，创建缓冲和使用缓冲和设置创建渲染目标视图。因为渲染目标视图可以认为是一张纸，这就是纹理，纹理是比较复杂的，将纹理理解为一副图像就行了。

```csharp
// 其他忽略的代码
    class KikuSimairme : IDisposable
// 其他忽略的代码

        private void InitializeDeviceResources()
// 其他忽略的代码

            using (D3D11.Texture2D backBuffer = _swapChain.GetBackBuffer<D3D11.Texture2D>(0))
            {
                _renderTargetView = new D3D11.RenderTargetView(_d3DDevice, backBuffer);
            }
```

总的创建过程很多，所以我使用了思维导图让大家知道每个步骤需要创建的

<!-- ![](image/C# 从零开始写 SharpDx 应用 初始化dx修改颜色/C# 从零开始写 SharpDx 应用 初始化dx修改颜色0.png) -->

![](http://7xqpl8.com1.z0.glb.clouddn.com/lindexi%2F201872164929884.jpg)

## 修改颜色

如果已经看过了之前的博客，那么知道已经有可以画的类，就可以开始画出。

本文没有告诉大家如何画出线和画出圆形，只是告诉大家初始资源，所以到这里本文就结束了。

但是大家可以看到这时的界面和之前一样，会说我的程序是不是写错了。所以我就简单修改一下界面，创建一个函数 Draw 在这个函数写代码

```csharp
// 其他忽略的代码
    class KikuSimairme : IDisposable
// 其他忽略的代码

        private void Draw()
        {
            _d3DDeviceContext.OutputMerger.SetRenderTargets(_renderTargetView);
            _d3DDeviceContext.ClearRenderTargetView(_renderTargetView, ColorToRaw4(Color.Coral));

            _swapChain.Present(1, PresentFlags.None);

            RawColor4 ColorToRaw4(Color color)
            {
                const float n = 255f;
                return new RawColor4(color.R / n, color.G / n, color.B / n, color.A / n);
            }
        }
```

这里为了画出颜色，使用 ColorToRaw4 的类，因为 RawColor4 是传入颜色是 [0,1]，但是很多代码使用的是[0,255]，为了让颜色比较容易写，我就写了这个类。

在`_d3DDeviceContext.OutputMerger.SetRenderTargets(_renderTargetView);` 设置了刚才创建的`_renderTargetView`激活，在每次我们想渲染一个特定的渲染目标的时候，必须在所有的绘制的函数调用之前对它进行设置。

第二句代码` _d3DDeviceContext.ClearRenderTargetView(_renderTargetView, ColorToRaw4(Color.Coral));`清理`_renderTargetView`设置颜色，把他放在第一个缓冲。在 dx 有两个缓冲，一个是看不见的，一个是显示的。第一个缓冲就是显示的，第二个就是在第一个显示的时候画出来，于是不停交换，让用户看到一个画好的缓冲。通过这个方法用户可以看到动画

调用交换链的Present函数在屏幕上显示渲染缓冲区的内容 `_swapChain.Present(1, PresentFlags.None);` 是等待垂直同步，在刷新完成在完成这个方法，第一个参数是同步间隔，第二个参数是演示的标志。

创建资源和颜色设置代码已经写好，现在需要调用方法

```csharp
// 其他忽略的代码
    class KikuSimairme : IDisposable
    {
        public KikuSimairme()
        {
            _renderForm = new RenderForm();
            _renderForm.ClientSize = new Size(Width, Height);

            InitializeDeviceResources();
        }
// 其他忽略的代码
    }
```

如果有看过之前的博客，会发现有一个方法是空的，现在可以在`RenderCallback`添加代码

```csharp
// 其他忽略的代码
    class KikuSimairme : IDisposable
    {
        private void RenderCallback()
        {
            Draw();
        }
// 其他忽略的代码
    }
```

还需要设置一下清理

```csharp
public void Dispose()
{
    renderTargetView.Dispose();
    swapChain.Dispose();
    d3dDevice.Dispose();
    d3dDeviceContext.Dispose();
    renderForm.Dispose();
}
```

现在按一下 F5 就可以运行，看到一个绿色的窗口

所有代码

```csharp
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using SharpDX.Direct3D;
using SharpDX.DXGI;
using SharpDX.Mathematics.Interop;
using SharpDX.Windows;
using D3D11 = SharpDX.Direct3D11;

namespace NawbemcemXadre
{
    class KikuSimairme : IDisposable
    {
        /// <inheritdoc />
        public KikuSimairme()
        {
            _renderForm = new RenderForm();
            _renderForm.ClientSize = new Size(Width, Height);

            InitializeDeviceResources();
        }

        private const int Width = 1280;

        private const int Height = 720;

        public void Run()
        {
            RenderLoop.Run(_renderForm, RenderCallback);
        }

        private RenderForm _renderForm;

        private D3D11.Device _d3DDevice;
        private D3D11.DeviceContext _d3DDeviceContext;
        private SwapChain _swapChain;
        private D3D11.RenderTargetView _renderTargetView;

        private void RenderCallback()
        {
            Draw();
        }

        private void InitializeDeviceResources()
        {
            ModeDescription backBufferDesc =
                new ModeDescription(Width, Height, new Rational(60, 1), Format.R8G8B8A8_UNorm);
            
            SwapChainDescription swapChainDesc = new SwapChainDescription()
            {
                ModeDescription = backBufferDesc,
                SampleDescription = new SampleDescription(1, 0),
                Usage = Usage.RenderTargetOutput,
                BufferCount = 1,
                OutputHandle = _renderForm.Handle,
                IsWindowed = true
            };

            D3D11.Device.CreateWithSwapChain(DriverType.Hardware, D3D11.DeviceCreationFlags.None, swapChainDesc,
                out _d3DDevice, out _swapChain);
            _d3DDeviceContext = _d3DDevice.ImmediateContext;

            using (D3D11.Texture2D backBuffer = _swapChain.GetBackBuffer<D3D11.Texture2D>(0))
            {
                _renderTargetView = new D3D11.RenderTargetView(_d3DDevice, backBuffer);
            }
        }

        private void Draw()
        {
            _d3DDeviceContext.OutputMerger.SetRenderTargets(_renderTargetView);
            _d3DDeviceContext.ClearRenderTargetView(_renderTargetView, ColorToRaw4(Color.Coral));

            _swapChain.Present(1, PresentFlags.None);

            RawColor4 ColorToRaw4(Color color)
            {
                const float n = 255f;
                return new RawColor4(color.R / n, color.G / n, color.B / n, color.A / n);
            }
        }

        /// <inheritdoc />
        public void Dispose()
        {
            _renderTargetView.Dispose();
            _swapChain.Dispose();
            _d3DDevice.Dispose();
            _d3DDeviceContext.Dispose();
            _renderForm?.Dispose();
        }
    }
}
```

参见：[SharpDX Beginners Tutorial Part 3: Initializing DirectX - Johan Falk](http://www.johanfalk.eu/blog/sharpdx-beginners-tutorial-part-3-initializing-directx )

在下一篇博客[C# 从零开始写 SharpDx 应用 画三角](https://lindexi.github.io/lindexi/post/C-%E4%BB%8E%E9%9B%B6%E5%BC%80%E5%A7%8B%E5%86%99-SharpDx-%E5%BA%94%E7%94%A8-%E7%94%BB%E4%B8%89%E8%A7%92.html )将会告诉大家如何创建 视口 ViewPort，视口定义了我们渲染到屏幕上的面积。

