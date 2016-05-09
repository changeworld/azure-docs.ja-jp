<properties
	pageTitle="Azure AD ドメイン サービス: AAD DC 管理者グループの作成 | Microsoft Azure"
	description="Azure Active Directory ドメイン サービス (プレビュー) の概要"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/25/2016"
	ms.author="maheshu"/>

# Azure AD ドメイン サービス *(プレビュー)* - "AAD DC 管理者" グループの作成

この記事では、Azure AD テナントの Azure AD ドメイン サービスを有効にするために必要な構成タスクについて説明します。

## タスク 1: "AAD DC 管理者" グループの作成
最初のタスクは、Azure Active Directory テナントでの管理グループの作成です。この特別な管理グループは、**AAD DC 管理者**と呼ばれます。このグループのメンバーには、セットアップする Azure AD ドメイン サービス ドメインにドメイン参加するコンピューターへの管理特権が付与されます。ドメインへの参加後、ドメイン参加しているコンピューターの "Administrators" グループにこのグループが追加されます。さらに、このグループのメンバーは、リモート デスクトップを使用して、ドメインに参加しているコンピューターにリモート接続できます。

> [AZURE.NOTE] Azure AD ドメイン サービスを使用して作成された管理対象ドメインに、ドメイン管理者またはエンタープライズ管理者の特権がありません。このドメインは管理対象ドメインであるため、これらの権限はサービスによって予約されており、ディレクトリ内でユーザーが使用することはできません。ただし、この構成タスクで作成された特殊な管理者グループを使用して、特権を持つ操作の一部を行うことができます。これらの操作には、ドメインへのコンピューターの参加、ドメインに参加しているコンピューターでの管理者グループへの所属、グループ ポリシーの構成などが含まれます。

この構成タスクでは、管理グループを作成し、そのグループにディレクトリ内の 1 人以上のユーザーを追加します。Azure AD ドメイン サービスの管理グループを作成するには、次の手順を実行します。

1. **Azure クラシック ポータル** ([https://manage.windowsazure.com](https://manage.windowsazure.com)) に移動します

2. 左ウィンドウで、**[Active Directory]** を選択します。

3. Azure AD ドメイン サービスを有効にする Azure AD テナント (ディレクトリ) を選択します。Azure AD ディレクトリごとに作成できるドメインは 1 つです。

    ![Azure AD ディレクトリの選択](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. **[グループ]** タブをクリックします。

5. ページの下部にある作業ウィンドウの **[グループの追加]** をクリックして、ディレクトリにグループを追加します。

6. **AAD DC Administrators** という名前のグループを作成します。

    > [AZURE.WARNING] Azure AD ドメイン サービス内でアクセスを有効にするために、この名前をそのまま使用してグループを作成する必要があります。

	![Create administrator group](./media/active-directory-domain-services-getting-started/create-admin-group.png)

7. Azure AD ドメイン サービス内で管理者特権を付与するためにこのグループが使用されることを Azure AD テナント内の他のユーザーが理解できるように、このグループの説明を追加できます。

8. グループを作成したら、グループの名前をクリックして、このグループのプロパティを表示します。下部のパネルの **[メンバーの追加]** をクリックして、ユーザーをこのグループのメンバーとして追加します。

9. **[メンバーの追加]** ダイアログ ボックスで、このグループのメンバーにするユーザーを選択し、チェック ボックスをオンします。

    ![管理者グループへのユーザーの追加](./media/active-directory-domain-services-getting-started/add-group-members.png)

<br>

## タスク 2: Azure 仮想ネットワークの作成または選択
次の構成タスクでは、[Azure 仮想ネットワークを作成または選択します](active-directory-ds-getting-started-vnet.md)。

<!---HONumber=AcomDC_0427_2016-->