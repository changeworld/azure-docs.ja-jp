<properties 
	pageTitle="Hybrid Connection Manager の使用 | Microsoft Azure App Service" 
	description="Hybrid Connection Manager をインストールして構成し、Azure App Service 内のオンプレミス コネクターに接続する" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/30/2015" 
	ms.author="mandia"/>

# Hybrid Connection Manager を使用して Azure App Service 内のオンプレミス コネクターに接続する
オンプレミス システムを使用するため、Azure App Service は Hybrid Connection Manager を使用します。一部のコネクタは、SQL Server、SAP、SharePoint などのオンプレミス システムに接続できます。

Hybrid Connection Manager (HCM) は、ワンクリック インストーラーであり、ネットワーク内のファイアウオールの背後にある IIS サーバーにインストールされます。HCM は、Azure Service Bus リレーを使用して、Azure 内にコネクタを持つオンプレミス システムを認証します。

> [AZURE.NOTE]Hybrid Connection Manager は、ファイアウォールの背後にあるオンプレミス リソースに接続する場合にのみ必要です。オンプレミス システムに接続しない場合、Hybrid Connection Manager は必要ありません。

開始するには、以下が必要です。

- Azure Service Bus リレー名前空間の SAS 接続文字列。どのレベルにリレーが含まれるかを特定するには、「[Service Bus 料金](http://azure.microsoft.com/pricing/details/service-bus/)」を参照してください。
- オンプレミス システムのサインイン情報 (ユーザー名とパスワード)。たとえば、オンプレミスの SQL Server に接続する場合は、SQL Server のログイン アカウントとパスワードが必要です。
- オンプレミス サーバー情報 (ポート番号とサーバー名)たとえば、オンプレミスの SQL Server に接続する場合は SQL Server の名前と TCP ポート番号が必要です。

## Service Bus 接続文字列を取得する

Azure ポータルで、Service Bus のルート SAS 接続文字列をコピーします。この接続文字列は、Azure コネクタをオンプレミス システムに接続します。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=213885)で、Service Bus 名前空間を選択し、**[接続情報]** をクリックします。

	![][SB_ConnectInfo]

2. SAS 接続文字列をコピーします。

	![][SB_SAS]

## Hybrid Connection Manager をインストールする

1. プレビュー [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)で、作成したコネクタを選択します。開くには、**[参照]**、**[API Apps]** の順に選択した後、コネクタまたは API App を選択します。<br/><br/>**[ハイブリッド接続]** の設定は **［未完了］** になっています。<br/> ![][2] 

2. **[ハイブリッド接続]** を選択します。前に入力した Service Bus 接続文字列が表示されます。
3. **プライマリ構成文字列**をコピーします。<br/>![][PrimaryConfigString]

