<properties

	pageTitle="Managing security groups in Azure Active Directory | Microsoft Azure"
	description="How to create and manage security groups to manage Azure resource access using Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/10/2016"
	ms.author="curtand"/>


#Azure Active Directory のセキュリティ グループの管理

Azure Active Directory (Azure AD) の主な機能の 1 つが、リソースへのアクセスを管理する機能です。こういったリソースは、ディレクトリ内のロールによってオブジェクトを管理するアクセス許可のケースのように、ディレクトリに含まれる場合と、SaaS アプリケーション、Azure サービス、SharePoint サイト、オンプレミスのリソースなどのように、ディレクトリの外部のリソースという場合があります。リソースの所有者によって、グループをリソースに割り当てることができます。これにより、そのグループのメンバーにリソースへのアクセス権が付与されます。グループのメンバーシップは、グループの所有者が管理できるようになります。実質的には、リソースの所有者が、ユーザーをリソースに割り当てる権限をグループの所有者に委任することになります。


##セキュリティ グループを作成して管理する方法

**Azure ポータルでグループを作成するには**

1. Azure ポータルで **[Active Directory]** をクリックし、該当する組織のディレクトリ名をクリックします。
2. **[グループ]** タブをクリックします。
3. [グループ] ページの **[グループの追加]** をクリックします。
4. **[グループの追加]** ウィンドウで、グループの名前と説明を指定します。
5. この作業には、Office 365 アカウント ポータル、Windows Intune アカウント ポータル、Azure ポータルを使用することができます。自分の組織がサブスクライブしているサービスに応じたポータルを使用してください。ポータルを使用した Azure Active Directory の管理の詳細については、[Azure AD ディレクトリの管理](active-directory-administer)に関するページを参照してください。

## セキュリティ グループのユーザーの割り当てと削除の方法

**Azure ポータルでグループにメンバーを追加するには**

1. Azure ポータルで **[Active Directory]** をクリックし、該当する組織のディレクトリ名をクリックします。
2. **[グループ]** タブをクリックします。
3. **[グループ]** ページで、メンバーの追加先となるグループの名前をクリックします。選択したグループの **[メンバー]** タブが既定で表示されます。
4. そのグループのページで、**[メンバーの追加]** をクリックします。
5. このグループのメンバーとして追加するユーザーまたはグループの名前を **[メンバーの追加]** ページでクリックし、その名前が [選択済み] ウィンドウに追加されたことを確認します。


**Azure ポータルでグループからメンバーを削除するには**

1. Azure ポータルで **[Active Directory]** をクリックし、該当する組織のディレクトリ名をクリックします。
2. **[グループ]** タブをクリックします。
3. [グループ] ページで、メンバーを削除するグループの名前をクリックします。
4. そのグループのページで、**[メンバー]** タブをクリックします。
5. グループから削除するメンバーの名前をそのグループのページでクリックし、**[削除]** をクリックします。
6. 削除の確認メッセージが表示されるので、このメンバーをグループから削除してよい場合は、**[はい]** をクリックします。


## ルールを使用してセキュリティ グループのメンバーを動的に管理する方法

**特定のグループの動的メンバーシップ管理を有効にするには、次の手順を実行します。**

1. Azure ポータルの **[グループ]** タブで編集対象のグループを選択し、そのグループの **[構成]** タブで **[動的メンバーシップを有効にする]** スイッチを **[はい]** に設定します。
2. ここで、このグループに対する動的メンバーシップの働きを制御する単純なルールを 1 つグループに設定することができます。**[追加するユーザーの所属]** オプション ボタンがオンになっていることを確認し、ユーザーのプロパティ (department、jobTitle など) をプルダウン メニューから選択します。
3. 次に、条件 (等しくない、等しい、指定値で始まらない、指定値で始まる、指定値を含まない、指定値を含む、一致しない、一致する) を選び、選択したユーザー プロパティの値を指定します。

たとえば、グループを SaaS アプリケーションに割り当てる場合 (詳細については、「Azure AD で SaaS アプリケーションにグループのアクセスを割り当てる」を参照) で、なおかつ jobTitle が Sales Rep と等しい (-eq) ときにユーザーを追加するようにルールを設定して、そのグループの動的メンバーシップを有効にした場合、Azure AD ディレクトリ内で、役職が Sales Rep に設定されているすべてのユーザーに、この SaaS アプリケーションへのアクセス権が割り当てられます。

## 追加情報

次の記事は、Azure Active Directory に関する追加情報を示します。

* [Azure Active Directory グループによるリソースのアクセス管理](active-directory-manage-groups.md)

* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)

* [Azure Active Directory とは](active-directory-whatis.md)

* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0218_2016-->