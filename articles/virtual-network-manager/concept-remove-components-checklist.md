---
title: Azure Virtual Network Manager プレビュー コンポーネントの削除のチェックリスト
description: この記事は、Azure Virtual Network Manager 内のコンポーネントを削除するためのチェックリストです。
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 65987c576ff16d766cb8da88ec824f0bb5bdd40a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131089742"
---
# <a name="remove-and-update-azure-virtual-network-manager-preview-components-checklist"></a>Azure Virtual Network Manager プレビュー コンポーネントの削除および更新のチェックリスト

この記事では、Azure Virtual Network Manager プレビューの構成コンポーネントを削除または更新するために必要な手順のチェックリストを紹介します。

## <a name="remove-components-checklist"></a><a name="remove"></a>コンポーネント削除のチェックリスト

| 操作 | 手順 | 
| ------ | ----- |
| 接続構成の展開を展開解除する | ターゲット リージョンに **[なし]** の接続構成をデプロイします。 |
| セキュリティ管理者構成の展開を展開解除する | ターゲット リージョンに **[なし]** の接続構成をデプロイします。 |
| セキュリティ管理者ルールを削除する | 1. セキュリティ管理者構成の展開を展開解除します。 </br> 2. セキュリティ構成に関連付けられているセキュリティ管理規則を削除します。 |
| セキュリティ規則コレクションを削除する | 1. セキュリティ管理者構成の展開を展開解除します。 </br> 2. セキュリティ構成に関連付けられているセキュリティ管理規則を削除します。 </br> 3. 規則コレクションを削除します。 |
| セキュリティ管理者の構成を削除する | 1. セキュリティ管理者構成の展開を展開解除します。 </br> 2. セキュリティ構成に関連付けられているセキュリティ管理規則を削除します。 </br> 3. 規則コレクションを削除します。 </br> 4. セキュリティ管理者の構成を削除します。 |
| 接続構成を削除する | 1. 接続構成の展開を展開解除します。 </br> 2. 接続構成を削除します。 |
| ネットワーク グループを削除する | 1. このネットワーク グループに関連付けられている接続構成を削除します。 </br> 2. ネットワーク グループに関連付けられているすべてのセキュリティ規則を削除します。 </br> 3. ネットワーク グループを削除します。 |
| Azure Virtual Network Manager インスタンスを削除する | 1. すべての接続とセキュリティ管理者の構成を削除します。 </br> 2. すべてのネットワーク グループを削除します。 </br> 3. Azure Virtual Network Manager インスタンスを削除します。 |

## <a name="update-components-checklist"></a>コンポーネントのチェックリストを更新する

この表に記載されている情報は、ターゲット リージョンに展開された接続またはセキュリティ管理者の構成があることを前提としています。

| 操作 | 手順 |
| ------ | ----- |
| 静的メンバーシップを使用して仮想ネットワークを追加または削除する | 1. 仮想ネットワークをネットワーク グループに追加します。 </br> 2. ネットワーク グループを含む接続またはセキュリティ構成をコミットします。 |
| 動的メンバーシップを使用して仮想ネットワークを追加または削除する | Azure Virtual Network Manager は、条件付きステートメントに一致するように自動的に変更を行います。 |
| セキュリティ規則を追加、削除、または更新する | * 静的メンバーを含むネットワーク グループにセキュリティ構成を適用する場合は、構成を再度展開して有効にする必要があります。 </br> * 動的メンバーを含むネットワーク グループは自動的に更新されます。 |
| 規則コレクションを追加、削除、または更新する | * 静的メンバーを含むネットワーク グループにセキュリティ構成を適用する場合は、構成を再度展開して有効にする必要があります。 </br> * 動的メンバーを含むネットワーク グループは自動的に更新されます。 |

## <a name="next-steps"></a>次のステップ

Azure portal を使用して [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) インスタンスを作成します。
