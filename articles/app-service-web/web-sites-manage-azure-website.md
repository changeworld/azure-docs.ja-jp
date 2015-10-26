<properties 
	pageTitle="Azure App Service での Web アプリの管理" 
	description="Azure App Service で Web アプリを管理するためのリソースへリンクしています。" 
	services="app-service\web" 
	documentationCenter="" 
	authors="erikre" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/31/2015" 
	ms.author="erikre"/>

# Azure App Service での Web アプリの管理

このトピックには、[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) の Web アプリの管理に関するさまざまなリソースへのリンクが含まれています。管理作業には、Web アプリのスムースな運用を維持するためのすべての作業が含まれます。

Web アプリのライフタイム全体にわたって、初期デプロイから、通常の運用、保守、更新に至るまで、さまざまな管理作業を実行することになります。

Azure ポータルでは、多くの Web アプリ管理作業を実行できます。

## Web アプリを運用環境にデプロイする前

### レベルの選択

Azure App Service には、 Free、Shared、Basic、Standard、および Premium の 5 つのレベルが用意されています。それぞれのレベルの機能と料金については、[料金の詳細](/pricing/details/app-service/)を参照してください。

- [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)により、複数の Web アプリを同じレベルにグループ化できます。
- Web アプリ作成後、いつでも[レベルの切り替え](web-sites-scale.md)が可能です。

### 構成

さまざまな構成オプションを設定するには、[Azure プレビュー ポータル](https://portal.azure.com/)を使用します。詳細については、「[Azure App Service での Web アプリの構成](web-sites-configure.md)」を参照してください。以下にクイック チェックリストを示します。

- 必要に応じて、.NET、PHP、Java、または Python の**ランタイム バージョン**を選択します。
- Web アプリで WebSocket プロトコルを使用する場合、**WebSockets** を有効にします。(これには [ASP.NET SignalR](http://www.asp.net/signalr) や [socket.io](web-sites-nodejs-chat-app-socketio.md) を使用するアプリが含まれます。)
- 継続的な Web ジョブを実行していますか? そうであれば、**[常時接続]** を有効にします。
- index.html など、**既定文書**を設定します。

これらの基本的な設定に加えて、以下の設定もあります。

- **Secure Socket Layer (SSL)** 暗号化。カスタム ドメイン名で SSL を使用するには、SSL 証明書を取得し、それを使用するように Web アプリを設定する必要があります。「[Azure App Service での Web アプリの HTTPS の有効化](web-sites-configure-ssl-certificate.md)」を参照してください。
- **カスタム ドメイン名。** Web アプリは、自動的に azurewebsites.net 下のサブドメインを持ちます。contoso.com などのカスタム ドメイン名を関連付けることができます。「[Azure App Service でのカスタム ドメイン名の構成](web-sites-custom-domain-name.md)」を参照してください。

言語固有の構成:

- **PHP**: 「[Azure App Service Web Apps での PHP の構成](web-sites-php-configure.md)」
- **Python**: 「[Azure App Service Web Apps による Python の構成](web-sites-python-configure.md)」


## Web アプリの運用中

Web アプリの運用中、それが利用可能であり、ユーザー トラフィックに対して適切なスケーリングになっていることを確認する必要があります。また、エラーのトラブルシューティングが必要になることがあるかもしれません。

### Monitoring

- Azure プレビュー ポータルにより、CPU 使用率やクライアント要求の数などの[パフォーマンス メトリックを追加する](web-sites-monitor.md)ことができます。
- 詳細に分析するには、New Relic を使用してパフォーマンスを監視および管理してください。「[Azure App Service の .NET Web アプリに対する New Relic によるパフォーマンス管理](store-new-relic-web-sites-dotnet-application-performance-management.md)」を参照してください。
- トラフィックに応じた [Web アプリのスケーリング](web-sites-scale.md)。レベルに応じて、VM の数や VM インスタンスのサイズのスケーリングが可能です。 Standard および Premium レベルの場合には、自動スケーリングを設定することができます。その場合、Web アプリは、固定スケジュールに従って、または負荷に応じて、自動的にスケーリングされます。  
 
### バックアップ

- Web アプリの[自動バックアップ](web-sites-backup.md)を設定します。バックアップについては、[このビデオ](http://azure.microsoft.com/documentation/videos/azure-websites-automatic-and-easy-backup/)を参照してください。
- Azure SQL データベースの[データベース復旧](http://msdn.microsoft.com/library/azure/hh852669.aspx)のさまざまなオプションを確認してください。

### トラブルシューティング

- 問題がある場合、クラウドで診断ログおよびライブ デバッグ機能を使用することにより、[Visual Studio でのトラブルシューティング](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)を実行できます。 
- Visual Studio 以外にも、診断ログ収集のためのさまざまな方法があります。「[Azure App Service での Web アプリの診断ログの有効化](web-sites-enable-diagnostic-log.md)」を参照してください。
- Node.js アプリケーションについては、「[Azure App Service での Node.js Web アプリのデバッグ方法](web-sites-nodejs-debug.md)」を参照してください。

### データの復元

- 以前にバックアップを取った Web アプリを[復元](web-sites-restore.md)します。


## Web アプリの更新時

自動バックアップを有効にしていない場合は、[手動バックアップ](web-sites-backup.md)を作成することができます。

[ステージング デプロイ](web-sites-staged-publishing.md)の使用を考慮してください。このオプションを使用すると、運用デプロイと併用実行されるステージング デプロイへ更新を公開することができます。

Visual Studio Online を使用する場合は、ソース管理から継続的デプロイを設定することができます。

- [Team Foundation バージョン管理 (TFVC) の使用](../cloud-services-continuous-delivery-use-vso.md) 
- [Git の使用](../cloud-services-continuous-delivery-use-vso-git.md)
 
[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
<!-- Anchors. -->


[Before you deploy your site to production]: #before-you-deploy-your-site-to-production
[While your website is running]: #while-your-website-is-running
[When you update your website]: #when-you-update-your-website

  

<!---HONumber=Oct15_HO3-->