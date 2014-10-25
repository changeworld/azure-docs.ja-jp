<properties linkid="develop-net-tutorials-multi-tier-web-site-1-overview" pageTitle="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, MVC Web Role tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande"></tags>

# Azure クラウド サービス チュートリアル: ASP.NET MVC Web ロール、Worker ロール、Azure ストレージ テーブル、キュー、BLOB (1/5)

このチュートリアル シリーズでは、Azure クラウド サービスで実行し、Azure のストレージ テーブル、キュー、BLOB を使用する、多層 ASP.NET MVC Web アプリケーションの作成および展開方法について説明します。MSDN コード ギャラリーから[完成したアプリケーション][完成したアプリケーション]をダウンロードするか、TechNet の電子書籍ギャラリーから以前のバージョンの[電子書籍][電子書籍]をダウンロードできます。

次の図に、アプリケーションの各パーツのやり取りを示します。

![電子メール メッセージの処理][電子メール メッセージの処理]

これは 5 部構成のチュートリアル シリーズです。クラウド サービス、キュー、BLOB の概要をすばやく簡単に理解するには、「[Azure Cloud Services と ASP.NET を使ってみる][Azure Cloud Services と ASP.NET を使ってみる]」を参照してください。代わりに Web サイトおよび Web ジョブで多層アプリケーションを実行できます。詳細については、「[Get Started with the Azure WebJobs SDK (Azure Web ジョブ SDK の概要)][Get Started with the Azure WebJobs SDK (Azure Web ジョブ SDK の概要)]」を参照してください。

このチュートリアル シリーズでは、次のことについて説明します。

-   Azure SDK をインストールして、Azure 向け開発用にコンピューターを準備する方法
-   ASP.NET MVC の Web ロール (1 つ) と Worker ロール (2 つ) を使用して、Visual Studio クラウド プロジェクトを作成する方法
-   作成したクラウド プロジェクトを Azure クラウド サービスに発行する方法
-   Azure キュー ストレージ サービスを使用して、複数の階層間または Worker ロール間でやり取りする方法
-   拡張性に優れたデータ ストアとして Azure テーブル ストレージ サービスを使用し、構造化された非リレーショナル データを格納する方法
-   Azure BLOB サービスを使用して、ファイルをクラウドに格納する方法
-   Visual Studio サーバー エクスプローラーを使用して、Azure のテーブル、キュー、BLOB を表示および編集する方法
-   SendGrid を使用して電子メールを送信する方法
-   トレース機能を構成し、トレース データを表示する方法
-   ワーカー ロール インスタンスの数を増やすことによって、アプリケーションの規模を拡張する方法

## <a name="toc"></a>このシリーズのチュートリアル

このシリーズには 5 つのチュートリアルがあります。

1.  **Azure 電子メール サービス アプリケーションの概要** (このチュートリアル): アプリケーションとそのアーキテクチャの詳細情報について説明します。展開方法またはコードを表示する場合は、この手順をスキップし、後でここに戻ってアーキテクチャをよく理解することができます。
2.  [Configuring and Deploying the Azure Email Service application (Azure 電子メール サービス アプリケーションの構成と展開)][Configuring and Deploying the Azure Email Service application (Azure 電子メール サービス アプリケーションの構成と展開)]: サンプル アプリケーションをダウンロードして構成し、ローカルでテストする方法、およびクラウドにデプロイしてテストする方法を説明します。
3.  [Building the web role for the Azure Email Service application (Azure 電子メール サービス アプリケーションで使用する Web ロールの作成)][Building the web role for the Azure Email Service application (Azure 電子メール サービス アプリケーションで使用する Web ロールの作成)]: このアプリケーションの MVC コンポーネントを作成し、それらをローカルでテストする方法を説明します。
4.  [Building worker role A (email scheduler) for the Azure Email Service application (Azure 電子メール サービス アプリケーションで使用する Worker ロール A (電子メール スケジューラ) の作成)][Building worker role A (email scheduler) for the Azure Email Service application (Azure 電子メール サービス アプリケーションで使用する Worker ロール A (電子メール スケジューラ) の作成)]: 電子メール送信に必要なキュー作業項目を作成するバックエンド コンポーネントを構築し、それをローカルでテストする方法を説明します。
5.  [Building worker role B (email sender) for the Azure Email Service application (Azure 電子メール サービス アプリケーションで使用する Worker ロール B (電子メール送信) の作成)][Building worker role B (email sender) for the Azure Email Service application (Azure 電子メール サービス アプリケーションで使用する Worker ロール B (電子メール送信) の作成)]電子メール送信に必要なキュー作業項目を処理するバックエンド コンポーネントを構築し、それをローカルでテストする方法を説明します。

## このチュートリアルの内容

-   [前提条件][前提条件]
-   [フロントエンドの概要][フロントエンドの概要]
-   [バックエンドの概要][バックエンドの概要]
-   [Azure テーブル][Azure テーブル]
-   [Azure キュー][Azure キュー]
-   [データ図][データ図]
-   [Azure BLOB][Azure BLOB]
-   [Azure クラウド サービスと Azure Web サイト][Azure クラウド サービスと Azure Web サイト]
-   [コスト][コスト]
-   [認証と権限承認][認証と権限承認]
-   [次のステップ][次のステップ]

## 前提条件

これらのチュートリアルの手順は、次の製品で機能します。

-   Visual Studio 2013 Update 2
-   Visual Studio 2013 Express for Web Update 2

Azure サブスクリプションも必要です。[無料評価版アカウント][無料評価版アカウント]を作成するか、[MSDN サブスクライバーの特典を有効にする][MSDN サブスクライバーの特典を有効にする]ことができます。

## <a name="frontend"></a>フロントエンドの概要

このアプリケーションは電子メール一覧サービスです。フロントエンドは、サービスの管理者が電子メール リストを管理するための Web ページで構成されます。

