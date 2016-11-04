---
title: Visual Studio 接続済みサービスを使用して Mobile Services を追加したことによる .NET プロジェクトへの影響 | Microsoft Docs
description: '接続済みサービスを使用して Azure Mobile Services を追加したことによる Visual Studio .NET プロジェクトへの影響について説明します '
services: mobile-services
documentationcenter: ''
author: mlhoop
manager: douge
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/21/2016
ms.author: mlearned

---
# 接続済みサービスを使用して Azure Mobile Services を追加したことによる Visual Studio .NET プロジェクトへの影響
[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

## リファレンスの追加
プロジェクトに Azure Mobile Services の Nuget パッケージが追加されました。その結果、次の .NET 参照がプロジェクトに追加されました。

* **Microsoft.WindowsAzure.Mobile**
* **Microsoft.WindowsAzure.Mobile.Ext**
* **Newtonsoft.Json**
* **System.Net.Http.Extensions**
* **System.Net.Http.Primitives**

## Mobile Services 用の接続文字列の値
選択したモバイル サービスのアプリケーション URL とアプリケーション キーを使用して、App.xaml.cs ファイル内に **MobileServiceClient** オブジェクトが作成されました。

## Mobile Services プロジェクトの追加
Connected Service Provider に .NET モバイル サービスが作成されている場合、モバイル サービス プロジェクトはソリューションに追加されています。

[モバイル サービスの詳細を確認する](https://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=AcomDC_0727_2016-->