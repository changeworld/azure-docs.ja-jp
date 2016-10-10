<properties
   pageTitle="ユーザー ロールを追加または削除する方法 | Microsoft Azure"
   description="Azure Active Directory Privileged Identity Management アプリケーションで特権 ID にロールを追加する方法について説明します。"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/22/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management: ユーザー ロールを追加または削除する方法

Azure Active Directory (AD) では、グローバル管理者 (または会社の管理者) は、Azure AD でどのユーザーにロールを**永続的に**割り当てるかを変更できます。これを行うには、`Add-MsolRoleMember` や `Remove-MsolRoleMember` などの PowerShell コマンドレットを使用するか、「[Azure Active Directory (Azure AD) の管理者ロールの割り当て](active-directory-assign-admin-roles.md)」に記載されているように、Azure クラシック ポータルを使用できます。

Azure AD Privileged Identity Management (PIM) アプリケーションを使用すると、特権ロール管理者も、永続的なロールの割り当てを行うことができます。さらに、管理者は、ユーザーをロールの**候補**とすることで一時的なロールの割り当てを行うこともできます。管理者候補は必要なときにロールをアクティブ化できます。作業が完了すると、そのアクセス許可は期限切れになります。

## Azure ポータルで PIM を使用してロールを管理する

組織の管理者は、Azure AD や Office 365 などの Microsoft のサービスおよびアプリケーションで、異なる管理者ロールにユーザーを割り当てることができます。利用可能なロールの詳細については、[Azure PIM におけるロール](active-directory-privileged-identity-management-roles.md)に関するページを参照してください。

Privileged Identity Management を使用してロール内のユーザーを追加または削除するには、PIM ダッシュ ボードを表示します。次に、**[管理者ロールのユーザー]** ボタンをクリックするか、またはロール テーブルから特定のロール (グローバル管理者など) を選択します。

> [AZURE.NOTE] まだ Azure ポータルで PIM を有効にしていない場合は、「[Azure AD Privileged Identity Management の使用](active-directory-privileged-identity-management-getting-started.md)」を参照してください。

PIM 自体へのユーザー アクセス権を追加で付与する必要がある場合は、「[PIM へのアクセス権を付与する方法](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)」で、PIM にアクセスするユーザーに必要なロールを確認してください。

## ユーザーをロールに追加する

1. [Azure ポータル](https://portal.azure.com/)にサインインし、ダッシュボードの [**Azure AD Privileged Identity Management**] タイルをクリックします。
2. **[特権ロールの管理]** をクリックします。
3. **[ロールの概要]** テーブルから管理するロールを選択します。
4. [ロール] ブレードで **[追加]** をクリックします。
5. **[ユーザーを選択する]** をクリックして、**[ユーザーを選択する]** ブレードでユーザーを検索します。
6. 検索結果の一覧からユーザーを選択し、**[完了]** をクリックします。
4. **[OK]** をクリックして選択を保存します。選択したユーザーが、ロールの候補として一覧に表示されます。

> [AZURE.NOTE]
既定では、ロールに新しく追加されたユーザーは、そのロールのみの候補になります。ロールを永続的にする場合は、一覧のユーザーをクリックします。ユーザーの情報が新しいブレードに表示されます。ユーザー情報メニューで **[永続化]** を選択します。ユーザーが Azure Multi-Factor Authentication (MFA) に登録できない場合、または Microsoft アカウント (例: @outlook.com) を使用している場合は、すべてのロールでこのユーザーを永続化する必要があります。一時的な管理者は、アクティブ化時に MFA への登録が求められます。

これで、ユーザーに一時的なロールが割り当てられました。「[ロールをアクティブ化または非アクティブ化する方法](active-directory-privileged-identity-management-how-to-activate-role.md)」の指示に従ってロールをアクティブ化できることを、ユーザーに通知してください。

## ユーザーをロールから削除する

ロール候補の割り当てからユーザーを削除することはできますが、必ず、永続的なグローバル管理者が少なくとも 1 人は常に存在するようにしてください。

特定のユーザーをロールから削除するには、次の手順に従います。

1. Azure AD PIM ダッシュボードでロールを選択するか、**[管理者ロールのユーザー]** をクリックして、ロールの一覧内の対象のロールに移動します。
2. ユーザーの一覧でユーザーをクリックします。
3. **[削除]** をクリックします。確認を求めるメッセージが表示されます。
4. **[はい]** をクリックすると、ユーザーからロールが削除されます。

どのユーザーに引き続きロールの割り当てが必要であるかわからない場合は、[ロールのアクセス レビューを開始](active-directory-privileged-identity-management-how-to-start-security-review.md)してください。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0928_2016-->