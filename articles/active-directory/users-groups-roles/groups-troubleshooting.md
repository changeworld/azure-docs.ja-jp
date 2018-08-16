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
ms.date: 08/01/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 82a5c57ce874e77a7912945a6fca07acee339197
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444489"
---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>グループの動的メンバーシップのトラブルシューティング

**グループに対するルールを構成しましたが、グループのメンバーシップが更新されません**<br/>ルールに使用しているユーザー属性の値を確認し、そのルールを満たすユーザーが存在することを確認してください。 何も問題がなさそうな場合、グループが設定されるまでしばらく待ってください。 グループを初めて設定する場合、またはルールの変更後に設定する場合、テナントのサイズによっては最大 24 時間かかる場合があります。

**ルールの設定を変更したのですが、そのルールの既存のメンバーが削除されてしまいました**<br/>これは正しい動作です。 ルールを有効にしたり変更を加えたりするとグループの既存のメンバーは削除されます。 ルールの評価から返されたユーザーは、グループのメンバーとして追加されます。

**ルールを追加または変更してもすぐにはメンバーシップの変更を確認できません。なぜでしょうか。**<br/>メンバーシップの評価に特化した機能が、非同期のバックグラウンド プロセスで定期的に実行されます。 プロセスにかかる時間は、ディレクトリ内のユーザー数と、ルールの結果として作成されるグループのサイズによって変わります。 ユーザー数の少ないディレクトリであれば通常、グループ メンバーシップの変更が数分以内に反映されます。 ディレクトリのユーザー数が多いと、変更が反映されるまでに 30 分以上かかる場合があります。

**ルール処理エラーが発生しました**<br/>次の表は、一般的な動的メンバーシップ ルール エラーとその修正方法をまとめたものです。

| ルール パーサー エラー | 間違った使用法 | 正しい使用法 |
| --- | --- | --- |
| エラー: 属性がサポートされていません。 |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>該当する属性が、[サポートされているプロパティ一覧](groups-dynamic-membership.md#supported-properties)に記載されていることを確認してください。 |
| エラー: 属性で演算子がサポートされていません。 |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/><br/>プロパティの型に対してサポートされていない演算子が使用されています (この例では、-contains をブール型で使用することはできません)。 プロパティの型に合った適切な演算子を使用してください。 |
| エラー: クエリ コンパイル エラー。 | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2.  (user.userPrincipalName -match "*@domain.ext") | 1.演算子が不足しています。 結合述語を 2 つ使用してください (-and または -or)。<br>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br>2.-match に使用されている正規表現に誤りがあります。<br>(user.userPrincipalName -match ".*@domain.ext")<br>あるいは: (user.userPrincipalName -match "@domain.ext$") |

## <a name="next-steps"></a>次の手順

次の記事は、Azure Active Directory に関する追加情報を示します。

* [Azure Active Directory グループによるリソースへのアクセス管理](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory のアプリケーション構成の管理に関する記事の索引](../active-directory-apps-index.md)
* [Azure Active Directory とは](../fundamentals/active-directory-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](../connect/active-directory-aadconnect.md)
