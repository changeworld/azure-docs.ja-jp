<properties 
   pageTitle="Azure SDK for .NET 2.6 リリース ノート" 
   description="Azure SDK for .NET 2.6 リリース ノート" 
   services="app-service/web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/29/2015"
   ms.author="juliako"/>


# Azure SDK for .NET 2.6 リリース ノート

このドキュメントには、Azure SDK for .NET 2.6 リリースのリリース ノートが含まれます。

Azure SDK 2.6 では、クラウド サービス ロールにターゲット .NET Framework を手動でインストールした場合に、.NET 4.5.2 または .NET 4.6 をターゲットとするクラウド サービス アプリケーション (PaaS) を開発できます。「[Install .NET on a Cloud Service Role (クラウド サービス ロールに .NET をインストール)](http://go.microsoft.com/fwlink/?LinkID=309796)」を参照してください。


## Service Bus の更新

- Event Hubs: 

	- Event Hubs の追加の発行者エンドポイントを公開することで、イベントの送信時に、対象のアクセス権を制御できるようになりました。
	- Event Hubs の機能と安定性が強化されました。
	- WebSocket を使用したメッセージングと Event Hubs の Amqp プロトコルのサポートが追加されました。

## HDInsight Tools for Visual Studio の更新

- **IntelliSense の強化**: リモート メタデータの推奨事項

	HDInsight Tools for Visual Studio で、Hive スクリプトの編集時にリモート メタデータの取得がサポートされるようになりました。たとえば、「**SELECT * FROM**」と入力すると、すべてのテーブル名が表示されます。また、テーブルを指定した後、列名も表示されます。

- **HDInsight Emulator のサポート**

	HDInsight Tools for Visual Studio で HDInsight Emulator への接続がサポートされるため、コストを導入することなくローカルで Hive スクリプトを開発し、HDInsight クラスターに対してこれらのスクリプトを実行できるようになりました。

	詳しくは、[このマニュアル](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409)を参照してください。

- **HDInsight Tools for Visual Studio での汎用 Hadoop クラスターのサポート** (プレビュー)

	HDInsight Tools for Visual Studio に汎用 Hadoop クラスターのサポートが追加されたため、HDInsight Tools for Visual Studio を使用して、次のことを実行できます。

	- クラスターに接続 
	- 強化された IntelliSense とオートコンプリート サポートを使用して、Hive クエリを記述 
	- 直感的な UI で、クラスター内のすべてのジョブを表示 

	詳しくは、[このマニュアル](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409)を参照してください。

## インロール キャッシュの更新

- **インロール キャッシュ**で **Microsoft Azure Storage SDK** バージョン 4.3 を使用できるようになりました。今まで**インロール キャッシュ**では Azure Storage SDK バージョン 1.7 を使用していました。

	Azure SDK 2.5 以前を使用しているお客様は、Azure SDK 2.6 に更新し、新しいバージョンの Azure Storage SDK に移行する必要があります。Azure Storage バージョン 2011-08-18 は 2015 年 12 月 9 日を以て廃止されるます。詳しくは、[こちらのお知らせ](http://azure.microsoft.com/blog/2014/08/04/microsoft-azure-storage-service-version-removal/)をお読みください。

	詳細については、「[Azure キャッシュ用のインロール キャッシュ](https://msdn.microsoft.com/library/azure/dn386103.aspx)」を参照してください。

## Azure App Service ツール

Azure SDK 2.6 リリースで次の項目が更新されました。

- Azure 発行に、デプロイ ターゲットとして Azure API Apps が追加されました。
- API Apps プロビジョニング機能により、ユーザーは API App の作成やプロビジョニング機能を使用できるようになります。
- Server Explorer で、リソース グループにより Web、モバイル、API Apps をグループ化して、新しい App Service ノードが反映されるようになりました。
- ほとんどの C# プロジェクトに追加された Azure API App Client ジェスチャが追加されました。これにより、Azure のサブスクリプションで実行する Swagger 対応 API Apps の自動生成が可能になります。
- Server Explorer の API Apps ツールと App Service ノードがVisual Studio 2013 でのみ利用できるようになりました。 

## Azure Resource Manager ツールの更新

Azure Resource Manager ツールで Virtual Machines、Networking、Storage のテンプレートを使用できるようになりました。JSON 編集操作にテンプレート用の新しいアウトライン表示と、JSON スニペットを使用してテンプレートを編集する機能が追加されました。Visual Studio からデプロイしたテンプレートでプロジェクトで指定した PowerShell スクリプトが使用されるため、スクリプトに対する変更が Visual Studio でも使用されます。

## クラウド サービスの診断機能の強化

Azure SDK 2.6 において、Azure コンピューティング エミュレーターでの診断ログの収集と、それらの開発スとレージへの転送機能が復活しました。アプリケーションのエミュレーターでの実行時に生成される診断ログ (アプリケーションのトレース ログ、Event Tracing for Windows (ETW) ログ、パフォーマンス カウンター、インフラストラクチャ ログ、Windows イベント ログなど) を開発ストレージに転送して、診断ログがローカル コンピューターで機能していることを確認できます。

診断ストレージ アカウントがサービス構成 (.cscfg) ファイルで指定されるため、さまざまな環境にあった診断ストレージ アカウントを簡単に使用できるようになります。接続文字列の Azure SDK 2.4 と Azure SDK 2.6 での動作に注目すべきいくつかの違いが見られます。診断ストレージの接続文字列の使用方法と、それがプロジェクトにどのような影響を及ぼすかについては、「[Azure Cloud 用の診断の構成](http://go.microsoft.com/fwlink/?LinkID=532784)」を参照してください。

## 重大な変更

### Azure Resource Manager ツール 

- Azure SDK 2.5 で使用できる **Cloud Deployment Projects** プロジェクト タイプの名前が **Azure Resource Group** に変わりました。
- Azure SDK 2.5 で作成されたプロジェクトの **Cloud Deployment Projects** タイプは 2.6 で使用できますが、Visual Studio からテンプレートをデプロイすることはできません。ただし、PowerShell スクリプトでのデプロイは引き続き機能します。2.6 で **Cloud Deployment Projects** を使用する方法については、こちらの[投稿](http://go.microsoft.com/fwlink/?LinkID=534086)をお読みください。
 
## 既知の問題

エミュレーターで診断ログを収集するには、64 ビットのオペレーティング システムが必要です。32 ビットのオペレーティング システムで実行した場合、診断ログは収集されません。これは、他のエミュレーター機能には影響しません。

<!--HONumber=52-->
