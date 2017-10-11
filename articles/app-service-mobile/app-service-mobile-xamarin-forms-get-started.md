---
title: "Xamarin.Forms を使用して、モバイル アプリを概要します。"
description: "Xamarin.Forms 開発用モバイル アプリの使用を開始するには、このチュートリアルをに従ってください。"
services: app-service\mobile
documentationcenter: xamarin
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: ee12caaad4095cff6dae3282f747ae804f93db81
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2017
---
# <a name="create-a-xamarinforms-app"></a>Xamarin.Forms アプリの作成
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>概要
このチュートリアルでは、Xamarin.Forms のモバイル アプリをバックエンドとして Azure App Service のモバイル アプリの機能を使用してクラウド ベースのバックエンド サービスを追加する方法を示します。 新しいモバイル アプリのバックエンドと Azure でのアプリ データを格納する単純な to do リスト Xamarin.Forms アプリの両方を作成します。

Xamarin.Forms の他のすべての Azure Mobile Apps のチュートリアルを行う前に、このチュートリアルを完了してください。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料モバイル アプリを入手できます。 このアプリは評価終了後も使用できます。 詳細については、次を参照してください。 [Azure 無料評価版](https://azure.microsoft.com/pricing/free-trial/)です。

* Visual Studio と Xamarin。 詳細については、次を参照してください。、[設定のセットアップと Visual Studio および Xamarin をインストール](https://msdn.microsoft.com/library/mt613162.aspx)ページ。

* Xcode v7.0 以降と Xamarin Studio Community がインストールされた Mac。 については、次を参照してください。[設定のセットアップと Visual Studio および Xamarin をインストール](https://msdn.microsoft.com/library/mt613162.aspx)と[設定を、インストール、および Mac のユーザーのことを確認](https://msdn.microsoft.com/library/mt488770.aspx)(MSDN)。

## <a name="create-a-new-mobile-apps-back-end"></a>新しいモバイル アプリ バックエンドを作成します。

新しいモバイル アプリ バックエンドを作成するには、次の操作を行います。

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

設定されました、モバイル アプリのバックアップをモバイル クライアント アプリケーションが使用する終了します。 次に、単純なタスク一覧のバック エンドのサーバー プロジェクトをダウンロードし、Azure に発行します。

## <a name="configure-the-server-project"></a>サーバー プロジェクトの構成

Node.js または .NET バックエンドを使用するサーバー プロジェクトを構成するのには、次の操作を行います。

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>Xamarin.Forms ソリューションのダウンロードと実行

2 つの方法のいずれかで、ソリューションをダウンロードすることができます。 Mac にダウンロードおよび Xamarin Studio で開きますを Windows コンピューターにダウンロードや iOS アプリを構築するためのネットワーク接続された Mac を使用して、Visual Studio で開きます。 詳細については、次を参照してください。[設定のセットアップと Visual Studio および Xamarin をインストール](https://msdn.microsoft.com/library/mt613162.aspx)です。

Mac または Windows のコンピューターでは、次の操作を行います。

1. [Azure ポータル]にアクセスします。

2. **設定**ブレードで、モバイル アプリの下にある**Mobile**を選択**開始** > **Xamarin.Forms**です。 **手順 3****アプリを新規作成**、し、**ダウンロード**です。

   この操作は、モバイル アプリに接続されているクライアント アプリケーションが含まれるプロジェクトをダウンロードします。 圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

3. ダウンロードしたプロジェクトを抽出し、Xamarin Studio (Mac) または Visual Studio (Windows) を開きします。

   ![Xamarin Studio で抽出されたプロジェクト][9]

   ![Visual Studio で抽出されたプロジェクト][8]

## <a name="optional-run-the-ios-project"></a>(省略可能) iOS プロジェクトの実行
このセクションでは、iOS デバイス向けの Xamarin iOS プロジェクトを実行します。 iOS デバイスを使用していない場合は、このセクションを省略できます。

#### <a name="in-xamarin-studio"></a>Xamarin Studio で使用する
1. IOS プロジェクトを右クリックし、**スタートアップ プロジェクトとして設定**です。

2. **実行**メニューの [**デバッグの開始]**プロジェクトをビルドし、iPhone エミュレーターでアプリを起動します。

#### <a name="in-visual-studio"></a>Visual Studio で使用する
1. IOS プロジェクトを右クリックし、**スタートアップ プロジェクトとして設定**です。

2. **ビルド**メニューの  **Configuration Manager**です。

3. **Configuration Manager**ダイアログ ボックスで、**ビルド**と**展開**iOS プロジェクトの横にあるチェック ボックスです。

4. プロジェクトをビルドし、iPhone エミュレーターでアプリを起動、選択、 **f5 キーを押して**キー。

   > [!NOTE]
   > プロジェクトのビルドに問題がある場合は、NuGet package manager を実行し、Xamarin のサポート パッケージの最新バージョンに更新します。 クイック スタート プロジェクトは、最新バージョンに更新に時間がかかる可能性があります。    
   >
   >

5. アプリなどの入力、意味のあるテキスト*学習 Xamarin*、プラス記号をクリックして (**+**)。

    ![][10]

    この操作は、Azure でホストされている新しいモバイル アプリのバック エンドに post 要求を送信します。 要求のデータは TodoItem テーブルに挿入されます。 テーブルに格納されている項目は、モバイル アプリ バックエンドによって返され、データが一覧に表示されます。

    > [!NOTE]
    > ソリューションのポータブル クラス ライブラリ プロジェクトの TodoItemManager.cs c# ファイルで、モバイル アプリのバック エンドにアクセスするコードがあります。
    >
    >

## <a name="optional-run-the-android-project"></a>(省略可能) Android プロジェクトの実行
このセクションでは、Android 用 Xamarin droid プロジェクトを実行します。 Android デバイスを使用していない場合は、このセクションを省略できます。

#### <a name="in-xamarin-studio"></a>Xamarin Studio で使用する

1. Android プロジェクトを右クリックし、**スタートアップ プロジェクトとして設定**です。

2. プロジェクトをビルドし、、Android エミュレーターでアプリを起動する、**実行**メニューの [**デバッグの開始]**です。

#### <a name="in-visual-studio"></a>Visual Studio で使用する

1. Android (Droid) プロジェクトを右クリックし、**スタートアップ プロジェクトとして設定**です。

2. **ビルド**メニューの  **Configuration Manager**です。

3. **Configuration Manager**ダイアログ ボックスで、**ビルド**と**展開**Android プロジェクトの横にあるチェック ボックスです。

4. プロジェクトをビルドし、Android エミュレーターでアプリを起動、選択、 **f5 キーを押して**キー。

   > [!NOTE]
   > プロジェクトのビルドに問題がある場合は、NuGet package manager を実行し、Xamarin のサポート パッケージの最新バージョンに更新します。 クイック スタート プロジェクトは、最新バージョンに更新に時間がかかる可能性があります。    
   >
   >

5. アプリなどの入力、意味のあるテキスト*学習 Xamarin*、プラス記号をクリックして (**+**)。

    ![][11]
    
    この操作は、Azure でホストされている新しいモバイル アプリのバック エンドに post 要求を送信します。 要求のデータは TodoItem テーブルに挿入されます。 テーブルに格納されている項目は、モバイル アプリ バックエンドによって返され、データが一覧に表示されます。
    
    > [!NOTE]
    > ソリューションのポータブル クラス ライブラリ プロジェクトの TodoItemManager.cs c# ファイルで、モバイル アプリのバック エンドにアクセスするコードがあります。
    >
    >

## <a name="optional-run-the-windows-project"></a>(省略可能) Windows プロジェクトの実行

このセクションでは、Windows デバイス用 Xamarin WinApp プロジェクトを実行します。 Windows デバイスを使用していない場合は、このセクションを省略できます。

#### <a name="in-visual-studio"></a>Visual Studio で使用する

1. 任意の Windows プロジェクトを右クリックし、**スタートアップ プロジェクトとして設定**です。

2. **ビルド**メニューの  **Configuration Manager**です。

3. **Configuration Manager**ダイアログ ボックスで、**ビルド**と**展開**ことを選択するには Windows プロジェクトの横にあるチェック ボックスです。

4. プロジェクトをビルドし、Windows エミュレーターでアプリを起動、選択、 **f5 キーを押して**キー。

   > [!NOTE]
   > プロジェクトのビルドに問題がある場合は、NuGet package manager を実行し、Xamarin のサポート パッケージの最新バージョンに更新します。 クイック スタート プロジェクトは、最新バージョンに更新に時間がかかる可能性があります。    
   >
   >

5. アプリなどの入力、意味のあるテキスト*学習 Xamarin*、プラス記号をクリックして (**+**)。

    この操作は、Azure でホストされている新しいモバイル アプリのバック エンドに post 要求を送信します。 要求のデータは TodoItem テーブルに挿入されます。 テーブルに格納されている項目は、モバイル アプリ バックエンドによって返され、データが一覧に表示されます。
    
    ![][12]
    
    > [!NOTE]
    > ソリューションのポータブル クラス ライブラリ プロジェクトの TodoItemManager.cs c# ファイルで、モバイル アプリのバック エンドにアクセスするコードがあります。
    >
    >

## <a name="next-steps"></a>次のステップ

* [アプリへの認証の追加](app-service-mobile-xamarin-forms-get-started-users.md)  
  ID プロバイダーを使用してアプリのユーザーを認証する方法について説明します。

* [アプリへのプッシュ通知の追加](app-service-mobile-xamarin-forms-get-started-push.md)  
  アプリにプッシュ通知のサポートを追加して、Azure Notification Hubs を使用して、プッシュ通知を送信する、モバイル アプリのバック エンドを構成する方法を説明します。

* [アプリのオフライン同期の有効化](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Mobile Apps バックエンドを使用してオフライン サポートをアプリに追加する方法について説明します。 オフラインの同期には、表示し、追加、またはネットワーク接続がない場合でも、モバイル アプリのデータを変更することができます。

* [モバイル アプリのマネージ クライアントを使用します。](app-service-mobile-dotnet-how-to-use-client-library.md)  
  Xamarin アプリでの管理されたクライアント SDK の操作方法について説明します。

<!-- Anchors. -->
[Get started with Mobile Apps back ends]:#getting-started
[Create a new Mobile Apps back end]:#create-new-service
[Next steps]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure ポータル]: https://portal.azure.com/
