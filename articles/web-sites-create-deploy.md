<properties linkid="manage-services-how-to-create-websites" urlDisplayName="How to create" pageTitle="How to create websites - Azure service management" metaKeywords="Azure creating website, Azure deleting website" description="Learn how to create a website using the Azure Management Portal." metaCanonical="" services="web-sites" documentationCenter="" title="How to Create and Deploy a Website" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Web サイトを作成する方法

このトピックでは、管理ポータルを使用してギャラリーから Web サイトを作成する方法について説明します。

作成した Azure Web サイトにコンテンツをデプロイする方法については、「[Azure Web サイト][Azure Web サイト]」の「**デプロイ**」を参照してください。

## 目次

-   [方法: 管理ポータルの使用による Web サイトの作成][方法: 管理ポータルの使用による Web サイトの作成]
-   [方法: ギャラリーからの Web サイトの作成][方法: ギャラリーからの Web サイトの作成]
-   [方法: Web サイトの削除][方法: Web サイトの削除]
-   [次のステップ][次のステップ]

## <a name="createawebsiteportal"></a>方法: 管理ポータルの使用による Web サイトの作成

Azure で Web サイトを作成するには、次の手順に従います。

1.  [Azure の管理ポータル][Azure の管理ポータル]にログインします。

2.  管理ポータルの左下にある **[新規作成]** アイコンをクリックします。

3.  **[Web サイト]** アイコン、**[簡易作成]** アイコンの順にクリックし、URL の値を入力して、ページの右下にある **[Web サイトの作成]** チェック マークをクリックします。

4.  Web サイトが作成されると、**"Web サイト \<*サイト名*\> の作成に成功しました"** というテキストが表示されます。ポータル ページの下部にある **[参照]** をクリックすると、Web サイトを参照できます。

5.  ポータルで、Web サイトの一覧に表示されている Web サイトの名前をクリックして、Web サイトの **[クイック スタート]** 管理ページを開きます。

6.  **[クイック スタート]** ページには、Web サイト開発ツールの入手、Web サイトの発行の設定、TFS や Git のようなソース管理プロバイダーからのデプロイの設定を行うためのオプションがあります。FTP 発行は Web サイトに対して既定で設定されており、**[ダッシュボード]** ページの **[概要]** セクションの **[FTP ホスト名]** に FTP ホスト名が表示されます。FTP または Git を使って発行する前に、**[ダッシュボード]** ページで **[展開資格情報のリセット]** を選択して、Web サイトにコンテンツを展開するときに FTP ホストまたは Git リポジトリに対して認証できるようにします。

7.  **[構成]** 管理ページでは、次のカテゴリについて Web サイトの設定が表示されます。

-   **[全般]**: Web アプリケーションに必要な .NET Framework または PHP のバージョンを設定します。標準モードのサイトでは、**[プラットフォーム]** オプションで 32 ビットまたは 64 ビット環境を選択できます。

-   **[証明書]**: 標準モードでは、カスタム ドメインの SSL 証明書をアップロードできます。

-   **[ドメイン名]**: Shared モードと Standard モードでは、Web サイトのカスタム ドメイン名を表示して管理できます。

-   **[SSL バインド]**: Standard モードでは、IP ベースまたは SNI ベースの SSL を使用してカスタム ドメイン名に SSL 証明書を割り当てることができます。

-   **[デプロイ]**: ソース管理からのデプロイを設定すると、ここでデプロイを構成できます。

-   **[アプリケーション診断]**: トレースを使ってインストルメント化された Web アプリケーションから診断トレースを収集するためのオプションを設定します。

-   **[サイト診断]**: Web サイトの診断情報を収集するためのログ記録オプションを設定します。オプションには、[Web サーバーのログ記録]、[詳細なエラー メッセージ]、[失敗した要求トレース] があります。

-   **[監視]**: Standard モードの Web サイトでは、この機能を使用して HTTP または HTTPS エンドポイントの可用性をテストすることができます。

-   **[アプリ設定]**: 起動時に Web アプリケーションによって読み込まれる名前と値のペアを指定します。.NET サイトの場合、実行時にこれらの設定が .NET 構成 **AppSettings** に挿入され、既存の設定がオーバーライドされます。PHP サイトおよび Node サイトの場合、実行時にこれらの設定が環境変数として使用できるようになります。

-   **[接続文字列]**: 接続文字列を表示して編集します。.NET サイトの場合、実行時にこれらの接続文字列が .NET 構成 **connectionStrings** 設定に挿入され、リンクされたデータベース名とキーが等しい既存のエントリがオーバーライドされます。PHP サイトおよび Node サイトの場合、実行時にこれらの設定が環境変数として使用できるようになります。

-   **[既定のドキュメント]**: Web サイトの既定のドキュメントとは、ユーザーが Web サイトに移動したときに既定で表示されるページです。Web アプリケーションの既定のドキュメントがこの一覧になければ追加します。Web サイトの既定のドキュメントは、一覧の先頭に位置している必要があります。

-   **[ハンドラー マッピング]**: \*.php などの特定のファイル拡張子に対する要求を処理するスクリプト プロセッサを指定します。

## <a name="howtocreatefromgallery"></a>方法: ギャラリーからの Web サイトの作成

[WACOM.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

## <a name="deleteawebsite"></a>方法: Web サイトの削除

Web サイトは、Azure の管理ポータルの **[削除]** アイコンを使用して削除します。**[削除]** アイコンは、Azure ポータル (**[Web サイト]** をクリックして Web サイトをすべて表示したとき) と各 Web サイトの管理ページの下部に表示されます。

## <a name="nextsteps"></a> 次のステップ

詳細については、「[Azure の Web サイト][Azure の Web サイト]」を参照してください。

  [Azure Web サイト]: /ja-jp/documentation/services/web-sites/
  [方法: 管理ポータルの使用による Web サイトの作成]: #createawebsiteportal
  [方法: ギャラリーからの Web サイトの作成]: #howtocreatefromgallery
  [方法: Web サイトの削除]: #deleteawebsite
  [次のステップ]: #nextsteps
  [Azure の管理ポータル]: http://manage.windowsazure.com/
  [Azure の Web サイト]: /ja-jp/documentation/services/web-sites/
