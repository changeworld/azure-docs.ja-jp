<properties linkid="develop-net-tutorials-multi-tier-web-site-1-overview" urlDisplayName="ステップ 1: 概要" pageTitle="多層 ASP.NET MVC 4 Web サイトのチュートリアル - ステップ 1: 概要" metaKeywords="Windows Azure チュートリアル, 電子メール リスト サービス アプリケーション, 電子メール サービス アーキテクチャ, Azure チュートリアルの概要" description="5 つのパートから成る多層 Web サイトのチュートリアルについて説明します。" metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="キュー" authors=""  solutions="" writer="tdykstra" manager="wpickett" editor="mollybos"  />



<div>
<div class="left-nav">
<div class="static-nav">
<ul>
<li class="menu-nodejs-compute"><a href="/en-us/develop/net/compute/">コンピューティング</a></li>
<li class="menu-nodejs-data"><a href="/en-us/develop/net/data/">データ サービス</a></li>
<li class="menu-nodejs-appservices"><a href="/en-us/develop/net/app-services/">アプリケーション サービス</a></li>
</ul>
<ul class="links">
<li class="forum"><a href="/en-us/support/forums/">フォーラム</a></li>
</ul>
<ul>
<li>シリーズの内容</li>
<li><strong>1. 概要</strong></li>
<li><a href="./cloud-services-dotnet-multi-tier-app-storage-2-download-run/">2. ダウンロードと実行</a></li>
<li><a href="./cloud-services-dotnet-multi-tier-app-storage-3-web-role/">3. Web ロール</a></li>
<li><a href="./cloud-services-dotnet-multi-tier-app-storage-4-worker-role-a/">4. Worker ロール A</a></li>
<li><a href="./cloud-services-dotnet-multi-tier-app-storage-5-worker-role-b/">5. Worker ロール B</a></li>
</ul>
</div>
<div class="floating-nav jump-to">
<ul>
<li>ページ (ジャンプ先):</li>
</ul>
</div>
</div>
</div>

# ストレージ テーブル、キュー、および BLOB を使用する .NET 多層アプリケーション (1/5)

このチュートリアル シリーズでは、Windows Azure のストレージ テーブル、キュー、および BLOB を使用する多層 ASP.NET MVC 4 Web アプリケーションを作成する方法、およびそのアプリケーションを Windows Azure のクラウド サービスに展開する方法を説明します。これらのチュートリアルは、Windows Azure を初めて使用するユーザーを対象としています。すべてのチュートリアルを完了すると、弾力性と拡張性に優れたデータ主導型の Web アプリケーションを構築し、クラウドに展開できるようになります。

