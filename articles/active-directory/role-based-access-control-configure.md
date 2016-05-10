<properties
	pageTitle="Azure ポータルにおけるロールベースのアクセス制御の使用 | Microsoft Azure"
	description="Azure ポータルでのロールベースのアクセス制御を使用したアクセス管理の基本について説明します。ロールの割り当てを使用して、ディレクトリ内でアクセス許可を割り当てます。"
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
	ms.date="05/03/2016"
	ms.author="kgremban"/>

# Azure Active Directory リソースへのアクセスをロールの割り当てによって管理する

Azure のロールベースのアクセス制御 (RBAC) では、Azure のアクセス権の詳細な管理を実現します。RBAC を使用すると、職務に必要な範囲のアクセス権だけをユーザーに付与することができます。この記事では、Azure ポータルにおける RBAC の基本的な使い方について説明しています。RBAC を使用した高度なアクセス管理については、「[What is Role-Based Access Control (ロールベースのアクセス制御とは)](role-based-access-control-what-is.md)」を参照してください。

## アクセス許可の表示
[Azure ポータル](https://portal.azure.com)のメイン ブレードで、リソース、リソース グループ、またはサブスクリプションへのアクセス権が付与されているユーザーを確認できます。たとえば、リソース グループのいずれかへのアクセス権があるユーザーを確認する場合は、次の手順を実行します。

1. 左側にあるナビゲーション バーで、**[リソース グループ]** アイコンをクリックします。 ![Resource groups - icon](./media/role-based-access-control-configure/resourcegroups_icon.png)
2. **[リソース グループ]** ブレードからリソース グループの名前を選択します。
3. [リソース グループ] ブレードの右上にある **[ユーザー]** を選択します。 ![Users - icon](./media/role-based-access-control-configure/users_icon.png)
4. **[ユーザー]** ブレードに、リソース グループへのアクセス権が付与されたすべてのユーザー、グループ、およびアプリケーションが一覧表示されます。  

	![Users blade - inherited vs assigned access screenshot](./media/role-based-access-control-configure/view-access.png)

アクセス権が**割り当て済み**になっているユーザーと**継承済み**になっているユーザーがいることに注目してください。アクセス権は、リソース グループに明示的に割り当てられる場合と、親サブスクリプションへの割り当てから継承される場合があります。

> [AZURE.NOTE] 従来のサブスクリプションの管理者と共同管理者は、新しい RBAC モデルではサブスクリプションの所有者と見なされます。


## アクセス権の追加
アクセス権は、リソース内、リソース グループ内、またはサブスクリプション内から付与します。これは、ロール割り当てのスコープになります。

1. **[ユーザー]** ブレードの **[追加]** を選択します。 ![Add - icon](./media/role-based-access-control-configure/add_icon.png)  
2. **[役割を選択]** ブレードから割り当てる役割を選択します。
3. ディレクトリで、アクセス権を付与するユーザー、グループ、またはアプリケーションを選択します。ディレクトリは、表示名、電子メール アドレス、およびオブジェクト識別子を使用して検索できます。  

	![Add users blade - search screenshot](./media/role-based-access-control-configure/grant-access2.png)

4. **[OK]** を選択して割り当てを作成します。**[ユーザーを追加中]** ポップアップに進行状況が表示されます。 ![Adding user progress bar - screenshot](./media/role-based-access-control-configure/addinguser_popup.png)

ロールの割り当てが正常に追加されると、**[ユーザー]** ブレードに表示されます。

## アクセス権の削除

1. **[ユーザー]** ブレードでロールの割り当てを選択します。
2. [割り当ての詳細] ブレードで **[削除]** を選択します。 ![Remove - icon](./media/role-based-access-control-configure/remove_icon.png)
3. **[はい]** を選択して削除を確定します。![Users blade - remove from role screenshot](./media/role-based-access-control-configure/remove-access1.png)

継承された割り当ては削除できません。以下の画像を見ると、削除ボタンが淡色表示されていることがわかります。そのようなときは、**[割り当て対象]** の詳細を確認します。そこにリストされているリソースにアクセスして、ロールの割り当てを削除してください。

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

<!---HONumber=AcomDC_0504_2016-->