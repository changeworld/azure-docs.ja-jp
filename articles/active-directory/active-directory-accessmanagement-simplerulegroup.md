<properties
	pageTitle="単純なルールを作成してグループの動的メンバーシップ管理を行う| Microsoft Azure"
	description="単純なルールを作成してグループの動的メンバーシップ管理を行う方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="femila"/>


# 単純なルールを作成してグループの動的メンバーシップ管理を行う

**特定のグループの動的メンバーシップ管理を有効にするには、次の手順を実行します。**

1. Azure 管理ポータルの **[グループ]** タブで編集対象のグループを選択し、そのグループの **[構成]** タブで **[動的メンバーシップを有効にする]** スイッチを **[はい]** に設定します。


2. ここで、このグループに対する動的メンバーシップの働きを制御する単純なルールを 1 つグループに設定することができます。**[追加するユーザーの所属]** オプション ボタンがオンになっていることを確認し、ユーザーのプロパティ (department、jobTitle など) をプルダウン メニューから選択します。

3. 次に、条件 (等しくない、等しい、指定値で始まらない、指定値で始まる、指定値を含まない、指定値を含む、一致しない、一致する) を選び、選択したユーザー プロパティの値を指定します。たとえば、グループを SaaS アプリケーションに割り当てる場合で、なおかつ jobTitle が Sales Rep と等しい (-eq) ときにユーザーを追加するようにルールを設定して、そのグループの動的メンバーシップを有効にした場合、Azure AD ディレクトリ内で、役職が Sales Rep に設定されているすべてのユーザーに、この SaaS アプリケーションへのアクセス権が割り当てられます。

ここでは、Azure Active Directory の追加情報を提供するいくつかのトピックを紹介します。

* [Azure Active Directory グループによるリソースのアクセス管理](active-directory-manage-groups.md)

* [Azure Active Directory とは](active-directory-whatis.md)

* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)

<!---HONumber=July15_HO5-->