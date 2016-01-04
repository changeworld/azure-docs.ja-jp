<properties 
	pageTitle="Azure App Service でコネクタと API Apps の管理と監視を行う | Microsoft Azure" 
	description="Azure App Service のコネクタと API Apps のパフォーマンスを表示する: マイクロサービス アーキテクチャ" 
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
	ms.date="12/07/2015" 
	ms.author="mandia"/>

# 組み込み API Apps とコネクタの管理と監視を実行する

組み込み API アプリを作成しました。次は何でしょうか。

Azure では、すべての API アプリは、Azure でホストされる独立した Web サイトです。その結果、行われた要求の数と、コネクタで使用されたデータの量を簡単に確認できます。さらに、API アプリのバックアップ、アラートの作成、Tinfoil Security の有効化、およびユーザーとロールの追加を実行できます。

このトピックでは、API アプリを管理するためのさまざまなオプションについて、その一部を説明します。

これらの組み込み機能を表示するには、API アプリを [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)で開きます。API アプリがスタート画面に表示されている場合は、それを選択するとプロパティが表示されます。**[参照]**、**[API Apps]** の順に選択した後、API アプリを選択することもできます。

![][browse]

## 入力済みのプロパティを表示する

API アプリを開くと、使用できるさまざまな機能とタスクが表示されます。

![][settings]

そのための方法は次のとおりです。

- **[設定]** は、API アプリに関する情報 (サブスクリプションの詳細を含みます) と、API アプリにアクセスできるユーザーを表示します。特にスケール機能を使用して、API アプリのインスタンス数を増減させることもできます。
- API アプリを制御するには、**[開始]** ボタンと **[停止]** ボタンを使用します。
- API アプリで使用されるファイルに対して製品の更新が行われた場合は、**[更新]** をクリックして最新のバージョンを取得できます。たとえば、マイクロソフトからリリースされた修正プログラムやセキュリティ更新プログラムがある場合に **[更新]** をクリックすると、その修正プログラムを含めるように API アプリが自動的に更新されます。 
- API アプリのデータの使用状況に基づいてアップグレードまたはダウングレードを行うには、**[プランの変更]** を選択します。この機能を使用して、データの使用状況を表示することもできます。
- SQL コネクタなどのテーブルを持つコネクタを作成する場合は、接続するテーブルの名前を入力できます (省略可能)。テーブルに基づくスキーマが自動的に作成され、**[スキーマのダウンロード]** をクリックしたときに使用できます。このダウンロードしたスキーマを使用して、変換またはマップを作成できます。 

## 入力済みのコネクタまたは API 構成の値を変更する

組み込みコネクタを構成または作成した後で、入力済みの値を変更できます。たとえば、SQL コネクタを構成しているときに、SQL Server またはテーブルの名前を変更する場合は、コネクタ用の [API アプリ] ブレードでこの操作を行うことができます。

手順は次のとおりです。

1. コネクタまたは API アプリを開きます。この操作を実行すると、API アプリ ブレードが開きます。
2. **[基本要素]** で、[ホスト] プロパティの下のハイパーリンクをクリックします。このハイパーリンクの名前は、*slackconnector* や *microsoftsqlconnector123* のようになります。

	![][apiapphost]

3. [API アプリのホスト] ブレードで、**[設定]** を選択します。[設定] ブレードで、**[アプリケーション設定]** をクリックします。**[アプリ設定]** の下に構成値が表示されます。
	
	![][hostsettings]

4. 変更する設定をクリックし、新しい値を入力し、変更を**保存**します。


## Hybrid Connection Manager をインストールする (省略可能)

![][hcsetup]

ハイブリッド接続マネージャーを使用すると、SQL Server、SAP などのオンプレミスのシステムに接続できます。このハイブリッド接続は、Azure Service Bus を使用して接続し、Azure リソースとオンプレミスのリソースの間のセキュリティを制御します。

「[Azure App Service での Hybrid Connection Manager の使用](app-service-logic-hybrid-connection-manager.md)」を参照してください。

> [AZURE.NOTE]Hybrid Connection Manager は、ファイアウォールの背後にあるオンプレミス リソースに接続する場合にのみ必要です。オンプレミスのシステムに接続していない場合、Hybrid Connection Manager は [コネクタ] ブレードに表示されない可能性があります。

## パフォーマンスを監視する
パフォーマンス メトリックは組み込み機能であり、作成するすべての API アプリに含まれます。これらのメトリックは、Azure でホストされる API アプリに固有です。メトリックの例:

![][monitoring]

そのための方法は次のとおりです。

