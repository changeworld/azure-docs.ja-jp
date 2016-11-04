---
title: Cordova プロジェクトへの影響 (Visual Studio 接続済みサービス) | Microsoft Docs
description: 'Visual Studio 接続済みサービスを使用して Azure Mobile Services を追加したことによる Azure Cordova プロジェクトへの影響について説明します '
services: mobile-services
documentationcenter: na
author: mlhoop
manager: douge
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 07/21/2016
ms.author: mlearned

---
# Visual Studio 接続済みサービスを使用して Azure Mobile Services を追加したことによる Azure Cordova プロジェクトへの影響
## リファレンスの追加
すべてのマルチデバイス ハイブリッド アプリケーションに含まれる Azure Mobile Service のクライアント プラグインが有効になりました。

## Mobile Services 用の接続文字列の値
`services\mobileServices\settings` に新しい JavaScript (.js) ファイルが生成されました。このファイルには、選択したモバイル サービスのアプリケーション URL とアプリケーション キーを格納した **MobileServiceClient** が含まれています。このファイルには、次のようなモバイル サービス クライアント オブジェクトの初期化コードが含まれています。

    var mobileServiceClient;
    document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
            "<your mobile service name>.azure-mobile.net",
            "<insert your key>"
        );

[モバイル サービスの詳細を確認する](https://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=AcomDC_0727_2016-->