(スクリーン ショットは、Visual Studio 2012 のテンプレート スタイルを示しています。コンテンツは Visual Studio 2013 と同じですが、スタイルが異なります。)

![Mailing List Index Page][Mailing List Index Page]

![Subscriber Index Page][Subscriber Index Page]

管理者がメーリング リストへ送信するメッセージを作成するためのページもあります。

![Message Index Page][Message Index Page]

![Message Create Page][Message Create Page]

このサービスは、自社の Web サイト上で顧客がメーリング リストに配信登録できるようにします。たとえば、Contoso 大学史学部のお知らせをメール配信するとしましょう。管理者はそのためのリストを設定します。史学部のお知らせを購読したいと思った学生が Contoso 大学の Web サイトでリンクをクリックすると、Azure 電子メール サービス アプリケーションが呼び出され、その学生に電子メールが送信されます。ここで送信される電子メールにはハイパーリンクが含まれています。メールを受け取った学生がこのリンクをクリックすると、史学部お知らせリストのウェルカム ページが表示されます。

![確認用メール][確認用メール]

![リストのウェルカム ページ][リストのウェルカム ページ]

送信されるすべての電子メール (登録確認用メールを除く) には、登録を解除するためのハイパーリンクが含まれています。メール受信者がこのリンクをクリックすると、登録を解除するかどうかを確認する Web ページが表示されます。

![登録解除の確認ページ][登録解除の確認ページ]

メール受信者が **[確認]** ボタンをクリックすると、そのユーザーがリストから削除されたことを知らせるページが表示されます。

![Unsubscribe confirmed page][Unsubscribe confirmed page]

## <a name="backend"></a>バックエンドの概要

フロントエンドは、電子メール リストとそれらのリストに送信するメッセージを Azure のテーブルに格納します。管理者がメッセージの送信スケジュールを設定すると、送信予定日とその他のデータ (件名など) を含むテーブル行が `message` テーブルに追加されます。Worker ロールは `message` テーブルを定期的にスキャンし、送信する必要があるメッセージを探します (これを Worker ロール A と呼びます)。

要送信メッセージが見つかった場合、Worker ロール A は次のタスクを実行します。

-   すべての電子メール アドレスをメール送信先リストに取り込む。
-   各電子メールの送信に必要な情報を `message` テーブルに追加する。
-   送信する各電子メールのキュー作業項目を作成する。

別の Worker ロール (Worker ロール B) が作業項目のキューをポーリングします。Worker ロール B が作業項目を見つけると、電子メールを送信し、その作業項目をキューから削除します。次の図はこれらの関連性を示しています。

![電子メール メッセージの処理][電子メール メッセージの処理]

Worker ロール B が停止して再起動する必要がある場合でも、電子メールの不達は発生しません。電子メールが送信されない限り、その電子メールのキュー作業項目は削除されないためです。Worker ロール A が停止して再起動する必要がある場合、アプリケーションは同じ電子メールが複数送信されないようにします。

![重複する電子メールの送信を防ぐ][重複する電子メールの送信を防ぐ]

Worker ロール B は、Web API サービス メソッドによって追加された作業項目の配信登録キューをポーリングし、新規登録がないかどうかをチェックします。新規登録が見つかった場合は、確認用の電子メールを送信します。

![配信登録キュー メッセージの処理][配信登録キュー メッセージの処理]

## <a name="tables"></a>Azure テーブル

Azure 電子メール サービス アプリケーションは、データを Azure のストレージ テーブルに保存します。Azure のテーブルは NoSQL データ ストアです。[Azure SQL データベース][Azure SQL データベース]のようなリレーショナル データベースではありません。Azure テーブルは、データの正規化やリレーショナル整合性より、効率性と拡張性が重要な場合に適しています。たとえばこのアプリケーションでは、キュー作業項目が作成されるたびに、Worker ロールが行を作成します。また、電子メールが送信されるたびに、別の Worker ロールが行を取得して更新します。リレーショナル データベースを使用した場合、このような処理はパフォーマンスのボトルネックになる可能性があります。さらに、Azure のテーブルは Azure SQL より安価です。Azure テーブルの詳細については、[このシリーズの最終チュートリアル][Building worker role B (email sender) for the Azure Email Service application (Azure 電子メール サービス アプリケーションで使用する Worker ロール B (電子メール送信) の作成)]を参照してください。

この後のセクションでは、Azure 電子メール サービス アプリケーションで使用する Azure のテーブルの内容について説明します。これらのテーブルと相互の関連性を示す図は、このページ後半の「[Azure 電子メール サービスのデータ図][データ図]」に記載されています。

### mailinglist テーブル

`mailinglist` テーブルには、メーリング リストおよびメーリング リスト登録者に関する情報が格納されます (慣例として、Azure のテーブル名ではすべて小文字を使用します)。

Azure テーブルでは、行ごとにそれぞれ異なるスキーマを設定できます。この柔軟性により、リレーショナル データベースであれば複数のテーブルが必要になる場合でも、1 つのテーブルにデータを格納できます。たとえば、メーリング リストのデータを SQL データベースに保存した場合、3 つのテーブル (メーリング リストに関する情報を格納する `mailinglist` テーブル、登録者に関する情報を格納する `subscriber` テーブル、メーリング リストと登録者とを関連付ける `mailinglistsubscriber` テーブル) が必要になります。このアプリケーションでは、`mailinglist` という名前の 1 つの NoSQL テーブルにこれらすべての情報を格納します。

Azure のテーブルでは、行ごとに *パーティション キー* が割り当てられ、さらに各行を一意に識別する *行キー* が割り当てられます。パーティション キーは、テーブルを論理的に複数のパーティションに分割します。1 つのパーティション内では、行キーによって各行が一意に識別されます。補助的なインデックスはありません。したがって、アプリケーションの拡張性を高めるには、常にパーティション キーと行キーでクエリを実行できるようにテーブルを設計することが重要です。