この内容は、[TechNet の電子書籍ギャラリー
](http://social.technet.microsoft.com/wiki/contents/articles/11608.e-book-gallery-for-microsoft-technologies.aspx#ASPNETMultiTierWindowsAzureApplicationUsingStorageTablesQueuesandBlobs)で無料の電子書籍としても入手できます。

<h2><a name="whatyoulllearn"></a><span class="short-header">学習する内容</span>学習する内容</h2>

このチュートリアル シリーズで学習する内容は次のとおりです。

* Windows Azure SDK をインストールして、Windows Azure 向け開発用にコンピューターを準備する方法
* MVC 4 の Web ロール (1 つ) と Worker ロール (2 つ) を使用して、Visual Studio クラウド プロジェクトを作成する方法
* 作成したクラウド プロジェクトを Windows Azure のクラウド サービスに発行する方法
* 必要であれば MVC 4 プロジェクトを Windows Azure の Web サイトに発行し、Worker ロールはクラウド サービスで実行する方法
* Windows Azure キュー ストレージ サービスを使用して、複数の階層間または Worker ロール間でやり取りする方法
* 拡張性に優れたデータ ストアとして Windows Azure テーブル ストレージ サービスを使用し、構造化された非リレーショナル データを格納する方法
* Windows Azure BLOB サービスを使用して、ファイルをクラウドに格納する方法
* Visual Studio または Azure ストレージ エクスプローラーを使用して、Windows Azure のテーブル、キュー、BLOB を表示および編集する方法
* SendGrid を使用して電子メールを送信する方法
* トレース機能を構成し、トレース データを表示する方法
* Worker ロール インスタンスの数を増やすことによって、アプリケーションの規模を拡張する方法

<h2><a name="frontendoverview"></a><span class="short-header">フロントエンドの概要</span>Front-end overview</h2>

ここで構築するアプリケーションは電子メール リスト サービスです。この多層アプリケーションのフロントエンドは、サービスの管理者が電子メール リストを管理するための Web ページで構成されます。

![メーリング リストのインデックス ページ][mtas-mailing-list-index-page]

![登録者のインデックス ページ][mtas-subscribers-index-page]

管理者がメーリング リストへ送信するメッセージを作成するためのページもあります。

![メッセージのインデックス ページ][mtas-message-index-page]

![メッセージ作成ページ][mtas-message-create-page]

このサービスは、自社の Web サイト上で顧客がメーリング リストに配信登録できるようにします。たとえば、Contoso 大学史学部のお知らせをメール配信するとしましょう。管理者はそのためのリストを設定します。史学部のお知らせを購読したいと思った学生が Contoso 大学の Web サイトでリンクをクリックすると、Windows Azure 電子メール サービス アプリケーションが呼び出され、その学生に電子メールが送信されます。ここで送信される電子メールにはハイパーリンクが含まれています。メールを受け取った学生がこのリンクをクリックすると、史学部お知らせリストのウェルカム ページが表示されます。

![確認用メール][mtas-subscribe-email]

![リストのウェルカム ページ][mtas-subscribe-confirmation-page]

このサービスで送信されるすべての電子メール (登録確認用メールを除く) には、登録を解除するためのハイパーリンクが含まれています。メール受信者がこのリンクをクリックすると、登録を解除するかどうかを確認する Web ページが表示されます。

![登録解除の確認ページ][mtas-unsubscribe-query-page]

メール受信者が **[確認]** ボタンをクリックすると、そのユーザーがリストから削除されたことを知らせるページが表示されます。

![登録解除完了のお知らせページ][mtas-unsubscribe-confirmation-page]




<h2><a name="whyanemaillistapp"></a><span class="short-header">チュートリアル</span>このシリーズのチュートリアル</h2>

ここで実施するチュートリアルとその内容は次のとおりです。

1. **Windows Azure 電子メール サービス アプリケーションの概要** (このチュートリアル): ここで作成するアプリケーションとそのアーキテクチャを紹介します。
2. [Configuring and Deploying the Windows Azure Email Service application (Windows Azure 電子メール サービス アプリケーションの構成と展開)][tut2]: サンプル アプリケーションをダウンロードして構成し、ローカルでテストする方法、およびクラウドに展開してテストする方法を説明します。
3. [Building the web role for the Windows Azure Email Service application (Windows Azure 電子メール サービス アプリケーションで使用する Web ロールの作成)][tut3]: このアプリケーションの MVC 4 コンポーネントを作成し、それらをローカルでテストする方法を説明します。
4. [Building worker role A (email scheduler) for the Windows Azure Email Service application (Windows Azure 電子メール サービス アプリケーションで使用する Worker ロール A (電子メール スケジューラ) の作成)][tut4]: 電子メール送信に必要なキュー作業項目を作成するバックエンド コンポーネントを構築し、それをローカルでテストする方法を説明します。
5. [Building worker role B (email sender) for the Windows Azure Email Service application (Windows Azure 電子メール サービス アプリケーションで使用する Worker ロール B (電子メール送信) の作成)][tut5]: 電子メール送信に必要なキュー作業項目を処理するバックエンド コンポーネントを構築し、それをローカルでテストする方法を説明します。

このアプリケーションをダウンロードして、実際に使ってみたい場合は、最初の 2 つのチュートリアルを実行してください。このようなアプリケーションを新しく作成するための手順をすべて参照したい場合は、最初の 2 つのチュートリアルを実行した後、後半 3 つのチュートリアルを実行してください。



<h2><a name="whyanemaillistapp"></a><span class="short-header">このアプリケーションを選んだ理由</span>電子メール リスト サービス アプリケーションを選んだ理由</h2>

今回、サンプル アプリケーションとして電子メール リスト サービスを選んだのは、優れた耐障害性と拡張性が求められ、Windows Azure に特に適しているアプリケーションだからです。

### 耐障害性

多数の登録者へメール送信している間にサーバーが故障した場合、新しいサーバーをすばやく準備して処理を再開し、一部の登録者に電子メールが届かなかったり、同じ電子メールが重複して届かないようにしなければなりません。Windows Azure クラウド サービスでは、Web ロール インスタンスまたはワーカー ロール インスタンス (仮想マシン) が故障した場合、それらが自動的に置き換えられます。また、Windows Azure のストレージ キューとテーブルを使用してサーバー間通信を実装すれば、障害が発生しても適切に処理を継続できます。

### 拡張性

電子メール サービスは処理量の急増にも対応できなければなりません。メールの配信登録者が少ない場合もあれば、急に増える場合もあります。多くのホスティング環境の場合、処理量の急増に対応するには追加のハードウェアを購入して管理する必要があります。その上、利用時間が全体の 5% であっても、常に全容量分のコストを支払わなければなりません。Windows Azure では、実際に必要なコンピューティング能力に対して、必要な期間だけ料金を支払います。登録者が増加して拡張が必要になったら、増加した処理量に合わせてサーバーの数を増やすだけです。この構成変更はプログラムで行えます。たとえば、キューで待機している作業項目が一定数を超えた場合、新しい Worker ロール インスタンスを自動的に作成し、それらの作業項目を処理するようにアプリケーションを構成できます。




<h2><a name="backendoverview"></a><span class="short-header">バックエンドの概要</span>バックエンドの概要</h2>

フロントエンドは、電子メール リストとそれらのリストに送信するメッセージを Windows Azure のテーブルに格納します。管理者がメッセージの送信スケジュールを設定すると、送信予定日とその他のデータ (件名など) を含むテーブル行が `message` テーブルに追加されます。Worker ロールは `message` テーブルを定期的にスキャンし、送信する必要があるメッセージを探します (これを Worker ロール A と呼びます)。

要送信メッセージが見つかった場合、ワーカー ロール A は次のタスクを実行します。

* すべての電子メール アドレスをメール送信先リストに取り込む。
* 各電子メールの送信に必要な情報を `message` テーブルに追加する。
* 送信する各電子メールのキュー作業項目を作成する。

別の Worker ロール (Worker ロール B) が作業項目のキューをポーリングします。ワーカー ロール B が作業項目を見つけると、電子メールを送信し、その作業項目をキューから削除します。次の図はこれらの関連性を示しています。

![電子メール メッセージの処理][mtas-worker-roles-a-and-b]

Worker ロール B が停止して再起動する必要がある場合でも、電子メールの不達は発生しません。電子メールが送信されない限り、その電子メールのキュー作業項目は削除されないためです。ワーカー ロール A が停止して再起動した場合、同じ電子メールが複数送信されないようにするためのテーブル処理もバックエンドで実装します。この場合、1 つのメール送信先アドレスについて、複数のキュー作業項目が生成される可能性があります。これを防ぐため、各メール送信先アドレスについて、電子メールの送信/未送信状況が `message` テーブルの行に記録されます。この行を参照することにより、再起動のタイミングと電子メールの処理状況に応じて、キュー作業項目が重複して作成されないようにしたり (ワーカー A)、同じ電子メールが重複して送信されないようにします (ワーカー B)。

![重複する電子メールの送信を防ぐ][mtas-message-processing]

Worker ロール B は、Web API サービス メソッドによって追加された作業項目の配信登録キューもポーリングし、新規登録がないかどうかをチェックします。新規登録が見つかった場合は、確認用の電子メールを送信します。

![配信登録キュー メッセージの処理][mtas-subscribe-diagram]





<h2><a name="tables"></a><span class="short-header">テーブル</span>Windows Azure のテーブル</h2>

Windows Azure 電子メール サービス アプリケーションは、データを Windows Azure のストレージ テーブルに保存します。Windows Azure のテーブルは NoSQL データ ストアです。[Windows Azure SQL データベース](http://msdn.microsoft.com/en-us/library/windowsazure/ee336279.aspx)のようなリレーショナル データベースではありません。したがって、データの正規化やリレーショナル整合性より、効率性と拡張性が重要な場合に適しています。たとえばこのアプリケーションでは、キュー作業項目が作成されるたびに、ワーカー ロールが行を作成します。また、電子メールが送信されるたびに、別のワーカー ロールが行を取得して更新します。リレーショナル データベースを使用した場合、このような処理はパフォーマンスのボトルネックになる可能性があります。さらに、Windows Azure のテーブルは Windows Azure SQL より安価です。Windows Azure のテーブルの詳細については、[このシリーズの最終チュートリアル][tut5]の末尾で紹介されている関連情報を参照してください。

この後のセクションでは、Windows Azure 電子メール サービス アプリケーションで使用する Windows Azure のテーブルの内容について説明します。これらのテーブルと相互の関連性を示す図は、このページ後半の「[Windows Azure 電子メール サービスのデータ図](#datadiagram)」に記載されています。

### mailinglist テーブル###

`mailinglist` テーブルには、メーリング リストおよびメーリング リスト登録者に関する情報が格納されます (慣例として、Windows Azure のテーブル名ではすべて小文字を使用します)。管理者は Web ページを使用して、メーリング リストを作成および編集します。顧客や登録者は、一連の Web ページとサービス メソッドを使用して登録と登録解除を行います。

NoSQL テーブルでは、行ごとにそれぞれ異なるスキーマを設定できます。この柔軟性により、リレーショナル データベースであれば複数のテーブルが必要になる場合でも、1 つの NoSQL にデータを格納できます。たとえば、メーリング リストのデータを SQL データベースに保存する場合、3 つのテーブル (メーリング リストに関する情報を格納する `mailinglist` テーブル、登録者に関する情報を格納する `subscriber` テーブル、メーリング リストと登録者とを関連付ける `mailinglistsubscriber` テーブル) を使用します。このアプリケーションでは、`mailinglist` という名前の 1 つの NoSQL テーブルにこれらすべての情報を格納します。

Windows Azure のテーブルでは、行ごとに "*パーティション キー*" が割り当てられ、さらに各行を一意に識別する "*行キー*" が割り当てられます。パーティション キーは、テーブルを論理的に複数のパーティションに分割します。1 つのパーティション内では、行キーによって各行が一意に識別されます。補助的なインデックスはありません。したがって、アプリケーションの拡張性を高めるには、クエリの Where 句で常にパーティション キーと行キーの値を指定できるようにテーブルを設計することが重要です。

`mailinglist` テーブルのパーティション キーはメーリング リストの名前です。

`mailinglist` テーブルの行キーは、定数 "mailinglist"、または登録者の電子メール アドレスになります。行キーが "mailinglist" である行には、そのメーリング リストに関する情報が格納されます。行キーが電子メール アドレスである行には、そのリストの登録者に関する情報が格納されます。

つまり、行キーが "mailinglist" である行は、リレーショナル データベースの `mailinglist` テーブルに該当します。行キー = 電子メール アドレスである行は、リレーショナル データベースの `subscriber` テーブルおよび `mailinglistsubscriber` 関連テーブルに該当します。

このように、1 つのテーブルを複数の目的で使用することでパフォーマンスが向上します。リレーショナル データベースでは、3 つのテーブルを読み取り、3 つの行セットを並べ替えて互いに照合しなければならないので、時間がかかります。ここでは、1 つのテーブルのみを読み取り、パーティション キー、行キーの順序でテーブル内の行が自動的に返されます。

次の表は、メーリング リスト情報を格納する行 (行キー = "MailingList") のプロパティを示しています。

<table border="1">

<tr bgcolor="lightgray">
<th>プロパティ</th>
<th>データ型</th>
<th>説明</th>
</tr>

<tr>
<td>PartitionKey</td>
<td>String</td>
<td>ListName: メーリング リストの一意の識別子 (contoso1 など)。通常、このテーブルは、特定のメーリング リストのすべての情報を取得するときに使用されます。したがって、リスト名を使用することでテーブルを効率的に分割できます。</td>
</tr>

<tr>
<td>RowKey</td>
<td>String</td>
<td>定数 "mailinglist"。</td>
</tr>

<tr>
<td>Description</td>
<td>String</td>
<td>メーリング リストの説明。例: "Contoso 大学史学部のお知らせ"</td>
</tr>

<tr>
<td>FromEmailAddress</td>
<td>String</td>
<td>このリストに送信する電子メールの "差出人" アドレス。例: donotreply@contoso.edu</td>
</tr>

</table>

次の表は、このリストの登録者情報が格納される行 (行キー = 電子メール アドレス) のプロパティを示しています。

<table border="1">

<tr bgcolor="lightgray">
<th>プロパティ</th>
<th>データ型</th>
<th>説明</th>
</tr>

<tr>
<td>PartitionKey</td>
<td>String</td>
<td>ListName: メーリング リストの名前 (一意の識別子)。例: contoso1</td>
</tr>

<tr>
<td>RowKey</td>
<td>String</td>
<td>EmailAddress: 登録者の電子メール アドレス。例: student1@contoso.edu</td>
</tr>

<tr>
<td>SubscriberGUID</td>
<td>String</td>
<td>電子メール アドレスがリストに追加された時点で生成されます。登録リンクと登録解除リンクで使用し、他の人の電子メール アドレスが登録または登録解除されないようにします。 
<br/><br/>
登録用 Web ページと登録解除用 Web ページの一部の照会では、PartitionKey とこのプロパティのみが指定されます。RowKey を使用しないでパーティションを照会すると、メーリング リストのサイズが大きくなるほど照会時間が長くなるので、このアプリケーションの拡張性が制限されます。拡張性を高めるには、RowKey プロパティが SubscriberGUID であるルックアップ行を追加する方法があります。たとえば、各電子メール アドレスについて、1 つの行の RowKey を "email:student1@domain.com" に設定します。さらに、同じ登録者の別の行の RowKey を "guid:6f32b03b-90ed-41a9-b8ac-c1310c67b66a" に設定します。同一パーティション内の複数行に対するアトミック バッチ トランザクションはコーディングが容易なので、このしくみはすぐに実装できます。このサンプル アプリケーションの次期リリースには反映させる予定です。詳細については、「<a href="http://msdn.microsoft.com/en-us/library/windowsazure/hh508997.aspx">Real World: Designing a Scalable Partitioning Strategy for Windows Azure Table Storage (リアル ワールド: Windows Azure テーブル ストレージ用の拡張性が高いパーティション分割方法の設計)</a>」を参照してください。
</td>
</tr>

<tr>
<td>Verified</td>
<td>Boolean</td>
<td>新規登録者に対して対象行が最初に作成されたとき、このプロパティには false が設定されます。確認電子メールを受け取った新規登録者が "確認用" ハイパーリンクをクリックすると、この値が true に変わります。メーリング リストにメッセージを送信する際、いずれかの登録者の Verified 値が false の場合、その登録者には電子メールが送信されません。</td>
</tr>

</table>

次に、このテーブルに格納されるデータの例を示します。

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">Partition Key</th>
<td>contoso1</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Row Key</th>
<td>mailinglist</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">説明</th>
<td>Contoso 大学史学部のお知らせ</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">FromEmailAddress</th>
<td>donotreply@contoso.edu</td>
</tr>

</table>

<hr/>

<table border="1">

<tr>
<th width="200" align="right" bgcolor="lightgray">Partition Key</th>
<td>contoso1</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Row Key</th>
<td>student1@domain.com</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubscriberGUID</th>
<td>6f32b03b-90ed-41a9-b8ac-c1310c67b66a</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Verified</th>
<td>true</td>
</tr>

</table>

<hr/>

<table border="1">

<tr>
<th width="200" align="right" bgcolor="lightgray">Partition Key</th>
<td>contoso1</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Row Key</th>
<td>student2@domain.com</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubscriberGUID</th>
<td>01234567-90ed-41a9-b8ac-c1310c67b66a</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Verified</th>
<td>false</td>
</tr>

</table>

<hr/>

<table border="1">

<tr>
<th width="200" align="right" bgcolor="lightgray">Partition Key</th>
<td>fabrikam1</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Row Key</th>
<td>mailinglist</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">説明</th>
<td>Fabrikam エンジニアの求人</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">FromEmailAddress</th>
<td>donotreply@fabrikam.com</td>
</tr>

</table>

<hr/>

<table border="1">

<tr>
<th width="200" align="right" bgcolor="lightgray">Partition Key</th>
<td>fabrikam1</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Row Key</th>
<td>applicant1@domain.com</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubscriberGUID</th>
<td>76543210-90ed-41a9-b8ac-c1310c67b66a</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Verified</th>
<td>true</td>
</tr>

</table>

### message テーブル###

`message` テーブルには、メーリング リストへ送信されるメッセージに関する情報が格納されます。管理者は Web ページを使用して、このテーブルの行を作成および編集します。また、各電子メールに関する情報を Worker ロール A から Worker ロール B へ渡すときも、このテーブルを使用します。

message テーブルのパーティション キーは電子メールの送信予定日 (yyyy-mm-dd 形式) です。これにより、message テーブルに対して最も頻繁に実行されるクエリ (`ScheduledDate` が今日以前の行を選択) 用にテーブルが最適化されます。ただし、Windows Azure のストレージ テーブルの最大スループットは 500 エントリ/秒なので、パフォーマンスが低下する可能性があります。このアプリケーションでは、送信する電子メールごとに `message` テーブル行の書き込み、行の読み取り、行の削除を行います。したがって、処理量の増加に合わせて追加する Worker ロールの数にかかわらず、1 日に送信する 1,000,000 通の電子メールを処理するには最短でおよそ 2 時間かかります。

`message` テーブルの行キーは、次の 2 つのうちいずれかの値になります。1 つは、定数 "message" + メッセージの一意キー (`MessageRef`)。もう1 つは、`MessageRef` 値 + 登録者の電子メール アドレスです。行キーが "message" で始まる行には、メッセージに関する情報 (送信先のメーリング リスト、送信予定日など) が格納されます。行キーが `MessageRef` と電子メール アドレスである行には、その電子メール アドレスにメールを送信する際に必要となるすべての情報が格納されます。

行キーが "message" で始まる行は、リレーショナル データベースの `message` テーブルに該当します。行キー = `MessageRef` + 電子メール アドレスである行は、リレーショナル データベースでは、`mailinglist`、`message`、`subscriber` の情報を含む結合クエリ ビューに該当します。

次の表は、`message` テーブルで、メッセージ自体に関する情報が格納される行のプロパティを示しています。

<table border="1">

<tr bgcolor="lightgray">
<th>プロパティ</th>
<th>データ型</th>
<th>説明</th>
</tr>

<tr>
<td>PartitionKey</td>
<td>String</td>
<td>メッセージの送信予定日 (yyyy-mm-dd 型式)。</td>
</tr>

<tr>
<td>RowKey</td>
<td>String</td>
<td>定数 "message" + <code>MessageRef</code> 値。<code>MessageRef</code> は、この行を作成する際、<code>DateTime.Now</code> から <code>Ticks</code> 値を取得することによって生成される一意の値です。<br/><br/>注: マルチスレッド、マルチインスタンスの大規模アプリケーションは、Ticks の使用に伴う重複 RowKey 例外を処理できるようにする必要があります。Ticks 値は一意とは限りません。</td>
</tr>

<tr>
<td>ScheduledDate</td>
<td>Date</td>
<td>メッセージの送信予定日 (<code>PartitionKey</code> と同じですが、Date 型式になります)。</td>
</tr>

<tr>
<td>SubjectLine</td>
<td>String</td>
<td>電子メールの件名。</td>
</tr>

<tr>
<td>ListName</td>
<td>String</td>
<td>このメッセージの送信先リスト。</td>
</tr>

<tr>
<td>Status</td>
<td>String</td>
<td><ul>
<li>"Pending" -- ワーカー ロール A は、メール送信をスケジュール設定するキュー メッセージをまだ作成していません。</li>
<li>"Queuing" -- ワーカー ロール A は、メール送信をスケジュール設定するキュー メッセージの作成を開始しました。</li>
<li>"Processing" -- ワーカー ロール A は、リスト内のすべての電子メール キュー作業項目を作成しました。ただし、まだ送信されていない電子メールもあります。</li>
<li>"Completed" -- ワーカー ロール B は、すべてのキュー作業項目の処理を完了しました (すべての電子メールが送信されました)。この後で説明するように、完了した行は <code>messagearchive</code> テーブルに格納されます。次期リリースでは、このプロパティが <code>enum</code> になる予定です。</li></ul></td>
</tr>

</table>

Worker ロール A は、リストへ送信する電子メールのキュー メッセージを作成する際、`message` テーブルに電子メール行を作成します。Worker ロール B はその電子メールを送信し、該当する電子メール行を `messagearchive` テーブルへ移動します。さらに、`EmailSent` プロパティを `true` に変更します。ステータスが "Processing" であるすべての電子メール行がアーカイブされると、Worker ロール A はステータスを "Completed" に設定し、その `message` 行を `messagearchive` テーブルへ移動します。

次の表は、`message` テーブルの電子メール行のプロパティを示しています。

<table border="1">

<tr bgcolor="lightgray">
<th>プロパティ</th>
<th>データ型</th>
<th>説明</th>
</tr>

<tr>
<td>PartitionKey</td>
<td>String</td>
<td>メッセージの送信予定日 (yyyy-mm-dd 型式)。</td>
</tr>

<tr>
<td>RowKey</td>
<td>String</td>
<td> <code>MessageRef</code> 値、および <code>mailinglist</code> テーブルの <code>subscriber</code> 行に指定されている送信先電子メール アドレス。
</td>
</tr>

<tr>
<td>MessageRef</td>
<td>Long</td>
<td><code>RowKey</code> の <code>MessageRef</code> 要素と同じ。</td>
</tr>

<tr>
<td>ScheduledDate</td>
<td>Date</td>
<td><code>message</code> テーブルの <code>message</code> 行に指定されている送信予定日 (<code>PartitionKey</code> と同じですが、Date 型式になります)。</td>
</tr>

<tr>
<td>SubjectLine</td>
<td>String</td>
<td><code>message</code> テーブルの <code>message</code> 行で指定されている電子メールの件名。</td>
</tr>

<tr>
<td>ListName</td>
<td>String</td>
<td><code>mailinglist</code> テーブルで設定されているメーリング リスト名。</td>
</tr>

<tr>
<td>From EmailAddress</td>
<td>String</td>
<td><code>mailinglist</code> テーブルの <code>mailinglist</code> 行で指定されている "差出人" 電子メール アドレス。</td>
</tr>

<tr>
<td>EmailAddress</td>
<td>String</td>
<td><code>mailinglist</code> テーブルの <code>subscriber</code> 行で指定されている電子メール アドレス。</td>
</tr>

<tr>
<td>SubscriberGUID</td>
<td>String</td>
<td><code>mailinglist</code> テーブルの <code>subscriber</code> 行で指定されている登録者 GUID。</td>
</tr>

<tr>
<td>EmailSent</td>
<td>Boolean</td>
<td>false = 電子メールは未送信。true = 電子メールは既に送信されています。</td>
</tr>

</table>

これらの行では一部のデータが重複しています。通常、リレーショナル データベースではデータの重複を避けますが、この場合は重複データのデメリットを差し引いても、処理効率と拡張性の高さに大きなメリットがあります。これらの各行には電子メールに必要なすべてのデータが格納されるので、Worker ロール B がキューから作業項目を取得したとき、1 つの行を読み取るだけで電子メールを送信できます。

では、電子メールの本文はどこから取得するのでしょうか。これらの行には、電子メールの本文を含むファイルの BLOB 参照がありません。この値は `MessageRef` 値から派生します。たとえば、`MessageRef` が "634852858215726983" である場合、BLOB には "634852858215726983.htm" および "634852858215726983.txt" という名前が付けられます。

次に、このテーブルに格納されるデータの例を示します。

<table border="1">

<tr>
<th width="200" align="right" bgcolor="lightgray">Partition Key</th>
<td>2012-10-15</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Row Key</th>
<td>message634852858215726983</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">MessageRef</th>
<td>634852858215726983</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">ScheduledDate</th>
<td>2012-10-15</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubjectLine</th>
<td>新しい講義</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">ListName</th>
<td>contoso1</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Status</th>
<td>Processing</td>
</tr>

</table>

<hr/>

<table border="1">

<tr>
<th width="200" align="right" bgcolor="lightgray">Partition Key</th>
<td>2012-10-15</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Row Key</th>
<td>634852858215726983student1@contoso.edu</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">MessageRef</th>
<td>634852858215726983</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">ScheduledDate</th>
<td>2012-10-15</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubjectLine</th>
<td>新しい講義</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">ListName</th>
<td>contoso1</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">FromEmailAddress</th>
<td>donotreply@contoso.edu</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">EmailAddress</th>
<td>student1@contoso.edu</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubscriberGUID</th>
<td>76543210-90ed-41a9-b8ac-c1310c67b66a</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">EmailSent</th>
<td>true</td>
</tr>

</table>

<hr/>

<table border="1">

<tr>
<th width="200" align="right" bgcolor="lightgray">Partition Key</th>
<td>2012-10-15</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Row Key</th>
<td>634852858215726983student2@contoso.edu</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">MessageRef</th>
<td>634852858215726983</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">ScheduledDate</th>
<td>2012-10-15</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubjectLine</th>
<td>新しい講義</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">ListName</th>
<td>contoso1</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">FromEmailAddress</th>
<td>donotreply@contoso.edu</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">EmailAddress</th>
<td>student2@contoso.edu</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubscriberGUID</th>
<td>12345678-90ed-41a9-b8ac-c1310c679876</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">EmailSent</th>
<td>true</td>
</tr>

</table>


<hr/>

<table border="1">

<tr>
<th width="200" align="right" bgcolor="lightgray">Partition Key</th>
<td>2012-11-15</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Row Key</th>
<td>message124852858215726999</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">MessageRef</th>
<td>124852858215726999</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">ScheduledDate</th>
<td>2012-11-15</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubjectLine</th>
<td>新しい求人</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">ListName</th>
<td>fabrikam</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Status</th>
<td>Pending</td>
</tr>

</table>

<br/>
<br/>

### messagearchive テーブル###

特に `PartitionKey` と `RowKey` 以外のフィールドを検索する場合、クエリの実行を効率化する 1 つの選択肢としてテーブルのサイズを制限する方法があります。Worker ロール A のクエリで、特定のメッセージがすべての登録者へメール送信されたかどうかをチェックするには、`message` テーブルで `EmailSent` = false である電子メール行を探す必要があります。`EmailSent` 値は PartitionKey または RowKey で指定されないので、電子メール行が多数あるメッセージでは効率的なクエリといえません。そこで、このアプリケーションでは、電子メールが送信された時点でその電子メール行を `messagearchive` テーブルへ移動します。これで、message テーブルの `PartitionKey` と `RowKey` を照会すれば、そのメッセージがすべての登録者に配信されているかどうかをチェックできます。message テーブルに電子メール行が見つかった場合は未送信のメッセージがあることを意味しており、そのメッセージを `Complete` に指定することはできません。

`messagearchive` テーブルを構成する行のスキーマは、`message` テーブルの行と同じです。このアーカイブ データの利用目的に応じて、各行に格納するプロパティの数を減らしたり、一定期間経過した行を削除したりすることで、そのサイズとコストを制限できます。



<h2><a name="queues"></a><span class="short-header">キュー</span>Windows Azure のキュー</h2>

Windows Azure のキューは、この多層アプリケーションの階層間でのやり取り、およびバックエンド層における Worker ロール間のやり取りを支援します。
アプリケーションの拡張性を高めるため、ワーカー ロール A とワーカー ロール B はキューを使用して互いにコミュニケーションをとります。Worker ロール A は、各電子メールについて message テーブルに行を作成できます。Worker ロール B は、message テーブルをスキャンして、未送信の電子メールを表している行を探します。ただし、Worker ロール B の新しいインスタンスを追加して作業を分散することはできません。ワーカー ロール A とワーカー ロール B 間の作業調整にテーブル行を使用する場合、問題は、必ず 1 つのワーカー ロール インスタンスがテーブル行を取得して処理するとは限らないことです。これを保証するのがキューです。ワーカー ロール インスタンスがキューから作業項目を取り出したとき、キュー サービスは、他のワーカー ロール インスタンスが同じ作業項目を取り出せないようにします。Windows Azure のキューに備わっているこの排他的リース機能により、Worker ロールの複数のインスタンス間でワークロードを共有できます。

Windows Azure にはサービス バス キュー サービスも用意されています。Windows Azure のストレージのキューとサービス バスのキューの詳細については、[このシリーズの最終チュートリアル][tut5]の末尾で紹介されている関連情報を参照してください。

Windows Azure 電子メール サービス アプリケーションでは、`AzureMailQueue` と `AzureMailSubscribeQueue` という 2 つのキューを使用します。

### AzureMailQueue ###

`AzureMailQueue` キューは、メーリング リストへの電子メール送信を調整します。Worker ロール A は、送信する各電子メールの作業項目をこのキューに追加します。Worker ロール B は、このキューから作業項目を取り出して電子メールを送信します。

キュー作業項目は、メッセージの送信予定日 (`message` テーブルのパーティション キー)、`MessageRef` 値と `EmailAddress` 値 (`message` テーブルの行キー)、および "Worker ロールが停止し、再起動後に作成された作業項目かどうか" を示すフラグで構成されたコンマ区切り文字列です。たとえば次のようになります。

      2012-10-15,634852858215726983,student1@contoso.edu,0

Worker ロール B はこれらの値を使用して、電子メール送信に必要なすべての情報が格納されている行を `message` テーブルで探します。再起動フラグが "再起動" を示している場合、Worker ロール B は、電子メールがまだ送信されていないことを確認した上でメッセージを送信します。

トラフィックが急増したときは、ワーカー ロール B のインスタンスを複数生成し、それぞれ独立してキューから作業項目を取り出せるようにクラウド サービスを再構成できます。

### AzureMailSubscribeQueue ###

`AzureMailSubscribeQueue` キューは登録確認メールの送信を調整します。サービス メソッドが呼び出されると、そのサービス メソッドは AzureMailSubscribeQueue キューに作業項目を追加します。Worker ロール B は、このキューから作業項目を取り出し、登録確認メールを送信します。

キュー作業項目には登録者 GUID が含まれます。この値は電子メール アドレスと登録先リストを一意に識別します。これらは、ワーカー ロール B が確認メールを送信する際に必要な情報です。前述のように、`PartitionKey` および `RowKey` でないフィールドを照会する必要があるため、効率的ではありません。このアプリケーションの拡張性を高めるには、`mailinglist` テーブルの構成を変更し、`RowKey` に登録者 GUID を追加する必要があります。




<h2><a name="datadiagram"></a><span class="short-header">データ図</span>Windows Azure 電子メール サービスのデータ図</h2>

次の図は、このアプリケーションで使用するテーブルとキュー、およびこれらの関連性を示しています。

   ![Windows Azure 電子メール サービス アプリケーションのデータ図][mtas-datadiagram]





<h2><a name="blobs"></a><span class="short-header">BLOB</span>Windows Azure の BLOB</h2>

BLOB は "Binary Large OBject (バイナリ ラージ オブジェクト)" の略です。Windows Azure BLOB サービスは、ファイルをクラウドにアップロードして保存するための手段を提供します。Windows Azure の BLOB の詳細については、[このシリーズの最終チュートリアル][tut5]の末尾で紹介されている関連情報を参照してください。

Windows Azure メール サービス管理者は、電子メールの本文を *.htm* ファイル (HTML 形式) と *.txt* ファイル (プレーンテキスト形式) に保存します。電子メールの送信スケジュールを設定する際、管理者はこれらのファイルを **Create Message** Web ページにアップロードします。この Web ページの ASP.NET MVC コントローラーは、アップロードされたファイルを Windows Azure BLOB に格納します。

ファイルがフォルダーに保存されるように、BLOB は BLOB コンテナーに格納されます。Windows Azure 電子メール サービス アプリケーションでは、**azuremailblobcontainer** という BLOB コンテナーを 1 つだけ使用します。このコンテナーに格納された BLOB の名前を生成するには、MessageRef 値にファイル拡張子を付加します。たとえば、
634852858215726983.htm や 634852858215726983.txt となります。

HTML メッセージとプレーンテキスト メッセージはどちらも文字列なので、BLOB ではなく、`Message` テーブルの string 型プロパティに電子メール本文を格納するようアプリケーションを設計することも可能でした。ただし、テーブル行のプロパティはサイズが 64 K に制限されています。BLOB を使用すれば、メール本文のこのサイズ制限を避けることができます (64 K はプロパティの最大合計サイズです。エンコードのオーバーヘッドがあるので、実際にプロパティに格納できる最大文字列サイズは 48 K 程度になります)。




<h2><a name="wawsvswacs"></a><span class="short-header">クラウド サービス vs. Web サイト</span>Windows Azure のクラウド サービス vs. Windows Azure の Web サイト</h2>

Windows Azure 電子メール サービスをダウンロードした時点では、フロントエンドとバックエンドがすべて 1 つの Windows Azure クラウド サービスで動作するように構成されています。

![アプリケーション アーキテクチャの概要][mtas-architecture-overview]

別のアーキテクチャとして、フロントエンドを Windows Azure の Web サイトで実行することもできます。

![別のアプリケーション アーキテクチャ][mtas-alternative-architecture]

すべてのコンポーネントをクラウド サービスに配置するのであれば、構成と展開は非常にシンプルです。アプリケーションの ASP.NET MVC フロントエンドを Windows Azure の Web サイトに配置する場合、展開は 2 つになります。1 つは Windows Azure の Web サイト、もう 1 つは Windows Azure クラウド サービスです。さらに、Windows Azure のクラウド サービスの Web ロールには次の機能が備わっているのに対し、Windows Azure の Web サイトではこれらの機能を使用できません。

- カスタム証明書とワイルドカード証明書をサポート。
- IIS 構成を詳細に制御できる。多くの IIS 機能は Windows Azure の Web サイトで使用できません。Windows Azure の Web ロールを使用すれば、[AppCmd](http://www.iis.net/learn/get-started/getting-started-with-iis/getting-started-with-appcmdexe "appCmd") プログラムを実行して IIS 設定を変更するスタートアップ コマンドを定義できます。これらの設定は *Web.config* ファイルでは構成できません。詳細については、「[Windows Azure の IIS コンポーネントを構成する](http://msdn.microsoft.com/en-us/library/windowsazure/gg433059.aspx)」および「[Web ロールのアクセスから特定の IP アドレスをブロックする](http://msdn.microsoft.com/en-us/library/windowsazure/jj154098.aspx)」を参照してください。
- [オートスケーリング アプリケーション ブロック][autoscalingappblock]を使用して、Web アプリケーションを自動的にスケーリング。
- 管理者特権でスタートアップ スクリプトを実行して、アプリケーションのインストール、レジストリ設定の変更、パフォーマンス カウンターのインストールなどを行える。
- [Windows Azure Connect](http://msdn.microsoft.com/en-us/library/windowsazure/gg433122.aspx) および [Windows Azure の仮想ネットワーク](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx)で使用するネットワーク分離。
- リモート デスクトップからアクセスして、デバッグや高度な診断を行う。
- [VIP スワップ](http://msdn.microsoft.com/en-us/library/windowsazure/ee517253.aspx "VIP swap")を使用したローリング アップグレード。この機能は、ステージング環境と運用環境のコンテンツを切り替えます。

同じ容量でも、クラウド サービスで実行する Web ロールより Windows Azure の Web サイトの方が安価なので、コスト面では代替アーキテクチャの方が有利といえます。シリーズの後半のチュートリアルでは、これら 2 つのアーキテクチャの違いについて詳しく説明します。

Windows Azure の Web サイトと Windows Azure のクラウド サービスの違いの詳細については、「[Windows Azure Execution Models (Windows Azure 実行モデル)](http://www.windowsazure.com/en-us/manage/windows/fundamentals/compute/)」を参照してください。




<h2><a name="cost"></a><span class="short-header">料金</span>料金</h2>

ここでは、サンプル アプリケーションを Windows Azure で実行する際の料金について簡単に説明します。なお、紹介するのは、このチュートリアルが公開された 2012 年 12 月時点での料金です。ビジネス戦略などを決定する前に、次の Web ページで最新の料金を確認してください。

* [Windows Azure 料金計算ツール](http://www.windowsazure.com/en-us/pricing/calculator/)
* [SendGrid Windows Azure](http://www.windowsazure.com/en-us/store/service/?id=f131eadb-7aa3-401a-a2fb-1c7e71f45c3c)

どのくらいの料金がかかるかは、管理する Web ロールと Worker ロールのインスタンス数によって異なります。[Azure クラウド サービス 99.95% サービス レベル アグリーメント (SLA)](https://www.windowsazure.com/en-us/support/legal/sla/ "SLA") の保証条件として、各ロールについて複数のインスタンスを展開する必要があります。ロール インスタンスを 2 つ以上実行しなければならないのは、オペレーティング システムをアップグレードするため、アプリケーションを実行する仮想マシンが月に 2 回ほど再起動されるためです (OS の更新の詳細については、「[Role Instance Restarts Due to OS Upgrades (OS のアップグレードに伴うロール インスタンスの再起動)](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)」を参照してください)。

このサンプルの場合、2 つの Worker ロールによって実行される作業は時間的制約が厳しくないので、99.5% SLA は必要ありません。したがって、処理量に対応できるのであれば、各ワーカー ロールのインスタンスは 1 つだけでかまいません。一方、Web ロール インスタンスには時間的制約があります。ユーザーが利用する Web サイトは中断が許されません。したがって、運用アプリケーションでは Web ロール インスタンスを 2 つ以上実行する必要があります。

次の表は、最小限の処理量を想定し、Windows Azure 電子メール サービス サンプル アプリケーションを既定のアーキテクチャで実行した場合の料金を示しています。これらの料金は、サイズ XS の共有仮想マシンを使用することを前提としています。Visual Studio クラウド プロジェクトを作成する際の既定の仮想マシン サイズは "S" です。S サイズの場合、XS サイズのおよそ 5 倍の料金がかかります。
  
<table border="1">

<tr bgcolor="lightgray">
<th>コンポーネントまたはサービス</th>
<th>料金</th>
<th>コスト/月</th>
</tr>

<tr>
<td>Web ロール</td>
<td>2 インスタンス - $.02/時間 (XS インスタンス)</td>
<td>$29.00</td>
</tr>

<tr>
<td>ワーカー ロール A (電子メールの送信スケジュールを設定)</td>
<td>1 インスタンス - $.02/時間 (XS インスタンス)</td>
<td>$14.50
</td>
</tr>

<tr>
<td>ワーカー ロール B (電子メールの送信)</td>
<td>1 インスタンス - $.02/時間 (XS インスタンス)</td>
<td>$14.50</td>
</tr>

<tr>
<td>Windows Azure のストレージ トランザクション</td>
<td>月あたり 100 万トランザクション - $0.10/百万 (各クエリーはトランザクションとしてカウントされます。ワーカーロール A はテーブルに対して継続的にクエリを実行し、送信するメッセージを指定します。このアプリケーションは診断データを Windows Azure のストレージに書き込むように構成されており、それぞれの書き込みがトランザクションとなります)。</td>
<td>$0.10</td>
</tr>

<tr>
<td>Windows Azure ローカル冗長ストレージ</td>
<td>25 GB で $2.33 (アプリケーション テーブルと診断データのストレージを含みます)</td>
<td>$2.33</td>
</tr>

<tr>
<td>帯域幅</td>
<td>5 GB まで無料</td>
<td>無料</td>
</tr>

<tr>
<td>SendGrid</td>
<td>Windows Azure ユーザーは 1 か月あたり 25,000 通の電子メールを無料で送信できます</td>
<td>無料</td>
</tr>

<tr>
<td colspan="2">合計</td>
<td>$60.43</td>
</tr>

</table>

上記の表からわかるように、合計料金の大部分はロール インスタンスが占めています。ロール インスタンスは停止している場合でも料金が発生します。料金がかからないようにするには、ロール インスタンスを削除しなければなりません。コストを節約するには、ワーカー ロール A とワーカー ロール B のすべてのコードを 1 つのワーカー ロールに移動する方法があります。このチュートリアルでは、スケールアウトを容易にするため、意図的に 2 つのワーカー ロール インスタンスを実装しています。ワーカー ロール B が行う作業は Windows Azure キュー サービスによって調整されます。つまり、ロール インスタンスの数を増やすことでワーカー ロール B をスケールアウトできます (処理量が増えたときはワーカー ロール B が制約要因となります)。ワーカー ロール A が実行する作業はキューによって調整されません。したがって、ワーカー ロール A については複数のインスタンスを実行できません。2 つのワーカー ロールを組み合わせてスケールアウトする場合は、ワーカー ロール A のタスクが必ず 1 つのインスタンスで実行されるようなしくみを実装する必要があります (この機能は [CloudFx](http://nuget.org/packages/Microsoft.Experience.CloudFx "CloudFX") に備わっています。[WorkerRole.cs サンプル](http://code.msdn.microsoft.com/windowsazure/CloudFx-Samples-60c3a852/sourcecode?fileId=57087&pathId=528472169)を参照してください)。

これら 2 つの Worker ロールのすべてのコードを Web ロールへ移動し、すべての処理を Web ロールで実行する方法もあります。ただし、ASP.NET ではバックグラウンド タスク実行できないので、このアーキテクチャの拡張性が損なわれてしまいます。詳細については、「[The Dangers of Implementing Recurring Background Tasks In ASP.NET (繰り返し実行されるバックグラウンド タスクを ASP.NET に実装する危険性)](http://haacked.com/archive/2011/10/16/the-dangers-of-implementing-recurring-background-tasks-in-asp-net.aspx)」を参照してください。また、「[How to Combine a Worker and Web Role in Windows Azure (Windows Azure の Worker ロールと Web ロールを結合する方法)](http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2010/12/how-to-combine-worker-and-web-role-in.html)」および「[Combining Multiple Azure Worker Roles into an Azure Web Role (複数の Azure Worker ロールを 1 つの Azure Web ロールに集約)](http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2012/02/combining-multiple-azure-worker-roles.html)」も参照してください。


コストを削減するには、[オートスケーリング アプリケーション ブロック][autoscalingappblock]を使用する方法もあります。この場合、指定した期間内のみ Worker ロールが自動的に展開され、作業が完了した時点で削除されます。オートスケーリングの詳細については、[このシリーズの最終チュートリアル][tut5]の末尾で紹介されている関連情報を参照してください。

将来、Windows Azure に再起動の通知機能が追加される予定です。その場合、再起動の間だけ Web ロール インスタンスを追加できます。99.95 SLA の対象にはなりませんが、再起動中も Web アプリケーションを実行しつつ、料金をほぼ半分に減らすことができます。


<h2><a name="auth"></a><span class="short-header">認証と承認</span>認証と承認</h2>

ASP.NET Web API サービス メソッドなど、ASP.NET MVC Web フロントエンドの ASP.NET メンバーシップ システムのように、運用アプリケーションに認証と承認メカニズムを実装します。Web API サービス メソッドをセキュリティで保護するには、その他の方法 (共有鍵など) もあります。設定と展開を容易にするため、サンプル アプリケーションには認証と承認機能が実装されていません (IP 制限を実装し、クラウドに展開したアプリケーションを未許可のユーザーが使用できないようにする方法は、本シリーズの 2 番目のチュートリアルで説明されています)。

ASP.NET MVC Web プロジェクトに認証と承認を実装する方法については、次の関連サイトを参照してください。

* [Authentication and Authorization in ASP.NET Web API (ASP.NET Web API の認証と承認)](http://www.asp.net/web-api/overview/security/authentication-and-authorization/authentication-and-authorization-in-aspnet-web-api)
* [Music Store Part 7: Membership and Authorization (音楽ストア パート 7: メンバーシップと承認)](http://www.asp.net/mvc/tutorials/mvc-music-store/mvc-music-store-part-7)

**注**: 共有シークレットを使用して Web API サービス メソッドを保護するしくみを追加する予定でしたが、初回リリースに間に合いませんでした。そのため、3 番目のチュートリアルでは、登録プロセス用の Web API コントローラーを構築する方法が説明されていません。このチュートリアルの次期バージョンでは安全な登録プロセスの実装方法を紹介できるよう、現在準備を進めています。それまでは、管理者 Web ページを使用して電子メール アドレスをリストに登録することで、アプリケーションをテストできます。




<h2><a name="nextsteps"></a><span class="short-header">次の手順</span>次の手順</h2>

[次のチュートリアル][tut2]では、サンプル プロジェクトをダウンロードして開発環境を構成し、それぞれの環境に合わせてプロジェクトを構成します。さらに、構成したプロジェクトをローカルおよびクラウドでテストします。この後のチュートリアルでは、プロジェクトを新しく構築する方法を学習します。

Windows Azure のストレージのテーブル、キュー、BLOB に関する参考情報は、[このシリーズの最終チュートリアル][tut5]の末尾に記載されています。

<div><a href="/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/" class="site-arrowboxcta download-cta">チュートリアル 2</a></div>

[tut2]: /en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
[tut3]: /en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/
[tut4]: /en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
[tut5]: /en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
[autoscalingappblock]: /en-us/develop/net/how-to-guides/autoscaling/




[mtas-architecture-overview]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-architecture-overview.png
[mtas-alternative-architecture]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-alternative-architecture.png
[mtas-mailing-list-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-mailing-list-index-page.png
[mtas-subscribers-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribers-index-page.png
[mtas-message-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-index-page.png
[mtas-message-create-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-create-page.png
[mtas-subscribe-confirmation-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-confirmation-page.png
[mtas-unsubscribe-query-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-unsubscribe-query-page.png
[mtas-unsubscribe-confirmation-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-unsubscribe-confirmation-page.png
[mtas-worker-roles-a-and-b]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-worker-roles-a-and-b.png
[mtas-message-processing]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-processing.png
[mtas-subscribe-email]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-email.png
[mtas-subscribe-diagram]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-diagram.png
[mtas-datadiagram]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-datadiagram.png



