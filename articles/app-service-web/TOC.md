# 概要    
## [Web Apps について](app-service-web-overview.md)
## [ホスティング オプションの比較](choose-web-site-cloud-service-vm.md)

# クイック スタート    
## [静的な HTML サイトの作成](app-service-web-get-started-html.md)
## [ASP.NET アプリの作成](app-service-web-get-started-dotnet.md)        
## [PHP アプリの作成](app-service-web-get-started-php.md) 
## [Node.js アプリの作成](app-service-web-get-started-nodejs.md) 
## [Java アプリの作成](app-service-web-get-started-java.md)        
## [Python アプリの作成](app-service-web-get-started-python.md)    

# サンプル
## [Azure CLI](app-service-cli-samples.md) 
## [PowerShell](app-service-powershell-samples.md) 

# Tutorials (チュートリアル)

# 概念
## [App Service の特長](../app-service/app-service-how-works-readme.md?toc=%2fazure%2fapp-service-web%2ftoc.json)    
## [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md?toc=%2fazure%2fapp-service-web%2ftoc.json)    
## [App Service 環境](app-service-app-service-environment-intro.md)
## [認証と権限承認](../app-service/app-service-authentication-overview.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
## [オンプレミスの AD による認証](web-sites-authentication-authorization.md)


# ハウツー ガイド
## アプリの開発    
### ASP.NET
#### [SQL データベースを使用した ASP.NET アプリ](web-sites-dotnet-get-started.md)
#### [VS Code による ASP.NET Core アプリの開発](web-sites-create-web-app-using-vscode.md)
### PHP
#### [MySQL を使用した Laravel アプリ](app-service-web-php-get-started.md)
#### [PHP プロジェクトの設定](web-sites-php-configure.md)
#### [WordPress マルチサイトの設定](web-sites-php-convert-wordpress-multisite.md) 
### Node.js
#### [NOSQL DB を使用した Sails.js アプリ](app-service-web-nodejs-sails.md)
#### [io.js の使用](web-sites-nodejs-iojs.md)
#### [Node.js アプリのデバッグ](web-sites-nodejs-debug.md)
### Java
#### [Eclipse を使用した Java アプリ](app-service-web-eclipse-create-hello-world-web-app.md)
#### [IntelliJ を使用した Java アプリ](app-service-web-intellij-create-hello-world-web-app.md)
#### [Azure SDK for Java の使用](java-create-azure-website-using-java-sdk.md)
#### [既存のアプリのアップロード](web-sites-java-add-app.md)
#### [Eclipse のリモート デバッグ](app-service-web-debug-java-web-app-in-eclipse.md)
#### [IntelliJ のリモート デバッグ](app-service-web-debug-java-web-app-in-intellij.md)
### Python
#### [MySQL を使用した Django アプリ](web-sites-python-ptvs-django-mysql.md)

### [SendGrid を使用した電子メールの送信](sendgrid-dotnet-how-to-send-email.md)

### ランタイムの構成    
#### [Windows の PHP](web-sites-php-configure.md)

#### [Java](web-sites-java-add-app.md)
#### [Linux の Node.js](app-service-linux-using-nodejs-pm2.md)
#### [Python](web-sites-python-configure.md)
            
### アプリケーションの構成
#### [アプリ設定の使用](web-sites-configure.md)
            
## [Azure へのデプロイ](web-sites-deploy.md)
### [FTP 経由でのデプロイ](app-service-deploy-content-sync.md)
### [クラウドの同期を使用したデプロイ](web-sites-deploy.md)
### [継続的なデプロイ](app-service-continuous-deployment.md)
### [ステージングへのデプロイ](web-sites-staged-publishing.md)
### [ローカル Git からのデプロイ](app-service-deploy-local-git.md)
### [テンプレートを使用したデプロイ](app-service-deploy-complex-application-predictably.md)
### [アジャイル デプロイ](app-service-agile-software-development.md)
### [ベータ テスト](app-service-web-test-in-production-controlled-test-flight.md)

### [デプロイ資格情報の設定](app-service-deployment-credentials.md)

### カスタム ドメインのマップ
#### [ドメインの購入](custom-dns-web-site-buydomains-web-app.md)
#### [サード パーティのドメインのマップ](web-sites-custom-domain-name.md)
#### [Traffic Manager を使用したドメインのマップ](web-sites-traffic-manager-custom-domain-name.md)
#### [GoDaddy ドメインのマップ](web-sites-godaddy-custom-domain-name.md)
#### [アクティブなドメインの移行](app-service-custom-domain-name-migrate.md)