`mailinglist` テーブルのパーティション キーはメーリング リストの名前です。

`mailinglist` テーブルの行キーは、定数 "mailinglist"、または登録者の電子メール アドレスになります。行キーが "mailinglist" である行には、そのメーリング リストに関する情報が格納されます。行キーが電子メール アドレスである行には、そのリストの登録者に関する情報が格納されます。

つまり、行キーが "mailinglist" である行は、リレーショナル データベースの `mailinglist` テーブルに該当します。行キー = 電子メール アドレスである行は、リレーショナル データベースの `subscriber` テーブルおよび `mailinglistsubscriber` 関連テーブルに該当します。

このように、1 つのテーブルを複数の目的で使用することでパフォーマンスが向上します。リレーショナル データベースでは、3 つのテーブルを読み取り、3 つの行セットを並べ替えて互いに照合しなければならないので、時間がかかります。ここでは、1 つのテーブルのみを読み取り、パーティション キー、行キーの順序でテーブル内の行が自動的に返されます。

次の表は、メーリング リスト情報を格納する行 (行キー = "mailinglist") のプロパティを示しています。

| プロパティ       | データ型 | 説明                                                                                                                                                                                                                     |
|------------------|----------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PartitionKey     | String   | ListName: メーリング リストの一意の識別子 (contoso1 など)。通常、このテーブルは、特定のメーリング リストのすべての情報を取得するときに使用されます。したがって、リスト名を使用することでテーブルを効率的に分割できます。 |
| RowKey           | String   | 定数 "mailinglist"。                                                                                                                                                                                                     |
| 説明             | String   | メーリング リストの説明。例:"Contoso 大学史学部のお知らせ"                                                                                                                                                               |
| FromEmailAddress | String   | このリストに送信する電子メールの "差出人" アドレス。例:donotreply@contoso.edu                                                                                                                                            |

次の表は、このリストの登録者情報が格納される行 (行キー = 電子メール アドレス) のプロパティを示しています。

| プロパティ     | データ型 | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|----------------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PartitionKey   | String   | ListName: メーリング リストの名前 (一意の識別子)。例: contoso1                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| RowKey         | String   | EmailAddress: 登録者の電子メール アドレス。例: student1@contoso.edu                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| SubscriberGUID | String   | 電子メール アドレスがリストに追加された時点で生成されます。登録リンクと登録解除リンクで使用し、他の人の電子メール アドレスが登録または登録解除されないようにします。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    
                              登録用 Web ページと登録解除用 Web ページの一部の照会では、PartitionKey とこのプロパティのみが指定されます。RowKey を使用しないでパーティションを照会すると、メーリング リストのサイズが大きくなるほど照会時間が長くなるので、このアプリケーションの拡張性が制限されます。拡張性を高めるには、RowKey プロパティが SubscriberGUID であるルックアップ行を追加する方法があります。たとえば、各電子メール アドレスについて、1 つの行の RowKey を "email:student1@domain.com" に設定します。さらに、同じ登録者の別の行の RowKey を "guid:6f32b03b-90ed-41a9-b8ac-c1310c67b66a" に設定します。同一パーティション内の複数行に対するアトミック バッチ トランザクションはコーディングが容易なので、このしくみはすぐに実装できます。詳細については、「[Real World: Designing a Scalable Partitioning Strategy for Azure Table Storage (リアル ワールド: Azure テーブル ストレージ用の拡張性が高いパーティション分割方法の設計)][Real World: Designing a Scalable Partitioning Strategy for Azure Table Storage (リアル ワールド: Azure テーブル ストレージ用の拡張性が高いパーティション分割方法の設計)]  |
| Verified       | Boolean  | 新規登録者に対して対象行が最初に作成されたとき、このプロパティには false が設定されます。確認電子メールを受け取った新規登録者が "確認用" ハイパーリンクをクリックすると、この値が true に変わります。メーリング リストにメッセージを送信する際、いずれかの登録者の Verified 値が false の場合、その登録者には電子メールが送信されません。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |

次に、このテーブルに格納されるデータの例を示します。

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partition Key

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Row Key

</th>
<td>
mailinglist

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
説明

</th>
<td>
Contoso 大学史学部のお知らせ

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
FromEmailAddress

</th>
<td>
donotreply@contoso.edu

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partition Key

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Row Key

</th>
<td>
student1@domain.com

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
6f32b03b-90ed-41a9-b8ac-c1310c67b66a

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Verified

</th>
<td>
true

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partition Key

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Row Key

</th>
<td>
student2@domain.com

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
01234567-90ed-41a9-b8ac-c1310c67b66a

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Verified

</th>
<td>
false

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partition Key

</th>
<td>
fabrikam1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Row Key

</th>
<td>
mailinglist

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
説明

</th>
<td>
Fabrikam エンジニアの求人

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
FromEmailAddress

</th>
<td>
donotreply@fabrikam.com

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partition Key

</th>
<td>
fabrikam1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Row Key

</th>
<td>
applicant1@domain.com

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
76543210-90ed-41a9-b8ac-c1310c67b66a

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Verified

</th>
<td>
true

</td>
</tr>
</table>
### message テーブル

`message` テーブルには、メーリング リストへ送信されるメッセージに関する情報が格納されます。管理者は Web ページを使用して、このテーブルの行を作成および編集します。また、各電子メールに関する情報を Worker ロール A から Worker ロール B へ渡すときも、このテーブルを使用します。

message テーブルのパーティション キーは電子メールの送信予定日 (yyyy-mm-dd 形式) です。これにより、このテーブルに対して最も頻繁に適用されるクエリ (`ScheduledDate` が今日以前の行を選択) を適切に実行できるようになります。ただし、Azure のストレージ テーブルの最大スループットは 500 エントリ/秒なので、パフォーマンスが低下する可能性があります。このアプリケーションでは、送信する電子メールごとに `message` テーブル行の書き込み、行の読み取り、行の削除を行います。したがって、処理量の増加に合わせて追加する Worker ロールの数にかかわらず、1 日に送信する 1,000,000 通の電子メールを処理するには最短でおよそ 2 時間かかります。

