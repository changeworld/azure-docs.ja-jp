<properties 
	pageTitle="Azure プレビュー ポータルでの役割ベースのアクセス制御" 
	description="ロール ベースのアクセス制御のしくみとその設定方法について説明します。" 
	services="" 
	documentationCenter="" 
	authors="Justinha" 
	manager="terrylan" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="Ibiza" 
	ms.workload="infrastructure-services" 
	ms.date="02/20/2015" 
	ms.author="justinha"/>

<!--これは、TOC、セクションと小見出し、他の azure.microsoft.com トピックへのリンク、他のサイトへのリンク、太字、斜体、番号付きリストと箇条書きリスト、コード スニペット、イメージを含むトピックを作成するマークダウンの使用方法を説明する基本的なテンプレートです。高度なマークダウンの場合は、公開されたトピックを見つけ、必要なマークダウンや HTML をコピーしてください。Markdown の使用に関する詳細については、次を参照してください。 http://sharepoint/sites/azurecontentguidance/wiki/Pages/Content%20Guidance%20Wiki%20Home.aspx.-->

<!--Properties section (above): this is required in all topics. Please fill it out!-->

<!--The next line, with one pound sign at the beginning, is the page title--> 
# Azure プレビュー ポータルでのロール ベースのアクセス制御 

<p> Azure プレビュー ポータルでの役割ベースのアクセス制御 (RBAC) のサポートが追加されたので、組織がアクセス管理の要件を簡単かつ正確に満たせるようになりました。<a href="http://go.microsoft.com/fwlink/?LinkId=511576" target="_blank">ブログの投稿</a>は、機能の概要を提供しを使い始めるためです。このトピックでは、概念について詳しく説明し、追加の使用事例も記載しています。 </p>

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## Azure での RBAC
                                                                   
各 Azure サブスクリプションは Azure Active Directory と関連付けられます。Azure 管理ポータルか Azure リソース マネージャーの API を使ってサブスクリプションのリソースにアクセスするユーザーやサービスは、最初に Azure Active Directory での認証を実行する必要があります。

![][1] 

Azure の役割ベースのアクセス制御を利用すると、サブスクリプション、リソース グループ、または個々のリソースのレベルで役割を割り当てて、該当するアクセス権を Azure AD のユーザー、グループ、サービスに付与できます。役割を割り当てると、Azure リソースに対するユーザー、グループ、サービスのアクセス権のレベルが定義されます。 

### 役割

