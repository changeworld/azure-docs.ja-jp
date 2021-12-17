---
title: Azure Virtual Network Manager (プレビュー) とは
description: Azure Virtual Network Manager を使用して仮想ネットワークの管理とスケーラビリティを簡略化する方法について説明します。
services: virtual-network-manager
author: duongau
ms.service: virtual-network-manager
ms.topic: overview
ms.date: 11/02/2021
ms.author: duau
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 81603472d4a5a254fe86f2a6866d8a7edcbbef88
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459636"
---
# <a name="what-is-azure-virtual-network-manager-preview"></a>Azure Virtual Network Manager (プレビュー) とは

Azure Virtual Network Manager は、サブスクリプション全体でグローバルに仮想ネットワークをグループ化、構成、デプロイ、管理できる管理サービスです。 Virtual Network Manager では、ネットワーク グループを定義して、仮想ネットワークを識別し、論理的に分割することができます。 次に、必要な接続とセキュリティの構成を決定し、ネットワーク グループ内の選択したすべての仮想ネットワークで一度に適用します。 

> [!IMPORTANT]
> 現在、Azure Virtual Network Manager は、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="how-does-azure-virtual-network-manager-work"></a>Azure Virtual Network Manager のしくみ

:::image type="content" source="./media/overview/management-group.png" alt-text="Virtual Network Manager の管理グループの図。":::

作成プロセスでは、Azure Virtual Network Manager が管理する対象のスコープを定義します。 スコープを定義するには、[管理グループ](../governance/management-groups/overview.md)を作成する必要があります。 スコープを定義したら、Virtual Network Manager の *接続性* や *SecurityAdmin* ロールなどの機能を有効にします。

Virtual Network Manager インスタンスを展開した後、条件付きステートメントを使用して *ネットワーク グループ* を作成し、名前、タグ、または ID (動的メンバーシップ) 別に仮想ネットワークを選択します。 また、特定の仮想ネットワーク (静的メンバーシップ) を選択することもできます。 定義されたネットワーク グループ ルールは、カスタム イニシアチブ定義と、仮想ネットワーク メンバーシップ用に定義したルールを示す対応する割り当てとして Azure Policy に反映されます。 Azure Policy イニシアチブの詳細については、[Azure Policy イニシアチブの構造](../governance/policy/concepts/initiative-definition-structure.md)に関する記事を参照してください。 これらのポリシーは、現在、読み取り専用モードで使用できます。 これらのポリシーを作成、更新、削除する方法の詳細については、[ネットワーク グループと Azure Policy](concept-network-groups.md#network-group-and-azure-policy) に関する記事を参照してください。 次に、トポロジとセキュリティのニーズに基づいて、これらのネットワーク グループに適用される接続またはセキュリティ構成を作成します。 

接続構成を使用すると、メッシュまたはハブ アンド スポーク ネットワーク トポロジを作成できます。 セキュリティ構成を使用すると、グローバル レベルで 1 つまたは複数のネットワーク グループに適用できる規則のコレクションを定義できます。 必要なネットワーク グループと構成を作成したら、任意の選択したリージョンに構成をデプロイできます。

## <a name="key-benefits"></a>主な利点

* リージョンとサブスクリプション間で、接続とセキュリティ ポリシーをグローバルに一元的に管理します。

* ハブとスポークの構成でスポーク間の推移的な通信を可能にします。このとき、メッシュ ネットワークの管理が複雑になることはありません。

* 非常にスケーラブルで可用性の高いサービスで、世界中に冗長性とレプリケーションを提供します。

* ネットワーク セキュリティ グループの規則を上書きするグローバル ネットワーク セキュリティ規則を作成する機能があります。

* 仮想ネットワーク ピアリングを使用して、異なる仮想ネットワーク内のリソース間で低待機時間と高帯域幅を実現します。

* 特定のリージョン シーケンスおよび選択した頻度でネットワークの変更をロールアウトします。

## <a name="public-preview-regions"></a>パブリック プレビューのリージョン

* 米国中北部

* 米国西部

* 米国西部 2

* 米国東部

* 米国東部 2

* 北ヨーロッパ

* 西ヨーロッパ

* フランス中部

## <a name="next-steps"></a>次の手順

- Azure portal を使用して [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) インスタンスを作成します。
- Azure Virtual Network Manager の[ネットワーク グループ](concept-network-groups.md)について詳しく学習します。
- [接続構成](concept-connectivity-configuration.md)でできることについて学習します。
- [セキュリティ管理者の構成](concept-security-admins.md)の詳細について学習します。