- **[要求およびエラー]** を選択して、よく知られている HTTP エラー コード (200、400、500 HTTP ステータス コードなど) を含むさまざまなパフォーマンス メトリックを追加。応答時間、API アプリに対して実行された要求の数、受信したデータの量、および送信したデータの量を確認できます。パフォーマンス メトリックに基づいて、メトリックが選択したしきい値を超えた場合に送信される電子メールアラートを作成できます。 
- **[使用]** で、API アプリによって使用されている **CPU** の量、現在の**使用量クォータ** (MB 単位)、およびコスト レベルに基づく最大データ使用量を確認できます。**概算料金**は、API アプリの実行にかかる予想コストを判断するのに役立ちます。
- **[プロセス]** を選択してプロセス エクスプローラーを表示。プロセス エクスプローラーには、Web インスタンスとそのプロパティ (スレッド数、メモリ使用量など) が表示されます。

これらのツールを使用して、App Service プランをビジネス ニーズに基づいてスケール アップまたはスケール ダウンする必要があるかどうかを判断できます。これらの機能は、追加ツールなしでポータルに組み込まれます。

## セキュリティを制御する

API Apps では、ロールベースのセキュリティを使用します。これらのロールは、API Apps とその他の Azure リソースを含む Azure のエクスペリエンス全体に適用されます。次のロールがあります。

役割 | 説明
--- | ---
所有者 | 管理エクスペリエンスへのフル アクセスを持ち、その他のユーザーまたはグループにアクセス権を付与できます。
共同作成者 | 管理エクスペリエンスへのフル アクセスを持ちます。その他のユーザーまたはグループにアクセス権を付与することはできません。
閲覧者 | 機密データを除くすべてのリソースを表示できます。
ユーザーアクセスの管理者 | すべてのリソースの表示、ロールの作成/管理、およびサポート チケットの作成/管理を実行できます。

「[Microsoft Azure ポータルでのロールベースのアクセス制御](role-based-access-control-configure.md)」を参照してください。

API アプリへのユーザーの追加と特定のロールの割り当ては簡単に実行できます。アクセス権があるユーザーと、各自に割り当てられたロールがポータルに表示されます。

![][access]

- ユーザーの追加、ロールの割り当て、およびユーザーの削除を実行するには、**[ユーザー]** を選択します。
- 特定のロールを持つすべてのユーザーの表示、ユーザーへのロールの追加、およびロールからのユーザーの削除を実行するには、**[ロール]** を選択します。 


## その他の便利な機能
- 特定の API アプリのために自動的に作成された Swagger ファイルを開くには、選択**[API 定義]** を選択します。
- API アプリによって要求されているファイルを表示するには、**[依存関係]** を選択します。たとえば、SAP コネクタを使用している場合は、オンプレミスの Hybrid Connection Manager に追加ファイルがいくつかインストールされます。これらの依存関係は、[API アプリ] ブレードに表示されます。 

> [AZURE.IMPORTANT]API アプリのプロパティを開き、**[基本要素]** を見ると、新しいブレードを開く **[ホスト]** リンクと **[ゲートウェイ]** リンクがあります。
> 
> ![][host]
> 
> これらのプロパティは、API アプリをホストする Web サイトに固有です。組み込み API アプリまたはコネクタを使用するとき、これらのプロパティの大半は実際には適用されないため、これらのプロパティを更新しないことをお勧めします。Visual Studio で独自の API アプリを作成し、Azure サブスクリプションにデプロイした場合は、[ホスト] ブレードと [ゲートウェイ] ブレードを使用できます。「[API アプリの管理](../app-service-api/app-service-api-manage-in-portal.md)」に、カスタム作成した API アプリに関して、これらのブレードで何ができるかについての詳細な説明があります。 



>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure Logic Apps の使用を開始する場合は、「[Azure App Service アプリケーションの作成](https://tryappservice.azure.com/?appservice=logic)」を参照してください。App Service で有効期間の短いスターター ロジック アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。


## 詳細

[Logic Apps を監視する](app-service-logic-monitor-your-logic-apps.md)<br/>
[App Service のコネクタと API Apps](app-service-logic-connectors-list.md)<br/>
[Microsoft Azure ポータルでのロールベースのアクセス制御](role-based-access-control-configure.md)<br/>
[Azure App Service での Hybrid Connection Manager の使用](app-service-logic-hybrid-connection-manager.md)


<!--Image references-->
[browse]: ./media/app-service-logic-monitor-your-connectors/browse.png
[settings]: ./media/app-service-logic-monitor-your-connectors/settings.png
[hcsetup]: ./media/app-service-logic-monitor-your-connectors/hcsetup.png
[monitoring]: ./media/app-service-logic-monitor-your-connectors/monitoring.png
[access]: ./media/app-service-logic-monitor-your-connectors/access.png
[host]: ./media/app-service-logic-monitor-your-connectors/host.png
[hostsettings]: ./media/app-service-logic-monitor-your-connectors/hostsettings.png
[apiapphost]: ./media/app-service-logic-monitor-your-connectors/apiapphost.png

<!---HONumber=AcomDC_1210_2015-->
