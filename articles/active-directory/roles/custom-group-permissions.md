---
title: Azure AD カスタム ロールに対するグループ管理アクセス許可 (プレビュー) - Azure Active Directory
description: Azure portal、PowerShell、または Microsoft Graph API での Azure AD カスタム ロールに対するグループ管理アクセス許可 (プレビュー)。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 10/26/2021
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.openlocfilehash: 5e768d9661a7030fb75d8089a6a49e9d5022d612
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057212"
---
# <a name="group-management-permissions-for-azure-ad-custom-roles-preview"></a>Azure AD カスタム ロールに対するグループ管理アクセス許可 (プレビュー)

> [!IMPORTANT]
> Azure AD カスタム ロールに対するグループ管理アクセス許可は、現在プレビュー段階です。
> ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

グループ管理アクセス許可を Azure Active Directory (Azure AD) のカスタム ロール定義で使用すると、次のようなきめ細かいアクセス権を付与できます。

- 名前や説明など、グループのプロパティを管理する
- メンバーと所有者を管理する
- グループを作成または削除する
- 監査ログを読み取る
- 特定の種類のグループを管理する

この記事では、さまざまなグループ管理シナリオのカスタム ロールで使用できるアクセス許可の一覧を示します。 カスタム ロールを作成する方法については、[カスタム ロールの作成と割り当て](custom-create.md)に関するページを参照してください。

> [!NOTE]
> Azureポータルを使用してグループスコープでカスタム役割を割り当てることは、現在、Azure AD Premium P1で **のみ** 可能です。

## <a name="how-to-interpret-group-management-permissions"></a>グループ管理アクセス許可を解釈する方法

グループ管理アクセス許可を解釈するには、さまざまなアクセス許可のサブタイプの意味を理解すると役立ちます。

> [!div class="mx-tableFixed"]
> | アクセス許可のサブタイプ | アクセス許可のサブタイプの説明 |
> | --- | --- |
> | groups | ロール割り当て可能なグループを除き、セキュリティ グループと Microsoft 365 グループを管理する |
> | groups.unified | ロール割り当て可能なグループを除き、メンバーシップ種類が動的と割り当て済みの両方の Microsoft 365 グループを管理する |
> | groups.unified.assignedMembership | ロール割り当て可能なグループを除き、メンバーシップの種類が割り当て済みのみの Microsoft 365 グループを管理する |
> | groups.security | ロール割り当て可能なグループを除き、メンバーシップ種類が動的と割り当て済みの両方のセキュリティ グループを管理する |
> | groups.security.assignedMembership | ロール割り当て可能なグループを除き、メンバーシップの種類が割り当て済みのみのセキュリティ グループを管理する |

次の表に、さまざまなサブタイプのグループ メンバーを更新するためのアクセス許可の例を示します。 

> [!div class="mx-tableFixed"]
> | アクセス許可の例 | アクセス許可の説明 |
> | --- | --- |
> | microsoft.directory/**groups**/members/update | ロールを割り当て可能なグループを除き、セキュリティ グループと Microsoft 365 グループのメンバーを更新する |
> | microsoft.directory/**groups.unified**/members/update | ロールを割り当て可能なグループを除き、Microsoft 365 グループのメンバーを更新する |
> | microsoft.directory/**groups.unified.assignedMembership**/members/update | ロール割り当て可能なグループを除き、メンバーシップの種類が割り当て済みである Microsoft 365 グループのメンバーを更新する |
> | microsoft.directory/**groups.security**/members/update | ロールを割り当て可能なグループを除き、セキュリティ グループのメンバーを更新する |
> | microsoft.directory/**groups.security.assignedMembership**/members/update | ロール割り当て可能なグループを除き、メンバーシップの種類が割り当て済みであるセキュリティ グループのメンバーを更新する |

## <a name="read-group-information"></a>グループ情報を読み取る

次のアクセス許可を使用すると、グループのプロパティ、メンバー、所有者を読み取ることができます。

> [!div class="mx-tableFixed"]
> | 権限 | 説明 |
> | ---------- | ----------- |
> | microsoft.directory/groups/allProperties/read | ロールを割り当て可能なグループを含む、セキュリティ グループと Microsoft 365 グループのすべてのプロパティ (特権プロパティを含む) を読み取る |
> | microsoft.directory/groups/standard/read | ロールを割り当て可能なグループを含め、セキュリティ グループと Microsoft 365 グループの標準プロパティを読み取る |
> | microsoft.directory/groups/members/read | ロールを割り当て可能なグループを含め、セキュリティ グループと Microsoft 365 グループのメンバーを読み取る |
> | microsoft.directory/groups/memberOf/read | ロールを割り当て可能なグループを含め、セキュリティ グループと Microsoft 365 グループの memberOf プロパティを読み取る |
> | microsoft.directory/groups/owners/read | ロールを割り当て可能なグループを含め、セキュリティ グループと Microsoft 365 グループの所有者を読み取る |

