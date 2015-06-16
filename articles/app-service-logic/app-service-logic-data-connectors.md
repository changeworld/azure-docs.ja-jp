<properties 
	pageTitle="Microsoft Azure API Apps のデータ コネクタ | API Apps マイクロサービス" 
	description="Microsoft Azure のデータ コネクタ API Apps を作成し、API アプリをロジック アプリ、マイクロサービスに追加する方法を説明します" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MSFTMan" 
	manager="dwrede" 
	editor=""/>

<tags
	ms.service="app-service-logic" 
	ms.workload="data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="deonhe"/>


# Microsoft Azure App Service のデータ コネクタ


## アプリ + データ サービス API アプリ コネクタとは
アプリおよびデータ サービス コネクタは、さまざまなデータ サービス アプリケーションに接続できる "API Apps" であり、認証されたユーザーに代わって複数の*アクション*を実行します。これらのコネクタの大部分は、*トリガー*を使用して構成することもできます。トリガーとは、コネクタに対してワークフローを開始するように構成できるイベントです (.NET Framework のイベントに似ています)。

たとえば、Dropbox コネクタのインスタンスは、監視中の Dropbox アカウントに新しいファイルが追加されるたびに実行される*新しいファイル* トリガーを使用して構成できます。さらにこのトリガーは、ファイルを*取得*してオンプレミスの SharePoint のリストにアップロードする*アクション*を開始するように構成できます。


次の表には、Azure ギャラリーで利用できる各種のアプリおよびデータ サービス コネクタの概要を示します。

<table>
<tr>
<th> 名前</th>
<th> 説明</th>
<th> トリガー</th>
<th>アクション</th>

<tr>
<td>Azure Media Services
<td>Azure Media Services コネクタでは、柔軟かつスケーラブルなエンコード、パッケージ化、配布を可能にするエンド ツー エンドのメディア ワークフローを作成できます。また、ビデオやオーディオのコンテンツを安全にアップロード、保存、エンコード、ストリーム化し、テレビ、PC、モバイル デバイスなどさまざまなエンドポイントに、オンデマンドとライブの両方式でストリーミング配信できるようになります。
<td>なし
<td>なし 
</tr>

<tr>
<td>Azure のサービス バス
<td>Azure Service Bus コネクタでは、キューやトピックなどの Service Bus エンティティからメッセージを送信し、キューやサブスクリプションなどの Service Bus エンティティからメッセージを受信できます。
<td>新しいメッセージ
<td>メッセージを送信
</tr>
<tr>
<td>Azure Storage Table
<td>Azure Storage Table コネクタでは、Azure Storage Table に接続して、エンティティの取得、エンティティの照会、エンティティの挿入、エンティティの更新、エンティティの削除、データを取得するためのトリガー機能など、さまざまなアクションを実行できます。
<td>新しいエンティティ
<td><li>エンティティを取得
	<li>エンティティを挿入
	<li>エンティティを削除
	<li>エンティティを更新
	<li>クエリ

</tr>

<tr>
<td>Box
<td>Box コネクタでは、Box に接続して、ファイルにかかわるさまざまなアクションを実行できます。
<td>新しいファイルが追加された
<td><li>ファイルをアップロード
<li>ファイルを取得
<li>ファイルを削除
<li>ファイルを一覧表示
</tr>

<tr>
<td>DB2
<td>DB2 コネクタを使用すると、Windows オペレーティング システムを実行している Azure 仮想マシン上およびオンプレミスの IBM DB2 データベースに接続できます。Informix Structured Query Language (Informix 構造化クエリ言語) のコマンドに Web API と OData API の操作をマップできます。オンプレミスで使用する場合は、VPN または Azure ExpressRoute を使用できます。このコネクタには、TCP/IP ネットワーク経由で Informix サーバーに接続するための Microsoft Client for DRDA も含まれています。
<td>なし
<td><li>テーブルの選択
<li>挿入
<li>更新
<li>削除
<li>カスタム ステートメント
</tr>

