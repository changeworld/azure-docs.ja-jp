<properties 
	pageTitle="Microsoft Azure App Service でオンプレミスの SAP サーバーと統合する"
	description="オンプレミスの SAP サーバーと統合する方法について説明します。"
	authors="rajeshramabathiran" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2015"
	ms.author="harish"/>


# オンプレミスの SAP サーバーと統合する
SAP コネクタを使用すると、Azure App Services を構成する Web アプリ、モバイル アプリ、ロジック アプリを既存の SAP サーバーに接続できます。RFC、BAPI、tRFC を呼び出すだけでなく、SAP サーバーに IDOC を送信することもできます。
	
オンプレミスのファイアウォールの背後に SAP サーバーを置くこともできます。オンプレミスのサーバーの場合は、図のように、ハイブリッド リスナーを介して接続が確立されます。

![Hybrid connectivity flow][1]

クラウドの SAP コネクタは、ファイアウォールの背後にある SAP サーバーに直接接続することはできません。これを補うのがリレー エンドポイントをホストするハイブリッド リスナーで、これにより、コネクタは SAP サーバーへの接続を安全に確立できます。


## SAP と統合するさまざまな方法
次のアクションがサポートされています。

- RFC の呼び出し
- TRFC の呼び出し
- BAPI の呼び出し
- IDOC の送信

## 前提条件
ハイブリッド リスナーをインストールし、実行するクライアント コンピューターには、SAP 固有のクライアント ライブラリが必要です。詳細については、[ここ][9] をクリックしてファイルをダウンロードし、**SAP アダプター**に関するセクションを参照してください。


## 新しい SAP アダプターの作成
1. Microsoft Azure の管理ポータルにサインインします。 
2. **[新規]** を選択します。
3. 作成するブレードで、**[コンピューティング]**、**[Azure Marketplace]** の順に選択します。
4. Marketplace のブレードで、**[API Apps]** を選択し、検索バーで "SAP" を検索します。
	
	![SAP Connector API App][2]	
5. マイクロソフト発行の **SAP コネクタ**を選択します。
6. SAP コネクタのブレードで、**[作成]** を選択します。
7. 表示された新しいブレードで、次のように入力します。
	1. **[場所]** - コネクタをデプロイする地理的な場所を選択します。
	2. **[サブスクリプション]** - コネクタを作成するサブスクリプションを選択します。
	3. **[リソース グループ]** - コネクタを格納するリソース グループを選択するか、作成します。
	4. **[Web ホスティング プラン]** - Web ホスティング プランを選択するか、作成します。
	5. **[価格レベル]** - コネクタの価格レベルを選択します。
	6. **[名前]** - SAP コネクタの名前を入力します。
	7. **パッケージ設定**
		- **[サーバー名]** - SAP サーバーの名前を入力します。例:"SAPserver" または "SAPserver.mydomain.com" とします。
		- **[ユーザー名]** - SAP サーバーに接続する有効なユーザー名を入力します。
		- **[パスワード]** - SAP サーバーに接続する有効なパスワードを入力します。
		- **[システム番号]** - SAP アプリケーション サーバーのシステム番号を入力します。
		- **[言語]** - 「EN」のように、ログオンの言語を入力します。値が入力されなかった場合は EN と見なされます。
		- **[オンプレミス]** - SAP サーバーがファイアウォールの背後にあるオンプレミスのサーバーかどうかを入力します。TRUE に設定した場合は、SAP サーバーにアクセスできるリスナー エージェントをサーバーにインストールする必要があります。このエージェントをインストールするには、API アプリ概要のページに移動し、 'Hybrid Connection' を選択します。
		- **[Service Bus の接続文字列]** - SAP サーバーがオンプレミスの場合は、このパラメーターを入力します。有効な Service Bus 名前空間の接続文字列である必要があります。
		- **[RFC]** - コネクタによる呼び出しができる SAP の RFC を入力します。
		- **[tRFC]** - コネクタによる呼び出しができる SAP の tRFC を入力します。
		- **[BAPI]** - コネクタによる呼び出しができる SAP の BAPI を入力します。
		- **[IDOC]** - コネクタで送信できる SAP の IDOC を入力します。
	8. [選択] を選択します。数分で SAP コネクタが作成されます。


## ハイブリッド リスナーをインストールする
**[参照]**、**[API Apps]**、 *コネクタの名前* の順に選択して、作成した SAP コネクタを参照します。

コネクタのブレードで、ハイブリッド接続ステータスが保留になっていることを確認してください。[ハイブリッド接続] を選択します。ハイブリッド接続のブレードが開きます。

![Hybrid connection blade][3]

プライマリ ゲートウェイの構成文字列をコピーします。この文字列は、後でハイブリッド リスナーをインストールする際の設定の一部として使用します。

**[ダウンロードして構成]** リンクを選択し、クリック ワンス インストーラーを実行します。

![Hybrid connection click once installer][4]

**[インストール]** を選択し、先ほどコピーしたゲートウェイ構成の設定を入力します。

![Relay listen connection string][5]

**[インストール]** を選択し、ハイブリッド接続マネージャーのセットアップを完了します。

![Hybrid connection manager installation in progress][6]

![Hybrid connection manager installation completed][7]

## ハイブリッド接続の検証
**[参照]**、**[API Apps]**、 *コネクタの名前*の順に選択して、作成した SAP コネクタを参照します。

コネクタのブレードで、ハイブリッド接続のステータスが *Connected*であることを確認します。

![Hybrid connection status - connected][8]


## ロジック アプリでの SAP コネクタの使用
作成した SAP コネクタは、ロジック アプリのワークフローで使用できます。

**[新規]**、**[ロジック アプリ]**、**[作成]** の順に選択して、新しいロジック アプリを作成します。ロジック アプリのメタデータ (リソース グループなど) を入力します。

**[トリガーとアクション]** を選択します。ロジック アプリのワークフロー デザイナーが開きます。

右側のウィンドウで SAP コネクタを選択し、[アクション] タブでアクションを選択します。 

> [AZURE.NOTE] アクションの一覧は、SAP コネクタを作成したときに入力した構成に基づいています。 

選択したアクションについては、入力と出力のパラメーターが表示されます。アクションに対する入力項目に入力することもできます。また、他の API Apps 内の現在のアクションの出力を使用して、ワークフロー内のより詳細な意思決定の可能性に備えることもできます。

<!--Image references-->
[1]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectivityFlow.PNG	
[2]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/SAPConnector.APIApp.PNG
[3]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.PNG
[4]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.ClickOnceInstaller.PNG
[5]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.ClickOnceInstaller.RelayInformation.PNG
[6]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectionManager.Install.InProgress.PNG
[7]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectionManager.Install.Completed.PNG
[8]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/SAPConnector.HybridConnection.Connected.PNG
[9]: http://download.microsoft.com/download/2/D/7/2D7CE8DF-A6C5-45F0-8319-14C3F1F9A0C7/InstallationGuide.htm




<!--HONumber=49-->