`message` テーブルの行キーは、1 つは、定数 "message" + メッセージの一意キー (`MessageRef`)。もう1 つは、`MessageRef` 値 + 登録者の電子メール アドレスです。行キーが "message" で始まる行には、メッセージに関する情報 (送信先のメーリング リスト、送信予定日など) が格納されます。行キーが `MessageRef` と電子メール アドレスである行には、その電子メール アドレスにメールを送信する際に必要となるすべての情報が格納されます。

行キーが "message" で始まる行は、リレーショナル データベースの `message`テーブルに該当します。行キー = `MessageRef` + 電子メール アドレスである行は、リレーショナル データベースでは、`mailinglist`、`message`、`subscriber` の情報を含む結合クエリ ビューに該当します。

次の表は、`message` テーブルで、メッセージ自体に関する情報が格納される行のプロパティを示しています。

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">プロパティ</th>
<th align="left">データ型</th>
<th align="left">説明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">PartitionKey</td>
<td align="left">String</td>
<td align="left">メッセージの送信予定日 (yyyy-mm-dd 型式)。</td>
</tr>
<tr class="even">
<td align="left">RowKey</td>
<td align="left">String</td>
<td align="left">定数 &quot;message&quot; + <code data-inline="1">MessageRef</code> 値。<code data-inline="1">MessageRef</code> は、この行を作成する際、<code data-inline="1">DateTime.Now</code> から <code data-inline="1">Ticks</code> 値を取得することによって生成される一意の値です。<br /><br />注: マルチスレッド、マルチインスタンスの大規模アプリケーションは、Ticks の使用に伴う重複 RowKey 例外を処理できるようにする必要があります。Ticks 値は一意とは限りません。</td>
</tr>
<tr class="odd">
<td align="left">ScheduledDate</td>
<td align="left">Date</td>
<td align="left">メッセージの送信予定日 (<code data-inline="1">PartitionKey</code> と同じですが、Date 型式になります)。</td>
</tr>
<tr class="even">
<td align="left">SubjectLine</td>
<td align="left">String</td>
<td align="left">電子メールの件名。</td>
</tr>
<tr class="odd">
<td align="left">ListName</td>
<td align="left">String</td>
<td align="left">このメッセージの送信先リスト。</td>
</tr>
<tr class="even">
<td align="left">ステータス</td>
<td align="left">String</td>
<td align="left"><ul>
<li>&quot;Pending&quot; -- Worker ロール A は、メール送信をスケジュール設定するキュー メッセージをまだ作成していません。</li>
<li>&quot;Queuing&quot; -- Worker ロール A は、メール送信をスケジュール設定するキュー メッセージの作成を開始しました。</li>
<li>&quot;Processing&quot; -- Worker ロール A は、リスト内のすべての電子メール キュー作業項目を作成しました。ただし、まだ送信されていない電子メールもあります。</li>
<li>&quot;Completed&quot; -- Worker ロール B は、すべてのキュー作業項目の処理を完了しました (すべての電子メールが送信されました)。この後で説明するように、完了した行は <code data-inline="1">messagearchive</code> テーブルに格納されます。次期リリースでは、このプロパティが <code data-inline="1">enum</code> になる予定です。</li>
</ul></td>
</tr>
</tbody>
</table>

リストへ送信する電子メールのキュー メッセージを作成する際、Worker ロール A は `message` テーブルに電子メール行を作成します。Worker ロール B はその電子メールを送信し、該当する電子メール行を `messagearchive` テーブルへ移動します。さらに、`EmailSent` プロパティを `true` に更新します。ステータスが "Processing" であるメッセージのすべての電子メール行がアーカイブされると、Worker ロール A はステータスを "Completed" に設定し、その `message` 行を `messagearchive` テーブルへ移動します。

次の表は、`message` テーブルの電子メール行のプロパティを示しています。

| プロパティ        | データ型 | 説明                                                                                                             |
|-------------------|----------|------------------------------------------------------------------------------------------------------------------|
| PartitionKey      | String   | メッセージの送信予定日 (yyyy-mm-dd 型式)。                                                                       |
| RowKey            | String   | `MessageRef` 値、および `mailinglist` テーブルの `subscriber` 行に指定されている送信先電子メール アドレス。      |
| MessageRef        | Long     | `RowKey` の `MessageRef` 要素と同じ。                                                                            |
| ScheduledDate     | Date     | `message` テーブルの `message` 行に指定されている送信予定日 (`PartitionKey` と同じですが、Date 型式になります)。 |
| SubjectLine       | String   | `message` テーブルの `message` 行で指定されている電子メールの件名。                                              |
| ListName          | String   | `mailinglist` テーブルで設定されているメーリング リスト名。                                                      |
| From EmailAddress | String   | `mailinglist` テーブルの `mailinglist` 行で指定されている "差出人" 電子メール アドレス。                         |
| EmailAddress      | String   | `mailinglist` テーブルの `subscriber` 行で指定されている電子メール アドレス。                                    |
| SubscriberGUID    | String   | `mailinglist` テーブルの `subscriber` 行で指定されている登録者 GUID。                                            |
| EmailSent         | Boolean  | false = 電子メールは未送信。true = 電子メールは既に送信されています。                                            |

これらの行では一部のデータが重複しています。通常、リレーショナル データベースではデータの重複を避けますが、この場合は重複データのデメリットを差し引いても、処理効率と拡張性の高さに大きなメリットがあります。これらの各行には電子メールに必要なすべてのデータが格納されるので、Worker ロール B がキューから作業項目を取得したとき、1 つの行を読み取るだけで電子メールを送信できます。