<tr>
<td>Dropbox
<td>Dropbox コネクタでは、Dropbox に接続して、ファイルのアップロード、ファイルの取得、ファイルの削除、ファイルの一覧表示、ファイルを取得するためのトリガー機能など、さまざまなアクションを実行できます。
<td>新しいファイルが追加された
<td><li>ファイルをアップロード
<li>ファイルを取得
<li>ファイルを削除
<li>ファイルを一覧表示
</tr>

<tr>
<td>HDInsight
<td>HDInsight コネクタでは、Azure 上で Hadoop クラスターを作成し、Hive、Pig、MapReduce、ストリーミング MapReduce など、さまざまな Hadoop ジョブを作成して送信できます。また、このコネクタを使用して、クラスターのスピン、ジョブの送信、ジョブの完了待機を実行できます。
<td>なし
<td><li>クラスターを作成
		<li>クラスターの作成を待機
		<li>Pig ジョブを送信
		<li>Hive ジョブを送信
		<li>MapReduce ジョブを送信
		<li>ジョブの完了を待機
		<li>クラスターを削除
<li>MapReduce ジョブを送信
<li>ストリーミング MapReduce ジョブを送信
</tr>

<tr>
<td>Informix
<td>Informix コネクタは、Windows オペレーティング システムを実行している Azure 仮想マシン上およびオンプレミスの IBM Informix データベースに接続します。Informix Structured Query Language (Informix 構造化クエリ言語) のコマンドに Web API と OData API の操作をマップできます。オンプレミスで使用する場合は、VPN または Azure ExpressRoute を使用できます。このコネクタには、TCP/IP ネットワーク経由で Informix サーバーに接続するための Microsoft Client for DRDA も含まれています。
<td>なし
<td><li>テーブルの選択
<li>挿入
<li>更新
<li>削除
<li>カスタム ステートメント
</tr>

<tr>
<td>Microsoft SQL
<td>Microsoft SQL コネクタでは、Microsoft SQL Server および Azure SQL データベースのテーブル エントリの作成と変更を実行できます。
<td>データのポーリング
<td><li>テーブルに挿入
	<li>テーブルの更新
	<li>テーブルから選択
	<li>テーブルから削除
	<li>ストアド プロシージャの呼び出し
</tr>

<tr>
<td>MongoDB
<td>MongoDB コネクタでは、MongoDB コレクションからのドキュメントの作成、更新、削除、取得を実行できます。
<td>新しいドキュメント
<td>	<li>ドキュメントを追加
		<li>ドキュメントを更新
		<li>ドキュメントを取得
		<li>ドキュメントを upsert
		<li>ドキュメントを削除
</tr>


<tr>
<td>MQ
<td>MQ コネクタは、Windows オペレーティング システムを実行している Azure 仮想マシン上およびオンプレミスの IBM WebSphere MQ Server Version 8 に接続します。オンプレミスで使用する場合は、VPN または Azure ExpressRoute を使用できます。コネクタには、Microsoft Client for MQ も含まれています。<br/><br/><strong>注</strong>: 現在のところ、Logic Apps で使用することはできません。
<td>なし
<td>なし
</tr>

<tr>
<td>Office 365
<td>Office 365 コネクタでは、電子メールの送受信、および予定表と連絡先の管理を実行できます。
<td>新しいメッセージ
<td>	<li>メールの送信
		<li>メールへの返信
		<li>送信イベント
		<li>連絡先の追加
</tr>

<tr>
<td>OneDrive
<td>OneDrive コネクタでは、個人の Microsoft OneDrive のストレージ アカウントに接続し、ファイルのアップロード、取得、削除、一覧表示などのさまざまなアクションを実行できます。
<td>新しいファイル
<td><li>ファイル: アップロード、削除、一覧表示、ダウンロード
</tr>

