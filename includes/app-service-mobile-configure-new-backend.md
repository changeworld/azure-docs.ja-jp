---
title: インクルード ファイル
description: インクルード ファイル
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: 3fe571a5e0b10da3c253bca233aad1e16d2ad852
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235968"
---
1. 次のプラットフォームのためのクライアント SDK クイック スタートをダウンロードします。
    
    [iOS (Objective-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS) [iOS (Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift) [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android) [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS) [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android) [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms) [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova) [Windows (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)
        
2. Azure Mobile Apps は .NET および Node バックエンド SDK をサポートしています。 アプリの種類によっては、オープン ソース リポジトリの [.NET](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet) または [Node](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/node) プロジェクトをダウンロードしてください。

3. データベース接続を追加するか、または既存の接続に接続する必要があります。 まず、データ ストアを作成するか、または既存のデータ ストアを使用するかを決定します。

4. **新しいデータ ストアを作成する**: データ ストアを作成する場合は、次のクイック スタートを使用します。

    [クイック スタート:Azure SQL Database の単一データベースの概要](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

5. **既存のデータ ソース**: 既存のデータベース接続を使用する場合は、次の手順に従います。
    1. SQL Database の接続文字列の形式 - `Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`
      
       **{your_SQLServer}** サーバーの名前。これは、データベースの概要ページで見つけることができ、通常は "server_name.database.windows.net" の形式になっています。
        **{ポート}** 通常は 1433 です。
        **{your_catalogue}** データベースの名前。
        **{your_username}** データベースにアクセスするユーザー名。
        **{your_password}** データベースにアクセスするためのパスワード。
        
        [SQL 接続文字列の形式](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)の詳細を確認してください。

    2. **モバイル アプリ**に接続文字列を追加します。App Service では、メニューの **[構成]** オプションを使用して、アプリケーションの接続文字列を管理できます。

        接続文字列を追加するには:

        1. **[アプリケーションの設定]** タブをクリックします。

        2. **[[+] New connection string] ([+] 新しい接続文字列)** をクリックします。

        3. 接続文字列の **[名前]** 、 **[値]** 、および **[種類]** を指定する必要があります。

        4. **[名前]** を「`MS_TableConnectionString`」と入力します。

        5. [値] は、前の手順で作成した接続文字列にしてください。

        6. SQL Azure データベースに接続文字列を追加している場合は、 **[種類]** の下の **[SQLAzure]** を選択します。        
