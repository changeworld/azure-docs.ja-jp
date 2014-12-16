<properties title="Role Based Access Control in Azure Preview Portal" pageTitle="Azure プレビュー ポータルでの役割ベースのアクセス制御" description="Describes how role based access control works and how to set it up" metaKeywords="" services="multiple" solutions="" documentationCenter="" authors="justinha" videoId="" scriptId="" manager="terrylan" />

<tags ms.service="multiple" ms.devlang="dotnet" ms.topic="article" ms.tgt_pltfrm="Ibiza" ms.workload="infrastructure-services" ms.date="09/12/2014" ms.author="justinha;Justinha@microsoft.com" />

<!--This is a basic template that shows you how to use mark down to create a topic that includes a TOC, sections with subheadings, links to other azure.microsoft.com topics, links to other sites, bold text, italic text, numbered and bulleted lists, code snippets, and images. For fancier markdown, find a published topic and copy the markdown or HTML you want. For more details about using markdown, see http://sharepoint/sites/azurecontentguidance/wiki/Pages/Content%20Guidance%20Wiki%20Home.aspx.-->

<!--Properties section (above): this is required in all topics. Please fill it out!-->

<!--The next line, with one pound sign at the beginning, is the page title--> 
# Azure プレビュー ポータルでの役割ベースのアクセス制御 

<p>Azure プレビュー ポータルでの役割ベースのアクセス制御 (RBAC) のサポートが追加されたので、組織がアクセス管理の要件を簡単かつ正確に満たせるようになりました。<a href="http://go.microsoft.com/fwlink/?LinkId=511576" target="_blank">ブログの記事</a>に、機能が簡単に紹介されているので、すぐに使い始めることができます。このトピックでは、概念について詳しく説明し、追加の使用事例も記載しています。 

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

##目次##

