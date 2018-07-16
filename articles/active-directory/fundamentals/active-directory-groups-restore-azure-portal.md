---
title: Azure AD で削除された Office 365 グループを復元する | Microsoft Docs
description: Azure Active Directory で、削除されたグループを復元する方法、復元可能なグループを表示する方法、およびグループを完全に削除する方法
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 08/28/2017
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 621260f0af781799c72fbfaed0900a68383044a1
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860466"
---
# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>Azure Active Directory で削除された Office 365 グループを復元する

Azure Active Directory (Azure AD) で Office 365 グループを削除すると、削除されたグループは表示されなくなりますが、削除日から 30 日間は保持されます。 これは、必要に応じて、グループとその内容を復元できるようにするためです。 この機能は、Azure AD の Office 365 グループに限定されます。 セキュリティ グループや配布グループでは使用できません。

> [!NOTE] 
> `Remove-MsolGroup`は使用しないでください。グループが完全に消去されます。 O365 グループを削除する場合は、常に `Remove-AzureADMSGroup` を使用してください。 

グループを復元するために必要なアクセス許可は、次のいずれかになります。

Role | アクセス許可 
--------- | ---------
会社の管理者、パートナー レベル 2 のサポート、InTune サービス管理者 | 削除されたすべての Office 365 グループを復元できます。 
ユーザー アカウント管理者、パートナー レベル 1 のサポート | 会社の管理者ロールに割り当てられているグループを除き、削除されたすべての Office 365 グループを復元できます。 
User | ユーザーが所有する、削除されたすべての Office 365 グループを復元できます。 


## <a name="view-the-deleted-office-365-groups-that-are-available-to-restore"></a>復元可能な削除済み Office 365 グループを表示する
次のコマンドレットを使用すると、削除されたグループを表示して、目的のグループがまだ完全には削除されていないことを確認できます。 これらのコマンドレットは、[Azure AD PowerShell モジュール](https://www.powershellgallery.com/packages/AzureAD/)に含まれています。 このモジュールの詳細については、「[Azure Active Directory PowerShell Version 2 (Azure Active Directory PowerShell バージョン 2)](/powershell/azure/install-adv2?view=azureadps-2.0)」をご覧ください。

1.  次のコマンドレットを実行して、テナント内のまだ復元可能なすべての削除済み Office 365 グループを表示します。
 ```
 Get-AzureADMSDeletedGroup
 ```

2.  特定のグループの objectID がわかっている場合 (手順 1 のコマンドレットで objectID を取得できる場合) は、次のコマンドレットを実行して、削除された特定のグループがまだ完全には削除されていないことを確認できます。
 ```
 Get-AzureADMSDeletedGroup –Id <objectId>
 ```



## <a name="how-to-restore-your-deleted-office-365-group"></a>削除された Office 365 グループを復元する方法
グループがまだ復元可能であることを確認したら、次のいずれかの手順を実行して削除されたグループを復元します。 グループにドキュメント、SP サイト、または他の永続的なオブジェクトが含まれている場合、グループとその内容を完全に復元するまでに最大 24 時間かかることがあります。

1.  次のコマンドレットを実行して、グループとその内容を復元します。
 
 ```
 Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
 ``` 

また、次のコマンドレットを実行して、削除されたグループを完全に削除することもできます。
 ```
 Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
 ```

## <a name="how-do-you-know-this-worked"></a>グループが復元されたことを確認する方法
Office 365 グループが正常に復元されたことを確認するには、`Get-AzureADGroup –ObjectId <objectId>` コマンドレットを実行してそのグループの情報を表示します。 復元要求が完了すると、次のようになります。
- Exchange の左側のナビゲーション バーにグループが表示されます。
- Planner にグループのプランが表示されます。
- Sharepoint サイトとそのすべてのコンテンツを利用できるようになります。
- Exchange エンドポイントと、Office 365 グループをサポートする他の Office 365 ワークロードからグループにアクセスできます。


## <a name="next-steps"></a>次の手順
次の記事は、Azure Active Directory グループに関する追加情報を提供します。

* [既存のグループの表示](active-directory-groups-view-azure-portal.md)
* [グループの設定の管理](active-directory-groups-settings-azure-portal.md)
* [グループのメンバーの管理](active-directory-groups-members-azure-portal.md)
* [グループのメンバーシップの管理](active-directory-groups-membership-azure-portal.md)
* [グループ内のユーザーの動的ルールの管理](../users-groups-roles/groups-dynamic-membership.md)
