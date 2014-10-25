<properties title="Manage an Azure website" pageTitle="Manage an Azure website" description="Links to resources for managing a Microsoft Azure website." services="web-sites" solutions="web" documentationCenter="" authors="mwasson" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/16/2014" ms.author="mwasson"></tags>

# Azure Web サイトの管理

このトピックには、Azure Web サイトの管理に関するさまざまなリソースへのリンクが含まれています。管理作業には、Web サイトのスムースな運用を維持するためのすべての作業が含まれます。

サイトのライフタイム全体にわたって、初期デプロイから、通常の運用、保守、更新に至るまで、さまざまな管理作業を実行することになります。

-   [サイトを運用環境にデプロイする前][サイトを運用環境にデプロイする前]
-   [Web サイトの運用中][Web サイトの運用中]
-   [Web サイトの更新時][Web サイトの更新時]

Azure ポータルでは、多くの Web サイト管理作業を実行できます。詳しくは、[Azure 管理ポータルによる Web サイトの管理][Azure 管理ポータルによる Web サイトの管理]を参照してください。

## サイトを運用環境にデプロイする前

### レベルの選択

Azure Websites は、Free、Shared、Basic、Standard の 4 種類のレベルで提供されます。それぞれのレベルの機能と料金については、[料金の詳細][料金の詳細]を参照してください。

-   [Web ホスティング プラン][Web ホスティング プラン]により、複数の Web サイトを同じレベルにグループ化できます。
-   Web サイト作成後、いつでも[レベルの切り替え][レベルの切り替え]が可能です。

### 構成

さまざまな構成オプションを設定するには、[Azure 管理ポータル][Azure 管理ポータル]を使用します。詳しくは、[Web サイトの設定方法][Web サイトの設定方法]を参照してください。以下にクイック チェックリストを示します。

-   必要に応じて、.NET、PHP、Java、または Python の**ランタイム バージョン**を選択します。
-   Web サイトで WebSocket プロトコルを使用する場合、**WebSocket** を有効にします。(これには [ASP.NET SignalR][ASP.NET SignalR] や [socket.io][socket.io] を使用するアプリが含まれます。)
-   継続的な Web ジョブを実行していますか?そうであれば、**[常時接続]** を有効にします。
-   index.html など、**既定文書**を設定します。

これらの基本的な設定に加えて、以下の設定もあります。

-   **Secure Socket Layer (SSL)** 暗号化。カスタム ドメイン名で SSL を使用するには、SSL 証明書を取得し、それを使用するように Web サイトを設定する必要があります。[Azure の Web サイトでの HTTPS の有効化][Azure の Web サイトでの HTTPS の有効化]を参照してください。
-   **カスタム ドメイン名。** Web サイトごとに、azurewebsites.net の下にサブドメインが 1 つ自動的に用意されます。contoso.com などのカスタム ドメイン名を関連付けることができます。[カスタム ドメイン名の構成][カスタム ドメイン名の構成]を参照してください。

言語固有の構成:

-   **PHP**:[Azure Websites での PHP の構成方法][Azure Websites での PHP の構成方法]。
-   **Python**:[Azure Websites での Python の構成][Azure Websites での Python の構成]

## Web サイトの運用中

サイトの運用中には、それが利用可能であること、またユーザー トラフィックに対して適切なスケーリングになっていることを確認するとよいでしょう。また、エラーのトラブルシューティングが必要になることがあるかもしれません。

### 監視

-   管理ポータルにより、CPU 使用率やクライアント要求の数などの[パフォーマンス メトリックを追加する][パフォーマンス メトリックを追加する]ことができます。
-   詳細に分析するには、New Relic を使用してパフォーマンスを監視および管理してください。[Azure Websites の New Relic によるアプリケーション パフォーマンス管理][Azure Websites の New Relic によるアプリケーション パフォーマンス管理]を参照してください。
-   トラフィックに応じた[Web サイトのスケーリング][レベルの切り替え]。レベルに応じて、VM の数や VM インスタンスのサイズのスケーリングが可能です。標準プランの場合には、自動スケーリングを設定することができます。その場合、サイトは、固定スケジュールに従って、または負荷に応じて、自動的にスケーリングされます。