## <a name="create-groups"></a>グループの作成

次のアクセス許可を使用すると、さまざまな種類のグループを作成できます。

> [!div class="mx-tableFixed"]
> | 権限 | 説明 |
> | ---------- | ----------- |
> | microsoft.directory/groups/create | ロールを割り当て可能なグループを除き、セキュリティ グループと Microsoft 365 グループを作成する |
> | microsoft.directory/groups.unified/create | ロールを割り当て可能なグループを除き、Microsoft 365 グループを作成する |
> | microsoft.directory/groups.unified.assignedMembership/create | ロール割り当て可能なグループを除き、メンバーシップの種類が割り当て済みの Microsoft 365 グループを作成する |
> | microsoft.directory/groups.security/create | ロールを割り当て可能なグループを除き、セキュリティ グループを作成する |
> | microsoft.directory/groups.security.assignedMembership/create | ロール割り当て可能なグループを除き、メンバーシップの種類が割り当て済みのセキュリティ グループを作成する |
> | microsoft.directory/groups/createAsOwner | ロール割り当て可能なグループを除き、セキュリティ グループと Microsoft 365 グループを作成する。 作成者は最初の所有者として追加されます。 |
> | microsoft.directory/groups.unified/createAsOwner | ロール割り当て可能なグループを除き、Microsoft 365 グループを作成する。 作成者は最初の所有者として追加されます。 |
> | microsoft.directory/groups.unified.assignedMembership/createAsOwner | ロール割り当て可能なグループを除き、メンバーシップの種類が割り当て済みの Microsoft 365 グループを作成する。 作成者は最初の所有者として追加されます。 |
> | microsoft.directory/groups.security/createAsOwner | ロールを割り当て可能なグループを除き、セキュリティ グループを作成する 作成者は最初の所有者として追加されます。 |
> | microsoft.directory/groups.security.assignedMembership/createAsOwner | ロール割り当て可能なグループを除き、メンバーシップの種類が割り当て済みのセキュリティ グループを作成する。 作成者は最初の所有者として追加されます。 |

## <a name="update-group-information"></a>グループ情報を更新する

次のアクセス許可を使用すると、グループのプロパティとメンバーを更新できます。

