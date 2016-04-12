<properties
	pageTitle="Azure Active Directory のロールベースのアクセス制御 | Microsoft Azure"
	description="Azure ポータルで Azure のロールベースのアクセス制御を使用したアクセス管理を開始します。ロールの割り当てを使用して、ディレクトリ内でアクセス許可を割り当てます。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="03/30/2016"
	ms.author="kgremban"/>

# Azure のロールベースのアクセス制御

## ロールベースのアクセス制御
Azure のロールベースのアクセス制御 (RBAC) では、Azure のアクセス権の詳細な管理を実現します。RBAC を使用して、開発チーム内で職務を分離し、職務に必要なアクセス権のみをユーザーに付与します。この記事では、アクセス管理の基礎について説明し、Azure ポータルで RBAC の利用を開始するのに役立つ情報を提供します。

### Azure でのアクセス管理の基礎
各 Azure サブスクリプションは、1 つの Azure Active Directory (AD) ディレクトリと関連付けられます。そのディレクトリに登録されたユーザー、グループ、およびアプリケーションのみが、Azure サブスクリプションでリソースを管理できます。このためのアクセス権は、Azure ポータル、Azure コマンドライン ツール、および Azure 管理 API を使用して付与します。

アクセス権を付与するには、ユーザー、グループ、およびアプリケーションに適切な RBAC ロールを特定のスコープで割り当てます。ロール割り当てのスコープには、サブスクリプション、リソース グループ、または単独のリソースを指定できます。親スコープでロールが割り当てられると、その親に含まれる子へのアクセス権も付与されます。たとえば、リソース グループへのアクセス権を持つユーザーは、Web サイト、仮想マシン、サブネットなど、リソース グループに含まれるすべてのリソースを管理できます。

![Relationship between Azure Active Directory elements - diagram](./media/role-based-access-control-configure/rbac_aad.png)

割り当てる RBAC ロールにより、そのスコープ内でユーザー、グループ、またはアプリケーションが管理できるリソースが決まります。

### 組み込みのロール
Azure RBAC には、すべてのリソースの種類に適用される 3 つの基本的なロールがあります。

- **所有者**は、他のユーザーへアクセス権を委任する権限を含め、すべてのリソースへのフル アクセス権を持ちます。
- **作成協力者**は、Azure リソースのすべてのタイプを作成および管理できますが、他のユーザーへアクセス権を付与することはできません。
- **閲覧者**は、既存の Azure リソースを表示できます。

残りの Azure RBAC ロールでは、特定の Azure リソースの管理が許可されます。たとえば、仮想マシンの作成協力者ロールが割り当てられたユーザーには、仮想マシンの作成と管理が許可されます。その一方で、仮想マシンが接続する仮想ネットワークまたはサブネットへのアクセス権は付与されません。

「[RBAC: 組み込みのロール](role-based-access-built-in-roles.md)」に、Azure で使用できる RBAC ロールが記載されています。各組み込みロールによってユーザーに付与される操作とスコープが説明されています。制御を強化するために独自のロールを定義する場合は、[Azure RBAC でカスタム ロール](role-based-access-control-custom-roles.md)を作成する方法を参照してください。

### リソース階層とアクセス権の継承
- Azure 内の各**サブスクリプション**は、1 つのディレクトリのみに属しています。
- 各**リソース グループ**は、1 つのサブスクリプションのみに属しています。
- 各**リソース**は、1 つのリソース グループのみに属しています。

親スコープで付与されたアクセス権は、子スコープに継承されます。次に例を示します。

- 閲覧者ロールをサブスクリプション スコープで Azure AD グループに割り当てると、そのグループのメンバーは、サブスクリプション内のすべてのリソース グループとすべてのリソースを表示できるようになります。  
- 作成協力者ロールをリソース グループ スコープでアプリケーションに割り当てると、そのアプリケーションでは、そのリソース グループ内のすべてのタイプのリソースを管理できるようになりますが、サブスクリプション内の他のリソース グループは管理できません。

