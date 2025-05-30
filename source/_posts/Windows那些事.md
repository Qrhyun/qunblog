---
title: Windows那些事
date: 2025-05-30 11:20:49
tags:
---
# Windows那些事

## 时间节点
> 1992: MFC → 1997: WTL → 2002: WinForms → 2006: WPF → 2012: WinRT/UWP → 
2015: React Native → 2019: WinUI 3 → 2021: Windows App SDK → 2021: React Native for Windows

## 1. 底层API和基础框架层
### Windows API (Win32 API)
- 定义：Windows操作系统的原生C语言API
- 特点：功能最全面，直接操作系统调用
- 用途：所有其他框架的底层基础
- 例子：CreateWindow(), MessageBox(), RegOpenKey()
### MFC (Microsoft Foundation Classes)
```cc
// MFC 示例
class CMyDialog : public CDialogEx
{
public:
    CMyDialog(CWnd* pParent = nullptr);
    
protected:
    virtual void DoDataExchange(CDataExchange* pDX);
    afx_msg void OnBnClickedOk();
    DECLARE_MESSAGE_MAP()
};
```
- 时代：1992年，Visual C++ 1.0
- 特点：C++ Win32 API封装，面向对象
- 优势：性能好，系统权限完整
- 劣势：学习曲线陡峭，UI现代化程度低
### WTL (Windows Template Library)
```cc
// WTL 示例
class CMainFrame : public CFrameWindowImpl<CMainFrame>
{
public:
    DECLARE_FRAME_WND_CLASS(NULL, IDR_MAINFRAME)
    
    BEGIN_MSG_MAP(CMainFrame)
        MESSAGE_HANDLER(WM_CREATE, OnCreate)
        MESSAGE_HANDLER(WM_DESTROY, OnDestroy)
    END_MSG_MAP()
};
```
- 时代：1997年
- 特点：基于C++模板的轻量级框架
- 优势：比MFC更轻量，性能更好
- 劣势：社区支持有限，学习资源少
### WinRT API
- 定义：Windows Runtime API，现代化的系统API
- 特点：面向对象，支持多语言，异步设计
- 关系：建立在Win32 API之上，但提供更现代的接口
- 用途：UWP和现代Windows应用的基础
## 2. 开发工具包层
### Windows SDK
- 内容：Win32 API + 部分WinRT API + 开发工具
- 作用：提供编译、调试Windows应用的基础工具
- 关系：是开发环境的基础，其他框架都依赖它
### Windows App SDK (原Project Reunion)
- 目标：统一Win32和UWP开发
- 内容：WinUI 3 + 精选的WinRT API + 现代Windows功能
- 关系：建立在Windows SDK之上，是微软的未来方向
## 3. UI框架层---建立在底层API和基础框架层之上
###  .NET生态UI框架
#### WinForms
```csharp
// WinForms
var form = new Form();
var button = new Button { Text = "Click Me" };
form.Controls.Add(button);
```
- 时代：.NET Framework 1.0+ (2002年)
- 特点：简单易用，基于Win32控件
- 局限：UI样式有限，不支持现代设计
#### WPF (Windows Presentation Foundation)
```xml
xml<!-- WPF XAML -->
<Button Content="Click Me" Click="Button_Click">
    <Button.Style>
        <Style TargetType="Button">
            <Setter Property="Background" Value="Blue"/>
        </Style>
    </Button.Style>
</Button>
```
- 时代：.NET Framework 3.0+ (2006年)
- 特点：XAML声明式UI，强大的样式和动画
- 优势：灵活的UI定制，数据绑定，MVVM模式
### 现代Windows ui平台
#### WinUI 2.x
- UWP应用的UI框架
- 基于XAML
- 只能用于UWP应用
#### WinUI 3
- 独立的UI框架
- 可用于Win32和UWP应用
- 现代的Windows 11风格设计
- 支持.NET和C++
```xml
<!-- WinUI 3 -->
<Grid>
    <Button Content="Modern Button" 
            Style="{StaticResource AccentButtonStyle}"/>
</Grid>
```
## 4. 应用平台层----综合上述的应用---原生
### Win32 Desktop
基于WinForms/WPF和Win32 API

`Win32 API (C++)`
```cc
HWND hwnd = CreateWindow(
    L"BUTTON", L"Click Me",
    WS_TABSTOP | WS_VISIBLE | WS_CHILD | BS_DEFPUSHBUTTON,
    10, 10, 100, 30,
    hWndParent, NULL, hInst, NULL);
```

`WinForms (C#)`
```c#
var button = new Button 
{ 
    Text = "Click Me", 
    Location = new Point(10, 10), 
    Size = new Size(100, 30) 
};
Controls.Add(button);
```

`WPF (XAML + C#)`
```c#
<Button Content="Click Me" Width="100" Height="30" 
        Margin="10" Click="Button_Click"/>
```
### UWP (Universal Windows Platform)----基于win ui 2.x和 WinRT API
```c#
// UWP 示例
public sealed partial class MainPage : Page
{
    public MainPage()
    {
        this.InitializeComponent();
    }
}
```
- 基础：完全基于WinRT API
- UI框架：WinUI 2.x
- 部署：Microsoft Store，.appx包
- 限制：沙盒环境，有功能限制
### WinUI 3
`WinUI 3 (XAML + C#)`
```xml
<Button Content="Click Me" Width="100" Height="30" 
        Margin="10" Click="Button_Click"
        Style="{StaticResource AccentButtonStyle}"/>
````
- 独立的UI框架
- 可用于Win32和UWP应用
- 现代的Windows 11风格设计
- 支持.NET和C++
  
### Windows App SDK
集成：WinUI 3 + 精选WinRT API + Win32兼容
### 从应用到底层的图
```md
┌─────────────────────────────────────────────────┐
│                应用层                            │
├─────────────────┬─────────────────┬─────────────┤
│   Win32 Desktop │       UWP       │ Windows App │
│                 │                 │     SDK     │
├─────────────────┼─────────────────┼─────────────┤
│   WinForms/WPF  │    WinUI 2.x    │  WinUI 3    │
│                 │                 │             │
├─────────────────┼─────────────────┼─────────────┤
│                 │   WinRT API     │             │
│   Win32 API     │                 │             │
│                 │                 │             │
├─────────────────┴─────────────────┴─────────────┤
│              Windows SDK                        │
├─────────────────────────────────────────────────┤
│            Windows 操作系统                      │