> [!div class="mx-tableFixed"]
> | 権限 | 説明 |
> | ---------- | ----------- |
> | microsoft.directory/groups/allProperties/update | ロール割り当て可能なグループを除き、セキュリティ グループと Microsoft 365 グループのすべてのプロパティ (特権プロパティを含む) を更新する |
> | microsoft.directory/groups.unified/allProperties/update | ロール割り当て可能なグループを除き、Microsoft 365 グループのすべてのプロパティ (特権プロパティを含む) を更新する |
> | microsoft.directory/groups.unified.assignedMembership/allProperties/update | ロール割り当て可能なグループを除き、メンバーシップの種類が割り当て済みである Microsoft 365 グループのすべてのプロパティ (特権プロパティを含む) を更新する |
> | microsoft.directory/groups.security/allProperties/update | ロール割り当て可能なグループを除き、セキュリティ グループのすべてのプロパティ (特権プロパティを含む) を更新する |
> | microsoft.directory/groups.security.assignedMembership/allProperties/update | ロール割り当て可能なグループを除き、メンバーシップの種類が割り当て済みであるセキュリティ グループのすべてのプロパティ (特権プロパティを含む) を更新する |
> | microsoft.directory/groups/basic/update | ロールを割り当て可能なグループを除き、セキュリティ グループと Microsoft 365 グループの基本プロパティを更新する |
> | microsoft.directory/groups.unified/basic/update | ロールを割り当て可能なグループを除き、Microsoft 365 グループの基本プロパティを更新する |
> | microsoft.directory/groups.unified.assignedMembership/basic/update | ロール割り当て可能なグループを除き、メンバーシップの種類が割り当て済みである Microsoft 365 グループの基本プロパティを更新する |
> | microsoft.directory/groups.security/basic/update | ロールを割り当て可能なグループを除き、セキュリティ グループの基本プロパティを更新する |
> | microsoft.directory/groups.security.assignedMembership/basic/update | ロール割り当て可能なグループを除き、メンバーシップの種類が割り当て済みであるセキュリティ グループの基本プロパティを更新する |
> | microsoft.directory/groups/classification/update | ロールを割り当て可能なグループを除き、セキュリティ グループと Microsoft 365 グループの分類プロパティを更新する |
> | microsoft.directory/groups.unified/classification/update | ロール割り当て可能なグループを除き、Microsoft 365 グループの分類プロパティを更新する |
> | microsoft.directory/groups.unified.assignedMembership/classification/update | ロール割り当て可能なグループを除き、メンバーシップの種類が割り当て済みである Microsoft 365 グループの分類プロパティを更新する |
> | microsoft.directory/groups.security/classification/update | ロールを割り当て可能なグループを除き、セキュリティ グループの分類プロパティを更新する |
> | microsoft.directory/groups.security.assignedMembership/classification/update | ロール割り当て可能なグループを除き、メンバーシップの種類が割り当て済みであるセキュリティ グループの分類プロパティを更新する |
> | microsoft.directory/groups/dynamicMembershipRule/update | ロールを割り当て可能なグループを除き、セキュリティ グループと Microsoft 365 グループの動的メンバーシップ ルールを更新する |
> | microsoft.directory/groups.unified/dynamicMembershipRule/update | ロール割り当て可能なグループを除き、Microsoft 365 グループの動的メンバーシップ ルールを更新する |
> | microsoft.directory/groups.security/dynamicMembershipRule/update | ロール割り当て可能なグループを除き、セキュリティ グループの動的メンバーシップ ルールを更新する |
> | microsoft.directory/groups/members/update | ロールを割り当て可能なグループを除き、セキュリティ グループと Microsoft 365 グループのメンバーを更新する |
> | microsoft.directory/groups.unified/members/update | ロールを割り当て可能なグループを除き、Microsoft 365 グループのメンバーを更新する |
> | microsoft.directory/groups.unified.assignedMembership/members/update | ロール割り当て可能なグループを除き、メンバーシップの種類が割り当て済みである Microsoft 365 グループのメンバーを更新する |
> | microsoft.directory/groups.security/members/update | ロールを割り当て可能なグループを除き、セキュリティ グループのメンバーを更新する |
> | microsoft.directory/groups.security.assignedMembership/members/update | ロール割り当て可能なグループを除き、メンバーシップの種類が割り当て済みであるセキュリティ グループのメンバーを更新する |

## <a name="update-members-of-different-group-types"></a>さまざまなグループの種類のメンバーを更新する

次のアクセス許可を使用すると、さまざまなグループの種類のメンバーを更新できます。

> [!div class="mx-tableFixed"]
> | 権限 | 説明 |
> | ---------- | ----------- |
> | microsoft.directory/groups/members/update | ロールを割り当て可能なグループを除き、セキュリティ グループと Microsoft 365 グループのメンバーを更新する |
> | microsoft.directory/groups.unified/members/update | ロールを割り当て可能なグループを除き、Microsoft 365 グループのメンバーを更新する |
> | microsoft.directory/groups.unified.assignedMembership/members/update | ロール割り当て可能なグループを除き、メンバーシップの種類が割り当て済みである Microsoft 365 グループのメンバーを更新する |
> | microsoft.directory/groups.security/members/update | ロールを割り当て可能なグループを除き、セキュリティ グループのメンバーを更新する |
> | microsoft.directory/groups.security.assignedMembership/members/update | ロール割り当て可能なグループを除き、メンバーシップの種類が割り当て済みであるセキュリティ グループのメンバーを更新する |
 
## <a name="delete-groups"></a>グループを削除する

次のアクセス許可を使用すると、グループを削除できます。

> [!div class="mx-tableFixed"]
> | 権限 | 説明 |
> | ---------- | ----------- |
> | microsoft.directory/groups/delete | ロールを割り当て可能なグループを除き、セキュリティ グループと Microsoft 365 グループを削除する |
> | microsoft.directory/groups.unified/members/update | ロールを割り当て可能なグループを除き、Microsoft 365 グループのメンバーを更新する |
> | microsoft.directory/groups.unified.assignedMembership/members/update | ロール割り当て可能なグループを除き、メンバーシップの種類が割り当て済みである Microsoft 365 グループのメンバーを更新する |
> | microsoft.directory/groups.security/members/update | ロールを割り当て可能なグループを除き、セキュリティ グループのメンバーを更新する |
> | microsoft.directory/groups.security.assignedMembership/members/update | ロール割り当て可能なグループを除き、メンバーシップの種類が割り当て済みであるセキュリティ グループのメンバーを更新する |

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory でカスタム ロールを作成して割り当てる](custom-create.md)
- [Azure AD ロールの割り当てを一覧表示する](view-assignments.md)