では、電子メールの本文はどこから取得するのでしょうか。これらの行には、電子メールの本文を含むファイルの BLOB 参照がありません。この値は `MessageRef` 値から生成されます。たとえば、`MessageRef` が "634852858215726983" である場合、BLOB には "634852858215726983.htm" および "634852858215726983.txt" という名前が付けられます。

次に、このテーブルに格納されるデータの例を示します。

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partition Key

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Row Key

</th>
<td>
message634852858215726983

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
MessageRef

</th>
<td>
634852858215726983

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ScheduledDate

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubjectLine

</th>
<td>
新しい講義

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ListName

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ステータス

</th>
<td>
Processing

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partition Key

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Row Key

</th>
<td>
634852858215726983student1@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
MessageRef

</th>
<td>
634852858215726983

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ScheduledDate

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubjectLine

</th>
<td>
新しい講義

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ListName

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
FromEmailAddress

</th>
<td>
donotreply@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
EmailAddress

</th>
<td>
student1@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
76543210-90ed-41a9-b8ac-c1310c67b66a

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
EmailSent

</th>
<td>
true

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partition Key

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Row Key

</th>
<td>
634852858215726983student2@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
MessageRef

</th>
<td>
634852858215726983

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ScheduledDate

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubjectLine

</th>
<td>
新しい講義

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ListName

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
FromEmailAddress

</th>
<td>
donotreply@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
EmailAddress

</th>
<td>
student1@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
12345678-90ed-41a9-b8ac-c1310c679876

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
EmailSent

</th>
<td>
true

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partition Key

</th>
<td>
2012-11-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Row Key

</th>
<td>
message124852858215726999

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
MessageRef

</th>
<td>
124852858215726999

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ScheduledDate

</th>
<td>
2012-11-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubjectLine

</th>
<td>
新しい求人

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ListName

</th>
<td>
fabrikam

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ステータス

</th>
<td>
Pending

</td>
</tr>
</table>

### messagearchive テーブル

`PartitionKey` と `RowKey` 以外のフィールドを検索する場合、クエリを効率化する 1 つの選択肢としてテーブルのサイズを制限する方法があります。Worker ロール A のクエリで、特定のメッセージがすべての登録者へメール送信されたかどうかをチェックするには、`message` テーブルで `EmailSent` = false である電子メール行を探す必要があります。`EmailSent` 値は PartitionKey または RowKey で指定されないので、電子メール行が多数あるメッセージでは効率的なクエリといえません。そこで、このアプリケーションでは、電子メールが送信された時点でその電子メール行を `messagearchive` テーブルへ移動します。これで、message テーブルの `PartitionKey` と `RowKey` を照会すれば、そのメッセージがすべての登録者に配信されているかどうかをチェックできます。message テーブルに電子メール行が見つかった場合は未送信のメッセージがあることを意味しており、そのメッセージを `Complete` に指定することはできません。

`messagearchive` テーブルを構成する行のスキーマは、`message` テーブルの行と同じです。このアーカイブ データの利用目的に応じて、各行に格納するプロパティの数を減らしたり、一定期間経過した行を削除したりすることで、そのサイズとコストを制限できます。

## <a name="queues"></a>Azure キュー

Azure のキューは、この多層アプリケーションの階層間でのやり取り、およびバックエンド層における Worker ロール間のやり取りを支援します。
アプリケーションの拡張性を高めるため、Worker ロール A と Worker ロール B はキューを使用して互いにコミュニケーションをとります。Worker ロール A は、各電子メールについて message テーブルに行を作成できます。Worker ロール B は、message テーブルをスキャンして、未送信の電子メールを表している行を探します。ただし、Worker ロール B の新しいインスタンスを追加して作業を分散することはできません。Worker ロール A と Worker ロール B 間の作業調整にテーブル行を使用する場合、問題は、必ず 1 つの Worker ロール インスタンスがテーブル行を取得して処理するとは限らないことです。これを保証するのがキューです。Worker ロール インスタンスがキューから作業項目を取り出したとき、キュー サービスは、他の Worker ロール インスタンスが同じ作業項目を取り出せないようにします。Azure のキューに備わっているこの排他的リース機能により、Worker ロールの複数のインスタンス間でワークロードを共有できます。

Azure にはサービス バス キューも用意されています。Azure Storage キューおよび Service Bus キューの詳細については、[このシリーズの最終チュートリアル][Building worker role B (email sender) for the Azure Email Service application (Azure 電子メール サービス アプリケーションで使用する Worker ロール B (電子メール送信) の作成)]を参照してください。

Azure 電子メール サービス アプリケーションでは、`AzureMailQueue` と `AzureMailSubscribeQueue` という 2 つのキューを使用します。

### AzureMailQueue

`AzureMailQueue` キューは、メーリング リストへの電子メール送信を調整します。Worker ロール A は、送信する各電子メールの作業項目をこのキューに追加します。Worker ロール B は、このキューから作業項目を取り出して電子メールを送信します。

キュー作業項目は、メッセージの送信予定日 (`message` テーブルのパーティション キー)、`MessageRef` 値と `EmailAddress` 値 (`message` テーブルの行キー)、および "Worker ロールが停止し、再起動後に作成された作業項目かどうか" を示すフラグで構成されたコンマ区切り文字列です。たとえば次のようになります。

      2012-10-15,634852858215726983,student1@contoso.edu,0

Worker ロール B はこれらの値を使用して、電子メール送信に必要なすべての情報が格納されている行を `message` テーブルで探します。再起動フラグが "再起動" を示している場合、Worker ロール B は、電子メールがまだ送信されていないことを確認した上でメッセージを送信します。

トラフィックが急増したときは、Worker ロール B のインスタンスを複数生成し、それぞれ独立してキューから作業項目を取り出せるようにクラウド サービスを再構成できます。