4. **[オンプレミスの Hybrid Connection Manager]** で、Hybrid Connection Manger をポータルから直接ダウンロードまたはインストールできます。<br/><br/> ポータルから直接インストール場合は、オンプレミスの IIS サーバーに移動し、ポータルを参照し、**[ダウンロードして構成]** をクリックします。<br/><br/> Hybrid Connection Manager をダウンロードする場合は、オンプレミスの IIS サーバーに移動し、 **[ClickOnce アプリケーション]** (http://hybridclickonce.azurewebsites.net/install/Microsoft.Azure.BizTalk.Hybrid.ClickOnce.application) にアクセスします。アプリケーションを実行できるように、インストールが自動的に開始されます。

5. **[リスナーのセットアップ**] ウィンドウで、手順 3 でコピーした**プライマリ構成文字列**を入力し、**[インストール]** をクリックします。

セットアップが完了すると、次のように表示されます。<br/> ![][3]

ここでもう一度コネクタを参照すると、ハイブリッド接続ステータスが **[接続済み]** になっています。場合によっては、コネクタを閉じて再度開く必要があります。<br/> 。![][4]

> [AZURE.NOTE]セカンダリ接続文字列に切り替えるには、ハイブリッド接続のセットアップをもう一度実行し、**セカンダリ構成文字列** を入力します。


## TCP ポートとセキュリティ

ハイブリッド接続を作成すると、Web サイトがローカルのオンプレミスの IIS サーバーに作成されます。IIS サーバーは、DMZ 内に存在できます。IIS サーバーの TCP ポート要件は次のとおりです。

TCP ポート | 理由
--- | ---
 | 着信 TCP 受信ポートは必要ありません。
9350 ～ 9354 | これらのポートはデータ転送に使用されます。Service Bus リレー マネージャーはポート 9350 を調べて、TCP 接続を利用できるかどうかを決定します。利用できる場合は、ポート 9352 も利用可能であるとみなされます。データ トラフィックはポート 9352 を経由します。<br/><br/>これらのポートへの発信接続を許可します。
5671 | ポート 9352 がデータ トラフィックに使用される場合、ポート 5671 は制御チャンネルとして使用されます。<br/><br/>これらのポートへの発信接続を許可します。 
80、443 | ポート 9352 とポート 5671 を使用できない場合、ポート 80 とポート 443 がデータ転送と制御チャネルに使用される代替ポートになります。<br/><br/>。これらのポートへの発信接続を許可します。
オンプレミス システムのポート | オンプレミス システムでは、システムによって使用されているポートが開きます。たとえば、SQL Server では、通常はポート 1433 を使用します。この TCP ポートを開きます。

[Service Bus を使用したファイアウォールの背後でのホスティング](http://msdn.microsoft.com/library/azure/ee706729.aspx)

## トラブルシューティング

![][HCMFlow]

### オンプレミスでのトラブルシューティング

1. IIS サーバーで、IIS Web ロールがインストールされ、すべての IIS サービスが開始されていることを確認します。
2. IIS サーバーで、Hybrid Connection Manager がインストールされ、実行されていることを確認します。
 - IIS Manager (inetmgr) に、MicrosoftAzureBizTalkHybridListener Web サイトが表示され、実行中である必要があります。 
 - この Web サイトは、NetworkService ローカル組み込みユーザー アカウントとして実行される HybridListenerAppPool を使用します。この AppPool も開始されている必要があります。
3. IIS サーバーで、コネクタがインストールされ、実行されていることを確認します。 
 - App Service コネクタ用の Web サイトが作成されます。たとえば、SQL コネクタを作成した場合は、MicrosoftSqlConnector\_nnn Web サイトが存在します。IIS マネージャー (inetmgr) で、この web サイトが表示され、開始されていることを確認します。 
 - この Web サイトは、HybridAppPoolnnn という名前の専用の IIS アプリケーション プールを使用します。この AppPool は、NetworkService ローカル組み込みユーザー アカウントとして実行されます。この Web サイトと AppPool の両方が開始されている必要があります。 
 - ローカル コネクタを参照します。たとえば、コネクタ Web サイトがポート 6569 を使用する場合は、http://localhost:6569を参照します。既定のドキュメントが構成されていないため、`HTTP Error 403.14 - Forbidden error` が予期されます。
4. ファイアウォールで、このトピックに示されている TCP ポートが開いていることを確認します。
5. 送信元または送信先のシステムを調べます。
 - 一部のオンプレミス システムでは、追加の依存関係ファイルが必要です。たとえば、オンプレミスの SAP に接続する場合は、追加の SAP ファイルをいくつか IIS サーバーにインストールする必要があります。
 - ログイン アカウントを使用してシステムへの接続を確認します。たとえば、システムによって使用される TCP ポート (SQL Server 用のポート 1433 など) が開いている必要があります。Azure ポータルで入力したログイン アカウントによってシステムにアクセスできる必要があります。
6. IIS サーバーのイベント ログで、エラーがないことを確認します。 
7. クリーンアップを行い、Hybrid Connection Manager を再インストールします。 
 - IIS で、コネクタ Web サイトとそのアプリケーション プールを手動で削除します。 
 - Hybrid Connection Manager を再実行し、コネクタ用の正しい**プライマリ構成文字列**を入力していることを確認します。



### Azure ポータルで次の操作を行います。

1. Service Bus 名前空間が **[アクティブ]** 状態であることを確認します。
2. コネクタを作成するときに、Service Bus の SAS 接続文字列を入力します。ACS 接続文字列を入力しないでください。


## FAQ

**質問**: Hybrid Connection Manager が 2 つあります。違いは何でしょうか。<br/> **回答**: オンプレミスに接続するために主に Web Apps (以前の Web サイト) と Mobile Apps (以前のモバイル サービス) によって使用される[ハイブリッド接続](../integration-hybrid-connection-overview.md)テクノロジがあります。この Hybrid Connections Manager には独自の[セットアップ](../integration-hybrid-connection-create-manage.md)があり、Azure BizTalk Service を (バック グラウンドで) 使用します。それは、TCP プロトコルと HTTP プロトコルのみをサポートします。

Azure App Service コネクタにも、Hybrid Connection Manager があります。この Hybrid Connection Manager は Azure BizTalk Service を (バック グラウンドで) 使用せず、TCP プロトコルと HTTP プロトコル以外のプロトコルもサポートします。「[Connectors and API Apps (コネクタと API Apps の一覧)](app-service-logic-connectors-list.md)」を参照してください。

どちらも、Azure Service Bus を使用してオンプレミス システムに接続します。

**質問**: カスタム API App を作成した場合、App Service Hybrid Connection Manager を使用してオンプレミスに接続できますか。 <br/> **回答**: 従来の意味ではできません。組み込みコネクタを使用し、App Service Hybrid Connection Manager を構成することで、オンプレミス システムに接続できます。その後、カスタム API App の中で、Logic App を利用してこのコネクタを使用します。現時点では、独自のハイブリッド API App (SQL コネクタやファイル コネクタなど) は開発することも作成することもできません。

カスタム API で TCP または HTTP ポートを使用する場合は、[ハイブリッド接続](../integration-hybrid-connection-overview.md) とその Hybrid Connection Manager を使用することができます。このシナリオでは、Azure BizTalk Service が使用されます。[オンプレミスの SQL Server に Web アプリから接続する](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)と便利な場合があります。


## 詳細

[Logic Apps を監視する](app-service-logic-monitor-your-logic-apps.md)<br/> [Service Bus 料金](http://azure.microsoft.com/pricing/details/service-bus/)



[SB_ConnectInfo]: ./media/app-service-logic-hybrid-connection-manager/SB_ConnectInfo.png
[SB_SAS]: ./media/app-service-logic-hybrid-connection-manager/SB_SAS.png
[PrimaryConfigString]: ./media/app-service-logic-hybrid-connection-manager/PrimaryConfigString.png
[HCMFlow]: ./media/app-service-logic-hybrid-connection-manager/HCMFlow.png
[2]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-hybrid-connection-manager/HybridSetup.jpg
[4]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupComplete.jpg

 

<!---HONumber=AcomDC_1203_2015-->