<tr>
<td>Oracle
<td>Oracle データベース コネクタでは、Oracle データベースのテーブル エントリを作成および変更できます。
<td>クエリに基づく新しいデータ
<td><li>テーブル: 挿入、更新、選択、削除
<li>ストアド プロシージャの呼び出し
</tr>

<tr>
<td>SharePoint Online
<td>Microsoft SharePoint コネクタでは、SharePoint サーバーまたは Microsoft SharePoint Online のドキュメントとリスト アイテムを作成および変更できます。
<td><li>新しいドキュメント
<li>新しいリスト アイテム
<td><li>ドキュメント ライブラリ: アップロード、削除、および取得
<li>リスト: アイテムの挿入

</tr>

<tr>
<td>SharePoint Server
<td>SharePoint サーバー コネクタでは、SharePoint サーバーのドキュメントとリスト アイテムを管理できます。既定の資格情報、Windows 認証、フォーム ベース認証がサポートされています。サービス バス接続文字列を提供し、サーバーに接続する前に、オンプレミスのリスナー エージェントをインストールする必要があります。
<td><li>新しいドキュメント
<li>新しいリスト アイテム
<td><li>ドキュメント ライブラリ: アップロード、削除、および取得
<li>リスト: アイテムの挿入
</tr>
</table>


## コネクタを使用する理由

コネクタを使用することによって、アプリの開発が促進されるだけでなく、開発者ではない場合でも、プログラミング言語を学習したりコードを記述したりすることなく、十分な機能を持つエンタープライズ レベルのアプリケーションを作成できます。

以上で、アプリ + データ サービス用コネクタの機能が理解できたと思います。以下では、これらのコネクタの簡単な使用例をいくつか紹介します。

### Dropbox を監視して SharePoint を更新
設計図を含む非常に大きなファイルを受信する建設会社について考えます。これらのファイルは、通常、電子メールで取り扱うには大きすぎるため、この会社は Dropbox アカウントを設定し、顧客に対して設計図を Dropbox にドロップするように依頼しています。また、従業員に対しては、Dropbox フォルダーに新しい設計図がないかどうかを常時確認し、あった場合には、SharePoint サーバーにアップロードするように指示しています。しかし、もっと良い方法があるのではないかと考えていました。 タイミングよく、マイクロソフトが、最近、Dropbox や Sharepoint などのデータ サービス用の、アプリ + データ サービス コネクタをリリースしたことを知りました。Dropbox と SharePoint 用のコネクタは、簡単な操作で作成してロジック アプリに追加し、Dropbox アカウントに新しいファイルが保存されるたびに、それを SharePoint リストにアップロードするように構成できます。Dropbox コネクタには、*新しいメッセージ* トリガーがあります。これによって、利用できる新しいファイルがあることをロジック アプリに通知できます。さらに、Dropbox コネクタがこのファイルをダウンロードするようにもできます。Sharepoint コネクタは、SharePoint の*アップロード* アクションを使用して、SharePoint リストにファイルをアップロードするように構成できます。これらの一連の作業は、コードを 1 行も記述することなく行うことができます。

それでは始めましょう。

## コネクタの作成
コネクタ API Apps は、Azure ポータルを使用して作成できます

### Microsoft Azure ポータルで SharePoint コネクタを作成する

