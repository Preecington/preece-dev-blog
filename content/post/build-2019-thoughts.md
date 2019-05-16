---
title: "Microsoft Build 2019 - Thoughts"
date: 2019-05-15T17:16:31+10:00
tags:
- Thoughts
- Microsoft
- .Net
thumbnailImage: /img/20190506_222905861_iOS.jpg
summary: "Last week I attended my fifth Build (in a row! 🤯). The conference and the company behind it has undergone a lot of change since 2015 when I first went, and I wanted to jot down my thoughts on the stuff I learned and the direction I think Microsoft is heading."
---
Last week I attended my fifth Build (in a row! 🤯). The conference and the company behind it has undergone a lot of change since 2015 when I first went, and I wanted to jot down my thoughts on the stuff I learned and the direction I think Microsoft is heading.

![Build 2019 Session](/img/20190506_222905861_iOS.jpg)

## What's Hot

My number one take away this year is that Artificial Intelligence and Machine Learning has really matured to a point where I should take it more seriously. My reaction last year to ML/AI was a firm "meh" as it seemed be either "call an API to see if you recognise my dog" to full on data-scientist mathematical machinations. [Projects like ML.NET](https://github.com/dotnet/machinelearning) have me thinking there is a middle ground for doing more engineering working in this space, which is exciting! I want to start thinking more about how I could use this sort of tech in a practical sense.

There were also some goodies for the Desktop App developer inside of me, including the quiet release of [MSIX](https://github.com/Microsoft/msix-packaging#msix-sdk) (sane installers for Windows 10 with auto updaters, but also works on Windows 7 and Android) with [Application Packaging Projects](https://blogs.windows.com/buildingapps/2017/12/04/extend-desktop-application-windows-10-features-using-new-visual-studio-application-packaging-project/#lq9eAJjU12J6eOgR.97) , XAML Hot Reloading support for WPF/UWP/Xamarin Forms and a [complete rewrite of React Native for Windows to be in C++](https://github.com/microsoft/react-native-windows/tree/master/vnext#reactnative-for-windows-vnext).

I also took a chance this week to play with compiling the [new Windows Terminal](https://github.com/Microsoft/Terminal). It's still absolutely early days but I'm going to be keeping a close eye on this one - it looks like the team are going to nail a modern terminal experience on Windows. 🤞

Other things I found cool that I'll be looking more into:

* [Unified YAML CI/CD Pipelines in Azure DevOps](https://devblogs.microsoft.com/devops/whats-new-with-azure-pipelines/)
* [Fluid Framework](https://www.microsoft.com/en-us/microsoft-365/blog/2019/05/06/build-2019-people-centered-experiences-microsoft-365-productivity-cloud/)
* [Try.NET](https://dotnet.microsoft.com/platform/try-dotnet)
* [PowerToys](https://github.com/microsoft/PowerToys) - Yup, they're back!
* C# 8 [Nullable Reference Types, Async Streams (IAsyncEnumerables)](https://devblogs.microsoft.com/dotnet/take-c-8-0-for-a-spin/) and [Default Interface Interfaces](https://devblogs.microsoft.com/dotnet/default-implementations-in-interfaces/)
* [.NET 5](https://devblogs.microsoft.com/dotnet/introducing-net-5/)
* [gRPC in ASP.NET Core 3](https://docs.microsoft.com/en-us/aspnet/core/grpc/?view=aspnetcore-3.0)

So, nothing revolutionary this year, but certainly a lot to sink my teeth into.

## Direction

At Build 2015 I was firmly a Windows/.NET developer (and devotee) clutching my Windows Phone in hand and dreaming of a UWP future.

Well. We all know how that turned out. 😅

Microsoft is still invested in making Windows an awesome place to develop, and .NET to be the platform to do anything on. But the "consumer Windows" idea that was so prevalent in 2015 is long gone. UWP has been replaced by "Windows Apps" that bring Win32, WPF and WinForms back into the fold. I'm no more sure about the mobile direction that Microsoft want to follow - I heard more about Blazor then Xamarin at Build, for instance. IoT Core still seems to be an afterthought.

The Microsoft in 2019 instead wants you to use whatever language and tech you think is best, with Azure providing all the smarts. As for where that leaves a C# developer like me - that's something I'll have to ponder a bit more! 🤔