### Azure RBAC と従来のサブスクリプションの管理者の比較
従来のサブスクリプションの管理者と共同管理者には、Azure サブスクリプションへのフル アクセス権があります。リソースの管理には、[Azure ポータル](https://portal.azure.com)と Azure Resource Manager API の組み合わせ、または [Azure クラシック ポータル](https://manage.windowsazure.com)と Azure Service Management API の組み合わせを使用できます。RBAC モデルで、従来の管理者は、サブスクリプション スコープで所有者ロールを割り当てられます。

Azure RBAC は Azure ポータルと新しい Azure Resource Manager API の組み合わせのみでサポートされています。RBAC ロールを割り当てられているユーザーとアプリケーションは、クラシック管理ポータルと Azure Service Management API を使用できません。

### 管理操作とデータ操作の許可
Azure RBAC は、Azure ポータルと Azure Resource Manager API での Azure リソースの管理操作のみに対応しています。RBAC による Azure リソースのデータ レベルの操作の中には、許可されていないものもあります。たとえば、ストレージ アカウントは RBAC で管理できますが、ストレージ アカウント内の BLOB とテーブルは管理できません。同様に、SQL データベースは管理できますが、その中のテーブルは管理できません。

## Azure ポータルを使用したアクセス権の管理
### アクセス許可の表示
[Azure ポータル](https://portal.azure.com)のメイン ブレードで、リソース、リソース グループ、またはサブスクリプションへのアクセス権が付与されているユーザーを確認できます。たとえば、リソース グループのいずれかへのアクセス権があるユーザーを確認する場合は、次の手順を実行します。

1. 左側にあるナビゲーション バーで、**[リソース グループ]** アイコンをクリックします。
2. **[リソース グループ]** ブレードで、確認するリソース グループの名前を選択します。
3. [リソース グループ] ブレードの右上にある **[ユーザー]** アイコンを選択します。
4. **[ユーザー]** ブレードに、リソース グループへのアクセス権が付与されたすべてのユーザー、グループ、およびアプリケーションが一覧表示されます。

![Users blade - inherited vs assigned access screenshot](./media/role-based-access-control-configure/view-access.png)

アクセス権が**割り当て済み**になっているユーザーと**継承済み**になっているユーザーがいることに注目してください。アクセス権は、リソース グループに明示的に割り当てられる場合と、親サブスクリプションへの割り当てから継承される場合があります。

> [AZURE.NOTE] 従来のサブスクリプションの管理者と共同管理者は、新しい RBAC モデルではサブスクリプションの所有者と見なされます。


### アクセス権の追加
アクセス権は、リソース内、リソース グループ内、またはサブスクリプション内から付与します。これは、ロール割り当てのスコープになります。

1. **[ユーザー]** ブレードで **[追加]** アイコンをクリックします。![Add access blade - select a role screenshot](./media/role-based-access-control-configure/grant-access1.png)
2. 割り当てるロールを選択します。
3. ディレクトリで、アクセス権を付与するユーザー、グループ、またはアプリケーションを選択します。ディレクトリは、表示名、電子メール アドレス、およびオブジェクト識別子を使用して検索できます。![Add users blade - search screenshot](./media/role-based-access-control-configure/grant-access2.png)

### アクセス権の削除

1. **[ユーザー]** ブレードで、削除するロールの割り当てを選択します。
2. [割り当ての詳細] ブレードで、**[削除]** アイコンをクリックします。
3. **[はい]** をクリックして削除を確定します。

![Users blade - remove from role screenshot](./media/role-based-access-control-configure/remove-access1.png)

継承された割り当ては、子スコープから削除できません。親スコープからロールの割り当てを削除する必要があります。

![Users blade - inherited access disables remove button screenshot](./media/role-based-access-control-configure/remove-access2.png)

## その他のアクセス管理ツール
Azure ポータル以外のツールでも Azure RBAC コマンドを使用したアクセス管理とロールの割り当てを実行できます。次のリンクをクリックして、前提条件の詳細と、Azure RBAC コマンドの使用方法について確認できます。

- [Azure PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure コマンド ライン インターフェイス](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)

## 次のステップ
- [アクセス変更履歴レポートの作成](role-based-access-control-access-change-history-report.md)
- [RBAC の組み込みロール](role-based-access-built-in-roles.md)の確認
- 独自の [Azure RBAC カスタム ロール](role-based-access-control-custom-roles.md)の定義

<!---HONumber=AcomDC_0406_2016-->