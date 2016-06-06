<properties
   pageTitle="ユーザー ロールを追加または削除する方法 | Microsoft Azure"
   description="Azure Active Directory Privileged Identity Management アプリケーションで特権 ID にロールを追加する方法について説明します。"
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

# Azure AD Privileged Identity Management: ユーザー ロールを追加または削除する方法

Azure Active Directory (AD) では、グローバル管理者 (または会社の管理者) は、Azure AD でどのユーザーにロールを**永続的に**割り当てるかを変更できます。これを行うには、`Add-MsolRoleMember` や `Remove-MsolRoleMember` などの PowerShell コマンドレットを使用するか、「[Azure Active Directory (Azure AD) の管理者ロールの割り当て](active-directory-assign-admin-roles.md)」に記載されているように、Azure クラシック ポータルを使用できます。

Azure AD Privileged Identity Management (PIM) アプリケーションを使用すると、特権ロール管理者も、永続的なロールの割り当てを行うことができます。ただし、このアプリケーションでは、ロールの**一時的な**割り当ての候補者を追加または削除することもできます。候補者は必要なときにロールをアクティブ化できます。作業が完了すると、そのアクセス許可は期限切れになります。

## Azure ポータルで PIM を使用してロールを管理する

組織の管理者は、Azure AD で別の管理者ロールにユーザーを割り当てることができます。このロール割り当てによって、ユーザーの追加や削除、サービス設定の変更などのタスクが制御され、割り当てられたユーザーは Azure AD や Office 365 などの Microsoft のサービスやアプリケーションを実行できるようになります。利用可能なロールの詳細については、[Azure PIM におけるロール](active-directory-privileged-identity-management-roles.md)に関するページを参照してください。

Privileged Identity Management を使用してロールへのユーザーの追加やロールからのユーザーの削除を行うには、PIM ダッシュボードを表示し、**[管理者ロールのユーザー]** をクリックするか、ロールのテーブルから特定のロール (グローバル管理者など) を選択します。

> [AZURE.NOTE] まだ Azure ポータルで PIM を有効にしていない場合は、「[Azure AD Privileged Identity Management の使用](active-directory-privileged-identity-management-getting-started.md)」を参照してください。

PIM 自体へのユーザー アクセス権を追加で付与する必要がある場合は、「[PIM へのアクセス権を付与する方法](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)」で、PIM にアクセスするユーザーに必要なロールを確認してください。

## ユーザーをロールに追加する
Azure AD PIM ダッシュボードでロールを選択するか、**[管理者ロールのユーザー]** をクリックして、ロール ブレードに移動した後、次の手順に従います。

1. **[追加]** をクリックします。
  - ロール テーブルでユーザー ロールをクリックしてここに移動した場合、ロールは既に選択されています。  
  - **[ロールを選択する]** をクリックし、ロールの一覧からロールを選択します。たとえば、**[パスワード管理者]** を選択します。
2. **[ユーザーを選択する]** ブレードでユーザーを検索します。ユーザーがディレクトリ内にある場合は、入力している途中から、そのアカウントが表示されます。
3. 検索結果の一覧からユーザーを選択し、**[実行]** をクリックします。
4. **[OK]** をクリックして選択を保存します。選択したユーザーが一覧に表示されます。ロールは既定では一時的です。

  >[AZURE.NOTE] ロールを永続的にする場合は、一覧のユーザーをクリックします。ユーザーの情報が新しいブレードに表示されます。ユーザー情報メニューで **[永続的にする]** を選択します。ユーザーが Azure Multi-Factor Authentication (MFA) に登録できないか、Microsoft アカウントを使用している場合は、これを行う必要があります。一時的な管理者は、アクティブ化時に MFA への登録が求められます。

これで、ユーザーに一時的なロールが割り当てられました。「[ロールをアクティブ化または非アクティブ化する方法](active-directory-privileged-identity-management-how-to-activate-role.md)」の指示に従ってロールをアクティブ化できることをユーザーに通知してください。

## ユーザーをロールから削除する

一時的なロールの割り当てからユーザーを削除することはできますが、必ず、永続的なグローバル管理者が少なくとも 1 人は常に存在するようにしてください。

特定のユーザーをロールから削除するには、次の手順に従います。

1. Azure AD PIM ダッシュボードでロールを選択するか、**[管理者ロールのユーザー]** をクリックして、ロールの一覧内の対象のロールに移動します。
2. ユーザーの一覧でユーザーをクリックします。
3. **[削除]** をクリックします。確認を求めるメッセージが表示されます。
4. **[はい]** をクリックすると、ユーザーからロールが削除されます。

どのユーザーに引き続きロールの割り当てが必要であるかわからない場合は、[ロールのセキュリティ レビューを開始](active-directory-privileged-identity-management-how-to-start-security-review.md)できます。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->