ロールは、Azure リソースで実行できるアクションのコレクションです。アクションが含まれている役割がユーザーやサービスに割り当てられると、Azure リソースに対するそのアクションの実行が許可されます。組み込みのロールの一覧についてと**、**アクションと**操作しない**のプロパティを参照してください[組み込みのロール](#builtinroles).

### 役割の割り当て

Azure AD のユーザーやサービスにアクセス権を付与するには、Azure リソースに対する該当する役割を割り当てます。 

#### Azure AD のセキュリティ プリンシパル

以下の種類の Azure AD セキュリティ プリンシパルに役割を割り当てることができます。

+ **ユーザー**。 ロールは、Azure のサブスクリプションが関連付けられている Azure AD で組織のユーザーに割り当てることができます。Azure プレビュー ポータルで招待アクションを使ってユーザーに役割を割り当てることにより、外部 Microsoft アカウント ユーザー (joe@outlook.com など) に役割を割り当てることもできます。役割を外部 Microsoft アカウント ユーザーに割り当てると、そのユーザーに関するゲスト アカウントが Azure AD 内に作成されます。ディレクトリ内でこのゲスト アカウントを無効にすると、外部ユーザーはアクセス権を付与されている Azure リソースへのアクセスが許可されなくなります。
+ **グループ**。 ロールは、Azure AD セキュリティ グループに割り当てることができます。アクセス権を付与されたグループのメンバーになったユーザーには、リソースに対するアクセス権を自動的に付与されます。同様にユーザーがグループから削除されると、リソースに対するアクセス権が自動的に失われます。役割を直接ユーザーに割り当てるより、役割をグループに割り当て、そのグループにユーザーを追加して、グループ単位でアクセスを管理する方がベスト プラクティスです。RBAC の azure では、同報リストにロールの割り当ては使用できません。
	役割をグループに割り当てる機能により、組織は既存のアクセス制御モデルをオンプレミスのディレクトリからクラウドへと拡張できるので、オンプレミスのアクセスを制御するために既に確立しているセキュリティ グループを再利用して Azure プレビュー ポータル内のリソースに対するアクセスを制御できます。ユーザーとグループは、内部設置型ディレクトリから同期するためのさまざまなオプションの詳細については、次を参照してください。[ディレクトリの統合](http://technet.microsoft.com/library/jj573653.aspx)です。Azure AD プレミアムにも用意されています、[グループ管理機能の委任](http://msdn.microsoft.com/library/azure/dn641267.aspx)するグループを作成および管理機能に委任できる管理者以外のユーザー Azure AD からとします。
+ **サービス プリンシパル**。 サービス id は、ディレクトリ内のサービス プリンシパルとして表されます。サービス プリンシパルは Azure AD での認証を実施し、相互に安全に通信できます。Azure リソースに対するアクセス権をサービスに付与するには、Windows PowerShell 用の Azure モジュールで、そのサービスを表す Azure AD サービス プリンシパルに役割を割り当てます。 

#### リソースのスコープ

サブスクリプション全体に対するアクセス権を付与する必要はありません。個々のリソースに加えて、リソース グループに関する役割を割り当てることもできます。Azure RBAC では、リソースは役割の割り当てを親のリソースから継承します。したがって、ユーザー、グループ、サービスがサブスクリプション内の 1 つのリソース グループのみに対するアクセス権を付与されている場合、そのリソース グループと含まれているリソースのみにアクセスでき、サブスクリプション内の他のリソース グループにはアクセスできません。別の例として、リソース グループについてはセキュリティ グループを閲覧者役割に追加しますが、そのリソース グループ内のデータベースについては共同作成者役割に追加することができます。

![][2]

## サブスクリプションの共同管理者で RBAC の共存

サブスクリプションの管理者と共同管理者には、引き続き Azure ポータルと管理 API に対するフル アクセス権が付与されます。RBAC モデルでは、サブスクリプション レベルで所有者役割を割り当てられます。  
ただし、新しい RBAC モデルは Azure プレビュー ポータルと Azure リソース マネージャーの API のみでサポートされます。RBAC 役割を割り当てられたユーザーやサービスは、Azure 管理ポータルやサービス管理の API にアクセスできません。Azure プレビュー ポータルでサブスクリプションの所有者役割にユーザーを追加しても、そのユーザーは完全版の Azure ポータル内のサブスクリプションの共同管理者にはなりません。

Azure プレビュー ポータルを使用して管理するのには使用できませんが、Azure のリソースにユーザーにアクセスを許可する場合は、完全、Azure 管理ポータルを使用して、サブスクリプションの共同管理者を追加する必要があります。現時点で RBAC を使って管理できないリソースの例には、Service Bus やクラウド サービスがあります。

## 管理用の承認とデータ操作

役割ベースのアクセス制御のサポートは、Azure プレビュー ポータルと Azure リソース マネージャーの API での Azure リソースの管理操作専用です。RBAC による Azure リソースのデータ レベルの操作の中には、許可されていないものもあります。たとえば、ストレージ アカウントの作成/読み取り/更新/削除は RBAC によって制御できますが、ストレージ アカウント内の BLOB やテーブルの作成/読み取り/更新/削除は現時点では RBAC によって制御できません。同様に、SQL DB の作成/読み取り/更新/削除は RBAC によって制御できますが、DB 内の SQL テーブルの作成/読み取り/更新/削除は現時点では RBAC によって制御できません。

## アクセス権の追加と削除の方法

組織内のリソース所有者がアクセスを管理する方法の例を見てみましょう。このシナリオでは、Azure リソースを使って構築されているさまざまなテスト プロジェクトと実働プロジェクトで複数の人が作業しています。アクセス権の付与に関するベスト プラクティスに従おうと思っています。ユーザーはすべての必須リソースに対するアクセス権を持っている必要がありますが、追加のアクセス権がありません。プロセスやツールに対する投資をすべて再利用することにより、オンプレミスの Active Directory で確立したセキュリティ グループを使用します。以下のセクションでは、これらのリソースに対するアクセス権をセットアップする方法について説明します。

* [アクセスを追加します。(#add)
* [アクセスを削除します。(#remove)
* [追加または外部ユーザーのアクセスの削除](#addremoveext)

<h3><a id="add"></a>アクセスを追加します。</h3>

アクセス要件と Azure でのセットアップ方法について以下に要約します。

ユーザー/グループ  | アクセス要件  | アクセスに関する役割とスコープ	
------------- |-------------  |------------
Jill Santos のチームの全員  | すべての Azure リソースの読み取り  | Jill Santos のチームを表す AD グループを Azure サブスクリプションに関する閲覧者役割に追加
Jill Santos のチームの全員  | テスト リソース グループ内のすべてのリソースの作成と管理  | Jill Santos のチームを表す AD グループをテスト リソース グループに関する共同作業者役割に追加
Brock  | 実働リソース グループ内のすべてのリソースの作成と管理  | Brock を実働リソース グループに関する共同作業者役割に追加


最初に、サブスクリプションのすべてのリソースに関する読み取りアクセス権を追加しましょう。クリックして**参照 > すべて > サブスクリプション**です。

![][3] 

クリックして *name of your subscription*** > リーダー > 追加**です。ユーザーとグループの一覧を選択または Active Directory グループの名前を入力します。

![][4]

次に、同じチームをテスト リソース グループの共同作業者役割に追加します。このリソース グループをクリックしてプロパティ ブレードを開きます。[**ロール**、] をクリックして**共同作成者 > 追加**チームの名前を入力します。

![][5]

には、Brock Prod リソース グループの寄稿者の役割を追加する] をクリックして、リソース グループ**共同作成者 > 追加**Brock の名前を入力します。 

![][6]

Windows PowerShell 用の Microsoft Azure モジュールを使って役割の割り当てを管理することもできます。ポータルではなく New-AzureRoleAssignment コマンドレットを使って Brock のアカウントを追加する例を以下に示します。

	PS C:\> New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDB

追加および削除のアクセスの Windows PowerShell の使用に関する詳細については、次を参照してください。 [Windows PowerShell でのロール基準アクセス制御の管理](http://azure.microsoft.com/documentation/articles/role-based-access-control-powershell/)です。 

<h3><a id="remove"></a>アクセスを削除します。</h3>

割り当ての削除も簡単です。Brad Adams という名前のユーザーを TestDB という名前のリソース グループに関する閲覧者役割から削除するとします。リソース グループのブレードを開き、[**リーダー > Brad Adams > 削除**です。

![][7]

Remove-AzureRoleAssignment コマンドレットを使って Brad Adams を削除する方法の例を以下に示します。

	PS C:\> Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDB

<h3><a id="addremoveext"></a>追加または外部ユーザーのアクセスの削除</h3>

**構成**ディレクトリのタブには外部ユーザーのアクセスを制御するオプションが含まれています。これらのオプションを変更できるのは、ディレクトリのグローバル管理者が完全版の Azure ポータルの UI においてだけです (Windows PowerShell や API による方法はありません)。 
開くには、**構成**完全 Azure ポータルで] タブ、[ **Active Directory**、ディレクトリの名前をクリックします。

![][10]

次に、外部ユーザーに関するアクセスを制御するオプションを編集できます。 

![][8]

既定では、ゲストを列挙できません、ディレクトリの内容は、ユーザーまたはグループに表示されないように、**メンバー リスト**です。ユーザーの完全なメール アドレスを入力してユーザーを検索してから、アクセス権を付与できます。ゲストに関する既定の制限のセットは以下のとおりです。

- ディレクトリ内のユーザーやグループの列挙はできません。
- ユーザーのメール アドレスが分かっている場合は、そのユーザーの詳細情報の一部を表示できます。
- グループ名が分かっている場合は、そのグループの詳細情報の一部を表示できます。

ユーザーやグループの詳細情報の一部をゲストが表示できる機能を利用すると、他者を招待したり、連携者の詳細を表示したりできます。  

外部ユーザーに関するアクセス権を追加するプロセスの手順を説明します。外部ユーザーを同じ TestDB リソース グループに関する閲覧者役割に追加して、ユーザーがエラーをデバッグできるようにします。リソース グループのブレードを開き、[**リーダー > 追加 > 招待**を追加するユーザーの電子メール アドレスを入力します。 

![][9]

外部ユーザーを追加する際には、ディレクトリ内にゲストが作成されます。その後、このゲストをグループに追加したりグループから削除したりするか、または他のディレクトリ ユーザーと同様に個別に役割から追加したり削除したりできます。 

ユーザーを削除する場合と同様に、ゲストを役割から削除することもできます。ゲストをリソース上の役割から削除しても、そのゲストはディレクトリから削除されません。 
 
## 役割ベースのアクセス制御を使用する際の既知の問題

プレビュー内にあるときに、役割ベース アクセス制御機能を使用する場合、問題が発生した場合を参照してください[ロール ベースのアクセス制御のトラブルシューティング](http://azure.microsoft.com/documentation/articles/role-based-access-control-troubleshooting/)の問題に関連する可能性が既知の問題です。


## 組み込みのロール

Azure のロール ベースのアクセス制御ユーザー、グループ、およびサービスに割り当てることのできる、次の組み込みのロールが付属します。組み込みの役割の定義は変更できません。Azure の RBAC の今後のリリースでは、一連の Azure リソースで実行できる使用可能なアクションの一覧からアクションを作成することによってカスタム ロールを定義することができます。

対応するリンクを参照してください] をクリックして、**アクション**と**操作しない**ロールの定義のプロパティです。**アクション**プロパティを Azure のリソースで実行できる操作を指定します。アクションの文字列にワイルドカード文字を使用できます。**操作しない**ロールの定義のプロパティには、許可されるアクションから除外する必要がありますアクションを指定します。 


ロール名。説明  	
------------- |-------------  
[API 管理サービスの共同作成者](#APIMgmt) |API 管理サービスがそれらにアクセスしないを管理できます。
[アプリケーションの Insights コンポーネントの投稿者](#AppInsights) |Application Insights のコンポーネントがないでアクセスできるように、管理できます。
[BizTalk の投稿者(#BizTalk) |ないへのアクセスが、BizTalk サービスを管理できます。
[ClearDB MySQL DB 投稿者](#ClearDB) |ClearDB MySQL データベースがそれらにアクセスしないを管理できます。
[投稿者(#Contributor) |投稿者のアクセスを除くすべてを管理できます。
[データの工場出荷時の投稿者](#DataFactory) |データのファクトリがないでアクセスできるように、管理できます。
[DB のドキュメントのアカウントの投稿者](#DocDBContrib) |DocumentDB のアカウントがないでアクセスできるように、管理できます。
[インテリジェント システム アカウントの投稿者](#IntelliSysContrib) |ないへのアクセスが、インテリジェント システム アカウントを管理できます。
[NewRelic APM アカウントの投稿者](#NewRelicContrib) |New relic によるアプリケーション パフォーマンス管理アカウントと、アプリケーションがそれらにアクセスしないを管理できます。
[所有者(#Owner) |所有者のアクセスを含む、すべてを管理できます。
[リーダー(#Reader) |リーダーは、すべてを表示できますが、変更を行うことはできません。
[Redis キャッシュの投稿者](#Redis) |管理、Redis キャッシュにアクセスしないことができます。
[SQL DB の投稿者](#SQLDBContrib) |それらにアクセスしないは SQL データベースを管理できます。また、そのセキュリティ関連のポリシーまたは、親の SQL server を管理することはできません。
[SQL セキュリティ マネージャー](#SQLSecMgr) |SQL server とデータベースにアクセスしないのは、セキュリティに関連するポリシーを管理できます。
[SQL Server の共同作成者](#SQLSrvContrib) |SQL サーバー、データベースがないへのアクセス、および、セキュリティに関連するポリシーではなく管理できます。
[スケジューラ ジョブ コレクションの投稿者](#SchedContrib) |スケジューラ ジョブのコレクションがそれらにアクセスしないを管理できます。
[検索サービスの共同作成者](#SearchContrib) |管理サービスの検索、それらにアクセスしないことができます。
[ストレージ アカウントの投稿者](#StorageContrib) |管理、ストレージ アカウントにアクセスしないことができます。
[ユーザー アクセスの管理者](#UserAccessAdmin) |Azure リソースへのユーザー アクセスを管理できます。
[仮想マシンの投稿者](#VMContrib) |により、仮想マシンがないでアクセスできるように、いない仮想ネットワークとに接続しているストレージ アカウントを管理できます。
[仮想ネットワークの投稿者](#VNetContrib) |管理仮想ネットワークは、それらにアクセスしないことができます。
[Web プランの共同作成者](#WebPlanContrib) |Web サイトがアクセスされていません web プランを管理できます。
[Web サイトの投稿者](#WebsiteContrib) |管理 (web プランされません) の web サイトにアクセスしないことができます。


<h3><a id="APIMgmt"></a>API 管理サービスの共同作成者</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.ApiManagement/Services/*</td>
<td>作成し、管理の API 管理サービス</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>読み取りのロールおよびロールの割り当て</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループを読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>作成して、リソース グループの展開の管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>作成して、アラート ルールの管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>作成して、サポート チケットの管理</td>
</tr>
</table>

<h3><a id="AppInsights"></a>アプリケーションの Insights コンポーネントの投稿者</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.Insights/components/*</td>
<td>作成し、Insights コンポーネントの管理</td>
</tr>
<tr>
<td>Microsoft.Insights/webtests/*</td>
<td>作成および Web テストを管理します。</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>読み取りのロールおよびロールの割り当て</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループを読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>作成して、リソース グループの展開の管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>作成して、アラート ルールの管理</td>
</tr>
<td>Microsoft.Support/*</td>
<td>作成して、サポート チケットの管理</td>
</tr>
</table>

<h3><a id="BizTalk"></a>BizTalk の投稿者</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.BizTalkServices/BizTalk/*</td>
<td>作成して、BizTalk サービスの管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>読み取りのロールおよびロールの割り当て</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループを読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>作成して、リソース グループの展開の管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>作成して、アラート ルールの管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>作成して、サポート チケットの管理</td>
</tr>
</table>

<h3><a id="ClearDB"></a>ClearDB MySQL DB 投稿者</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>successbricks.cleardb/databases/*</td>
<td>作成し、[ClearDB MySQL データベースの管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>読み取りのロールおよびロールの割り当て</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループを読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>作成して、リソース グループの展開の管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>作成して、アラート ルールの管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>作成して、サポート チケットの管理</td>
</tr>
</table>

<h3><a id="Contributor"></a>投稿者</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>*</td>
<td>作成し、管理のすべての種類のリソース</td>
</tr>
<tr>
<th colspan="2">not actions</th>
</tr>
<tr>
<td>Microsoft.Authorization/*/Write</td>
<td>ロールおよびロールの割り当てを作成することはできません。 </td>
</tr>
<tr>
<td>Microsoft.Authorization/*/Delete</td>
<td>ロールおよびロールの割り当てを削除することはできません。</td>
</tr>
</table>

<h3><a id="DataFactory"></a>データの工場出荷時の投稿者</h3>

<table style=width:100%">
<tr>
<td>Microsoft.DataFactory/dataFactories/*</td>
<td>作成し、データのファクトリを管理します。</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>読み取りのロールおよびロールの割り当て</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループを読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>作成して、リソース グループの展開の管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>作成して、アラート ルールの管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>作成して、サポート チケットの管理</td>
</tr>
</table>

<h3><a id="DocDBContrib"></a>DB のドキュメントのアカウントの投稿者</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.DocumentDb/databaseAccounts/*</td>
<td>作成および DocumentDB アカウントの管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>読み取りのロールおよびロールの割り当て</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループを読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>作成して、リソース グループの展開の管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>作成して、アラート ルールの管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>作成して、サポート チケットの管理</td>
</tr>
</table>

<h3><a id="IntelliSysContrib"></a>インテリジェント システム アカウントの投稿者</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.IntelligentSystems/accounts/*</td>
<td>作成およびインテリジェント システム アカウントの管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>読み取りのロールおよびロールの割り当て</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループを読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>作成して、リソース グループの展開の管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>作成して、アラート ルールの管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>作成して、サポート チケットの管理</td>
</tr>
</table>

<h3><a id="NewRelicContrib"></a>NewRelic APM アカウントの投稿者</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>NewRelic.APM/accounts/*</td>
<td>作成し、NewRelic アプリケーション パフォーマンス管理アカウントの管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>読み取りのロールおよびロールの割り当て</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループを読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>作成して、リソース グループの展開の管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>作成して、アラート ルールの管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>作成して、サポート チケットの管理</td>
</tr>
</table>

<h3><a id="Owner"></a>所有者</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>*</td>
<td>作成し、管理のすべての種類のリソース</td>
</tr>
</table>

<h3><a id="Reader"></a>リーダー</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>*/の読み取り</td>
<td>すべての種類のリソースを読み取る。ただしの機密情報を読み取ることができません。</td>
</tr>
</table>

<h3><a id="Redis"></a>Redis キャッシュの投稿者</h3>

<table style=width:100%">
<tr>
<td>Microsoft.Cache/redis/*</td>
<td>作成し、管理の Redis キャッシュ</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>読み取りのロールおよびロールの割り当て</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループを読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>作成して、リソース グループの展開の管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>作成して、アラート ルールの管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>作成して、サポート チケットの管理</td>
</tr>
</table>

<h3><a id="SQLDBContrib"></a>SQL DB の投稿者</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/read</td>
<td>SQL Server を読み取り</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/*</td>
<td>作成して、SQL データベースの管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>読み取りのロールおよびロールの割り当て</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループを読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>作成して、リソース グループの展開の管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>作成して、アラート ルールの管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>作成して、サポート チケットの管理</td>
</tr>
<tr>
<th colspan="2">not actions</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>SQL データベースの監査ポリシーを管理することはできません。</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>SQL データベース接続のポリシーを管理することはできません。</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>SQL データベースのデータのマスキング ポリシーを管理することはできません。</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>SQL データベースのセキュリティ基準を管理することはできません。</td>
</tr>
</table>

<h3><a id="SQLSecMgr"></a>SQL セキュリティ マネージャー</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/read</td>
<td>SQL Server を読み取り</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>作成し、SQL Server の監査ポリシーの管理</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/read</td>
<td>SQL データベースを読み取り</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>作成して、SQL データベースの監査ポリシーの管理</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>SQL データベース接続のポリシー作成および管理</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>作成および SQL データベースのデータのマスキング ポリシーの管理</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>作成し、SQL データベースのセキュリティ基準を管理します。</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>読み取りのロールおよびロールの割り当て</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループを読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>作成して、リソース グループの展開の管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>作成して、アラート ルールの管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>作成して、サポート チケットの管理</td>
</tr>
</table>

<h3><a id="SQLSrvContrib"></a>SQL Server の共同作成者</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.ApiManagement/Services/*</td>
<td>作成し、管理の API 管理サービス</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>読み取りのロールおよびロールの割り当て</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループを読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>作成して、リソース グループの展開の管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>作成して、アラート ルールの管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>作成して、サポート チケットの管理</td>
</tr>
<tr>
<th colspan="2">not actions</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>SQL Server の監査ポリシーを管理することはできません。</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>SQL データベースの監査ポリシーを管理することはできません。</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>SQL データベース接続のポリシーを管理することはできません。</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>SQL データベースのデータのマスキング ポリシーを管理することはできません。</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>SQL データベースのセキュリティ基準を管理することはできません。</td>
</tr>
</table>

<h3><a id="SchedContrib"></a>スケジューラ ジョブ コレクションの投稿者</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.Scheduler/jobcollections/*</td>
<td>作成して、スケジューラ ジョブ コレクションの管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>読み取りのロールおよびロールの割り当て</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループを読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>作成して、リソース グループの展開の管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>作成して、アラート ルールの管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>作成して、サポート チケットの管理</td>
</tr>
</table>

<h3><a id="SearchContrib"></a>検索サービスの共同作成者</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.Search/searchServices/*</td>
<td>作成し、検索サービスの管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>読み取りのロールおよびロールの割り当て</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループを読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>作成して、リソース グループの展開の管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>作成して、アラート ルールの管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>作成して、サポート チケットの管理</td>
</tr>
</table>

<h3><a id="StorageContrib"></a>ストレージ アカウントの投稿者</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/*</td>
<td>作成してストレージ アカウントの管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>読み取りのロールおよびロールの割り当て</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループを読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>作成して、リソース グループの展開の管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>作成して、アラート ルールの管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>作成して、サポート チケットの管理</td>
</tr>
</table>

<h3><a id="UserAccessAdmin"></a>ユーザー アクセスの管理者</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>*/の読み取り</td>
<td>すべての種類のリソースを読み取る</td>
</tr>
<tr>
<td>Microsoft.Authorization/*</td>
<td>作成し、ロールおよびロールの割り当てを管理します。</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>作成して、サポート チケットの管理</td>
</tr>
</table>

<h3><a id="VMContrib"></a>仮想マシンの投稿者</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/read</td>
<td>ストレージ アカウントを読み取り</td>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/listKeys/action</td>
<td>ストレージ アカウント キーを取得します。</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/read</td>
<td>仮想ネットワークの読み取り</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/join/action</td>
<td>仮想ネットワークに参加します。</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/reservedIps/read</td>
<td>予約済み IP アドレスの読み取り</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/reservedIps/link/action</td>
<td>予約済み IP アドレスへのリンク</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/domainNames/*</td>
<td>作成して、クラウド サービスの管理</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/virtualMachines/*</td>
<td>作成して、仮想マシンの管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>読み取りのロールおよびロールの割り当て</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループを読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>作成して、リソース グループの展開の管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>作成して、アラート ルールの管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>作成して、サポート チケットの管理</td>
</tr>
</table>

<h3><a id="VNetContrib"></a>仮想ネットワークの投稿者</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/*</td>
<td>作成し、管理仮想ネットワーク</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>読み取りのロールおよびロールの割り当て</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループを読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>作成して、リソース グループの展開の管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>作成して、アラート ルールの管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>作成して、サポート チケットの管理</td>
</tr>
</table>

<h3><a id="WebPlanContrib"></a>Web プランの共同作成者</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/*</td>
<td>作成および Web プランの管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>読み取りのロールおよびロールの割り当て</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループを読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>作成して、リソース グループの展開の管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>作成して、アラート ルールの管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>作成して、サポート チケットの管理</td>
</tr>
</table>

<h3><a id="WebsiteContrib"></a>Web サイトの投稿者</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/read</td>
<td>Web プランの読み取り</td>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/join/action</td>
<td>Web プランを参加します。</td>
</tr>
<tr>
<td>Microsoft.Web/sites/*</td>
<td>作成して、Web サイトの管理</td>
</tr>
<tr>
<td>Microsoft.Web/certificates/*</td>
<td>作成して、Web サイトの証明書の管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>読み取りのロールおよびロールの割り当て</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループを読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>作成して、リソース グループの展開の管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>作成して、アラート ルールの管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>作成して、サポート チケットの管理</td>
</tr>
</table>


## フィードバックを提供する方法

Azure の RBAC を実行してくださいをお送りください[フィードバック](http://aka.ms/azurerbacfeedback)です。 


## 次のステップ

役割ベースのアクセス制御の使用法に関するその他のリソースを以下に示します。 

+ [Windows PowerShell でのロール基準アクセス制御の管理](http://azure.microsoft.com/documentation/articles/role-based-access-control-powershell/)
+ [XPLAT-CLI でのロール基準アクセス制御の管理](http://azure.microsoft.com/documentation/articles/role-based-access-control-xplat-cli/)
+ [ロール ベースのアクセス制御のトラブルシューティング](http://azure.microsoft.com/documentation/articles/role-based-access-control-troubleshooting/)
+ [Azure Active Directory](http://msdn.microsoft.com/library/azure/jj673460.aspx)
+ [Azure Active Directory Premium、および Basic](http://msdn.microsoft.com/library/azure/dn532272.aspx)
+ [Azure のサブスクリプションに Azure AD に関連付けられた方法](http://msdn.microsoft.com/library/azure/dn629581.aspx)
+ セキュリティ グループのセルフ サービス グループ管理の概要について、以下を参照してください、 [Active Directory チームのブログ。](http://blogs.technet.com/b/ad/archive/2014/02/24/more-preview-enhancements-for-windows-azure-ad-premium.aspx)



<!--Image references-->
[1]: ./media/role-based-access-control-configure/RBACSubAuthDir.png
[2]: ./media/role-based-access-control-configure/RBACAssignmentScopes.png
[3]: ./media/role-based-access-control-configure/RBACSubscriptionBlade.png
[4]: ./media/role-based-access-control-configure/RBACAddSubReader_NEW.png
[5]: ./media/role-based-access-control-configure/RBACAddRGContributor_NEW.png
[6]: ./media/role-based-access-control-configure/RBACAddProdContributor_NEW.png
[7]: ./media/role-based-access-control-configure/RBACRemoveRole.png
[8]: ./media/role-based-access-control-configure/RBACGuestAccessControls.png
[9]: ./media/role-based-access-control-configure/RBACInviteExtUser_NEW.png
[10]: ./media/role-based-access-control-configure/RBACDirConfigTab.png



<!--HONumber=47-->
