<properties
   pageTitle="Azure Active Directory 認証を使用して SQL Database に接続する | Microsoft Azure"
   description="Azure Active Directory 認証を使用して SQL Database に接続する方法について説明します。"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/14/2015"
   ms.author="rick.byham@microsoft.com"/>

# Azure Active Directory 認証を使用して SQL Database に接続する 

Azure Active Directory 認証は、Azure Active Directory (Azure AD) の ID を使用して Microsoft Azure SQL Database に接続するメカニズムです。Azure Active Directory 認証を使用すると、データベース ユーザーの ID や他の Microsoft サービスを一元管理できます。ID の一元管理では、1 か所で SQL Database ユーザーを管理できるようになるため、アクセス許可の管理が容易になります。次のような利点があります。

- SQL Server 認証の代替方法が用意されています。
- データベース サーバー全体でユーザー ID が急増するのを防ぎます。
- 1 か所でのパスワードのローテーションを許可します。
- 顧客は外部の (AAD) グループを使用してデータベースのアクセス許可を管理できます。
- 統合 Windows 認証や、Azure Active Directory でサポートされる他の認証形式を有効にすることで、パスワードが保存されないようにすることができます。
- Azure Active Directory 認証では、包含データベース ユーザーを使用して、データベース レベルで ID を認証します。

