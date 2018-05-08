---
title: グループの動的メンバーシップのトラブルシューティング | Microsoft Docs
description: Azure AD でのグループの動的メンバーシップ管理に関する問題について、トラブルシューティングのヒントを紹介します。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: it-pro
ms.openlocfilehash: 6d8d04273e9f29b2634c8b77b0268f3c7b77b1e9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>グループの動的メンバーシップのトラブルシューティング
**グループに対するルールを構成しましたが、グループのメンバーシップが更新されません**<br/>ルールに使用しているユーザー属性の値を確認し、そのルールを満たすユーザーが存在することを確認してください。 何も問題がなさそうな場合、グループが設定されるまでしばらく待ってください。 グループを初めて設定する場合、またはルールの変更後に設定する場合、テナントのサイズによっては最大 24 時間かかる場合があります。

**ルールの設定を変更したのですが、そのルールの既存のメンバーが削除されてしまいました**<br/>これは正しい動作です。 ルールを有効にしたり変更を加えたりするとグループの既存のメンバーは削除されます。 ルールの評価から返されたユーザーは、グループのメンバーとして追加されます。     

**ルールを追加または変更してもすぐにはメンバーシップの変更を確認できません。なぜでしょうか。**<br/>メンバーシップの評価に特化した機能が、非同期のバックグラウンド プロセスで定期的に実行されます。 プロセスにかかる時間は、ディレクトリ内のユーザー数と、ルールの結果として作成されるグループのサイズによって変わります。 ユーザー数の少ないディレクトリであれば通常、グループ メンバーシップの変更が数分以内に反映されます。 ディレクトリのユーザー数が多いと、変更が反映されるまでに 30 分以上かかる場合があります。

### <a name="next-steps"></a>次の手順
次の記事は、Azure Active Directory に関する追加情報を示します。

* [Azure Active Directory グループによるリソースのアクセス管理](active-directory-manage-groups.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)
* [Azure Active Directory とは](active-directory-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)
