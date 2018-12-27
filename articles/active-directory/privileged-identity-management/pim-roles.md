---
title: PIM で管理できる Azure AD のディレクトリ ロール | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で管理できる Azure AD ディレクトリ ロールについて説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 07/23/2018
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: 531d19925d24930b6a2bd642a8ff0ec55bd6d16f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52841576"
---
# <a name="azure-ad-directory-roles-you-can-manage-in-pim"></a>PIM で管理できる Azure AD のディレクトリ ロール
<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

Azure AD の別の管理者ロールに組織内のユーザーを割り当てることができます。 これらのロール割り当てによって、ユーザーの追加や削除、サービス設定の変更などのタスクが制御され、ユーザーは Azure AD、Office 365、およびその他の Microsoft オンライン サービスと接続型アプリケーションを実行できます。  

全体管理者は、[Azure Active Directory の管理者ロールの割り当て](../users-groups-roles/directory-assign-admin-roles.md)で説明されているようにポータルから、または [PowerShell のコマンド](/powershell/module/azuread#directory_roles)を使用して、どのユーザーが**永続的に** Azure AD のロールに割り当てられているかを更新できます。

Azure AD Privileged Identity Management (PIM) は、Azure AD でのユーザーの特権アクセス用のポリシーを管理します。 PIM は、Azure AD で 1 つ以上のロールにユーザーを割り当てます。あるユーザーをロールに永続的に割り当てることも、ロールの候補にすることもできます。 ユーザーが永続的にロールに割り当てられるか、ロールの候補としての割り当てを有効にすると、ユーザーは Azure Active Directory、Office 365、およびユーザーのロールにアクセス許可が割り当てられているその他のアプリケーションを管理できます。

ロールへの永続的な割り当てと、ロールの候補としての割り当てに、アクセスの違いはありません。 常時のアクセスを必要としないユーザーがいる、というのが唯一の違いです。 その場合はロールの候補として、必要な場合にアクセスをオンにしたりオフにしたりできます。

## <a name="roles-managed-in-pim"></a>PIM で管理されるロール
Privileged Identity Management を使用して、以下を含む共通の管理者ロールにユーザーを割り当てることができます。

* **全体管理者** (会社の管理者とも呼ばれます) は、すべての管理機能にアクセスできます。 組織内のグローバル管理者を複数にすることができます。 Office 365 の購入にサインアップするユーザーが、自動的にグローバル管理者になります。
* **特権ロール管理者**は、Azure AD PIM を管理し、他のユーザーへのロールの割り当てを更新します。  
* **課金管理者**は購入、サブスクリプションの管理、サポート チケットの管理、サービス正常性の監視を行います。
* **パスワード管理者**はパスワードのリセット、サービス要求の管理、サービス正常性の監視を行います。 パスワード管理者は、ユーザーのパスワードのリセットのみできます。
* **サービス管理者**はサービス要求の管理とサービス正常性の監視を行います。
  
  > [!NOTE]
  > Office 365 を使用している場合、サービス管理者ロールをユーザーに割り当てる前に、ユーザー管理権限を Exchange Online などのサービスに割り当ててください。
  > 
  > 
* **ユーザー管理者**はパスワードのリセット、サービス正常性の監視、ユーザー アカウント、ユーザー グループ、サービス要求の管理を行います。 ユーザー管理者は、グローバル管理者の削除、他の管理者ロールの作成、課金管理者、グローバル管理者、サービス管理者のパスワードのリセットを行うことができません。
* **Exchange 管理者**は、Exchange 管理センター (EAC) を通じた Exchange Online への管理アクセスが可能で、Exchange Online でのほぼすべてのタスクを実行できます。
* **SharePoint サービス管理者**は、SharePoint Online 管理センターを通じた SharePoint Online への管理アクセスが可能で、SharePoint Online でのほぼすべてのタスクを実行できます。 対象となるユーザーは、PIM でアクティブにした後に SharePoint 内でこのロールを使用すると、遅延が発生する場合があります。
* **Skype for Business 管理者**は、Skype for Business 管理センターを通じた Skype for Business への管理アクセスが可能で、Skype for Business Online でのほぼすべてのタスクを実行できます。

[Azure AD での管理者ロールの割り当て](../users-groups-roles/directory-assign-admin-roles.md)および [Office 365 での管理者ロールの割り当て](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504)の詳細については、これらの記事を参照してください。

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


ユーザーが[必要なときにロールをアクティブ化](pim-how-to-activate-role.md)できるように、PIM から[これらのロールをユーザーに割り当てる](pim-how-to-add-role-to-user.md)ことができます。

PIM 自体で管理するためのアクセス権を別のユーザーに付与する場合に PIM でユーザーに必要なロールについては、 [PIM へのアクセス権を付与する方法](pim-how-to-give-access-to-pim.md)に関する記事で詳しく説明されています。

<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>PIM で管理されないロール
Exchange Online または SharePoint Online 内のロールについて、上述以外のロールは Azure AD で表示されないため、PIM でも表示されません。 これらの Office 365 サービスでのきめ細かなロールの割り当てを変更する方法の詳細については、「 [Office 365 の権限](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d)」を参照してください。

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>ユーザーのロールとサインイン
一部の Microsoft サービスとアプリケーションでは、ロールにユーザーを割り当てるだけでは、そのユーザーを管理者にするには十分でない場合があります。

Azure Portal にアクセスするには、ユーザーは Azure サブスクリプションを管理する必要がない場合でも、Azure サブスクリプションの所有者である必要があります。  たとえば、Azure AD の構成設定を管理するには、ユーザーは Azure AD のグローバル管理者と Azure サブスクリプションの所有者の両方である必要があります。  Azure サブスクリプションにユーザーを追加する方法については、「[RBAC と Azure portal を使用してアクセスを管理する](../..//role-based-access-control/role-assignments-portal.md)」を参照してください。

Microsoft Online Services へのアクセスでは、ユーザーがサービスのポータルを開いたり、管理タスクを実行したりできるようになるには、ユーザーにライセンスを割り当てる必要もある場合があります。

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Azure AD でのユーザーへのライセンスの割り当て

1. [Azure portal](https://portal.azure.com) にグローバル管理者または所有者ロールでサインインします。

1. ライセンスが関連付けられている、対象の Azure AD ディレクトリを選択します。

1. 左側のナビゲーションで、**[Azure Active Directory]** をクリックします。

1. **[ライセンス]** をクリックします。 利用可能なライセンスの一覧が表示されます。

    ![Azure Active Directory ライセンス](./media/pim-roles/licenses-overview.png)

1. **[製品]** をクリックします。

1. 配布するライセンスが含まれているライセンス プランをクリックします。

    ![[ライセンス] - [製品]](./media/pim-roles/licenses-products.png)

1. **[割り当て]** をクリックして [ライセンスの割り当て] ウィンドウを開きます。

    ![ライセンスされているユーザー](./media/pim-roles/licenses-licensed-users.png)

1. ライセンスを割り当てるユーザーまたはグループを選択します。

    ![ライセンスの割り当て](./media/pim-roles/licenses-assign-license.png)

1. **[割り当てオプション]** をクリックし、割り当てオプションを構成します。

    ![割り当てオプション](./media/pim-roles/licenses-assignment-options.png)

1. **[割り当て]** をクリックしてライセンスを割り当てます。 ユーザーにライセンスが付与されます。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>次の手順

- [PIM の使用を開始する](pim-getting-started.md)
- [PIM で Azure AD ディレクトリ ロールを割り当てる](pim-how-to-add-role-to-user.md)

