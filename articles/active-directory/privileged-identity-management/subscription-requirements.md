---
title: Privileged Identity Management を使用するためのライセンス要件 - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) を使用するためのライセンスの要件について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 04/05/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d0428f73c4df3d97002def03823ff8cced1d647
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552633"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Privileged Identity Management を使用するためのライセンスの要件

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使用するには、有効なライセンスがディレクトリになければいけません。 さらに、管理者と関連ユーザーにライセンスが割り当てられている必要があります。 この記事では、Privileged Identity Management を使用するためのライセンス要件について説明します。

## <a name="valid-licenses"></a>有効なライセンス

PIM とそのすべての設定を使用するには、[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)] が必要です。 現時点では、アクセス レビューのスコープを、Azure AD へのアクセス権を持つサービス プリンシパルと、テナントで Azure Active Directory Premium P2 エディションがアクティブになっている Azure リソース ロール (プレビュー) に設定できます。 サービス プリンシパルのライセンス モデルは、この機能の一般提供のために終了する予定です。このため、追加のライセンスが必要になる場合があります。

## <a name="licenses-you-must-have"></a>必要なライセンス

少なくとも、次のタスクを実行する従業員と同じ数の Azure AD Premium P2 ライセンスがディレクトリにあることを確認します。

- PIM を使用して管理された Azure AD または Azure ロールに対象として割り当てられたユーザー
- 資格のあるメンバーまたは特権アクセス グループの所有者として割り当てられたユーザー
- PIM でアクティブ化要求を承認または却下できるユーザー
- アクセス レビューに割り当てられたユーザー
- アクセス レビューを実行するユーザー

Azure AD Premium P2 ライセンスは、次のタスクでは必要 **ありません**。

- PIM の設定、ポリシーの構成、アラートの受信、アクセス レビューの設定を行うユーザーに、ライセンスは必要ありません。

ライセンスの詳細については、[Azure Active Directory ポータルを使用したライセンスの割り当てと削除](../fundamentals/license-users-groups.md)に関するページを参照してください。

## <a name="example-license-scenarios"></a>ライセンスのシナリオ例

必要なライセンス数の決定に役立つライセンスのシナリオ例をいくつか以下に示します。

| シナリオ | 計算 | ライセンス数 |
| --- | --- | --- |
| Woodgrove Bank には、各部門に 10 人の管理者がいて、PIM を構成および管理するグローバル管理者が 2 人います。 5 人の管理者を対象とします。 | 資格のある管理者用の 5 ライセンス | 5 |
| Graphic Design Institute には 25 人の管理者がいて、そのうちの 14 人は PIM で管理されています。 ロールのアクティブ化には承認が必要であり、組織にはアクティブ化を承認できるユーザーが 3 人います。 | 資格のあるロール用の 14 ライセンス + 3 承認者 | 17 |
| Contoso には 50 人の管理者がいて、そのうちの 42 人は PIM で管理されています。 ロールのアクティブ化には承認が必要であり、組織にはアクティブ化を承認できるユーザーが 5 人います。 また、Contoso では、管理者ロールに割り当てられているユーザーのレビューが毎月行われており、レビュー担当者はユーザーのマネージャーで、そのうちの 6 人は PIM によって管理される管理者ロールにはなっていません。 | 資格のあるロール用の 42 ライセンス + 5 承認者 + 6 レビュー担当者 | 53 |

## <a name="when-a-license-expires"></a>ライセンスの有効期限が切れた場合

Azure AD Premium P2、EMS E5、または試用版ライセンスの有効期限が切れた場合、Privileged Identity Management の機能はお使いのディレクトリで利用できなくなります。

- Azure AD ロールへの永続的なロールの割り当てには影響しません。
- 特権ロールのアクティブ化、特権アクセスの管理、または特権ロールのアクセス レビューの実行のために、Azure portal の Privileged Identity Management サービスと、Privileged Identity Management の Graph API コマンドレットおよび PowerShell インターフェイスを利用することはできなくなります。
- ユーザーが今後特権ロールをアクティブ化できなくなるため、Azure AD ロールの候補ロールの割り当ては削除されます。
- Azure AD ロールのすべての実行中のアクセス レビューが終了し、Privileged Identity Management の構成設定が削除されます。
- ロールの割り当てを変更しても、Privileged Identity Management から電子メールが送信されなくなります。

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management をデプロイする](pim-deployment-plan.md)
- [Privileged Identity Management の使用を開始する](pim-getting-started.md)
- [Privileged Identity Management で管理できないロール](pim-roles.md)
- [PIM で Azure リソース ロールのアクセス レビューを作成する](pim-resource-roles-start-access-review.md)
- [PIM で Azure AD ロールのアクセス レビューを作成する](pim-how-to-start-security-review.md)