> [AZURE.IMPORTANT]Azure Active Directory 認証はプレビュー機能で、ライセンス契約 (例: Enterprise Agreement、Microsoft Azure 契約、マイクロソフト オンライン サブスクリプション契約) に定められたプレビューの規定に従うものとします。また、[Microsoft Azure プレビューの追加使用条件](http://azure.microsoft.com/support/legal/preview-supplemental-terms/)が適用される場合があります。

構成の手順には、Azure Active Directory 認証を構成して使用する次の手順が含まれます。

1. Azure Active Directory を作成して設定する
2. データベースが Azure SQL Database V12 にあることを確認する
3. 省略可能: Active Directory を関連付けるか、現在 Azure サブスクリプションに関連付けられている Active Directory を変更する
4. Azure SQL Server 用に Azure Active Directory 管理者を作成する
5. クライアント コンピューターを構成する
6. Azure AD の ID にマップされている包含データベース ユーザーをデータベースに作成する
7. Azure AD の ID を使用してデータベースに接続する


## 信頼のアーキテクチャ
 
次の図では、Azure SQL Database で Azure AD 認証を使用する場合のソリューション アーキテクチャの概要を示しています。矢印は通信経路を示します。

![aad auth diagram][1]

次の図では、フェデレーション、信頼、ホスティングの関係を示します。これらの関係により、クライアントは、Azure AD によって認証された、データベースが信頼するトークンを送信することで、データベースに接続できます。重要なのは、Azure AD 認証を使用してデータベースにアクセスするには、ホストしているサブスクリプションを Azure Active Directory に関連付ける必要があることを理解することです。

![subscription relationship][2]

## 管理者の構造
 
Azure AD 認証を使用すると、SQL Database サーバーの管理者アカウントは、元の SQL Server 管理者と Azure AD 管理者の 2 つになります。ユーザー データベースに最初の Azure AD 包含データベース ユーザーを作成できるのは、Azure AD アカウントに基づく管理者のみです。Azure AD の管理者ログインには、Azure AD ユーザーまたは Azure AD グループを使用できます。管理者がグループ アカウントの場合は、SQL Server インスタンスに複数の Azure AD 管理者を有効にすることで、すべてのグループ メンバーがそのアカウントを使用できます。グループ アカウントを管理者として使用すると、SQL Database でユーザーまたはアクセス許可を変更することなく Azure AD でグループ メンバーを一元的に追加および削除できるため、より管理しやすくなります。いつでも構成できる Azure AD 管理者 (ユーザーまたはグループ) は 1 つだけです。

![admin structure][3]

## アクセス許可
 
新しいユーザーを作成するには、データベースにおける **ALTER ANY USER** アクセス許可が必要です。**ALTER ANY USER** アクセス許可は、任意のデータベース ユーザーに付与できます。また、**ALTER ANY USER** アクセス許可は、サーバーの管理者アカウント、そのデータベースの **CONTROL ON DATABASE** または **ALTER ON DATABASE** アクセス許可を持つデータベース ユーザー、**db\_owner** データベース ロールのメンバーも保持しています。

Azure SQL Database に包含データベース ユーザーを作成するには、Azure AD の ID を使用してデータベースに接続する必要があります。最初の包含データベース ユーザーを作成するには、(データベースの所有者である) Azure AD 管理者を使用してデータベースに接続する必要があります。これについては、以下の手順 4. ～ 5. で説明します。

## Azure AD の機能と制限事項 

Azure Active Directory の次のメンバーは、Azure SQL Server でプロビジョニングできます。- ネイティブ メンバー: 管理対象ドメインまたは顧客のドメイン内の Azure AD に作成されたメンバー。詳細については、「[Azure AD への独自のドメイン名の追加](active-directory-add-domain.md)」を参照してください。- フェデレーション ドメインのメンバー: フェデレーション ドメインで Azure AD に作成されたメンバー。詳細については、[Windows Azure による Windows Server Active Directory とのフェデレーションのサポート](http://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)に関するブログを参照してください。- ネイティブ メンバーまたはフェデレーション ドメインのメンバーである他の Azure Active Directory からインポートされたメンバー。- セキュリティ グループとして作成された Active Directory グループ。

Microsoft アカウント (outlook.com、hotmail.com、live.com など) またはその他のゲスト アカウント (gmail.com、yahoo.com など) はサポートされていません。アカウントとパスワードを使用して [https://login.live.com](https://login.live.com) にログインできる場合は、Azure SQL Database の Azure AD 認証でサポートされていない Microsoft アカウントを使用しています。

### 追加の考慮事項 

- さらに管理しやすくするには、管理者として専用の Azure Active Directory グループをプロビジョニングすることをお勧めします。
- Azure SQL Server 用にいつでも構成できる Azure AD 管理者 (ユーザーまたはグループ) は 1 つだけです。
- Azure Active Directory アカウントを使用して最初に Azure SQL Server に接続できるのは、Azure Active Directory 管理者のみです。Active Directory 管理者は、それ以降の Azure Active Directory のデータベース ユーザーを構成できます。
- 接続のタイムアウトを 30 秒に設定することをお勧めします。
- BI や Excel など、一部のツールはサポートされていません。
- Azure Active Directory 認証では、**.NET Framework Data Provider for SqlServer** (.NET Framework 4.6 以降のバージョン) のみがサポートされています。そのため、Management Studio (SQL Server 2016 で使用可能) とデータ層アプリケーション (DAC と .bacpac) は接続できますが、**sqlcmd.exe** は接続できません。**sqlcmd** では ODBC プロバイダーが使用されているためです。
- 2 要素認証や他の形式の対話型認証はサポートされていません。


## 1\.Azure Active Directory を作成して設定する
 
Azure Active Directory を作成し、ユーザーとグループを設定します。次のトピックがあります。

- 初期ドメインとして Azure AD の管理対象ドメインを作成します。
- オンプレミスの Active Directory ドメイン サービスを Azure Active Directory とフェデレーションします。

詳細については、「[Azure AD への独自のドメイン名の追加](active-directory-add-domain.md)」、[Windows Azure による Windows Server Active Directory とのフェデレーションのサポート](http://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)に関するブログ、「[Azure AD ディレクトリの管理](https://msdn.microsoft.com/library/azure/hh967611.aspx)」、「[Windows PowerShell による Azure AD の管理](https://msdn.microsoft.com/library/azure/jj151815.aspx)」を参照してください。

## 2\.データベースが Azure SQL Database V12 にあることを確認する
 
Azure Active Directory 認証は、最新の SQL Database V12 でサポートされています。SQL Database V12 の概要と、自身のリージョンで SQL Database V12 が使用可能かどうかについては、[最新の SQL Database V12 の新機能](sql-database-v12-whats-new.md)に関するページを参照してください。

既存のデータベースがある場合は、(たとえば SQL Server Management Studio を使用して) データベースに接続して `SELECT @@VERSION;` を実行することで、SQL Database V12 でホストされていることを確認します。SQL Database V12 のデータベースで予想される出力は **Microsoft SQL Azure (RTM) - 12.0** 以上です。

データベースが SQL Database V12 でホストされていない場合は、「[SQL Database V12 へのアップグレードの計画と準備をする](sql-database-v12-plan-prepare-upgrade.md)」を参照してから、Azure ポータルにアクセスしてデータベースを SQL Database V12 に移行してください。

または、[SQL Database 更新プログラムでのデータベースの作成](sql-database-create.md)に関するページに記載されている手順に従って、SQL Database V12 で新しいデータベースを作成することもできます。**ヒント**: 新しいデータベースのサブスクリプションを選択する前に、次の手順をお読みください。

## 3\.省略可能: Active Directory を関連付けるか、現在 Azure サブスクリプションに関連付けられている Active Directory を変更する

データベースを組織の Azure AD ディレクトリに関連付けるには、そのディレクトリを、データベースをホストする Azure サブスクリプションの信頼できるディレクトリにします。詳細については、[Azure サブスクリプションを Azure AD に関連付ける方法](https://msdn.microsoft.com/library/azure/dn629581.aspx)を参照してください。

**追加情報:** すべての Azure サブスクリプションには、Azure AD インスタンスとの間に信頼関係があります。つまり、ディレクトリを信頼してユーザー、サービス、デバイスを認証します。複数のサブスクリプションが同じディレクトリを信頼できますが、1 つのサブスクリプションは 1 つのディレクトリだけを信頼します。サブスクリプションが信頼しているディレクトリは、[https://manage.windowsazure.com/](https://manage.windowsazure.com/) の **[設定]** タブで確認できます。このサブスクリプションとディレクトリの間の信頼関係は、サブスクリプションと Azure 内の他のすべてのリソース (Web サイト、データベースなど) の間の関係と異なります。後者は、サブスクリプションの子リソースにより近いものです。サブスクリプションの有効期限が切れた場合、サブスクリプションに関連付けられたこれらの他のリソースへのアクセスも停止します。一方、ディレクトリは Azure 内に残っており、別のサブスクリプションをそのディレクトリと関連付けて、ディレクトリ ユーザーの管理を継続できます。リソースの詳細については、「[Azure でのリソース アクセスを理解する](https://msdn.microsoft.com/library/azure/dn584083.aspx)」を参照してください。

次の手順では、特定のサブスクリプションの関連付けられたディレクトリを変更する方法について詳しく説明します。

1. Azure サブスクリプションの管理者を使用して [Azure ポータル](https://manage.windowsazure.com/)に接続します。
2. 左側のバナーで **[設定]** を選択します。 
3. [設定] 画面にサブスクリプションが表示されます。目的のサブスクリプションが表示されない場合は、上部にある **[サブスクリプション]** をクリックし、**[ディレクトリでフィルター]** ボックスの一覧で、サブスクリプションを含むディレクトリを選択して、**[適用]** をクリックします。

	![サブスクリプションを選択する][4]
4. **[設定]** 領域でサブスクリプションをクリックし、ページの下部にある **[ディレクトリの編集]** をクリックします。

	![ad-settings-portal][5]
5. **[ディレクトリの編集]** ボックスで、SQL Server に関連付けられている Azure Active Directory を選択し、矢印をクリックして次に進みます。

	![edit-directory-select][6]
6. **[ディレクトリ マッピングの確認]** ダイアログ ボックスで、"**共同管理者は全員削除されます。**" と表示されていることを確認します。

	![edit-directory-confirm][7]
7. チェック マークをクリックしてポータルを再度読み込みます。

> [AZURE.NOTE]ディレクトリを変更すると、共同管理者、Azure AD のユーザーとグループ、ディレクトリでサポートされているリソース ユーザーすべてへのアクセスが削除され、それらはこのサブスクリプションまたはそのリソースにアクセスできなくなります。サービス管理者の場合のみ、新しいディレクトリに基づくプリンシパルのアクセスを構成できます。この変更は、すべてのリソースに反映されるまで相当な時間がかかる場合があります。ディレクトリを変更すると、SQL Database の Azure AD 管理者も変更されるため、既存のすべての Azure AD ユーザーは SQL Database にアクセスできなくなります。Azure AD の管理者を再設定し (後で説明)、新しい Azure AD ユーザーを作成する必要があります。

## 4\.Azure SQL Server 用に Azure Active Directory 管理者を作成する
 
各 Azure SQL Server の開始には、Azure SQL Server 全体の管理者である 1 つのサーバー管理者アカウントを使用します。Azure AD アカウントであるもう 1 つのサーバー管理者を作成する必要があります。このプリンシパルは、master データベースの包含データベース ユーザーとして作成されます。管理者の場合、サーバー管理者アカウントは、すべてのユーザー データベースの **db\_owner** ロールのメンバーであり、**dbo** ユーザーとして各ユーザー データベースに入ります。サーバー管理者アカウントの詳細については、「[Azure SQL Database におけるデータベースとログインの管理](sql-database-manage-logins.md)」と、「[Azure SQL Database のセキュリティのガイドラインと制限事項](sql-database-security-guidelines.md)」の「**ログインとユーザー**」セクションを参照してください。

> [AZURE.NOTE]Azure AD アカウント (Azure SQL Server の管理者アカウントを含む) に基づいていないユーザーは、Azure AD に基づくユーザーを作成できません。これは、Azure AD で提示されるデータベース ユーザーを検証するアクセス許可がないためです。

### Azure ポータルを使用して、Azure SQL Server の Azure Active Directory 管理者をプロビジョニングします。 

1. [Azure ポータル](https://portal.azure.com/)の右上隅にある接続をクリックすると、考えられる Active Directory のボックスの一覧が表示されます。既定の Azure AD として適切な Active Directory を選択します。この手順では、Active Directory とサブスクリプションの関連付けを Azure SQL Database とリンクすることで、Azure AD と SQL Server の両方に同じサブスクリプションが使用されるようにします。

	![choose-ad][8]
2. 左側のバナーで **[SQL Server]** を選択し、使用する **SQL Server** を選択した後、**[SQL Server]** ブレードの上部にある **[設定]** をクリックします。

	![ad settings][9]
3. **[設定]** ブレードで **[Active Directory 管理者 (プレビュー)]** をクリックして、プレビューの句をそのまま使用します。
4. **[Active Directory 管理者 (プレビュー)]** ブレードで、クリックしてプレビューの条件を確認したら、**[OK]** をクリックしてその条件に同意します。
5. **[Active Directory 管理者 (プレビュー)]** ブレードで **[Active Directory 管理者]** をクリックし、上部にある **[管理者の設定]** をクリックします。
6. **[管理者の追加]** ブレードで、ユーザーを検索し、管理者にするユーザーまたはグループを選択したら、**[選択]** をクリックします。[Active Directory 管理者] ブレードには、Active Directory のメンバーとグループがすべて表示されます。淡色表示されているユーザーまたはグループは、Azure AD 管理者としてサポートされていないため選択できません(サポートされている管理者の一覧については、上記の「**Azure AD の機能と制限事項**」を参照してください)。
7. **[Active Directory 管理者]** ブレードの上部にある **[保存]** をクリックします。![choose admin][10]

	管理者を変更する処理には数分かかる場合があります。処理が完了すると、**[Active Directory 管理者]** ボックスに新しい管理者が表示されます。

後で管理者を削除するには、**[Active Directory 管理者]** ブレードの上部にある **[管理者の削除]** をクリックします。

### PowerShell を使用して Azure SQL Server の Azure AD 管理者をプロビジョニングする 

続行するには、バージョン 0.9.8以降の Azure PowerShell をインストールして構成する必要があります。詳細については、「[Azure PowerShell のインストールと構成の方法](powershell-install-configure.md#Install)」を参照してください。

Azure AD 管理者をプロビジョニングするには、次のような Azure PowerShell コマンドを実行する必要があります。

- Add-AzureAccount
- Select-AzureSubscription
- Switch-AzureMode -Name AzureResourceManager

Azure AD 管理者のプロビジョニングと管理に使用するコマンドレットは、次のとおりです。

| コマンドレット名 | 説明 |
|---------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| Set-AzureSqlServerActiveDirectoryAdministrator | Azure SQL Server の Azure Active Directory 管理者をプロビジョニングします(現在のサブスクリプションから実行する必要があります)。 |
| Remove-AzureSqlServerActiveDirectoryAdministrator | Azure SQL Server の Azure Active Directory 管理者を削除します。 |
| Get-AzureSqlServerActiveDirectoryAdministrator | 現在 Azure SQL Server 用に構成されている Azure Active Directory 管理者に関する情報を返します。 |

これらの各コマンドの詳細を確認するには、``get-help Set-AzureSqlServerActiveDirectoryAdministrator`` のように PowerShell コマンドの get-help を使用します。

次のスクリプトでは、**Group-23** という名前のリソース グループ内にあるサーバー **demo\_server** に対して、**DBA\_Group** という名前の Azure AD 管理者グループ (オブジェクト ID `40b79501-b343-44ed-9ce7-da4c8cc7353f`) をプロビジョニングします。

```
Set-AzureSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" 
–ServerName "demo_server" -DisplayName "DBA_Group"
```

**DisplayName** 入力パラメーターには、Azure AD の表示名とユーザー プリンシパル名のいずれかを使用できます。たとえば、``DisplayName="John Smith"`` や ``DisplayName="johns@contoso.com"`` のようになります。Azure AD グループの場合は、Azure AD の表示名のみがサポートされています。

> [AZURE.NOTE]Azure PowerShell コマンド ```Set-AzureSqlServerActiveDirectoryAdministrator``` によって、サポートされていないユーザーの Azure AD 管理者をプロビジョニングできなくなることはありません。サポートされていないユーザーのプロビジョニングは可能ですが、このようなユーザーはデータベースに接続できません(サポートされている管理者の一覧については、上記の「**Azure AD の機能と制限事項**」を参照してください)。

次の例では、オプションとして **ObjectID** を使用します。

```
Set-AzureSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" 
–ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [AZURE.NOTE]**DisplayName** が一意でない場合は、Azure AD の **ObjectID** が必要です。**ObjectID** と **DisplayName** の値を取得するには、Azure ポータルの [Active Directory] セクションを使用して、ユーザーまたはグループのプロパティを表示します。

次の例では、Azure SQL Server の現在の Azure AD 管理者に関する情報が返されます。

```
Get-AzureSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" –ServerName "demo_server" | Format-List
```

次の例では、Azure AD 管理者が削除されます。```
Remove-AzureSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" –ServerName "demo_server"
```

## 5\.クライアント コンピューターを構成する
 
Azure AD の ID を使用して Azure SQL Database に接続するアプリケーションまたはユーザーが存在するすべてのクライアント コンピューターには、次のソフトウェアをインストールする必要があります。

- [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx) から入手できる .NET framework 4.6 以降。
- SQL Server 用の Azure Active Directory 認証ライブラリ (**ADALSQL.DLL**)。ダウンロード センターの「[Microsoft SQL Server 用の Microsoft Active Directory 認証ライブラリ](http://www.microsoft.com/download/details.aspx?id=48742)」で複数の言語 (x 89 と amd64 の両方) が提供されています。

### ツール

- [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) または [SQL Server Data Tools for Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) をインストールすると、.NET Framework 4.6 の要件が満たされます。 
- SSMS の場合、x86 バージョンの **ADALSQL.DLL** がインストールされます。 
- SSDT の場合、amd64 バージョンの **ADALSQL.DLL** がインストールされます。SSDT では、Azure AD 認証が部分的にしかサポートされていません。
- 「[Visual Studio ダウンロード](https://www.visualstudio.com/downloads/download-visual-studio-vs)」にある最新の Visual Studio をインストールすると、.NET Framework 4.6 の要件は満たされますが、必要な amd64 バージョンの **ADALSQL.DLL** はインストールされません。

## 6\.Azure AD の ID にマップされている包含データベース ユーザーをデータベースに作成する 

### 包含データベース ユーザーについて

Azure Active Directory 認証では、データベース ユーザーを包含データベース ユーザーとして作成することが必要です。Azure AD の ID に基づく包含データベース ユーザーは、master データベースにログインを持たないデータベース ユーザーで、そのデータベースに関連付けられている Azure AD ディレクトリの ID にマップされています。Azure AD の ID には、個々のユーザー アカウントにもグループ アカウントにもなります。包含データベース ユーザーの詳細については、「[包含データベース ユーザー - データベースの可搬性を確保する](https://msdn.microsoft.com/library/ff929188.aspx)」を参照してください。

### SQL Server Management Studio を使用してユーザー データベースに接続する
 
Azure AD 管理者が正しく設定されていることを確認するには、Azure AD の管理者アカウントを使用して **master** データベースに接続します。Azure AD ベースの包含データベース ユーザー (データベースを所有しているサーバー管理者以外) をプロビジョニングするには、そのデータベースへのアクセス権を持つ Azure AD の ID を使用してデータベースに接続します。

> [AZURE.IMPORTANT]Azure Active Directory 認証のサポートは、[SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) で利用可能です。

#### Active Directory 統合認証を使用して接続する 

フェデレーション ドメインから Azure Active Directory の資格情報を使用して Windows にログオンしている場合は、この方法を使用します。

1. Management Studio を起動して、**[データベース エンジンへの接続]** (または **[サーバーへの接続]**) ダイアログ ボックスの **[認証]** ボックスで、**[Active Directory 統合認証]** を選択します。接続用の既存の資格情報が表示されるため、パスワードは必要ないか、入力できません。
2. **[オプション]** ボタンをクリックし、**[接続プロパティ]** ページの **[データベースへの接続]** ボックスに、接続先となるユーザー データベースの名前を入力します。

#### Active Directory パスワード認証を使用して接続する 

Azure AD の管理対象ドメインを使用して Azure AD のプリンシパル名で接続する場合は、この方法を使用します。また、リモートで作業する場合など、ドメインにアクセスできないフェデレーション アカウントにもこの方法を使用できます。

この方法を使用するのは、Azure とフェデレーションされていないドメインの資格情報を使用して Windows にログオンしている場合や、初期ドメインまたはクライアント ドメインに基づく Azure AD で Azure AD 認証を使用する場合です。

1. Management Studio を起動して、**[データベース エンジンへの接続]** (または **[サーバーへの接続]**) ダイアログ ボックスの **[認証]** ボックスで、**[Active Directory パスワード認証]** を選択します。
2. **[ユーザー名]** ボックスに、****username@domain.com** 形式で Azure Active Directory のユーザー名を入力します。これは、Azure Active Directory のアカウントか、Azure Active Directory とフェデレーションするドメインのアカウントである必要があります。
3. **[パスワード]** ボックスに、Azure Active Directory アカウントまたはフェデレーション ドメイン アカウントのユーザー パスワードを入力します。
4. **[オプション]** ボタンをクリックし、**[接続プロパティ]** ページの **[データベースへの接続]** ボックスに、接続先となるユーザー データベースの名前を入力します。


### ユーザー データベースに Azure AD の包含データベース ユーザーを作成する

Azure AD ベースの包含データベース ユーザー (データベースを所有するサーバー管理者以外) を作成するには、少なくとも **ALTER ANY USER** アクセス許可を持つユーザーとして、(前の手順で説明したように) Azure AD の ID でデータベースに接続します。その後、次の Transact-SQL 構文を使用します。

	CREATE USER Azure_AD_principal_name 
	FROM EXTERNAL PROVIDER;


*Azure\_AD\_principal\_name* には、Azure AD ユーザーのユーザー プリンシパル名か、Azure AD のグループまたはアプリケーションの表示名を指定できます。

**例:** Azure AD のフェデレーション ドメインまたは管理対象ドメインのユーザーを表す包含データベース ユーザーを作成するには、次のようにします。

	CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
	CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;

Azure AD またはフェデレーション ドメインのグループを表す包含データベース ユーザーを作成するには、次のようにします。

	CREATE USER [Nurses] FROM EXTERNAL PROVIDER;


Azure Active Directory の ID に基づく包含データベース ユーザーの作成の詳細については、「[CREATE USER (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)」を参照してください。

データベース ユーザーを作成すると、そのユーザーは **CONNECT** アクセス許可が付与されるため、**PUBLIC** ロールのメンバーとしてそのデータベースに接続できます。最初にこのユーザーが利用できるアクセス許可は、**PUBLIC** ロールに付与されているアクセス許可か、またはそのユーザーが属している Windows グループに付与されているアクセス許可のみです。Azure AD ベースの包含データベース ユーザーをプロビジョニングすると、他の種類のユーザーにアクセス許可を付与する場合と同様に、そのユーザーに追加のアクセス許可を付与できます。通常は、データベース ロールにアクセス許可を付与し、そのロールにユーザーを追加します。詳細については、[データベース エンジンのアクセス許可の基本知識](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx)に関するページを参照してください。特殊な SQL Database ロールの詳細については、「[Azure SQL Database におけるデータベースとログインの管理](sql-database-manage-logins.md)」を参照してください。管理対象ドメインにインポートされるフェデレーション ドメイン ユーザーは、管理対象ドメインの ID を使用する必要があります。

> [AZURE.NOTE]データベースのメタデータでは、Azure AD ユーザーはタイプ E (EXTERNAL\_USER)、グループまたはアプリケーションはタイプ X (EXTERNAL\_GROUPS) でマークされます。詳細については、「[sys.database\_principals](https://msdn.microsoft.com/library/ms187328.aspx)」を参照してください。


## 7\.Azure Active Directory の ID を使用してデータベースに接続する
 
Azure Active Directory 認証では、Azure AD の ID を使用してデータベースに接続する次の方法がサポートされています。

- 統合 Windows 認証を使用する
- Azure AD のプリンシパル名とパスワードを使用する

### 7\.1.統合 (Windows) 認証を使用して接続する
 
統合 Windows 認証を使用するには、ドメインの Active Directory を Azure Active Directory とフェデレーションする必要があります。また、データベースに接続しているクライアント アプリケーション (またはサービス) を、ユーザーのドメイン資格情報でドメインに参加しているコンピューター上で実行している必要があります。

統合認証と Azure AD の ID を使用してデータベースに接続するには、データベース接続文字列内の Authentication キーワードを "Active Directory Integrated" に設定する必要があります。次の C# のコード サンプルでは、ADO .NET を使用します。

	string ConnectionString = 
	@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated;";
	SqlConnection conn = new SqlConnection(ConnectionString);
	conn.Open();

接続文字列のキーワード ``Integrated Security=True`` は、Azure SQL Database への接続ではサポートされていません。

### 7\.2.Azure AD のプリンシパル名とパスワードを使用して接続する 
統合認証と Azure AD の ID を使用してデータベースに接続するには、Authentication キーワードを "Active Directory Password" に設定し、接続文字列にユーザー ID (UID) とパスワード (PWD) のキーワードと値を含める必要があります。次の C# のコード サンプルでは、ADO .NET を使用します。

	string ConnectionString =
	  @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
	SqlConnection conn = new SqlConnection(ConnectionString);
	conn.Open();

Azure AD 認証に関連した具体的なコード例については、MSDN の [SQL Server セキュリティに関するブログ](http://blogs.msdn.com/b/sqlsecurity/)を参照してください。

## 関連項目

[Azure SQL Database におけるデータベースとログインの管理](sql-database-manage-logins.md)

[包含データベース ユーザー](https://msdn.microsoft.com/library/ff929071.aspx)

[CREATE USER (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)

<!--Image references-->
[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
<!--anchors-->

<!---HONumber=Sept15_HO3-->