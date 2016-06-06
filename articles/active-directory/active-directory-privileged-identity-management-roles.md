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
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management におけるロール

<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

Azure AD の別の管理者ロールに組織内のユーザーを割り当てることができます。これらのロール割り当てによって、ユーザーの追加や削除、サービス設定の変更などのタスクが制御され、ユーザーは Azure AD、Office 365、およびその他の Microsoft オンライン サービスと接続型アプリケーションを実行できます。

グローバル管理者は、`Add-MsolRoleMember` や `Remove-MsolRoleMember` などの PowerShell コマンドレットを使用して、または [Azure Active Directory の管理者ロールの割り当て](active-directory-assign-admin-roles.md)で説明されているとおりにクラシックポータルから、どのユーザーが**永続的に** Azure AD のロールに割り当てられているかを更新できます。

Azure AD Privileged Identity Management (PIM) は、Azure AD でのユーザーの特権アクセス用のポリシーを管理します。PIM によって、Azure AD の 1 つ以上のロールにユーザーが割り当てられます。これらの割り当ては永続的または一時的になります。ユーザーが永続的にロールに割り当てられるか、一時的なロールの割り当てを有効にすると、ユーザーは Azure Active Directory、Office 365、およびユーザーのロールにアクセス許可が割り当てられているその他のアプリケーションを管理できます。


## PIM で管理されるロール

Privileged Identity Management を使用して、以下を含む共通の管理者ロールにユーザーを割り当てることができます。


- **全体管理者** (会社の管理者とも呼ばれます) は、すべての管理機能にアクセスできます。組織内のグローバル管理者を複数にすることができます。Office 365 の購入にサインアップするユーザーが、自動的にグローバル管理者になります。
- **特権ロール管理者**は、Azure AD PIM を管理し、他のユーザーへのロールの割り当てを更新します。  
- **課金管理者**は、購入、サブスクリプションの管理、サポート チケットの管理、サービス正常性の監視を行います。
- **パスワード管理者**は、パスワードのリセット、サービス要求の管理、サービス正常性の監視を行います。パスワード管理者は、ユーザーのパスワードのリセットのみできます。
- **サービス管理者**は、サービス要求の管理とサービス正常性の監視を行います。

  > [AZURE.NOTE] Office 365 を使用している場合、サービス管理者ロールをユーザーに割り当てる前に、ユーザー管理権限を Exchange Online などのサービスに割り当ててください。

- **ユーザー管理の管理者**は、パスワードのリセット、サービス正常性の監視、ユーザー アカウント、ユーザー グループ、およびサービス要求の管理を行います。ユーザー管理の管理者は、グローバル管理者の削除、他の管理者ロールの作成、または課金管理者、グローバル管理者、サービス管理者のパスワードのリセットを行うことができません。
- **Exchange 管理者**は、Exchange 管理センター (EAC) を通じた Exchange Online への管理アクセスが可能で、Exchange Online でのほぼすべてのタスクを実行できます。
- **SharePoint 管理者**は、SharePoint Online 管理センターを通じた SharePoint Online への管理アクセスが可能で、SharePoint Online でのほぼすべてのタスクを実行できます。
- **Skype for Business 管理者**は、Skype for Business 管理センターを通じた Skype for Business への管理アクセスが可能で、Skype for Business Online でのほぼすべてのタスクを実行できます。

[Azure AD での管理者ロールの割り当て](active-directory-assign-admin-roles.md)および [Office 365 での管理者ロールの割り当て](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504)の詳細については、これらの記事を参照してください。

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


ユーザーが[必要なときにロールをアクティブ化](active-directory-privileged-identity-management-how-to-activate-role.md)できるように、PIM から[これらのロールを一時的にユーザーに割り当てる](active-directory-privileged-identity-management-how-to-add-role-to-user.md)ことができます。

PIM 自体で管理するためのアクセス権を別のユーザーに付与する場合に PIM でユーザーに必要なロールについては、[PIM へのアクセス権を付与する方法](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)に関する記事で詳しく説明されています。


<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## PIM で管理されないロール

Exchange Online または SharePoint Online 内のロールについて、上述以外のロールは Azure AD で表示されないため、PIM でも表示されません。これらの Office 365 サービスでのきめ細かなロールの割り当てを変更する方法の詳細については、「[Office 365 の権限](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d)」を参照してください。

Azure サブスクリプションとリソース グループも、Azure AD では表示されません。Azure サブスクリプションを管理するには「[Azure 管理者ロールを追加または変更する方法](../billing-add-change-azure-subscription-administrator.md)」を、Azure RBAC の詳細については、[Azure のロールベースのアクセス制御](role-based-access-control-configure.md)に関するページを参照してください。

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## ユーザーのロールとログイン
一部の Microsoft サービスとアプリケーションでは、ロールにユーザーを割り当てるだけでは、そのユーザーを管理者にするには十分でない場合があります。

Azure クラシック ポータルにアクセスするには、ユーザーは Azure サブスクリプションを管理する必要がない場合でも、Azure サブスクリプションのサービス管理者または共同管理者である必要があります。たとえば、クラシック ポータルで Azure AD の構成設定を管理するには、ユーザーは Azure AD のグローバル管理者と Azure サブスクリプションのサブスクリプション共同管理者の両方である必要があります。Azure サブスクリプションにユーザーを追加する方法については、「[Azure 管理者ロールを追加または変更する方法](../billing-add-change-azure-subscription-administrator.md)」を参照してください。

Microsoft Online Services へのアクセスでは、ユーザーがサービスのポータルを開いたり、管理タスクを実行したりできるようになるには、ユーザーにライセンスを割り当てる必要もある場合があります。

## Azure AD でのユーザーへのライセンスの割り当て

1. 全体管理者アカウントまたは共同管理者アカウントで、[Azure クラシック ポータル](http://manage.windowsazure.com)にサインインします。
2. メイン メニューから、**[すべてのアイテム]** を選択します。
3. ライセンスが関連付けられていて使用するディレクトリを選択します。
4. **[ライセンス]** を選択します。利用可能なライセンスの一覧が表示されます。
5. 配布するライセンスが含まれているライセンス プランを選択します。
6. **[ユーザーの割り当て]** を選択します。
7. ライセンスを割り当てるユーザーを選択します。
8. **[割り当て]** ボタンをクリックします。ユーザーは Azure にログインできるようになります。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->