<properties
   pageTitle="PIM におけるロール |Microsoft Azure"
   description="Azure Privileged Identity Management 拡張機能で特権 ID に使用されるロールについて説明します。"
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
   ms.date="03/17/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management: ロール

<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

## Azure Active Directory、Office 365、その他のソースからのロール

Azure Privileged Identity Management (PIM) は、既定の管理者ロールとして次のロールを使用します。

- グローバル管理者
- 課金管理者
- サービス管理者
- ユーザー管理者
- パスワード管理者

Office 365、Exchange Online、SharePoint Online、Skype for Business のロールの詳細については、「[Office 365 での管理者ロールの割り当て](https://support.office.com/ja-JP/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504?ui=ja-JP&rs=ja-JP&ad=US)」を参照してください。

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## ユーザーのロールとログイン
> [AZURE.NOTE]ユーザーが Azure PIM にサインインするには、Azure のライセンスが必要です。

## Azure AD でのユーザーへのライセンスの割り当て

> [AZURE.NOTE] ライセンス オプションは、そのサブスクリプションに対してライセンスが実際に存在する場合にのみ表示されます。

1. 全体管理者アカウントまたは共同管理者アカウントで、[http://manage.windowsazure.com](http://manage.windowsazure.com) にサインインします。
2. メイン メニューの **[すべての項目]** をクリックします。
3. ライセンスが関連付けられていて使用するディレクトリを選択します。
4. **[ライセンス]** をクリックします。利用可能なライセンスの一覧が表示されます。
5. 配布するライセンスが含まれているライセンス プランをクリックします。
6. **[ユーザーの割り当て]** をクリックします。
7. ライセンスを割り当てるユーザーを選択します。
8. **[割り当て]** ボタンをクリックします。ユーザーは Azure にログインできるようになります。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0323_2016-->