```
### 具体区别对比
| 技术 | API基础 | UI框架 | 部署方式 | 系统权限 | 适用场景 |
|------|---------|--------|----------|----------|----------|
| **Win32 + WinForms** | Win32 API | WinForms | 传统安装包 | 完全权限 | 传统桌面应用 |
| **Win32 + WPF** | Win32 API | WPF | 传统安装包 | 完全权限 | 现代桌面应用 |
| **UWP** | WinRT API | WinUI 2.x | Store/.appx | 沙盒限制 | 跨设备应用 |
| **Windows App SDK** | Win32+WinRT | WinUI 3 | 灵活部署 | 可配置 | 统一现代应用 |

## 5. 跨平台web现代框架
### React Native
```js
// React Native 原生移动端
import React from 'react';
import { View, Text, Button } from 'react-native';

const App = () => (
  <View>
    <Text>Hello React Native!</Text>
    <Button title="Click Me" onPress={() => alert('Clicked!')} />
  </View>
);
```
- 定位：主要用于iOS/Android移动应用
- 技术：JavaScript + 原生组件桥接
- 优势：一套代码多平台，Web技术栈

### React Native for Windows (RNW)
```js
// React Native for Windows
import React from 'react';
import { View, Text, Button } from 'react-native';

const WindowsApp = () => (
  <View style={{ flex: 1, justifyContent: 'center' }}>
    <Text style={{ fontSize: 20 }}>Windows Desktop App</Text>
    <Button 
      title="Native Windows Button" 
      onPress={() => console.log('Windows action')} 
    />
  </View>
);
```
- 时代：2021年正式发布
- 基础：基于React Native，但使用WinUI作为渲染层
- 优势：Web开发者易上手，跨平台代码复用
- 劣势：性能不如原生，生态相对较新

### 对上面的总结
```md
┌─────────────────────────────────────────────────────────────────┐
│                        应用开发层                                │
├──────────┬──────────┬──────────┬──────────┬──────────┬──────────┤
│Win32原生 │ .NET桌面 │   UWP    │现代统一  │ 跨平台JS │ 移动端   │
│          │          │          │          │          │          │
│MFC/WTL   │WinForms  │          │Windows   │React     │React     │
│          │WPF       │          │App SDK   │Native    │Native    │
│          │          │          │          │for Win   │          │
├──────────┼──────────┼──────────┼──────────┼──────────┼──────────┤
│          │          │          │ WinUI 3  │          │          │
│          │          │WinUI 2.x │          │          │          │
├──────────┼──────────┼──────────┼──────────┼──────────┼──────────┤
│Win32 API │Win32 API │WinRT API │Win32 +   │WinRT +   │平台原生  │
│          │          │          │WinRT     │Bridge    │API       │
├──────────┴──────────┼──────────┼──────────┼──────────┼──────────┤
│     Windows SDK     │          │          │JavaScript│   各平台 │
│                     │          │          │Runtime   │   SDK    │
├─────────────────────┴──────────┴──────────┴──────────┴──────────┤
│                    操作系统层                                    │
│              Windows / iOS / Android                           │
└─────────────────────────────────────────────────────────────────┘
```

### 全面技术对比表
| 技术框架 | 开发语言 | API基础 | UI特点 | 性能 | 学习难度 | 部署方式 | 跨平台 | 适用场景 |
|----------|----------|---------|---------|------|----------|----------|---------|----------|
| **MFC** | C++ | Win32 | 传统Windows | 极高 | 很难 | 传统安装包 | 否 | 系统级工具 |
| **WTL** | C++ | Win32 | 轻量传统 | 极高 | 难 | 传统安装包 | 否 | 轻量桌面应用 |
| **WinForms** | C# | Win32 | 简单控件 | 高 | 简单 | 传统/.NET | 否 | 快速桌面开发 |
| **WPF** | C#/VB | Win32 | 现代XAML | 高 | 中等 | 传统/.NET | 否 | 现代桌面应用 |
| **UWP** | C#/C++ | WinRT | 现代设计 | 中高 | 中等 | Store/.appx | Windows设备 | 现代商店应用 |
| **WinUI 3** | C#/C++ | Win32+WinRT | 最新设计 | 高 | 中等 | 灵活 | 否 | 现代Windows应用 |
| **Windows App SDK** | C#/C++ | 统一API | WinUI 3 | 高 | 中等 | 灵活 | 否 | 统一现代开发 |
| **React Native** | JavaScript | 各平台原生 | 跨平台组件 | 中 | 中等 | 各平台标准 | 是 | 移动应用 |
| **React Native for Windows** | JavaScript | WinRT+Bridge | 类原生 | 中 | 中等 | 传统/Store | 部分 | 跨平台桌面 |

### 性能对比