### [IIS から移行](web-sites-migration-from-iis-server.md)
### [運用環境におけるテスト](app-service-web-test-in-production-get-start.md)
### [Web アプリへの機能の追加](app-service-web-get-started-2.md)

## DB/リソースへの接続        

### [オンプレミスのデータに接続する](web-sites-hybrid-connection-get-started.md) 
### [Azure VNet に接続する](web-sites-integrate-with-vnet.md)
### [PowerShell を使用して Azure VNet に接続する](app-service-vnet-integration-powershell.md)
### [Azure VM 上の MongoDB に接続する](web-sites-dotnet-store-data-mongodb-vm.md)
            
##アプリのセキュリティ保護
### ユーザーを認証する        
#### [Azure AD による認証](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [Facebook による認証](../app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [Google による認証](../app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [Microsoft アカウントによる認証](../app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [Twitter による認証](../app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [オンプレミスの AD による認証](web-sites-authentication-authorization.md)
#### [マルチテナント データベースを使用したアプリ](web-sites-dotnet-entity-framework-row-level-security.md)
### カスタム SSL の割り当て
#### [SSL 証明書の購入](web-sites-purchase-ssl-web-site.md)
#### [サード パーティの SSL 証明書の構成](web-sites-configure-ssl-certificate.md)
### [HTTPS の適用](web-sites-configure-ssl-certificate.md#enforce-https-on-your-app)
### [TLS 相互認証の構成](app-service-web-configure-tls-mutual-auth.md)
            
##アプリのスケール        
### [スケールアップ](web-sites-scale.md)
### [スケールアウト](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
### [Traffic Manager による負荷分散](web-sites-traffic-manager.md)
### [App Service Environment による高スケール](../app-service/app-service-app-service-environments-readme.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
### [グローバルな展開に向けた Azure CDN の使用](cdn-websites-with-cdn.md)
### [Memcache 経由での Redis Cache への接続](web-sites-connect-to-redis-using-memcache-protocol.md)
### [Redis Cache の作成](../redis-cache/cache-redis-cache-arm-provision.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
### [Azure Redis Cache でセッション状態を管理する](web-sites-dotnet-session-state-caching.md)

## 監視 
### [アプリの監視](web-sites-monitor.md)
### [ログの有効化](web-sites-enable-diagnostic-log.md)
### [ログのストリーミング](web-sites-streaming-logs-and-console.md)

## コンテンツのバックアップ        
### [アプリのバックアップ](web-sites-backup.md)
### [バックアップからのアプリの復元](web-sites-restore.md)
### [REST API を使用したバックアップ](websites-csm-backup.md)

## アプリのリソースの管理
### [PowerShell を使用したアプリの複製](app-service-web-app-cloning.md)
### [ポータルを使用したアプリの複製](app-service-web-app-cloning-portal.md)
### [リソースの移動](app-service-move-resources.md)
### [PowerShell で Azure Resource Manager を使用する](app-service-web-app-azure-resource-manager-powershell.md)
### [Azure Automation を使用してアプリを管理する](automation-manage-web-app.md)



# リファレンス    
## [CLI 2.0](/cli/azure/appservice)
## [PowerShell](/powershell)
## [REST API](/rest/api/appservice/) 
        
# リソース    
## トラブルシューティング        
### [Visual Studio を使用したトラブルシューティング](web-sites-dotnet-troubleshoot-visual-studio.md)
### [Node.js アプリのトラブルシューティング](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
### [HTTP 502 と 503 のトラブルシューティング](app-service-web-troubleshoot-http-502-http-503.md)
### [パフォーマンスに関する問題のトラブルシューティング](app-service-web-troubleshoot-performance-degradation.md)
## [料金](https://azure.microsoft.com/pricing/details/app-service/)     
## [クォータの情報](../azure-subscription-service-limits.md#app-service-limits)    
## [サービスの更新情報とリリース ノート](https://azure.microsoft.com/updates/?product=app-service)    
## [ベスト プラクティス](app-service-best-practices.md)
## [サンプル](https://azure.microsoft.com/resources/samples/?service=app-service)    
## [ビデオ](https://azure.microsoft.com/resources/videos/index/?services=app-service)
## クックブック    
### [参照用アーキテクチャ](../guidance/guidance-ra-app-service.md)    
### [デプロイ スクリプト](https://azure.microsoft.com/documentation/scripts/)    