### バックアップ

-   Web サイトの[自動バックアップ][自動バックアップ]を設定します。バックアップについては、[このビデオ][このビデオ]を参照してください。
-   Azure SQL データベースの[データベース復旧][データベース復旧]のさまざまなオプションを確認してください。

### トラブルシューティング

-   問題がある場合、クラウドで診断ログおよびライブ デバッグ機能を使用することにより、[Visual Studio でのトラブルシューティング][Visual Studio でのトラブルシューティング]を実行できます。
-   Visual Studio 以外にも、診断ログ収集のためのさまざまな方法があります。[診断ログの有効化: Azure Websites][診断ログの有効化: Azure Websites]を参照してください。
-   Node.js アプリケーションについては、[Azure Websites での Node.js アプリケーションのデバッグ方法][Azure Websites での Node.js アプリケーションのデバッグ方法]を参照してください。

### データの復元

-   以前にバックアップを取った Web サイトを[復元][復元]します。

## Web サイトの更新時

自動バックアップを有効にしていない場合は、[手動バックアップ][自動バックアップ]を作成することができます。

[ステージング デプロイ][ステージング デプロイ]の使用を考慮してください。このオプションを使用すると、運用デプロイと併用実行されるステージング デプロイへ更新を公開することができます。

Visual Studio Online を使用する場合は、ソース管理から継続的デプロイを設定することができます。

-   [Team Foundation バージョン管理 (TFVC) の使用][Team Foundation バージョン管理 (TFVC) の使用]
-   [Git の使用][Git の使用]

<!-- Anchors. -->

  [サイトを運用環境にデプロイする前]: #before-you-deploy-your-site-to-production
  [Web サイトの運用中]: #while-your-website-is-running
  [Web サイトの更新時]: #when-you-update-your-website
  [Azure 管理ポータルによる Web サイトの管理]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-manage/
  [料金の詳細]: http://azure.microsoft.com/ja-jp/pricing/details/websites/
  [Web ホスティング プラン]: http://azure.microsoft.com/ja-jp/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview
  [レベルの切り替え]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-scale/
  [Azure 管理ポータル]: https://manage.windowsazure.com/
  [Web サイトの設定方法]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-configure/
  [ASP.NET SignalR]: http://www.asp.net/signalr
  [socket.io]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-nodejs-chat-app-socketio/
  [Azure の Web サイトでの HTTPS の有効化]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-configure-ssl-certificate/
  [カスタム ドメイン名の構成]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-custom-domain-name/
  [Azure Websites での PHP の構成方法]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-php-configure/
  [Azure Websites での Python の構成]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-python-configure/
  [パフォーマンス メトリックを追加する]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-monitor
  [Azure Websites の New Relic によるアプリケーション パフォーマンス管理]: http://azure.microsoft.com/ja-jp/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/
  [自動バックアップ]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-backup/
  [このビデオ]: http://azure.microsoft.com/ja-jp/documentation/videos/azure-websites-automatic-and-easy-backup/
  [データベース復旧]: http://msdn.microsoft.com/ja-jp/library/azure/hh852669.aspx
  [Visual Studio でのトラブルシューティング]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/#remotedebug
  [診断ログの有効化: Azure Websites]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-enable-diagnostic-log/
  [Azure Websites での Node.js アプリケーションのデバッグ方法]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-nodejs-debug/
  [復元]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-restore/
  [ステージング デプロイ]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-staged-publishing/
  [Team Foundation バージョン管理 (TFVC) の使用]: http://azure.microsoft.com/ja-jp/documentation/articles/cloud-services-continuous-delivery-use-vso/
  [Git の使用]: http://azure.microsoft.com/ja-jp/documentation/articles/cloud-services-continuous-delivery-use-vso-git/