* [Azure での RBAC](#whatisrbac)
* [RBAC とサブスクリプションの共同管理者の共存](#coexist)
* [管理用の承認とデータ操作](#authmgmt)
* [アクセス権の追加と削除の方法](#addremoveaccess)
* [役割ベースのアクセス制御を使用する際の既知の問題](#knownissues)
* [フィードバックを提供する方法](#feedback)
* [次のステップ](#next)

<h2><a id="whatisrbac"></a>Azure での RBAC</h2>
                                                                   
各 Azure サブスクリプションは Azure Active Directory と関連付けられます。Azure の管理ポータルか Azure リソース マネージャーの API を使ってサブスクリプションのリソースにアクセスするユーザーやサービスは、最初に Azure Active Directory での認証を実行する必要があります。

![][1] 

Azure の役割ベースのアクセス制御を利用すると、サブスクリプション、リソース グループ、または個々のリソースのレベルで役割を割り当てて、該当するアクセス権を Azure AD のユーザー、グループ、サービスに付与できます。役割を割り当てると、Azure リソースに対するユーザー、グループ、サービスのアクセス権のレベルが定義されます。 

### 役割

役割とは、Azure リソースに対して実行できるアクションのコレクションのことです。アクションが含まれている役割がユーザーやサービスに割り当てられると、Azure リソースに対するそのアクションの実行が許可されます。

#### 組み込みの役割

最初のプレビュー版では、Azure の役割ベースのアクセス制御には、ユーザー、グループ、サービスに割り当てることができる 3 つの組み込みの役割が付属しています。

+ **所有者**:Azure リソースをきめ細かく制御できます。所有者は、アクセス管理など、リソースに対するすべての管理操作を実行できます。 
+ **共同作成者**:アクセス管理以外のすべての管理操作を実行できます。つまり、共同作成者は他者にアクセス権を付与できません。
+ **閲覧者**:リソースの表示のみ可能です。閲覧者はリソースに関連付けられた機密情報を表示できません。 

組み込みの役割の定義は変更できません。Azure RBAC 今後のリリースでは、Azure リソースに対して実行できるアクションのリストからアクションのセットを構成して、カスタム役割を定義できるようになります。

#### actions と not actions

役割定義の **actions** プロパティは、Azure リソースに対して許可するアクションを指定します。アクションの文字列にワイルドカード文字を使用できます。役割定義の **not actions** プロパティは、許可されているアクションから除外しなければならないアクションを指定します。 

**組み込みの役割**  | Actions | Not Actions	
------------- | -------------  | ------------
所有者 (すべてのアクションが許可される)  | *  | 
共同作成者 (役割の割り当ての作成と削除を除くすべてのアクションが許可される)  | *  | Microsoft.Authorization/ * /Write, Microsoft.Authorization/ * /Delete
閲覧者 (すべての読み取りアクションが許可される)  | */Read  | 

### 役割の割り当て

Azure AD のユーザーやサービスにアクセス権を付与するには、Azure リソースに対する該当する役割を割り当てます。 

#### Azure AD のセキュリティ プリンシパル

以下の種類の Azure AD セキュリティ プリンシパルに役割を割り当てることができます。

+ **ユーザー**:Azure サブスクリプションが関連付けられている Azure AD の組織内ユーザーに役割を割り当てることができます。Azure プレビュー ポータルで招待アクションを使ってユーザーに役割を割り当てることにより、外部 Microsoft アカウント ユーザー (joe@outlook.com など) に役割を割り当てることもできます。役割を外部 Microsoft アカウント ユーザーに割り当てると、そのユーザーに関するゲスト アカウントが Azure AD 内に作成されます。ディレクトリ内でこのゲスト アカウントを無効にすると、外部ユーザーはアクセス権を付与されている Azure リソースへのアクセスが許可されなくなります。
+ **グループ**:Azure AD セキュリティ グループに役割を割り当てることができます。アクセス権を付与されたグループのメンバーになったユーザーには、リソースに対するアクセス権を自動的に付与されます。同様にユーザーがグループから削除されると、リソースに対するアクセス権が自動的に失われます。役割を直接ユーザーに割り当てるより、役割をグループに割り当て、そのグループにユーザーを追加して、グループ単位でアクセスを管理する方がベスト プラクティスです。Azure RBAC は配布リストへの役割の割り当てを許可しません。
	役割をグループに割り当てる機能により、組織は既存のアクセス制御モデルをオンプレミスのディレクトリからクラウドへと拡張できるので、オンプレミスのアクセスを制御するために既に確立しているセキュリティ グループを再利用して Azure プレビュー ポータル内のリソースに対するアクセスを制御できます。オンプレミスのディレクトリからユーザーやグループを同期化するさまざまなオプションの詳細については、「[ディレクトリ統合の概要](http://technet.microsoft.com/library/jj573653.aspx)」を参照してください。Azure AD プレミアムは、[委任されたグループ管理機能](http://msdn.microsoft.com/library/azure/dn641267.aspx)も提供しており、グループを作成して管理する機能を Azure AD から管理者以外のユーザーに委任できます。
+ **サービス プリンシパル**:サービス ID はディレクトリ内でサービス プリンシパルとして表されます。サービス プリンシパルは Azure AD での認証を実施し、相互に安全に通信できます。Azure リソースに対するアクセス権をサービスに付与するには、Windows PowerShell 用の Azure モジュールで、そのサービスを表す Azure AD サービス プリンシパルに役割を割り当てます。 

#### リソースのスコープ

サブスクリプション全体に対するアクセス権を付与する必要はありません。個々のリソースに加えて、リソース グループに関する役割を割り当てることもできます。Azure RBAC では、リソースは役割の割り当てを親のリソースから継承します。したがって、ユーザー、グループ、サービスがサブスクリプション内の 1 つのリソース グループのみに対するアクセス権を付与されている場合、そのリソース グループと含まれているリソースのみにアクセスでき、サブスクリプション内の他のリソース グループにはアクセスできません。別の例として、リソース グループについてはセキュリティ グループを閲覧者役割に追加しますが、そのリソース グループ内のデータベースについては共同作成者役割に追加することができます。

![][2]

<h2><a id="coexist"></a>RBAC とサブスクリプションの共同管理者の共存</h2>

サブスクリプションの管理者と共同管理者には、引き続き Azure ポータルと管理 API に対するフル アクセス権が付与されます。RBAC モデルでは、サブスクリプション レベルで所有者役割を割り当てられます。  
ただし、新しい RBAC モデルは Azure プレビュー ポータルと Azure リソース マネージャーの API のみでサポートされます。RBAC 役割を割り当てられたユーザーやサービスは、Azure 管理ポータルやサービス管理の API にアクセスできません。Azure プレビュー ポータルでサブスクリプションの所有者役割にユーザーを追加しても、そのユーザーは完全版の Azure ポータル内のサブスクリプションの共同管理者にはなりません。

まだ Azure プレビュー ポータルで管理できない Azure リソースに対するアクセス権をユーザーに付与する場合は、完全版の Azure の管理ポータルを使ってサブスクリプションの共同管理者に追加する必要があります。現時点で RBAC を使って管理できないリソースの例には、Service Bus や Cloud Services があります。

<h2><a id="authmgmt"></a>管理用の承認とデータ操作</h2>

役割ベースのアクセス制御のサポートは、Azure プレビュー ポータルと Azure リソース マネージャーの API での Azure リソースの管理操作専用です。RBAC による Azure リソースのデータ レベルの操作の中には、許可されていないものもあります。たとえば、ストレージ アカウントの作成/読み取り/更新/削除は RBAC によって制御できますが、ストレージ アカウント内の BLOB やテーブルの作成/読み取り/更新/削除は現時点では RBAC によって制御できません。同様に、SQL DB の作成/読み取り/更新/削除は RBAC によって制御できますが、DB 内の SQL テーブルの作成/読み取り/更新/削除は現時点では RBAC によって制御できません。

<h2><a id="addremoveaccess"></a>アクセス権の追加と削除の方法</h2>

組織内のリソース所有者がアクセスを管理する方法の例を見てみましょう。このシナリオでは、Azure リソースを使って構築されているさまざまなテスト プロジェクトと実働プロジェクトで複数の人が作業しています。アクセス権の付与に関するベスト プラクティスに従おうと思っています。ユーザーはすべての必須リソースに対するアクセス権を持っている必要がありますが、追加のアクセス権がありません。プロセスやツールに対する投資をすべて再利用することにより、オンプレミスの Active Directory で確立したセキュリティ グループを使用します。以下のセクションでは、これらのリソースに対するアクセス権をセットアップする方法について説明します。

* [アクセス権の追加](#add)
* [アクセス権の削除](#remove)
* [外部ユーザーに関するアクセス権の追加または削除](#addremoveext)

<h3><a id="add"></a>アクセス権の追加</h2>

アクセス要件と Azure でのセットアップ方法について以下に要約します。

ユーザー/グループ  | アクセス要件  | アクセスに関する役割とスコープ	
------------- | -------------  | ------------
Jill Santos のチームの全員  | すべての Azure リソースの読み取り  | Jill Santos のチームを表す AD グループを Azure サブスクリプションに関する閲覧者役割に追加
Jill Santos のチームの全員  | テスト リソース グループ内のすべてのリソースの作成と管理  | Jill Santos のチームを表す AD グループをテスト リソース グループに関する共同作業者役割に追加
Brock  | 実働リソース グループ内のすべてのリソースの作成と管理  | Brock を実働リソース グループに関する共同作業者役割に追加


最初に、サブスクリプションのすべてのリソースに関する読み取りアクセス権を追加しましょう。**[参照]、[すべて]、[サブスクリプション]** の順にクリックします。

![][3] 

*ご使用のサブスクリプションの名前***、[閲覧者]、[追加]** の順にクリックします。ユーザーとグループのリストから、Active Directory グループの名前を選択するか入力します。

![][4]

次に、同じチームをテスト リソース グループの共同作業者役割に追加します。このリソース グループをクリックしてプロパティ ブレードを開きます。**[役割]** の下で、**[共同作業者]、[追加]** の順にクリックし、チームの名前を入力します。

![][5]

Brock を実働リソース グループの共同作業者役割に追加するには、このリソース グループをクリックし、**[共同作業者]、[追加]** の順にクリックして、Brock の名前を入力します。 

![][6]

Windows PowerShell 用の Microsoft Azure モジュールを使って役割の割り当てを管理することもできます。ポータルではなく New-AzureRoleAssignment コマンドレットを使って Brock のアカウントを追加する例を以下に示します。

	PS C:\> New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDB

Windows PowerShell を使ったアクセス権の追加や削除の詳細については、「[Windows PowerShell を使用した役割ベースのアクセス制御の管理](http://azure.microsoft.com/ja-jp/documentation/articles/role-based-access-control-powershell/)」を参照してください。 

<h3><a id="remove"></a>アクセス権の削除</h2>

割り当ての削除も簡単です。Brad Adams という名前のユーザーを TestDB という名前のリソース グループに関する閲覧者役割から削除するとします。リソース グループ ブレードを開き、**[閲覧者]、[Brad Adams]、[削除]** の順にクリックします。

![][7]

Remove-AzureRoleAssignment コマンドレットを使って Brad Adams を削除する方法の例を以下に示します。

	PS C:\> Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDB

<h3><a id="addremoveext"></a>外部ユーザーに関するアクセス権の追加または削除</h2>

ディレクトリの **[構成]** タブには、外部ユーザーに関するアクセスを制御するオプションが含まれています。これらのオプションを変更できるのは、ディレクトリのグローバル管理者が完全版の Azure ポータルの UI においてだけです (Windows PowerShell や API による方法はありません)。 
完全版の Azure ポータル内で **[構成]** タブを開くには、**[Active Directory]** をクリックしてから、ディレクトリの名前をクリックします。

![][10]

次に、外部ユーザーに関するアクセスを制御するオプションを編集できます。 

![][8]

既定では、ゲストはディレクトリのコンテンツを列挙できないので、**[メンバー一覧]** 内にユーザーやグループは表示されません。ユーザーの完全なメール アドレスを入力してユーザーを検索してから、アクセス権を付与できます。ゲストに関する既定の制限のセットは以下のとおりです。

- ディレクトリ内のユーザーやグループの列挙はできません。
- ユーザーのメール アドレスが分かっている場合は、そのユーザーの詳細情報の一部を表示できます。
- グループ名が分かっている場合は、そのグループの詳細情報の一部を表示できます。

ユーザーやグループの詳細情報の一部をゲストが表示できる機能を利用すると、他者を招待したり、連携者の詳細を表示したりできます。  

外部ユーザーに関するアクセス権を追加するプロセスの手順を説明します。外部ユーザーを同じ TestDB リソース グループに関する閲覧者役割に追加して、ユーザーがエラーをデバッグできるようにします。リソース グループ ブレードを開き、**[閲覧者]、[追加]、[招待]** の順にクリックして、追加するユーザーのメール アドレスを入力します。

![][9]

外部ユーザーを追加する際には、ディレクトリ内にゲストが作成されます。その後、このゲストをグループに追加したりグループから削除したりするか、または他のディレクトリ ユーザーと同様に個別に役割から追加したり削除したりできます。 

ユーザーを削除する場合と同様に、ゲストを役割から削除することもできます。ゲストをリソース上の役割から削除しても、そのゲストはディレクトリから削除されません。 
 
<h2><a id="knownissues"></a>役割ベースのアクセス制御を使用する際の既知の問題</h2>

プレビュー版で役割ベースのアクセス制御機能の使用時に問題が発生した場合は、その問題に関連している可能性がある既知の問題について「[役割ベースのアクセス制御のトラブルシューティング](http://azure.microsoft.com/ja-jp/documentation/articles/role-based-access-control-troubleshooting/)」を参照してください。


<h2><a id="feedback"></a>フィードバックを提供する方法</h2>

Azure RBAC を試用して、[feedback](http://aka.ms/azurerbacfeedback). をお送りください。


<h2><a id="next"></a>次のステップ</h2>

役割ベースのアクセス制御の使用法に関するその他のリソースを以下に示します。 

+ [Windows PowerShell を使用した役割ベースのアクセス制御の管理](http://azure.microsoft.com/ja-jp/documentation/articles/role-based-access-control-powershell/)
+ [XPLAT CLI を使用した役割ベースのアクセス制御の管理](http://azure.microsoft.com/ja-jp/documentation/articles/role-based-access-control-xplat-cli/)
+ [役割ベースのアクセス制御のトラブルシューティング](http://azure.microsoft.com/ja-jp/documentation/articles/role-based-access-control-troubleshooting/)
+ [Azure Active Directory](http://msdn.microsoft.com/library/azure/jj673460.aspx)
+ [Azure Active Directory Premium および Basic](http://msdn.microsoft.com/ja-jp/library/azure/dn532272.aspx)
+ [Azure サブスクリプションを Azure AD に関連付ける方法](http://msdn.microsoft.com/ja-jp/library/azure/dn629581.aspx)
+ セキュリティ グループに関するセルフサービス グループ管理の概要については、「[Active Directory Team Blog (Active Directory チームのブログ)](http://blogs.technet.com/b/ad/archive/2014/02/24/more-preview-enhancements-for-windows-azure-ad-premium.aspx)」を参照してください。



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