### AzureMailSubscribeQueue

`AzureMailSubscribeQueue` キューは登録確認メールの送信を調整します。サービス メソッドが呼び出されると、そのサービス メソッドは AzureMailSubscribeQueue キューに作業項目を追加します。Worker ロール B は、このキューから作業項目を取り出し、登録確認メールを送信します。

キュー作業項目には登録者 GUID が含まれます。この値は電子メール アドレスと登録先リストを一意に識別します。これらは、Worker ロール B が確認メールを送信する際に必要な情報です。前述のように、`PartitionKey` および `RowKey` でないフィールドを照会する必要があるため、効率的ではありません。このアプリケーションの拡張性を高めるには、`mailinglist` テーブルの構成を変更し、`RowKey` に登録者 GUID を追加する必要があります。

## <a name="datadiagram"></a><span class="short-header">データ図</span>Azure 電子メール サービスのデータ図

次の図は、このアプリケーションで使用するテーブルとキュー、およびこれらの関連性を示しています。

![Azure 電子メール サービス アプリケーションのデータ図][Azure 電子メール サービス アプリケーションのデータ図]

## <a name="blobs"></a>Azure BLOB

BLOB は "Binary Large OBject (バイナリ ラージ オブジェクト)" の略です。Azure BLOB サービスは、ファイルをクラウドにアップロードして保存するための手段を提供します。Azure BLOB の詳細については、[このシリーズの最終チュートリアル][Building worker role B (email sender) for the Azure Email Service application (Azure 電子メール サービス アプリケーションで使用する Worker ロール B (電子メール送信) の作成)]を参照してください。

Azure メール サービス管理者は、電子メールの本文を *.htm* ファイル (HTML 形式) と *.txt* ファイル (プレーンテキスト形式) に保存します。電子メールの送信スケジュールを設定する際、管理者はこれらのファイルを **Create Message** Web ページにアップロードします。この Web ページの ASP.NET MVC コントローラーは、アップロードされたファイルを Azure の BLOB に格納します。

ファイルがフォルダーに保存されるように、BLOB は BLOB コンテナーに格納されます。Azure 電子メール サービス アプリケーションでは、**azuremailblobcontainer** という BLOB コンテナーを 1 つだけ使用します。このコンテナーに格納された BLOB の名前を生成するには、MessageRef 値にファイル拡張子を付加します。たとえば、"634852858215726983.htm"、"634852858215726983.txt" のようになります。

HTML メッセージとプレーンテキスト メッセージはどちらも文字列なので、BLOB ではなく、`Message` テーブルの string 型プロパティに電子メール本文を格納するようアプリケーションを設計することも可能でした。ただし、テーブル行のプロパティはサイズが 64 K に制限されています。BLOB を使用すれば、メール本文のこのサイズ制限を避けることができます (64 K はプロパティの最大合計サイズです。エンコードのオーバーヘッドがあるので、実際にプロパティに格納できる最大文字列サイズは 48 K 程度になります)。

## <a name="wawsvswacs"></a>Azure クラウド サービスと Azure Web サイト

Azure 電子メール サービスは、フロントエンドとバックエンドがすべて 1 つの Azure クラウド サービスで動作するように構成されています。

![アプリケーション アーキテクチャの概要][アプリケーション アーキテクチャの概要]

別のアーキテクチャとして、フロントエンドを Azure Web サイトで実行することもできます。

![別のアプリケーション アーキテクチャ][別のアプリケーション アーキテクチャ]

別の方法としては、フロントエンドを Azure Web サイトで実行し、Web ジョブ機能を使用して、フロントエンドを実行する同じサーバーでバックエンドを実行することです。詳細については、[Get Started with the Azure WebJobs SDK (Azure Web ジョブ SDK の概要)][Get Started with the Azure WebJobs SDK (Azure Web ジョブ SDK の概要)] を参照してください。

## <a name="cost"></a>コスト

ここでは、サンプル アプリケーションを Azure で実行する際の料金について簡単に説明します。ただし紹介するのは、このチュートリアルが公開された 2012 年 12 月時点での料金です。ビジネス戦略などを決定する前に、次の Web ページで最新の料金を確認してください。

-   [Azure 料金計算ツール][Azure 料金計算ツール]
-   [SendGrid Azure][SendGrid Azure]

どのくらいのコストがかかるかは、管理する Web ロールと Worker ロールのインスタンス数によって異なります。[Azure クラウド サービス 99.95% サービス レベル アグリーメント (SLA)][Azure クラウド サービス 99.95% サービス レベル アグリーメント (SLA)] の保証条件として、各ロールについて複数のインスタンスを展開する必要があります。ロール インスタンスを 2 つ以上実行しなければならないのは、オペレーティング システムをアップグレードするため、アプリケーションを実行する仮想マシンが月に 2 回ほど再起動されるためです (OS の更新の詳細については、「[Role Instance Restarts Due to OS Upgrades (OS のアップグレードに伴うロール インスタンスの再起動)][Role Instance Restarts Due to OS Upgrades (OS のアップグレードに伴うロール インスタンスの再起動)]」を参照してください)。

このサンプルの場合、2 つの Worker ロールによって実行される作業は時間的制約が厳しくないので、99.5% SLA は必要ありません。したがって、処理量に対応できるのであれば、各 Worker ロールのインスタンスは 1 つだけでかまいません。一方、Web ロール インスタンスには時間的制約があります。ユーザーが利用する Web サイトは中断が許されません。したがって、運用アプリケーションでは Web ロール インスタンスを 2 つ以上実行する必要があります。

次の表は、最小限の処理量を想定し、Azure 電子メール サービス サンプル アプリケーションを既定のアーキテクチャで実行した場合の料金を示しています。これらの料金は、サイズ XS の共有仮想マシンを使用することを前提としています。Visual Studio クラウド プロジェクトを作成する際の既定の仮想マシン サイズは "S" です。S サイズの場合、XS サイズのおよそ 5 倍の料金がかかります。

