<properties
	pageTitle="Azure Active Directory プレビューで新しいグループを作成する | Microsoft Azure"
	description="Azure Active Directory でグループを作成し、ユーザー (メンバー) をグループに追加する方法"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/12/2016"
	ms.author="curtand"/>


# Azure Active Directory プレビューで新しいグループを作成する

この記事では、Azure Active Directory (Azure AD) プレビューで新しいグループを作成して設定する方法について説明します。プレビューの機能については、[こちらの記事](active-directory-preview-explainer.md)をご覧ください。 グループを使用して管理タスクを実行します。たとえば、複数のユーザーやデバイスにライセンスまたはアクセス許可を一度に割り当てることができます。

## どのようにしてグループを作成しますか?

1. ディレクトリの全体管理者であるアカウントで [Azure ポータル](https://portal.azure.com)にサインインします。

2. **[その他のサービス]** を選択し、テキスト ボックスに「**ユーザーとグループ**」と入力して、**Enter** キーを押します。

  ![ユーザー管理を開く](./media/active-directory-groups-create-azure-portal/search-user-management.png)

3. **[ユーザーとグループ]** ブレードで、**[すべてのグループ]** を選択します。

  ![グループ ブレードを開く](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)

4. **[ユーザーとグループ - すべてのグループ]** ブレードで、**[追加]** をクリックします。

  ![[追加] をクリックする](./media/active-directory-groups-create-azure-portal/add-group-command.png)

5. **[グループ]** ブレードで、グループの名前と説明を追加します。

6. グループに追加するメンバーを選択するには、**[メンバーシップの種類]** ボックスで **[割り当て済み]** を選択し、**[メンバー]** を選択します。グループのメンバーシップを動的に管理する方法の詳細については、[グループ内のユーザーの動的ルールの管理](active-directory-groups-dynamic-users-azure-portal.md)または[グループ内のデバイスの動的ルールの管理](active-directory-groups-dynamic-devices-azure-portal.md)に関する記事をご覧ください。

  ![追加するメンバーを選択する](./media/active-directory-groups-create-azure-portal/select-members.png)

5. **[メンバー]** ブレードで、グループに追加する 1 人以上のユーザーまたは 1 つ以上のデバイスを選択し、ブレードの下部にある **[選択]** をクリックしてグループに追加します。**[ユーザー]** ボックスでは、入力内容とユーザー名またはデバイス名の一部との一致に基づいて表示がフィルター処理されます。このボックスではワイルドカード文字は使用できません。

6. グループへのメンバーの追加が完了したら、**[グループ]** ブレードの **[作成]** をクリックします。

  ![グループの作成の確認](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)




## 追加情報

次の記事は、Azure Active Directory に関する追加情報を示します。

* [既存のグループの表示](active-directory-groups-view-azure-portal.md)
* [グループの設定の管理](active-directory-groups-settings-azure-portal.md)
* [グループのメンバーの管理](active-directory-groups-members-azure-portal.md)
* [グループのメンバーシップの管理](active-directory-groups-membership-azure-portal.md)
* [グループ内のユーザーの動的ルールの管理](active-directory-groups-dynamic-membership-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->