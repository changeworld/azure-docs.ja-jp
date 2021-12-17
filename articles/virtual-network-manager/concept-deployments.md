---
title: Azure Virtual Network Manager での構成のデプロイ (プレビュー)
description: Azure Virtual Network Manager での構成のデプロイのしくみについて説明します。
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: f7e192133bac66d9ec593350a43d0d005bb05933
ms.sourcegitcommit: 1a0fe16ad7befc51c6a8dc5ea1fe9987f33611a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131866679"
---
# <a name="configuration-deployments-in-azure-virtual-network-manager-preview"></a>Azure Virtual Network Manager での構成のデプロイ (プレビュー)

この記事では、ネットワーク リソースに構成がどのように適用されるかについて説明します。 また、構成のデプロイの更新が、メンバーシップの種類ごとにどのように異なるかについても説明します。 さらに、"*デプロイ ステータス*" と "*目標状態モデル*" について詳しく説明します。

> [!IMPORTANT]
> 現在、Azure Virtual Network Manager は、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="deployment"></a>展開

"*デプロイ*" は、ネットワーク グループ内の仮想ネットワークに構成を適用するために Azure Virtual Network Manager で使用される方法です。 デプロイがコミット済みでない場合、ネットワーク グループ、接続、セキュリティ管理者の構成に加えた変更は、有効になりません。 デプロイをコミットするときに、構成が適用されるリージョンを選択します。 デプロイ要求が Azure Virtual Network Manager に送信されると、ネットワーク リソースの[目標状態](#goalstate)が計算され、インフラストラクチャに必要な変更が要求されます。 構成の規模によっては、変更には約 15 から 20 分かかることがあります。

## <a name="deployment-against-network-group-membership-types"></a><a name="deployment"></a>ネットワーク グループのメンバーシップの種類に対応したデプロイ

ネットワーク グループの定義を変更しても、このネットワーク グループを使用する構成がデプロイされない限り、影響はありません。 そのため、デプロイの更新は、ネットワーク グループ内のグループ メンバーが静的な場合と動的な場合で異なります。 名前に "production" が含まれているすべての仮想ネットワークなど、動的なグループ メンバーシップが定義されている場合、動的メンバーが構成の要件を満たしているかどうかが Azure Virtual Network Manager によって自動的に判別され、構成を再デプロイすることなく調整されます。 これは、メンバーシップの条件が既に定義されており、定義が変更されていないためです。 ただし、静的メンバーとして追加した仮想ネットワークがある場合は、変更を適用するために構成をもう一度デプロイする必要があります。 たとえば、新しい仮想ネットワークを静的メンバーとして追加した場合は、構成を有効にするために再度デプロイする必要があります。

## <a name="deployment-status"></a>[デプロイ ステータス]

構成のデプロイをコミットすると、API で POST 操作が実行され、後でデプロイの完了を確認することはできません。 デプロイ要求を行うと、Azure Virtual Network Manager でネットワークの目標状態が計算され、基になるインフラストラクチャに変更が要求されます。 デプロイ ステータスは、Virtual Network Manager の *[デプロイ]* ページで確認できます。

:::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deployment-in-progress.png" alt-text="デプロイの一覧で進行中のデプロイのスクリーンショット。":::

## <a name="goal-state-model"></a><a name = "goalstate"></a>目標状態モデル

構成のデプロイをコミットする場合、最終的な結果として必要な構成の目標状態を記述します。 たとえば、*Config1* と *Config2* という名前の構成をリージョンにコミットすると、これら 2 つの構成が適用されます。 *Config1* と *Config3* という名前の構成を同じリージョンにコミットすることにした場合は、*Config2* が削除され、*Config3* が追加されます。 すべての構成を削除するには、構成を適用する必要がなくなったリージョンに対して、 **[なし]** 構成をデプロイします。

## <a name="next-steps"></a>次のステップ

[Azure Virtual Network Manager インスタンスを作成する](create-virtual-network-manager-portal.md)方法を確認します。
