---
title: Xamarin.iOS アプリで開始する
description: このチュートリアルに従って、Xamarin.iOS 開発用の Azure Mobile Apps を使用します。
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 6c35189e7c841fa2724f1cfe84afc689d5510676
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77459006"
---
# <a name="create-a-xamarinios-app"></a>Xamarin.iOS アプリを作成する
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>概要
このチュートリアルでは、Azure Mobile Apps バックエンドを使用して Xamarin.iOS モバイル アプリにクラウドベースのバックエンド サービスを追加する方法を示します。  新しいモバイル アプリ バックエンドと、アプリのデータを Azure に保存する簡単な *Todo list* Xamarin.iOS アプリの両方を作成します。

このチュートリアルは、Azure App Service での Mobile Apps 機能の使用に関する他のすべての Xamarin.iOS チュートリアルを実行する前に完了しておく必要があります。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 アカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料モバイル アプリを入手します。このアプリは評価終了後も使用できます。 詳細については、「[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)」を参照してください。
* Visual Studio for Mac。 [Visual Studio for Mac のセットアップとインストール](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)に関するページを参照してください
* Mac と Xcode 9.0 以降。
  
## <a name="create-an-azure-mobile-app-backend"></a>Azure モバイル アプリ バックエンドの作成
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>データベース接続を作成し、クライアントとサーバー プロジェクトを構成する
[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinios-app"></a>Xamarin.iOS アプリを実行する
1. Xamarin.iOS プロジェクトを開きます。

2. [Azure portal](https://portal.azure.com/) に移動し、作成したモバイル アプリに移動します。 `Overview` ブレードで、モバイル アプリのパブリック エンドポイントである URL を探します。 例 - アプリ名 "test123" のサイト名は https://test123.azurewebsites.net になります。

3. xamarin.iOS/ZUMOAPPNAME フォルダーの `QSTodoService.cs` ファイルを開きます。 アプリケーション名は `ZUMOAPPNAME` です。

4. `QSTodoService` クラスで、`ZUMOAPPURL` 変数を上のパブリック エンドポイントに置き換えます。

    `const string applicationURL = @"ZUMOAPPURL";`

    →
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. F5 キーを押して、iPhone エミュレーターでアプリをデプロイおよび実行します。

6. アプリで、意味のあるテキスト (たとえば、"*チュートリアルの完了*") を入力し、[+] ボタンをクリックします。

    ![][10]

    要求のデータは TodoItem テーブルに挿入されます。 テーブルに格納された項目がモバイル アプリ バックエンドによって返され、データが一覧に表示されます。

   > [!NOTE]
   > モバイル アプリ バックエンドにアクセスしてデータのクエリと挿入を行うコードを確認できます (ToDoActivity.cs C# ファイルにあります)。
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png
