---
title: "Azure Active Directory 内の Office 365 グループの有効期限 | Microsoft Docs"
description: "Azure Active Directory (プレビュー) で Office 365 グループの有効期限を設定する方法"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: c2dd56bd34e5b7845298fab1f36e231113a2e28e
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2017
---
# <a name="configure-expiration-for-office-365-groups-preview"></a>Office 365 グループの有効期限の構成 (プレビュー)

Office 365 グループの有効期限機能を設定して、そのライフサイクルを管理できるようになりました。 有効期限を設定できるのは、Azure Active Directory (Azure AD) 内の Office 365 グループのみです。 グループに有効期限を設定した場合:
-   有効期限が近づくと、グループの更新を促す通知がグループの所有者に送られます
-   更新されないグループはすべて削除されます
-   削除された Office 365 グループは、30 日以内であればグループの所有者または管理者が復元できます

> [!NOTE]
> Office 365 グループの有効期限を設定するには、有効期限の設定が適用されるグループの全メンバーに、Azure AD Premium ライセンスまたは Azure AD Basic EDU ライセンスが必要です。
> 
> Azure AD Basic EDU ライセンスのお客様の場合: このポリシーを初めて構成する際は、Azure Active Directory PowerShell コマンドレットを使用してください。 その後、Azure AD テナント内のユーザー アカウント管理者またはグローバル管理者であるアカウントを使用して、PowerShell と Azure AD ポータルのいずれかで有効期限の設定を更新できます。

Azure AD PowerShell コマンドレットをダウンロードしてインストールする方法については、「[Azure Active Directory PowerShell for Graph - Public Preview Release 2.0.0.137 (Azure Active Directory PowerShell for Graph - パブリック プレビュー リリース 2.0.0.137)](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)」を参照してください。

## <a name="set-group-expiration"></a>グループの有効期限の設定

1. Azure AD テナントのグローバル管理者のアカウントを使用して、[Azure AD 管理センター](https://aad.portal.azure.com) を開きます。

2. Azure AD を開き、**[ユーザーとグループ]** を選択します。

3. **[グループ設定]** を選択し、**[有効期限]** を選択して有効期限の設定画面を開きます。
  
  ![[有効期限] ブレード](./media/active-directory-groups-lifecycle-azure-portal/expiration-settings.png)

4. **[有効期限]** ブレードで、次のことができます。

  * グループの有効期間を日数で設定する。 プリセット値かカスタム値 (31 日以上である必要があります) のいずれかを選択する。 
  * グループに所有者がいない場合に、更新と有効期限の通知を送信する電子メール アドレスを指定する。 
  * どの Office 365 グループを有効期限切れにするかを選択する。 **[すべて]** の Office 365 グループに対して有効期限を有効にするか、Office 365 グループの中から選択するか、**[なし]** を選択してすべてのグループに対して有効期限を無効にすることができます。
  * **[保存]** を選択して完了し、設定を保存する。


次のような電子メール通知が、グループの有効期限の 30 日前、15 日前、1 日前に Office 365 グループの所有者に送信されます。

![有効期限に関する電子メール通知](./media/active-directory-groups-lifecycle-azure-portal/expiration-notification.png)

グループの所有者は、**[Renew group]\(グループの更新\)** を選択して Office 365 グループを更新できます。または **[Go to group]\(グループに移動\)** を選択してグループのメンバーとその他の詳細を確認できます。

グループが有効期限切れになると、有効期限日の 1 日後にグループが削除されます。 次のような電子メール通知が Office 365 グループの所有者に送信され、その Office 365 グループの有効期限とその後の削除について通知されます。

![グループの削除に関する電子メール通知](./media/active-directory-groups-lifecycle-azure-portal/deletion-notification.png)

**[Restore group]\(グループの復元\)** を選択するか、または「Azure Active Directory で削除された Office 365 グループを復元する」 (https://docs.microsoft.com/azure/active-directory/active-directory-groups-restore-azure-portal) に記載されているように PowerShell コマンドレットを使用することで、グループを復元できます。
    
復元するグループにドキュメント、SharePoint サイト、または他の永続的なオブジェクトが含まれている場合は、グループとその内容を完全に復元するまでに最大 24 時間かかることがあります。

> [!NOTE]
> * 有効期限を初めて設定する場合、有効期限の間隔よりも古いグループは、有効期限まで残り 30 日に設定されます。 最初の更新に関する通知電子メールは 1 日以内に送信されます。 
>   たとえば、グループ A は 400 日前に作成され、有効期限の間隔は 180 日に設定されているとします。 有効期限の設定を適用すると、グループ A は所有者が更新しないかぎりは削除まで 30 日に設定されます。
> * 動的なグループを削除し復元する場合、そのグループは新しいグループとみなされ、規則に従って再度追加されます。 このプロセスには最大で 24 時間かかることがあります。

## <a name="next-steps"></a>次のステップ
次の記事では、Azure AD グループに関する追加情報が提供されています。

* [既存のグループの表示](active-directory-groups-view-azure-portal.md)
* [グループの設定の管理](active-directory-groups-settings-azure-portal.md)
* [グループのメンバーの管理](active-directory-groups-members-azure-portal.md)
* [グループのメンバーシップの管理](active-directory-groups-membership-azure-portal.md)
* [グループ内のユーザーの動的ルールの管理](active-directory-groups-dynamic-membership-azure-portal.md)