<table border="1">
<tr bgcolor="lightgray">
<th>
コンポーネントまたはサービス

</th>
<th>
料金

</th>
<th>
コスト/月

</th>
</tr>
<tr>
<td>
Web ロール

</td>
<td>
2 インスタンス - $.02/時間 (XS インスタンス)

</td>
<td>
$29.00

</td>
</tr>
<tr>
<td>
Worker ロール A (電子メールの送信スケジュールを設定)

</td>
<td>
1 インスタンス - $.02/時間 (XS インスタンス)

</td>
<td>
$14.50

</td>
</tr>
<tr>
<td>
Worker ロール B (電子メールの送信)

</td>
<td>
1 インスタンス - $.02/時間 (XS インスタンス)

</td>
<td>
$14.50

</td>
</tr>
<tr>
<td>
Azure のストレージ トランザクション

</td>
<td>
月あたり 100 万トランザクション - $0.10/100 万トランザクション (各クエリはトランザクションとしてカウントされます。Worker ロール A はテーブルに対して継続的にクエリを実行し、送信するメッセージを指定します。このアプリケーションは診断データを Azure のストレージに書き込むように構成されており、それぞれの書き込みがトランザクションとなります)。

</td>
<td>
$0.10

</td>
</tr>
<tr>
<td>
Azure ローカル冗長ストレージ

</td>
<td>
25 GB で $2.33 (アプリケーション テーブルと診断データのストレージを含みます)

</td>
<td>
$2.33

</td>
</tr>
<tr>
<td>
帯域幅

</td>
<td>
5 GB まで無料

</td>
<td>
無料

</td>
</tr>
<tr>
<td>
SendGrid

</td>
<td>
Azure ユーザーは 1 か月あたり 25,000 通の電子メールを無料で送信できます

</td>
<td>
無料

</td>
</tr>
<tr>
<td colspan="2">
合計

</td>
<td>
$60.43

</td>
</tr>
</table>
上記の表からわかるように、合計料金の大部分はロール インスタンスが占めています。ロール インスタンスは停止している場合でも料金が発生します。料金がかからないようにするには、ロール インスタンスを削除しなければなりません。コストを節約するには、Worker ロール A と Worker ロール B のすべてのコードを 1 つの Worker ロールに移動する方法があります。このチュートリアルでは、スケールアウトを容易にするため、意図的に 2 つの Worker ロール インスタンスを実装しています。Worker ロール B が行う作業は Azure キュー サービスによって調整されます。つまり、ロール インスタンスの数を増やすことで Worker ロール B をスケールアウトできます (処理量が増えたときは Worker ロール B が制約要因となります)。Worker ロール A が実行する作業はキューによって調整されません。したがって、Worker ロール A については複数のインスタンスを実行できません。2 つの Worker ロールを組み合わせてスケールアウトする場合は、Worker ロール A のタスクが必ず 1 つのインスタンスで実行されるようなしくみを実装する必要があります (この機能は [CloudFx][CloudFx] に備わっています。[WorkerRole.cs サンプル][WorkerRole.cs サンプル]を参照してください)。

これら 2 つの Worker ロールのすべてのコードを Web ロールへ移動し、すべての処理を Web ロールで実行する方法もあります。ただし、ASP.NET ではバックグラウンド タスク実行できないので、このアーキテクチャの拡張性が損なわれてしまいます。詳細については、「[The Dangers of Implementing Recurring Background Tasks In ASP.NET (繰り返し実行されるバックグラウンド タスクを ASP.NET に実装する危険性)][The Dangers of Implementing Recurring Background Tasks In ASP.NET (繰り返し実行されるバックグラウンド タスクを ASP.NET に実装する危険性)]」を参照してください。また、「[How to Combine a Worker and Web Role in Azure (Azure の Worker ロールと Web ロールを結合する方法)][How to Combine a Worker and Web Role in Azure (Azure の Worker ロールと Web ロールを結合する方法)]」および「[Combining Multiple Azure Worker Roles into an Azure Web Role (複数の Azure Worker ロールを 1 つの Azure Web ロールに集約)][Combining Multiple Azure Worker Roles into an Azure Web Role (複数の Azure Worker ロールを 1 つの Azure Web ロールに集約)]」も参照してください。この方法を行う場合、適したソリューションは、[Azure の Web サイトで実行し、バックエンド タスクに Web ジョブ機能を使用する][Azure の Web サイトで実行し、バックエンド タスクに Web ジョブ機能を使用する]ことです。

コストを削減するには、[オートスケーリング アプリケーション ブロック][オートスケーリング アプリケーション ブロック]を使用する方法もあります。この場合、指定した期間内のみ Worker ロールが自動的に展開され、作業が完了した時点で削除されます。オートスケーリングの詳細については、[このシリーズの最終チュートリアル][Building worker role B (email sender) for the Azure Email Service application (Azure 電子メール サービス アプリケーションで使用する Worker ロール B (電子メール送信) の作成)]を参照してください。

将来、Azure に再起動の通知機能が追加される予定です。その場合、再起動の間だけ Web ロール インスタンスを追加できます。99.95 SLA の対象にはなりませんが、再起動中も Web アプリケーションを実行しつつ、料金をほぼ半分に減らすことができます。

## <a name="auth"></a>認証と権限承認

ASP.NET Web API サービス メソッドなど、ASP.NET MVC Web フロントエンドの [ASP.NET Identity][ASP.NET Identity] のように、運用アプリケーションに認証と承認メカニズムを実装します。Web API サービス メソッドをセキュリティで保護するには、その他の方法 (共有鍵など) もあります。設定と展開を容易にするため、サンプル アプリケーションには認証と承認機能が実装されていません