1. Azure ポータルで、**[新規]**、**[Web + モバイル]**、**[Azure Marketplace]** の順に選択します。
2. コネクタを**検索**するか、一覧から選択します。選択すると、新しいブレード (ウィンドウ) が開きます。**[作成]** を選択します。 
3. コネクタについて、次のプロパティを入力します。<table> <tr><th>プロパティ</th> <th>説明</th> </tr> <tr><td>名前</td> <td>API アプリの任意の名前を入力します。たとえば、RulesDiscountTaxCode や APIAppValidateXML などの名前を付けます。</td> </tr> <tr><td>App Service プラン</td> <td>支払プランの一覧を表示します。プランは必要なリソースの増減に応じて変更することができます。</th> </td> <tr><td>価格レベル</td> <td>Azure サブスクリプション内での価格のカテゴリの一覧を表示する読み取り専用プロパティです。</td> </tr> <tr><td>リソース グループ</td> <td>新しいリソース グループを作成するか、既存のグループを使用します。リソース グループの使用に関するページに、このプロパティの説明があります。</td> </tr> <tr><td>サブスクリプション</td> <td>現在のサブスクリプションを一覧表示する読み取り専用のプロパティ</td> </tr> <tr><td>場所</td> <td>Azure サービスをホストする地理的な場所 </td></tr> <tr><td>スタート画面に追加</td> <td>スタート画面 (ホーム ページ) に API アプリを追加するには、このプロパティを選択します。</td></tr> </table> 
4. **[作成]** を選択します。コネクタが作成されます。完了するまで、しばらくかかる場合があります。コネクタを作成している間は、ホーム画面が表示されます。コネクタの状態を監視するには、左側の [通知] メニュー項目を使用します。

最初のコネクタの作成が完了しました。このコネクタを使用した Web アプリ、モバイル アプリ、またはロジック アプリの作成を検討してください。


### REST API を使用したコネクタへのアクセス

[REST API によるコネクタへのアクセス](http://go.microsoft.com/fwlink/p/?LinkId=529766)

## アプリケーションへのコネクタの追加 
Microsoft Azure App Service は、上記のコネクタが使用できるさまざまな種類のアプリケーションを公開しています。たとえば、1 つ以上のコネクタを 1 つのアプリと*論理的に*組み合わせることによって、*ロジック* アプリを作成できます。

*ロジック* アプリ内でコネクタを使用する場合は、構成済みのコネクタを一覧から選択してデザイン ワーク フローに追加し、必要な構成変更を行って、いつでも使用できるようにしてください。

上記手順を実行する場合は、Web アプリ、モバイル アプリ、またはロジック アプリが必要です。具体的な方法については、<> を参照してください。アプリケーションが使用できる状態になったら、コネクタを追加します。その方法は次のとおりです。

以下の手順に従って、コネクタをロジック アプリに追加します。

1. Azure ポータルのスタート画面 (ホーム ページ) で、**Marketplace** に移動し、ロジック アプリ、モバイル アプリ、または Web アプリを検索します。 

	新規にアプリを作成する場合は、ロジック アプリ、モバイル アプリ、または Web アプリを検索します。アプリを選択し、新しいブレードで **[作成]** を選択します。[ロジック アプリの作成](app-service-logic-create-a-logic-app.md)に関するページに、この手順が記載されています。

2. アプリを開き、**[トリガーとアクション]** を選択します。
3. **[ギャラリー]** でコネクタを選択します。選択したコネクタがアプリに追加されます。
4. コネクタを構成します。
5. どのコネクタにも、接続しているサービスおよび環境に固有のプロパティがあります。プロパティの詳細を入力します。一部のプロパティは省略可能です。
6. **[OK]** を選択して変更を保存します。


## セキュリティ
コネクタは、OAuth か、ユーザー名とパスワードを使用します。


## Logic Apps と Web Apps に関するガイド
[Logic Apps とは](app-service-logic-what-are-logic-apps.md) | [Azure App Service の Websites と Web Apps](../app-service-web/app-service-web-app-azure-portal.md) |



## その他のコネクタ
[BizTalk 統合コネクタ](app-service-logic-integration-connectors.md) | [エンタープライズ コネクタ](app-service-logic-enterprise-connectors.md) | [企業間コネクタ](app-service-logic-b2b-connectors.md) | [ソーシャル コネクタ](app-service-logic-social-connectors.md) | [プロトコル コネクタ](app-service-logic-protocol-connectors.md) | [アプリおよびデータ サービス コネクタ](app-service-logic-data-connectors.md) | [コネクタと API Apps の一覧](app-service-logic-connectors-list.md)<br/><br/> [コネクタと BizTalk API Apps とは](app-service-logic-what-are-biztalk-api-apps.md)

<!--HONumber=54--> 