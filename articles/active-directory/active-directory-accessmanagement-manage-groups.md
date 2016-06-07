<properties

	pageTitle="Managing groups in Azure Active Directory | Microsoft Azure"
	description="How to create and manage groups to manage Azure users using Azure Active Directory."
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
	ms.date="05/26/2016"
	ms.author="curtand"/>


# Azure Active Directory におけるグループの管理

Azure Active Directory (Azure AD) ユーザー管理の機能の 1 つに、ユーザーのグループを作成する機能があります。グループを使用して、ライセンスをユーザーのクラスに割り当てることができます。また、グループは次のリソースに対するアクセス許可の割り当てにも使用できます。

- ディレクトリ内のオブジェクトなどのリソース
- SaaS アプリケーションや Azure サービス、SharePoint サイト、オンプレミスのリソースなど、ディレクトリの外部に存在するリソース

加えてリソース所有者は、リソースへのアクセス権を Azure AD グループに対して割り当てることができます。この場合そのグループのメンバーに、リソースへのアクセス権が付与されます。グループのメンバーシップは、グループの所有者が管理します。実質的にはユーザーをリソースに割り当てる権限が、リソースの所有者からグループの所有者に委任されます。

## どのようにしてグループを作成しますか?

この作業には、Office 365 アカウント ポータル、Windows Intune アカウント ポータル、Azure クラシック ポータルを使用することができます。自分の組織がサブスクライブしているサービスに応じたポータルを使用してください。Azure 以外のポータルを使用した Azure Active Directory の管理の詳細については、[Azure AD ディレクトリの管理](active-directory-administer.md)に関するページを参照してください。

1. [Azure クラシック ポータル](https://manage.windowsazure.com)で **[Active Directory]** を選択し、該当する組織のディレクトリ名を選択します。

2. **[グループ]** タブを選択します。

3. **[グループの追加]** を選択します。

4. **[グループの追加]** ウィンドウで、グループの名前と説明を指定します。


## セキュリティ グループのユーザーを個別に追加したり削除したりする方法

**個々のユーザーをグループに追加するには**

1. [Azure クラシック ポータル](https://manage.windowsazure.com)で **[Active Directory]** を選択し、該当する組織のディレクトリ名を選択します。

2. **[グループ]** タブを選択します。

3. メンバーの追加先となるグループを開きます。選択したグループの **[メンバー]** タブが既定で表示されます。

4. **[メンバーの追加]** を選択します。

5. このグループのメンバーとして追加するユーザーまたはグループの名前を **[メンバーの追加]** ページで選択し、その名前が **[選択済み]** ウィンドウに追加されたことを確認します。


**個々のユーザーをグループから削除するには**

1. [Azure クラシック ポータル](https://manage.windowsazure.com)で **[Active Directory]** を選択し、該当する組織のディレクトリ名を選択します。

2. **[グループ]** タブを選択します。

3. 削除するメンバーのグループを開きます。

4. **[メンバー]** タブを選択し、そのグループから削除するメンバーの名前を選択して **[削除]** をクリックします。

6. このメンバーをグループから削除してよいか確認するメッセージが表示されるので、削除してよい場合は操作を確定します。


## グループのメンバーシップを動的に管理する方法

Azure AD では、グループのメンバーとして追加するユーザーを特定のルールに基づいて指定できます。単純なルール (比較を 1 回だけ行うルール) であればその設定はごく簡単です。たとえば、SaaS アプリケーションにグループを割り当て、役職が "Sales Rep" であるユーザーを追加するようにルールを設定した場合、Azure AD ディレクトリ内でその役職に該当するすべてのユーザーに、この SaaS アプリケーションへのアクセス権が与えられます。

> [AZURE.NOTE] セキュリティ グループまたは Office 365 グループには、動的メンバーシップのルールを設定できます。現在、アプリケーションに対するグループ ベースの割り当てでは入れ子になったグループ メンバーシップはサポートされていません。
>
> グループの動的メンバーシップを実行するには、次のユーザーに Azure AD Premium ライセンスが割り当てられている必要があります。
>
> - グループに対するルールを管理する管理者
> - ルールによってグループのメンバーとして選択されるすべてのユーザー

**グループに対する動的メンバーシップを有効にするには**

1. [Azure クラシック ポータル](https://manage.windowsazure.com)で **[Active Directory]** を選択し、該当する組織のディレクトリ名を選択します。

2. **[グループ]** タブを選択し、編集するグループを開きます。

3. **[構成]** タブを選択し、**[動的メンバーシップを有効にする]** を **[はい]** に設定します。

4. このグループに対する動的メンバーシップの働きを制御する単純なルールを 1 つグループに設定することができます。**[追加するユーザーの所属]** オプションがオンになっていることを確認し、ユーザーのプロパティ (department、jobTitle など) をリストから選択します。

5. 次に、条件 (等しくない、等しい、指定値で始まらない、指定値で始まる、指定値を含まない、指定値を含む、一致しない、一致する) を選び、選択したユーザー プロパティの値を指定します。

動的グループのメンバーシップ管理を目的とした*高度*なルール (複数の比較を伴うルール) を作成する方法については、「[属性を使用した高度なルールの作成](active-directory-accessmanagement-groups-with-advanced-rules.md)」を参照してください。

## 追加情報

次の記事は、Azure Active Directory に関する追加情報を示します。

* [Azure Active Directory グループによるリソースのアクセス管理](active-directory-manage-groups.md)

* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)

* [Azure Active Directory とは](active-directory-whatis.md)

* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0601_2016-->