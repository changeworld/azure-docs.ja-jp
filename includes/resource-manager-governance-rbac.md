---
title: インクルード ファイル
description: インクルード ファイル
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: b4b06119b9d46781b967fc8d98808c60d2b41ccb
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38753673"
---
組織のユーザーがこれらのリソースへの適切なアクセス レベルを持つようにします。 ユーザーに無制限のアクセス権を許可したくはありませんが、ユーザーが自分の作業を実行できるようにすることも必要です。 ロールベースのアクセス制御 (RBAC) を使うと、あるスコープで特定のアクションを実行するアクセス許可を持つユーザーを管理することができます。 ロールには、許可される一連のアクションを定義します。 ロールをスコープに割り当て、スコープのそのロールに属するユーザーを指定します。

アクセス制御戦略を計画する場合は、ユーザーの作業を実行できる最低限の特権をユーザーに付与します。 次の図は、RBAC 割り当てパターン例です。

![Scope (スコープ)](./media/resource-manager-governance-rbac/role-examples.png)

すべてのリソースに適用される 3 つの基本的なロール (所有者、共同作成者、閲覧者) があります。 所有者ロールに割り当てるアカウントは厳格に管理し、ほとんど使用しないことをお勧めします。 ソリューションの状態を監視する特権のみが必要なユーザーには、閲覧者ロールを付与することをお勧めします。

ほとんどのユーザーには、サブスクリプション レベルまたはリソース グループ レベルで、[リソース固有のロール](../articles/role-based-access-control/built-in-roles.md)または[カスタム ロール](../articles/role-based-access-control/custom-roles.md)を付与します。 これらのロールには、許可するアクションを厳格に定義します。 ユーザーをこれらのロールに割り当てると、高すぎる特権を許可することなく、必要なアクセス権をユーザーに付与することができます。 1 つのアカウントを複数のロールに割り当てることができます。この場合、ユーザーには複数のロールを組み合わせたアクセス許可が付与されます。 リソース レベルでアクセス権を付与すると、多くの場合、ユーザーの制限が厳しくなりすぎますが、特定のタスク向けに設計された自動プロセスには役立つ場合もあります。

### <a name="who-can-assign-roles"></a>ロールを割り当てることができるユーザー

ロールの割り当てを作成および削除するには、`Microsoft.Authorization/roleAssignments/*` アクセス権が必要です。 このアクセス権は、所有者ロールまたはユーザー アクセス管理者ロールを通じて許可されます。