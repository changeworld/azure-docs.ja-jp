<properties
   pageTitle="PIM へのアクセス権を付与する方法 |Microsoft Azure"
   description="Azure Active Directory Privileged Identity Management 拡張機能でユーザーにロールを追加して、ユーザーが PIM を管理できるようにする方法について説明します。"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management を管理するアクセス権を付与する方法

組織の Azure AD Privileged Identity Management (PIM) を有効にする最初のユーザーは、グローバル管理者である必要があります。ただし、他のグローバル管理者には既定で PIM へのアクセス権が付与されていないため、一時的な割り当てを管理することはできません。PIM へのアクセス権を付与するには、最初のユーザーが他のユーザーに特権ロール管理者ロールを割り当てます。この割り当ては、PIM 内から実行する必要があり、PowerShell や他のポータルを使用して変更することはできません。

> [AZURE.NOTE] Azure AD PIM を管理するには、Azure MFA が必要です。Microsoft アカウントは Azure MFA に登録できないため、Microsoft アカウントでサインインしたユーザーは Azure AD PIM にアクセスできません。

1 人のユーザーがロックアウトされたり、アカウントが削除されたりしたときのために、特権ロール管理者ロールを割り当てられたユーザーが常に少なくとも 2 人いるようにしてください。

## PIM を管理するためのアクセス権を別のユーザーに付与する

1. [Azure ポータル](https://portal.azure.com/)にサインインし、ダッシュボードで **Azure AD Privileged Identity Management** アプリを選択します。
2. **[privileged role administrator]** (特権ロール管理者) ロールを選択します。そのロールの現在のユーザー一覧が表示されます。
3. **[追加]** をクリックすると、ウィザード ブレードが表示されます。ロールは既に選択されています。
4. **[ユーザーを選択する]** をクリックすると、ユーザーの一覧ブレードが表示されます。
5. [検索] フィールドにユーザーの名前を入力します。ユーザーがディレクトリ内にある場合は、入力している途中から、そのアカウントが表示されます。
6. 検索結果からユーザーを選択し、**[実行]** をクリックします。
7. **[OK]** をクリックして選択を保存します。選択したユーザーが一覧に表示されます。ロールは既定では一時的です。

  - ロールを永続的にする場合は、一覧のユーザーをクリックします。ユーザーの情報が新しいブレードに表示されます。ユーザー情報メニューで **[永続的にする]** を選択します。

8. [Azure AD Privileged Identity Management の使用](active-directory-privileged-identity-management-getting-started.md)に関する Azure のドキュメントへのリンクをユーザーに送信します。


## 別のユーザーに付与された、PIM を管理するためのアクセス権を削除する

特権ロール管理者ロールからユーザーを削除する際は、割り当てられているユーザーの人数が削除後も 2 人以上になるように、必ず事前に確認してください。

1. PIM ダッシュボードで、**[privileged role administrator]** (特権ロール管理者) ロールをクリックします。そのロールの現在のユーザー一覧が表示されます。
2. ユーザーの一覧でユーザーをクリックします。
3. **[削除]** をクリックします。確認メッセージが表示されます。
4. **[はい]** をクリックすると、ユーザーからロールが削除されます。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->