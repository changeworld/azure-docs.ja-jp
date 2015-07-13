<properties 
	pageTitle="Microsoft Azure ポータルでのロールベースのアクセス制御" 
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
	ms.date="05/05/2015" 
	ms.author="justinha"/>

# Microsoft Azure ポータルでのロールベースのアクセス制御 

Microsoft Azure ポータルでのロールベースのアクセス制御 (RBAC) のサポートが追加されたので、組織がアクセス管理の要件を簡単かつ正確に満たせるようになりました。[ブログの記事](http://go.microsoft.com/fwlink/?LinkId=511576)に、機能が簡単に紹介されているので、すぐに使い始めることができます。このトピックでは、概念について詳しく説明し、追加の使用事例も記載しています。


## Azure での RBAC
                                                                   
各 Azure サブスクリプションは Azure Active Directory と関連付けられます。Microsoft Azure 管理ポータルか Azure リソース マネージャーの API を使ってサブスクリプションのリソースにアクセスするユーザーやサービスは、最初に Azure Active Directory での認証を実行する必要があります。

![][1]

Azure のロールベースのアクセス制御を利用すると、サブスクリプション、リソース グループ、または個々のリソースのレベルでロールを割り当てて、該当するアクセス権を Azure AD のユーザー、グループ、サービスに付与できます。ロールを割り当てると、Azure リソースに対するユーザー、グループ、サービスのアクセス権のレベルが定義されます。

### ロール

ロールとは、Azure リソースに対して実行できるアクションのコレクションのことです。アクションが含まれているロールがユーザーやサービスに割り当てられると、Azure リソースに対するそのアクションの実行が許可されます。組み込みのロールとその **actions** や **not actions** プロパティの一覧については、「[組み込みのロール](#builtinroles)」をご覧ください。

### ロールの割り当て

Azure AD のユーザーやサービスにアクセス権を付与するには、Azure リソースに対する該当するロールを割り当てます。

#### Azure AD のセキュリティ プリンシパル

以下の種類の Azure AD セキュリティ プリンシパルにロールを割り当てることができます。

+ **ユーザー**: Azure サブスクリプションが関連付けられている Azure AD 内の組織ユーザーにロールを割り当てることができます。Azure ポータルで招待アクションを使ってユーザーにロールを割り当てることにより、外部 Microsoft アカウント ユーザー (joe@outlook.com など) にロールを割り当てることもできます。ロールを外部 Microsoft アカウント ユーザーに割り当てると、そのユーザーに関するゲスト アカウントが Azure AD 内に作成されます。ディレクトリ内でこのゲスト アカウントを無効にすると、外部ユーザーはアクセス権を付与されている Azure リソースへのアクセスが許可されなくなります。
+ **グループ**: Azure AD セキュリティ グループにロールを割り当てることができます。アクセス権を付与されたグループのメンバーになったユーザーには、リソースに対するアクセス権を自動的に付与されます。同様にユーザーがグループから削除されると、リソースに対するアクセス権が自動的に失われます。ロールを直接ユーザーに割り当てるより、ロールをグループに割り当て、そのグループにユーザーを追加して、グループ単位でアクセスを管理する方がベスト プラクティスです。Azure RBAC は配布リストへのロールの割り当てを許可しません。ロールをグループに割り当てる機能により、組織は既存のアクセス制御モデルをオンプレミスのディレクトリからクラウドへと拡張できるので、オンプレミスのアクセスを制御するために既に確立しているセキュリティ グループを再利用して Azure ポータル内のリソースに対するアクセスを制御できます。オンプレミスのディレクトリからユーザーやグループを同期化するさまざまなオプションの詳細については、「[ディレクトリ統合の概要](http://technet.microsoft.com/library/jj573653.aspx)」を参照してください。Azure AD Premium は、[委任されたグループ管理機能](http://msdn.microsoft.com/library/azure/dn641267.aspx)も提供しており、グループを作成して管理する機能を Azure AD から管理者以外のユーザーに委任できます。
+ **サービス プリンシパル**: サービス ID はディレクトリ内でサービス プリンシパルとして表されます。サービス プリンシパルは Azure AD での認証を実施し、相互に安全に通信できます。Azure リソースに対するアクセス権をサービスに付与するには、Windows PowerShell 用の Azure モジュールで、そのサービスを表す Azure AD サービス プリンシパルにロールを割り当てます。 

#### リソースのスコープ

サブスクリプション全体に対するアクセス権を付与する必要はありません。個々のリソースに加えて、リソース グループに関するロールを割り当てることもできます。Azure RBAC では、リソースはロールの割り当てを親のリソースから継承します。したがって、ユーザー、グループ、サービスがサブスクリプション内の 1 つのリソース グループのみに対するアクセス権を付与されている場合、そのリソース グループと含まれているリソースのみにアクセスでき、サブスクリプション内の他のリソース グループにはアクセスできません。別の例として、リソース グループについてはセキュリティ グループを閲覧者ロールに追加しますが、そのリソース グループ内のデータベースについては共同作成者ロールに追加することができます。

![][2]

## RBAC とサブスクリプションの共同管理者の共存

サブスクリプションの管理者と共同管理者には、引き続き Azure ポータルと管理 API に対するフル アクセス権が付与されます。RBAC モデルでは、サブスクリプション レベルで所有者ロールを割り当てられます。ただし、新しい RBAC モデルは Azure ポータルと Azure リソース マネージャーの API のみでサポートされます。RBAC のロールが割り当てられたユーザーやサービスは、Azure 管理ポータルやサービス管理の API にアクセスできません。Azure ポータルでサブスクリプションの所有者ロールにユーザーを追加しても、そのユーザーは完全版の Azure ポータル内のサブスクリプションの共同管理者にはなりません。

まだ Azure ポータルで管理できない Azure リソースに対するアクセス権をユーザーに付与する場合は、Azure 管理ポータルを使ってサブスクリプションの共同管理者に追加する必要があります。現時点で RBAC を使って管理できないリソースの例には、Service Bus や Cloud Services があります。

## 管理用の承認とデータ操作

ロールベースのアクセス制御のサポートは、Azure ポータルと Azure リソース マネージャーの API での Azure リソースの管理操作専用です。RBAC による Azure リソースのデータ レベルの操作の中には、許可されていないものもあります。たとえば、ストレージ アカウントの作成/読み取り/更新/削除は RBAC によって制御できますが、ストレージ アカウント内の BLOB やテーブルの作成/読み取り/更新/削除は現時点では RBAC によって制御できません。同様に、SQL DB の作成/読み取り/更新/削除は RBAC によって制御できますが、DB 内の SQL テーブルの作成/読み取り/更新/削除は現時点では RBAC によって制御できません。

## アクセス権の追加と削除の方法

組織内のリソース所有者がアクセスを管理する方法の例を見てみましょう。このシナリオでは、Azure リソースを使って構築されているさまざまなテスト プロジェクトと実働プロジェクトで複数の人が作業しています。アクセス権の付与に関するベスト プラクティスに従おうと思っています。ユーザーはすべての必須リソースに対するアクセス権を持っている必要がありますが、追加のアクセス権がありません。プロセスやツールに対する投資をすべて再利用することにより、オンプレミスの Active Directory で確立したセキュリティ グループを使用します。以下のセクションでは、これらのリソースに対するアクセス権をセットアップする方法について説明します。

* [アクセス権の追加](#add-access)
* [アクセス権の削除](#remove-access)
* [外部ユーザーに関するアクセス権の追加または削除](#add-or-remove-access-for-external-user)

### アクセス権の追加

アクセス要件と Azure でのセットアップ方法について以下に要約します。

ユーザー/グループ | アクセス要件 | アクセスに関するロールとスコープ	
------------- | -------------  | ------------
Jill Santos のチームの全員 | すべての Azure リソースの読み取り | Jill Santos のチームを表す AD グループを Azure サブスクリプションに関する閲覧者ロールに追加
Jill Santos のチームの全員 | テスト リソース グループ内のすべてのリソースの作成と管理 | Jill Santos のチームを表す AD グループをテスト リソース グループに関する共同作業者ロールに追加
Brock | 実働リソース グループ内のすべてのリソースの作成と管理 | Brock を実働リソース グループに関する共同作業者ロールに追加


最初に、サブスクリプションのすべてのリソースに関する読み取りアクセス権を追加しましょう。**[参照] > [すべて] > [サブスクリプション]** をクリックします。

![][3]

*ご使用のサブスクリプションの名前* ** > [閲覧者] > [追加]** をクリックします。ユーザーとグループのリストから、Active Directory グループの名前を選択するか入力します。

![][4]

次に、同じチームをテスト リソース グループの共同作業者ロールに追加します。このリソース グループをクリックしてプロパティ ブレードを開きます。**[ロール]** の下で、**[共同作業者] > [追加]** をクリックし、チームの名前を入力します。

![][5]

Brock を実働リソース グループの共同作業者ロールに追加するには、このリソース グループをクリックし、**[共同作業者] > [追加]** をクリックして、Brock の名前を入力します。

![][6]

Windows PowerShell 用の Microsoft Azure モジュールを使ってロールの割り当てを管理することもできます。ポータルではなく New-AzureRoleAssignment コマンドレットを使って Brock のアカウントを追加する例を以下に示します。

	PS C:\> New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDB

Windows PowerShell を使ったアクセス権の追加や削除の詳細については、「[Windows PowerShell を使用したロールベースのアクセス制御の管理](role-based-access-control-powershell.md)」を参照してください。

### アクセス権の削除

割り当ての削除も簡単です。Brad Adams という名前のユーザーを TestDB という名前のリソース グループに関する閲覧者ロールから削除するとします。リソース グループ ブレードを開き、**[閲覧者] > [Brad Adams] > [削除]** をクリックします。

![][7]

Remove-AzureRoleAssignment コマンドレットを使って Brad Adams を削除する方法の例を以下に示します。

	PS C:\> Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDB

### 外部ユーザーに関するアクセス権の追加または削除

ディレクトリの **[構成]** タブには、外部ユーザーに関するアクセスを制御するオプションが含まれています。これらのオプションを変更できるのは、ディレクトリのグローバル管理者が完全版の Azure ポータルの UI においてだけです (Windows PowerShell や API による方法はありません)。Azure ポータルで **[構成]** タブを開くには、**[Active Directory]** をクリックし、次にディレクトリの名前をクリックします。

![][10]

次に、外部ユーザーに関するアクセスを制御するオプションを編集できます。

![][8]

既定では、ゲストはディレクトリのコンテンツを列挙できないので、**[メンバー一覧]** 内にユーザーやグループは表示されません。ユーザーの完全なメール アドレスを入力してユーザーを検索してから、アクセス権を付与できます。ゲストに関する既定の制限のセットは以下のとおりです。

- ディレクトリ内のユーザーやグループの列挙はできません。
- ユーザーのメール アドレスが分かっている場合は、そのユーザーの詳細情報の一部を表示できます。
- グループ名が分かっている場合は、そのグループの詳細情報の一部を表示できます。

ユーザーやグループの詳細情報の一部をゲストが表示できる機能を利用すると、他者を招待したり、連携者の詳細を表示したりできます。

外部ユーザーに関するアクセス権を追加するプロセスの手順を説明します。外部ユーザーを同じ TestDB リソース グループに関する閲覧者ロールに追加して、ユーザーがエラーをデバッグできるようにします。リソース グループ ブレードを開き、**[閲覧者] > [追加] > [招待]** をクリックして、追加するユーザーのメール アドレスを入力します。

![][9]

外部ユーザーを追加する際には、ディレクトリ内にゲストが作成されます。その後、このゲストをグループに追加したりグループから削除したりするか、または他のディレクトリ ユーザーと同様に個別にロールから追加したり削除したりできます。

ユーザーを削除する場合と同様に、ゲストをロールから削除することもできます。ゲストをリソース上のロールから削除しても、そのゲストはディレクトリから削除されません。
 
## ロールベースのアクセス制御を使用する際の既知の問題

ロールベースのアクセス制御機能の使用時に問題が発生した場合は、その問題に関連している可能性がある既知の問題について、[ロールベースのアクセス制御のトラブルシューティング](role-based-access-control-troubleshooting.md)に関するページを参照してください。


## 組み込みのロール

Azure のロールベースのアクセス制御では、ユーザー、グループ、サービスに割り当てられる次の組み込みのロールが提供されています。組み込みのロールの定義は変更できません。Azure RBAC の今後のリリースでは、Azure リソースに対して実行できるアクションのリストからアクションのセットを構成して、カスタムのロールを定義できるようになります。

対応するリンクをクリックし、ロール定義の **actions** と **not actions** プロパティを確認します。**actions** プロパティは、Azure リソースに対して許可するアクションを指定します。アクションの文字列にワイルドカード文字を使用できます。ロール定義の **not actions** プロパティは、許可されているアクションから除外しなければならないアクションを指定します。


ロール名 | 説明  	
------------- | -------------  
[API 管理サービスの共同作業者](#api-management-service-contributor) | アクセス権以外の API Management サービスを管理します。
[Application Insights コンポーネントの共同作業者](#application-insights-component-contributor) | アクセス権以外の Application Insights コンポーネントを管理します。
[BizTalk の共同作業者](#biztalk-contributor) | アクセス権以外の BizTalk Services を管理します。
[ClearDB MySQL DB の共同作業者](#cleardb-mysql-db-contributor) | アクセス権以外の ClearDB MySQL データベースを管理します。
[共同作成者](#contributor) | 共同作業者は、アクセス権以外のすべてを管理できます。
[Data Factory の共同作業者](#data-factory-contributor) | アクセス権以外の Data Factory を管理します。
[Document DB アカウントの共同作業者](#document-db-account-contributor) | アクセス権以外の DocumentDB アカウントを管理します。
[Intelligent Systems アカウントの共同作業者](#intelligent-systems-account-contributor) | アクセス権以外の Intelligent Systems アカウントを管理します。
[NewRelic APM アカウントの共同作業者](#newrelic-apm-account-contributor) | アクセス権以外の New Relic Application Performance Management アカウントとアプリケーションを管理します。
[所有者](#owner) | 所有者は、アクセス権を含めすべてを管理できます。
[閲覧者](#reader) | 閲覧者はすべてを閲覧できますが、変更を加えることはできません。
[Redis Cache の共同作業者](#redis-cache-contributor) | アクセス権以外の Redis Cache を管理します。
[SQL DB の共同作業者](#sql-db-contributor) | アクセス権以外の SQL データベースを管理します。また、セキュリティ関連ポリシーや親の SQL Server も管理できません。
[SQL セキュリティ管理者](#sql-security-manager) | アクセス権以外の SQL Server やデータベースのセキュリティ関連ポリシーを管理します。
[SQL Server の共同作業者](#sql-server-contributor) | アクセス権以外、またセキュリティ関連ポリシー以外の SQL Server やデータベースを管理します。
[Scheduler Job Collection の共同作業者](#scheduler-job-collections-contributor) | アクセス権以外の Scheduler Job Collection を管理します。
[Search サービスの共同作業者](#search-service-contributor) | アクセス権以外の Search サービスを管理します。
[ストレージ アカウントの共同作業者](#storage-account-contributor) | アクセス権以外のストレージ アカウントを管理します。
[ユーザーアクセスの管理者](#user-access-administrator) | Azure リソースに対するユーザー アクセスを管理します。
[仮想マシンの共同作業者](#virtual-machine-contributor) | アクセス権以外、接続している仮想ネットワークやストレージ アカウント以外の仮想マシンを管理します。
[仮想ネットワークの共同作業者](#virtual-network-contributor) | アクセス権以外の仮想ネットワークを管理します。
[Web プランの共同作業者](#web-plan-contributor) | アクセス権以外の Web サイトの Web プランを管理します。
[Web サイトの共同作業者](#website-contributor) | アクセス権以外、Web プラン以外の Web サイトを管理します。


### API 管理サービスの共同作業者

<table style=width:100%">
<tr>
<th colspan="2">アクション</th>
</tr>
<tr>
<td>Microsoft.ApiManagement/Services/*</td>
<td>API 管理サービスの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>ロールとロール割り当ての読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループの読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>リソース グループ デプロイの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>アラート ルールの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>サポート チケットの作成と管理</td>
</tr>
</table>

### Application Insights コンポーネントの共同作業者

<table style=width:100%">
<tr>
<th colspan="2">アクション</th>
</tr>
<tr>
<td>Microsoft.Insights/components/*</td>
<td>Insights コンポーネントの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Insights/webtests/*</td>
<td>Web テストの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>ロールとロール割り当ての読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループの読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>リソース グループ デプロイの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>アラート ルールの作成と管理</td>
</tr>
<td>Microsoft.Support/*</td>
<td>サポート チケットの作成と管理</td>
</tr>
</table>

### BizTalk の共同作業者

<table style=width:100%">
<tr>
<th colspan="2">アクション</th>
</tr>
<tr>
<td>Microsoft.BizTalkServices/BizTalk/*</td>
<td>BizTalk Services の作成と管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>ロールとロール割り当ての読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループの読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>リソース グループ デプロイの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>アラート ルールの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>サポート チケットの作成と管理</td>
</tr>
</table>

### ClearDB MySQL DB の共同作業者

<table style=width:100%">
<tr>
<th colspan="2">アクション</th>
</tr>
<tr>
<td>successbricks.cleardb/databases/*</td>
<td>ClearDB MySQL Databases の作成と管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>ロールとロール割り当ての読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループの読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>リソース グループ デプロイの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>アラート ルールの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>サポート チケットの作成と管理</td>
</tr>
</table>

### 共同作成者

<table style=width:100%">
<tr>
<th colspan="2">アクション</th>
</tr>
<tr>
<td>*</td>
<td>あらゆるタイプのリソースの作成と管理</td>
</tr>
<tr>
<th colspan="2">not actions</th>
</tr>
<tr>
<td>Microsoft.Authorization/*/Write</td>
<td>ロールとロール割り当ては作成できません </td>
</tr>
<tr>
<td>Microsoft.Authorization/*/Delete</td>
<td>ロールとロール割り当ては削除できません</td>
</tr>
</table>

### Data Factory の共同作業者

<table style=width:100%">
<tr>
<td>Microsoft.DataFactory/dataFactories/*</td>
<td>Data Factory の作成と管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>ロールとロール割り当ての読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループの読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>リソース グループ デプロイの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>アラート ルールの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>サポート チケットの作成と管理</td>
</tr>
</table>

### Document DB アカウントの共同作業者

<table style=width:100%">
<tr>
<th colspan="2">アクション</th>
</tr>
<tr>
<td>Microsoft.DocumentDb/databaseAccounts/*</td>
<td>DocumentDB アカウントの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>ロールとロール割り当ての読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループの読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>リソース グループ デプロイの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>アラート ルールの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>サポート チケットの作成と管理</td>
</tr>
</table>

### Intelligent Systems アカウントの共同作業者

<table style=width:100%">
<tr>
<th colspan="2">アクション</th>
</tr>
<tr>
<td>Microsoft.IntelligentSystems/accounts/*</td>
<td>Intelligent Systems アカウントの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>ロールとロール割り当ての読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループの読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>リソース グループ デプロイの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>アラート ルールの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>サポート チケットの作成と管理</td>
</tr>
</table>

### NewRelic APM アカウントの共同作業者

<table style=width:100%">
<tr>
<th colspan="2">アクション</th>
</tr>
<tr>
<td>NewRelic.APM/accounts/*</td>
<td>NewRelic Application Performance Management アカウントの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>ロールとロール割り当ての読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループの読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>リソース グループ デプロイの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>アラート ルールの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>サポート チケットの作成と管理</td>
</tr>
</table>

### 所有者

<table style=width:100%">
<tr>
<th colspan="2">アクション</th>
</tr>
<tr>
<td>*</td>
<td>あらゆるタイプのリソースの作成と管理</td>
</tr>
</table>

### 閲覧者

<table style=width:100%">
<tr>
<th colspan="2">アクション</th>
</tr>
<tr>
<td>*/read</td>
<td>あらゆるタイプのリソースを読み込みます。シークレット情報は読み込めません。</td>
</tr>
</table>

### Redis Cache の共同作業者

<table style=width:100%">
<tr>
<td>Microsoft.Cache/redis/*</td>
<td>Redis Caches の作成と管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>ロールとロール割り当ての読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループの読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>リソース グループ デプロイの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>アラート ルールの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>サポート チケットの作成と管理</td>
</tr>
</table>

### SQL DB の共同作業者

<table style=width:100%">
<tr>
<th colspan="2">アクション</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/read</td>
<td>SQL Server の読み込み</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/*</td>
<td>SQL の作成と管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>ロールとロール割り当ての読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resources/read</td>
<td>サブスクリプションのリソースの読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループの読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/resources/read</td>
<td>リソース グループのリソースの読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>リソース グループ デプロイの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>アラート ルールの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>サポート チケットの作成と管理</td>
</tr>
<tr>
<th colspan="2">not actions</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>SQL Database の監査ポリシーは管理できません</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>SQL Database の接続ポリシーは管理できません</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>SQL Database のデータ マスキング ポリシーは管理できません</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>SQL Database のセキュリティ基準は管理できません</td>
</tr>
</table>

### SQL セキュリティ管理者

<table style=width:100%">
<tr>
<th colspan="2">アクション</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/read</td>
<td>SQL Server の読み込み</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>SQL Server 監査ポリシーの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/read</td>
<td>SQL Database の読み込み</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>SQL Database 監査ポリシーの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>SQL Database 接続ポリシーの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>SQL Database データ マスキング ポリシーの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>SQL Database セキュリティ基準の作成と管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>ロールとロール割り当ての読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループの読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>リソース グループ デプロイの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>アラート ルールの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>サポート チケットの作成と管理</td>
</tr>
</table>

### SQL Server の共同作業者

<table style=width:100%">
<tr>
<th colspan="2">アクション</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/*</td>
<td>SQL Server の作成と管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>ロールとロール割り当ての読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resources/read</td>
<td>サブスクリプションのリソースの読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループの読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/resources/read</td>
<td>リソース グループのリソースの読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>リソース グループ デプロイの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>アラート ルールの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>サポート チケットの作成と管理</td>
</tr>
<tr>
<th colspan="2">not actions</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>SQL Server 監査ポリシーは管理できません</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>SQL Database の監査ポリシーは管理できません</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>SQL Database の接続ポリシーは管理できません</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>SQL Database のデータ マスキング ポリシーは管理できません</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>SQL Database のセキュリティ基準は管理できません</td>
</tr>
</table>

### Scheduler Job Collection の共同作業者

<table style=width:100%">
<tr>
<th colspan="2">アクション</th>
</tr>
<tr>
<td>Microsoft.Scheduler/jobcollections/*</td>
<td>Scheduler Job Collection の作成と管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>ロールとロール割り当ての読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループの読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>リソース グループ デプロイの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>アラート ルールの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>サポート チケットの作成と管理</td>
</tr>
</table>

### Search サービスの共同作業者

<table style=width:100%">
<tr>
<th colspan="2">アクション</th>
</tr>
<tr>
<td>Microsoft.Search/searchServices/*</td>
<td>Search サービスの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>ロールとロール割り当ての読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループの読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>リソース グループ デプロイの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>アラート ルールの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>サポート チケットの作成と管理</td>
</tr>
</table>

### ストレージ アカウントの共同作業者

<table style=width:100%">
<tr>
<th colspan="2">アクション</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/*</td>
<td>ストレージ アカウントの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>ロールとロール割り当ての読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループの読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>リソース グループ デプロイの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>アラート ルールの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>サポート チケットの作成と管理</td>
</tr>
</table>

### ユーザーアクセスの管理者

<table style=width:100%">
<tr>
<th colspan="2">アクション</th>
</tr>
<tr>
<td>*/read</td>
<td>あらゆるタイプのリソースの読み込み</td>
</tr>
<tr>
<td>Microsoft.Authorization/*</td>
<td>ロールとロール割り当ての作成と管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>サポート チケットの作成と管理</td>
</tr>
</table>

### 仮想マシンの共同作業者

<table style=width:100%">
<tr>
<th colspan="2">アクション</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/read</td>
<td>ストレージ アカウントの読み取り</td>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/listKeys/action</td>
<td>ストレージ アカウント キーの取得</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/read</td>
<td>仮想ネットワークの読み取り</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/join/action</td>
<td>仮想ネットワークへの参加</td>
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
<td>クラウド サービスの作成と管理</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/virtualMachines/*</td>
<td>仮想マシンの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>ロールとロール割り当ての読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループの読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>リソース グループ デプロイの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>アラート ルールの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>サポート チケットの作成と管理</td>
</tr>
</table>

### 仮想ネットワークの共同作業者

<table style=width:100%">
<tr>
<th colspan="2">アクション</th>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/*</td>
<td>仮想ネットワークの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>ロールとロール割り当ての読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループの読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>リソース グループ デプロイの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>アラート ルールの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>サポート チケットの作成と管理</td>
</tr>
</table>

### Web Plan の共同作業者

<table style=width:100%">
<tr>
<th colspan="2">アクション</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/*</td>
<td>Web Plan の作成と管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>ロールとロール割り当ての読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループの読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>リソース グループ デプロイの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>アラート ルールの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>サポート チケットの作成と管理</td>
</tr>
</table>

### Web サイトの共同作業者

<table style=width:100%">
<tr>
<th colspan="2">アクション</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/read</td>
<td>Web Plan の読み取り</td>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/join/action</td>
<td>Web プランへの参加</td>
</tr>
<tr>
<td>Microsoft.Web/sites/*</td>
<td>Web サイトの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Web/certificates/*</td>
<td>Web サイト証明書の作成と管理</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>ロールとロール割り当ての読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>リソース グループの読み取り</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>リソース グループ デプロイの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>アラート ルールの作成と管理</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>サポート チケットの作成と管理</td>
</tr>
</table>


## フィードバックを提供する方法

Azure RBAC を試用して、[フィードバック](http://aka.ms/azurerbacfeedback)をお送りください。


## 次のステップ

ロールベースのアクセス制御の使用法に関するその他のリソースを以下に示します。

+ [Windows PowerShell を使用したロールベースのアクセス制御の管理](role-based-access-control-powershell.md)
+ [Azure CLI を使用したロールベースのアクセス制御の管理](role-based-access-control-xplat-cli.md)
+ [ロールベースのアクセス制御のトラブルシューティング](role-based-access-control-troubleshooting.md)
+ [Azure Active Directory Premium および Basic](active-directory-editions.md)
+ [Azure サブスクリプションを Azure AD に関連付ける方法](active-directory-how-subscriptions-associated-directory.md)
+ セキュリティ グループに関するセルフサービス グループ管理の概要については、「[Active Directory チームのブログ](http://blogs.technet.com/b/ad/archive/2014/02/24/more-preview-enhancements-for-windows-azure-ad-premium.aspx)」を参照してください。

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

<!---HONumber=62-->