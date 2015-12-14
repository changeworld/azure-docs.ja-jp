<properties 
   pageTitle="Service Bus サンプルの概要 | Microsoft Azure"
   description="Service Bus サンプルを分類して説明し、各サンプルへのリンクが含まれています。"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/08/2015"
   ms.author="sethm" />

# Service Bus サンプル

Service Bus のサンプルでは、Service Bus (クラウド サービス) と Service Bus for Windows Server の主な機能を紹介します。この記事では、使用可能なサンプルを分類して説明しています。また、各サンプルへのリンクも含まれています。

>[AZURE.NOTE]Service Bus のサンプルは SDK と共にインストールされません。サンプルを入手するには、[Azure SDK のサンプル ページ](https://code.msdn.microsoft.com)にアクセスしてください。

## Service Bus ブローカー メッセージング

次のサンプルでは、Service Bus を使用するアプリケーションの作成方法を説明しています。

ブローカー メッセージングのサンプルでは、ご使用の Service Bus サービスの名前空間にアクセスするための接続文字列が必要です。

### Azure Service Bus の接続文字列を取得するには

1. [Azure クラシック ポータル](http://manage.windowsazure.com)にログオンします。

1. 左側の列で、**[Service Bus]** をクリックします。

1. 一覧から、ご使用のサービスの名前空間の名前をクリックします。

1. **[接続情報]** をクリックします。**[接続情報へのアクセス]** ダイアログ ボックスで、接続文字列をクリップボードにコピーします。

1. 接続文字列をサンプルの App.config ファイルに貼り付けます。

### Service Bus for Windows Server の接続文字列を取得するには

1. 次の PowerShell コマンドレットを実行します。

	```
	get-sbClientConfiguration
	```

2. 接続文字列をサンプルの App.config ファイルに貼り付けます。

### 入門サンプル

次のサンプルでは、基本的なメッセージングとリレー機能について説明します。

|サンプル名|説明|SDK の最小バージョン|対応|
|---|---|---|---|
|[はじめに: キューによるメッセージング](http://code.msdn.microsoft.com/Getting-Started-Brokered-aa7a0ac3)|Microsoft Azure Service Bus を使用して、キューからメッセージを送受信する方法を示します。|1\.8|Microsoft Azure Service Bus; Service Bus for Windows Server|
|[はじめに: トピックによるメッセージング](http://code.msdn.microsoft.com/Getting-Started-Brokered-614d42e5)|Microsoft Azure Service Bus を使用して、複数のサブスクリプションを持つトピックからメッセージを送受信する方法を示します。|1\.8|Microsoft Azure Service Bus; Service Bus for Windows Server|
|[Event Hubs の使用](http://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097)|イベント ハブの作成、イベント ハブへのイベントの送信、イベント プロセッサを使用したイベントの利用など、Event Hubs の基本的な機能について説明します。|2\.4|Microsoft Azure Service Bus|

### 機能の探索

次のサンプルでは、Service Bus のさまざまな機能について説明します。

|サンプル名|説明|SDK の最小バージョン|対応|
|---|---|---|---|
|[HTTP トークン プロバイダー](http://code.msdn.microsoft.com/Service-Bus-HTTP-Token-38f2cfc5)|Service Bus を使用して HTTP/REST クライアントを認証する際のさまざまな方法について説明します。|2\.1|Microsoft Azure Service Bus; Service Bus for Windows Server|
|[Service Bus HTTP クライアント](http://code.msdn.microsoft.com/Service-Bus-HTTP-client-fe7da74a)|Service Bus から HTTP/REST 経由でメッセージを送受信する方法について説明します。|2\.3|Microsoft Azure Service Bus; Service Bus for Windows Server|
|[Service Bus の自動転送](http://code.msdn.microsoft.com/Service-Bus-Autoforwarding-b9df470b)|キュー、サブスクリプション、配信不能キューからのメッセージを別のキューまたはトピックに自動的に転送する方法について説明します。また、転送キュー経由でキューまたはトピックにメッセージを送信する方法についても説明します。|2\.3|Microsoft Azure Service Bus; Service Bus for Windows Server|
|[ブローカー メッセージング: WCF チャネル セッションのサンプル](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-0a526451)|Windows Communication Foundation (WCF) チャネルを使用して、Microsoft Azure Service Bus を使用する方法について説明します。このサンプルでは、WCF チャネルを使用した Service Bus キュー経由のメッセージの送受信を示します。このサンプルは、Service Bus でのセッションの通信とセッション以外の通信を示します。|1\.8|Microsoft Azure Service Bus; Service Bus for Windows Server|
|[ブローカー メッセージング: トランザクション](http://code.msdn.microsoft.com/Brokered-Messaging-8cd41d1e)|メッセージング操作のバッチがアトミックにコミットされるように、トランザクション スコープ内の Microsoft Azure Service Bus のメッセージング機能を使用する方法について説明します。|1\.8|Microsoft Azure Service Bus; Service Bus for Windows Server|
|[ブローカー メッセージング: REST を使用した管理操作](http://code.msdn.microsoft.com/Brokered-Messaging-569cff88)|REST を使用して Service Bus の管理操作を実行する方法について説明します。|1\.8|Microsoft Azure Service Bus; Service Bus for Windows Server|
|[リソース プロバイダー REST API](http://code.msdn.microsoft.com/Service-Bus-Resource-5d887203)|新しい Service Bus RDFE REST API を使用して、名前空間とメッセージング エンティティを管理する方法について説明します。|1\.8|Microsoft Azure Service Bus; Service Bus for Windows Server|
|[ブローカー メッセージング: WCF サービス セッションのサンプル](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-db4262c2)|WCF サービス モデルを使用して、Microsoft Azure Service Bus を使用する方法について説明します。このサンプルでは、WCF サービス モデルを使用した Service Bus キュー経由のセッションベースの通信を示します。|1\.8|Microsoft Azure Service Bus; Service Bus for Windows Server|
|[Brokered Messaging: Request Response (ブローカー メッセージング: 要求応答)](http://code.msdn.microsoft.com/Brokered-Messaging-Request-2b4ff5d8)|Microsoft Azure Service Bus と要求/応答機能の使用方法について説明します。このサンプルでは、Service Bus キューを介した単純なクライアントとサーバーの通信を示します。|1\.8|Microsoft Azure Service Bus; Service Bus for Windows Server|
|[ブローカー メッセージング: 配信不能キュー](http://code.msdn.microsoft.com/Brokered-Messaging-Dead-22536dd8)|Microsoft Azure Service Bus とメッセージングの "配信不能キュー" 機能を使用する方法を示します。このサンプルでは、Service Bus キューを介した送信者と受信者の単純な通信を示します。|1\.8|Microsoft Azure Service Bus; Service Bus for Windows Server|
|[ブローカー メッセージング: 遅延メッセージ](http://code.msdn.microsoft.com/Brokered-Messaging-ccc4f879)|Microsoft Azure Service Bus のメッセージ遅延機能を使用する方法について説明します。このサンプルでは、Service Bus キューを介した送信者と受信者の単純な通信を示します。|1\.8|Microsoft Azure Service Bus; Service Bus for Windows Server|
|[ブローカー メッセージング: セッション メッセージ](http://code.msdn.microsoft.com/Brokered-Messaging-Session-41c43fb4)|Microsoft Azure Service Bus とメッセージング セッション機能を使用する方法について説明します。このサンプルでは、Service Bus キューを介した送信者と受信者の単純な通信を示します。|1\.8|Microsoft Azure Service Bus; Service Bus for Windows Server|
|[ブローカー メッセージング: 要求応答トピック](http://code.msdn.microsoft.com/Brokered-Messaging-Request-6759a36e)|Microsoft Azure Service Bus のトピックとサブスクリプションを使用して要求/応答パターンを実装する方法について説明します。このサンプルでは、Service Bus トピックを介したクライアントとサーバーの単純な通信を示します。|1\.8|Microsoft Azure Service Bus; Service Bus for Windows Server|
|[ブローカー メッセージング: 要求応答キュー](http://code.msdn.microsoft.com/Brokered-Messaging-Request-0ce8fcaf)|Microsoft Azure Service Bus と要求/応答機能の使用方法について説明します。このサンプルでは、2 つの Service Bus キューを介したクライアントとサーバーの単純な通信を示します。|1\.8|Microsoft Azure Service Bus; Service Bus for Windows Server|
|[ブローカー メッセージング: 重複データ検出](http://code.msdn.microsoft.com/Brokered-Messaging-c0acea25)|キューと共に Microsoft Azure Service Bus の重複メッセージ検出を使用する方法について説明します。このサンプルでは 2 つのキューを作成します。1 つは重複データ検出を有効にし、もう 1 つは重複検出を使用しません。|1\.8|Microsoft Azure Service Bus; Service Bus for Windows Server|
|[ブローカー メッセージング: 非同期メッセージング](http://code.msdn.microsoft.com/Brokered-Messaging-Async-211c1e74)|Microsoft Azure Service Bus を使用して、キューから非同期的にメッセージを送受信する方法について説明します。このキューは、1 人の送信者と任意の数の受信者 (ここでは 1 人) の間で分離された非同期通信を提供します。|1\.8|Microsoft Azure Service Bus; Service Bus for Windows Server|
|[ブローカー メッセージング: 高度なフィルター](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)|Microsoft Azure Service Bus のパブリッシュ/サブスクライブの高度なフィルターを使用する方法について説明します。このサンプルでは、フィルター定義の異なる 1 つのトピックと 3 つのサブスクリプションを作成し、そのトピックにメッセージを送信して、サブスクリプションからすべてのメッセージを受信します。|1\.8|Microsoft Azure Service Bus; Service Bus for Windows Server|
|[ブローカー メッセージング: メッセージのプリフェッチ](http://code.msdn.microsoft.com/Brokered-Messaging-be2dac1d)|Microsoft Azure Service Bus のメッセージのプリフェッチ機能を使用する方法について説明します。ここでは、受信時にメッセージのプリフェッチ機能を使用する方法を示します。|1\.8|Microsoft Azure Service Bus; Service Bus for Windows Server|

## Service Bus Relay

Service Bus Relay を説明するサンプルです。

### 使用の開始

|サンプル名|説明|SDK の最小バージョン|対応|
|---|---|---|---|
|[リレー型メッセージング: Azure](http://code.msdn.microsoft.com/Relayed-Messaging-Windows-0d2cede3)|Azure で Service Bus のクライアントとサービスを実行する方法について説明します。このサンプルでは、プログラムによって Service Bus を構成します。構成ファイルに格納されるのは、環境情報とセキュリティ情報のみです。|1\.8|Microsoft Azure Service Bus|
|[リレー型メッセージング認証: 共有シークレット](http://code.msdn.microsoft.com/Relayed-Messaging-92b04c02)|発行者名と発行者のシークレットを使用して、Service Bus で認証する方法について説明します。|1\.8|Microsoft Azure Service Bus|
|[リレー型メッセージング認証: WebNoAuth](http://code.msdn.microsoft.com/Relayed-Messaging-a4f0b831)|クライアント ユーザー認証を必要としない HTTP サービスの公開方法について説明します。|1\.8|Microsoft Azure Service Bus|
|[リレー型メッセージング バインド: WebHttp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-a6477ba0)|Web プログラミング モデルを使用して、**WebHttpRelayBinding** バインドでバイナリ データを返す方法について説明します。|1\.8|Microsoft Azure Service Bus|
|[リレー型メッセージング バインド: NetTcp のリレー](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-2dec7692)|**NetTcpRelayBinding** バインドの使用方法について説明します。|1\.8|Microsoft Azure Service Bus|

### 機能の探索

Service Bus Relay のさまざまな機能を説明するサンプルです。

|サンプル名|説明|SDK の最小バージョン|対応|
|---|---|---|---|
|[リレー型メッセージング認証: 単純な WebToken](http://code.msdn.microsoft.com/Relayed-Messaging-32c74392)|単純な Web トークンの資格情報を使用して、Service Bus で認証する方法について説明します。このサンプルは Echo サンプルに似ていますが、いくつかの変更点があります。具体的には、このサンプルでは、ServiceHost (サービス) アプリケーションと ChannelFactory (クライアント) アプリケーションに動作を追加します。|1\.8|Microsoft Azure Service Bus|
|[リレー型メッセージング: 負荷分散](http://code.msdn.microsoft.com/Relayed-Messaging-Load-bd76a9f8)|Microsoft Azure Service Bus を使用して複数の受信者にメッセージをルーティングする方法について説明します。**NetTcpRelayBinding** バインドを介してクライアントと通信する簡単なサービスの複数のインスタンスを示します。|1\.8|Microsoft Azure Service Bus|
|[リレー型メッセージング バインド: Net イベント](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-c0176977)|Microsoft Azure Service Bus での **NetEventRelayBinding** バインドの使用について説明します。|1\.8|Microsoft Azure Service Bus|
|[リレー型メッセージング バインド: WS2007Http セッション](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ef1f1fcb)|信頼できるセッションを有効にした **WS2007HttpRelayBinding** バインドの使用について説明します。また、プログラムではなく構成ファイルで Service Bus 資格情報を指定する方法も示します。|1\.8|Microsoft Azure Service Bus|
|[リレー型メッセージング バインド: WS2007Http MsgSecCertificate](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-f29c9da5)|**WS2007HttpRelayBinding** バインドとメッセージ セキュリティを使用して、クライアントに Service Bus での認証を要求しながらエンド ツー エンドのメッセージをセキュリティで保護する方法について説明します。|1\.8|Microsoft Azure Service Bus|
|[リレー型メッセージング: メタデータ交換](http://code.msdn.microsoft.com/Relayed-Messaging-Metadata-f122312e)|リレー バインドを使用するメタデータ エンドポイントを公開する方法について説明します。**MetadataExchange** は、**NetTcpRelayBinding**、**NetOnewayRelayBinding**、**BasicHttpRelayBinding**、**WS2007HttpRelayBinding** というリレー バインドでサポートされます。|1\.8|Microsoft Azure Service Bus|
|[リレー型メッセージング バインド: NetTcp Direct](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ca039161)|**ハイブリッド**接続モードをサポートするように **NetTcpRelayBinding** バインドを構成する方法について説明します。このモードでは、最初にリレー型接続が確立され、可能な場合に、クライアントとサービス間の直接接続に自動的に切り替わります。|1\.8|Microsoft Azure Service Bus|
|[リレー型メッセージング バインド: NetTcp MsgSec UserName](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-30542392)|メッセージ セキュリティでの **NetTcpRelayBinding** バインドの使用方法について説明します。|1\.8|Microsoft Azure Service Bus|
|[リレー型メッセージング バインド: Net Oneway](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-bb5b813a)|**NetOnewayRelayBinding** バインドを使用したサービス エンドポイントの公開方法と使用方法について説明します。|1\.8|Microsoft Azure Service Bus|
|[リレー型メッセージング バインド: WS2007Http Simple](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-aa4b793a)|**WS2007HttpRelayBinding** バインドの使用方法について説明します。セキュリティ オプションを使用せず、クライアントに認証を要求しない簡単なサービスを示します。|1\.8|Microsoft Azure Service Bus|

## Service Bus リファレンス ツール

次のサンプルでは、サービスのその他さまざまな機能について説明します。

|サンプル名|説明|SDK の最小バージョン|対応|
|---|---|---|---|
|[Service Bus エクスプローラー](http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)|Service Bus エクスプローラーを使用すると、ユーザーは Service Bus サービスの名前空間に接続し、簡単にメッセージング エンティティを管理できます。このツールには、インポート/エクスポート機能、メッセージング エンティティとリレー サービスをテストする機能など、高度な機能が用意されています。|1\.8|Microsoft Azure Service Bus; Service Bus for Windows Server|
|[承認: SBAzTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93)|このサンプルでは、Service Bus で使用するための Microsoft Azure Active Directory Access Control (Access Control Service または ACS とも呼ばれます) のサービス ID を作成、管理する方法について説明します。|該当なし|Microsoft Azure Service Bus|

## 次のステップ

Service Bus のその他の概要については、次のトピックを参照してください。

- [Service Bus メッセージングの概要](service-bus-messaging-overview.md)
- [Service Bus のアーキテクチャ](service-bus-architecture.md)
- [Service Bus の基礎](service-bus-fundamentals-hybrid-solutions.md)

<!---HONumber=AcomDC_1203_2015-->