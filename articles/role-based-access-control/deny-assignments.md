---
title: Azure RBAC の拒否割り当てについて | Microsoft Docs
description: Azure のリソースに対するロールベースのアクセス制御 (RBAC) の拒否割り当てについて説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 8ef3a2ec44c5eff80d3a50a6c56805667e164ba8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980170"
---
# <a name="understand-deny-assignments"></a>拒否割り当てについて

ロールの割り当てと同様に、*拒否割り当て*ではアクセスの拒否を目的として、特定のスコープでユーザー、グループ、またはサービス プリンシパルに一連の拒否アクションがバインドされます。 さらに拒否割り当てでは、プリンシパルを除外できるほか、子スコープへの継承を避けることができます。これらの点は、ロールの割り当てと異なります。 現在、拒否割り当ては**読み取り専用**であり、Azure によってのみ設定されます。 この記事では、拒否割り当てがどのように定義されるかについて説明します。

## <a name="deny-assignment-properties"></a>拒否割り当てのプロパティ

 拒否割り当てには、以下のプロパティがあります。

> [!div class="mx-tableFixed"]
> | プロパティ | 必須 | type | 説明 |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | [はい] | String | 拒否割り当ての表示名。 名前は、指定のスコープで一意である必要があります。 |
> | `Description` | いいえ  | String | 拒否割り当ての説明。 |
> | `Permissions.Actions` | 少なくとも 1 つの Actions または DataActions | String[] | 拒否割り当てによってアクセスがブロックされる管理操作を指定する文字列の配列。 |
> | `Permissions.NotActions` | いいえ  | String[] | 拒否割り当てから除外される管理操作を指定する文字列の配列。 |
> | `Permissions.DataActions` | 少なくとも 1 つの Actions または DataActions | String[] | 拒否割り当てによってアクセスがブロックされるデータ操作を指定する文字列の配列。 |
> | `Permissions.NotDataActions` | いいえ  | String[] | 拒否割り当てから除外されるデータ操作を指定する文字列の配列。 |
> | `Scope` | いいえ  | String | 拒否割り当てが適用されるスコープを指定する文字列。 |
> | `DoNotApplyToChildScopes` | いいえ  | Boolean | 拒否割り当てが子スコープに適用されるかどうかを指定します。 既定値は false です。 |
> | `Principals[i].Id` | [はい] | String[] | 拒否割り当てが適用される Azure AD プリンシパル オブジェクト ID (ユーザー、グループ、またはサービス プリンシパル) の配列。 すべてのユーザー、グループ、サービス プリンシパルを表すために空の GUID `00000000-0000-0000-0000-000000000000` に設定されます。 |
> | `Principals[i].Type` | いいえ  | String[] | Principals[i].Id によって表されるオブジェクトの種類の配列。すべてのユーザー、グループ、サービス プリンシパルを表すために `Everyone` に設定されます。 |
> | `ExcludePrincipals[i].Id` | いいえ  | String[] | 拒否割り当てが適用されない Azure AD プリンシパル オブジェクト ID (ユーザー、グループ、またはサービス プリンシパル) の配列。 |
> | `ExcludePrincipals[i].Type` | いいえ  | String[] | ExcludePrincipals[i].Id によって表されるオブジェクトの種類の配列。 |
> | `IsSystemProtected` | いいえ  | Boolean | この拒否割り当てが Azure によって作成されたものかどうか、およびこの拒否割り当てを編集または削除できるかどうかを指定します。 現在、すべての拒否割り当てはシステムによって保護されています。 |

## <a name="everyone-principal"></a>Everyone プリンシパル

拒否割り当てをサポートするために、Everyone プリンシパルが導入されました。 Everyone プリンシパルは、Azure AD ディレクトリ内のすべてのユーザー、グループ、サービス プリンシパルを表します。 プリンシパル ID がゼロ GUID `00000000-0000-0000-0000-000000000000` で、プリンシパルの種類が `Everyone` の場合、プリンシパルはすべてのユーザー、グループ、サービス プリンシパルを表します。 Everyone プリンシパルは、すべての拒否から一部のユーザーを除外するために、`ExcludePrincipals` と組み合わせることができます。 Everyone プリンシパルには以下の制約があります。

- `Principals` でのみ使用することができ、`ExcludePrincipals` では使用できません。
- `Principals[i].Type` は `Everyone` に設定する必要があります。

## <a name="next-steps"></a>次の手順

* [RBAC と REST API を使用して拒否割り当てを一覧表示する](deny-assignments-rest.md)
* [ロール定義について](role-definitions.md)
