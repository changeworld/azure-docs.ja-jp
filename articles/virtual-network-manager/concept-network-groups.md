---
title: Azure Virtual Network Manager (プレビュー) のネットワーク グループとは
description: ネットワーク グループが仮想ネットワークの管理にどのように役立つかを説明します。
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 5da75fba68e285ea4b01feee514bd38f6ceae036
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493602"
---
# <a name="what-is-a-network-group-in-azure-virtual-network-manager-preview"></a>Azure Virtual Network Manager (プレビュー) のネットワーク グループとは

この記事では、*ネットワーク グループ* について説明し、それらが、管理がより容易になるように仮想ネットワークをまとめてグループ化することに、どのように役立つかを解説します。 *静的グループ メンバーシップ*、*動的グループ メンバーシップ*、それぞれの種類のメンバーシップの使用方法についても説明します。

> [!IMPORTANT]
> 現在、Azure Virtual Network Manager は、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="network-group"></a>ネットワーク グループ

*ネットワーク グループ* は、手動で、または条件付きステートメントを使用して選択した、一連の仮想ネットワークです。 仮想ネットワークを手動で選択する場合は、*静的メンバー* と呼ばれます。 条件付きステートメントを使用して選択された仮想ネットワークは、*動的メンバー* と呼ばれます。 

## <a name="static-membership"></a>静的メンバーシップ

ネットワーク グループを作成するときには、提供された一覧から個々の仮想ネットワークを手動で選択して、グループに仮想ネットワークを追加できます。 仮想ネットワークの一覧は、Azure Virtual Network Manager のデプロイ時に定義されたスコープ (管理グループまたはサブスクリプション) によって決まります。 この方法は、ネットワーク グループに追加する仮想ネットワークがいくつかある場合に便利です。 静的メンバーが含まれる構成の更新を再度デプロイして、新しい変更が適用されるようにする必要があります。

## <a name="dynamic-membership"></a>動的メンバーシップ

動的メンバーシップを使用すると、柔軟に、定義した条件付きステートメントを満たす場合には一度に複数の仮想ネットワークを選択できます。 この方法は、1 つ以上のサブスクリプション内に数百や数千の仮想ネットワークがあり、名前、ID、タグのいずれかで少数のネットワークを選択する必要がある場合に便利です。 各条件は列挙されている順序で処理されて、それらの条件を満たすように構成が仮想ネットワークに適用されます。 条件付きステートメントの構成方法については、「[動的メンバーシップから要素を除外する](how-to-exclude-elements.md)」を参照してください。

## <a name="network-group-and-azure-policy"></a>ネットワーク グループと Azure ポリシー

ネットワーク グループを作成するときには、仮想ネットワークのメンバーシップに加えられた変更について Azure Virtual Network Manager に通知されるように Azure ポリシーが作成されます。 定義されているポリシーは参照できますが、現在のところ、ユーザーが編集することはできません。 ネットワーク グループ用の Azure ポリシーの定義や割り当てを作成、変更、削除することは、現在のところ、Azure ネットワーク マネージャーからのみ可能です。 

Azure ネットワーク マネージャー リソース用の Azure ポリシー イニシアチブの定義や割り当てを作成するには、必要な構成を持つネットワーク グループを作成してデプロイします。 既存の Azure ポリシー イニシアチブの定義や対応する割り当てを更新するには、Azure Virtual Network Manager リソース内のネットワーク グループに変更を加えてデプロイする必要があります。 Azure ポリシー イニシアチブの定義と割り当てを削除するには、ポリシーに関連付けられている Azure Virtual Network Manager リソースのデプロイ解除と削除を行う必要があります。 これには、構成のデプロイ解除、構成の削除、ネットワーク グループの削除が含まれる場合があります。 削除の詳細については、Azure Virtual Network Manager の、[コンポーネント削除のためのチェックリスト](concept-remove-components-checklist.md)を参照してください。  

## <a name="next-steps"></a>次のステップ

- Azure portal を使用して [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) インスタンスを作成します。
- Azure Virtual Network Manager を使用して[ハブ アンド スポーク トポロジ](how-to-create-hub-and-spoke.md)を作成する方法を学習します。
- [SecurityAdmin 構成](how-to-block-network-traffic-portal.md)を使用してネットワーク トラフィックをブロックする方法を学習します。