## <a name="nextsteps"></a>次のステップ

[次のチュートリアル][Configuring and Deploying the Azure Email Service application (Azure 電子メール サービス アプリケーションの構成と展開)]では、サンプル プロジェクトをダウンロードして開発環境を構成し、それぞれの環境に合わせてプロジェクトを構成します。さらに、構成したプロジェクトをローカルおよびクラウドでテストします。チュートリアル 3 ～ 5 では、プロジェクトを新しく構築する方法を学習します。

Azure Storage のテーブル、キュー、BLOB に関する参考情報については、[このシリーズの最終チュートリアル][このシリーズの最終チュートリアル]を参照してください。

<div><a href="/ja-jp/develop/net/tutorials/multi-tier-web-site/2-download-and-run/" class="site-arrowboxcta download-cta">チュートリアル 2</a></div>

  [完成したアプリケーション]: http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [電子書籍]: http://social.technet.microsoft.com/wiki/contents/articles/11608.e-book-gallery-for-microsoft-technologies.aspx#ASPNETMultiTierWindowsAzureApplicationUsingStorageTablesQueuesandBlobs
  [電子メール メッセージの処理]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-worker-roles-a-and-b.png
  [Azure Cloud Services と ASP.NET を使ってみる]: /ja-jp/documentation/articles/cloud-services-dotnet-get-started/
  [Get Started with the Azure WebJobs SDK (Azure Web ジョブ SDK の概要)]: /ja-jp/documentation/articles/websites-dotnet-webjobs-sdk-get-started/
  [Configuring and Deploying the Azure Email Service application (Azure 電子メール サービス アプリケーションの構成と展開)]: /ja-jp/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [Building the web role for the Azure Email Service application (Azure 電子メール サービス アプリケーションで使用する Web ロールの作成)]: /ja-jp/develop/net/tutorials/multi-tier-web-site/3-web-role/
  [Building worker role A (email scheduler) for the Azure Email Service application (Azure 電子メール サービス アプリケーションで使用する Worker ロール A (電子メール スケジューラ) の作成)]: /ja-jp/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
  [Building worker role B (email sender) for the Azure Email Service application (Azure 電子メール サービス アプリケーションで使用する Worker ロール B (電子メール送信) の作成)]: /ja-jp/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
  [前提条件]: #prerequisites
  [フロントエンドの概要]: #frontend
  [バックエンドの概要]: #backend
  [Azure テーブル]: #tables
  [Azure キュー]: #queues
  [データ図]: #datadiagram
  [Azure BLOB]: #blobs
  [Azure クラウド サービスと Azure Web サイト]: #wawsvswacs
  [コスト]: #cost
  [認証と権限承認]: #auth
  [次のステップ]: #nextsteps
  [無料評価版アカウント]: /ja-jp/pricing/free-trial/
  [MSDN サブスクライバーの特典を有効にする]: /ja-jp/pricing/member-offers/msdn-benefits/
  [Mailing List Index Page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-mailing-list-index-page.png
  [Subscriber Index Page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribers-index-page.png
  [Message Index Page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-index-page.png
  [Message Create Page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-create-page.png
  [確認用メール]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-email.png
  [リストのウェルカム ページ]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-confirmation-page.png
  [登録解除の確認ページ]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-unsubscribe-query-page.png
  [Unsubscribe confirmed page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-unsubscribe-confirmation-page.png
  [重複する電子メールの送信を防ぐ]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-processing.png
  [配信登録キュー メッセージの処理]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-diagram.png
  [Azure SQL データベース]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee336279.aspx
  [Real World: Designing a Scalable Partitioning Strategy for Azure Table Storage (リアル ワールド: Azure テーブル ストレージ用の拡張性が高いパーティション分割方法の設計)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh508997.aspx
  [Azure 電子メール サービス アプリケーションのデータ図]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-datadiagram.png
  [アプリケーション アーキテクチャの概要]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-architecture-overview.png
  [別のアプリケーション アーキテクチャ]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-alternative-architecture.png
  [Azure 料金計算ツール]: http://www.windowsazure.com/ja-jp/pricing/calculator/
  [SendGrid Azure]: http://sendgrid.com/windowsazure.html
  [Azure クラウド サービス 99.95% サービス レベル アグリーメント (SLA)]: https://www.windowsazure.com/ja-jp/support/legal/sla/ "SLA"
  [Role Instance Restarts Due to OS Upgrades (OS のアップグレードに伴うロール インスタンスの再起動)]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
  [CloudFx]: http://nuget.org/packages/Microsoft.Experience.CloudFx "CloudFX"
  [WorkerRole.cs サンプル]: http://code.msdn.microsoft.com/windowsazure/CloudFx-Samples-60c3a852/sourcecode?fileId=57087&pathId=528472169
  [The Dangers of Implementing Recurring Background Tasks In ASP.NET (繰り返し実行されるバックグラウンド タスクを ASP.NET に実装する危険性)]: http://haacked.com/archive/2011/10/16/the-dangers-of-implementing-recurring-background-tasks-in-asp-net.aspx
  [How to Combine a Worker and Web Role in Azure (Azure の Worker ロールと Web ロールを結合する方法)]: http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2010/12/how-to-combine-worker-and-web-role-in.html
  [Combining Multiple Azure Worker Roles into an Azure Web Role (複数の Azure Worker ロールを 1 つの Azure Web ロールに集約)]: http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2012/02/combining-multiple-azure-worker-roles.html
  [Azure の Web サイトで実行し、バックエンド タスクに Web ジョブ機能を使用する]: http://go.microsoft.com/fwlink/?LinkId=390226
  [オートスケーリング アプリケーション ブロック]: /ja-jp/develop/net/how-to-guides/autoscaling/
  [ASP.NET Identity]: http://asp.net/identity
  [このシリーズの最終チュートリアル]: /ja-jp/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/#nextsteps
