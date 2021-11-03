---
title: Azure Virtual Network Manager を使用してハブおよびスポーク トポロジを作成する (プレビュー)
description: Azure Virtual Network Manager でハブおよびスポーク ネットワーク トポロジを作成する方法を学習します。
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: c07eccfda6492a5957ce9d2f0df6e0133e5b9d3d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092106"
---
# <a name="create-a-hub-and-spoke-topology-with-azure-virtual-network-manager-preview"></a>Azure Virtual Network Manager を使用してハブおよびスポーク トポロジを作成する (プレビュー)

この記事では、Azure Virtual Network Manager を使用してハブおよびスポーク ネットワーク トポロジを作成する方法について説明します。 この構成で、ハブとして機能する仮想ネットワークを選択します。すべてのスポーク仮想ネットワークで、既定でハブのみを使用する双方向ピアリングがあります。 また、スポーク仮想ネットワーク間の直接接続を有効にし、スポーク仮想ネットワークでハブの仮想ネットワーク ゲートウェイを使用できるようにすることもできます。

> [!IMPORTANT]
> 現在、*Azure Virtual Network Manager* は、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳細については、「[**Microsoft Azure プレビューの追加使用条件**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

* [ハブおよびスポーク](concept-connectivity-configuration.md#hub-and-spoke-topology) ネットワーク トポロジについての説明を参照してください。
* [Azure Virtual Network Manager インスタンス](create-virtual-network-manager-portal.md#create-virtual-network-manager)を作成します。
* ハブおよびスポーク構成で使用する仮想ネットワークを特定するか、新しい[仮想ネットワーク](../virtual-network/quick-create-portal.md)を作成します。 

## <a name="create-a-network-group"></a><a name="group"></a>ネットワーク グループを作成する

このセクションでは、ハブおよびスポーク ネットワーク トポロジに使用する仮想ネットワークを含むネットワーク グループを作成する方法について説明します。

1. Azure Virtual Network Manager インスタンスに移動します。 この攻略ガイドでは、[クイックスタート](create-virtual-network-manager-portal.md) ガイドを使用して作成済みであることを前提としています。

1. *[設定]* の下の **[ネットワーク グループ]** を選択し、 **[+ 追加]** を選択して新しいネットワーク グループを作成します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-network-group.png" alt-text="[ネットワーク グループの追加] ボタンのスクリーンショット。":::

1. *[基本]* タブで、ネットワーク グループの **[名前]** と **[説明]** を入力します。

    :::image type="content" source="./media/how-to-create-hub-and-spoke/basics.png" alt-text="ネットワーク グループを追加するための [基本] タブのスクリーンショット。":::

1. 仮想ネットワークを手動で追加するには、 **[静的グループ メンバー]** タブを選択します。詳細については、「[静的メンバー](concept-network-groups.md#static-membership)」を参照してください。

    :::image type="content" source="./media/how-to-create-hub-and-spoke/static-group.png" alt-text="[静的グループ メンバー] タブのスクリーンショット。":::

1. 仮想ネットワークを動的に追加するには、 **[条件付きステートメント]** タブを選択します。詳細については、「[動的メンバーシップ](concept-network-groups.md#dynamic-membership)」を参照してください。

    :::image type="content" source="./media/how-to-create-hub-and-spoke/conditional-statements.png" alt-text="[条件付きステートメント] タブのスクリーンショット。":::

1. ネットワーク グループ用に選択した仮想ネットワークに問題がなければ、 **[確認 + 作成]** を選択します。 次に、検証に合格したら、 **[作成]** を選択します。
 
## <a name="create-a-hub-and-spoke-connectivity-configuration"></a>ハブおよびスポーク接続構成を作成する

このセクションでは、前のセクションで作成したネットワーク グループを使用してハブおよびスポーク構成を作成する方法について説明します。

1. **[設定]** の下の *[構成]* を選択し、 **[+ 構成の追加]** を選択します。

    :::image type="content" source="./media/how-to-create-hub-and-spoke/configuration-list.png" alt-text="[構成] 一覧のスクリーンショット。":::

1. ドロップダウン メニューで **[接続]** を選択します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/configuration-menu.png" alt-text="[構成] ドロップダウン メニューのスクリーンショット。":::

1. *[接続構成を追加する]* ページで、次の情報を入力または選択します。

    :::image type="content" source="./media/how-to-create-hub-and-spoke/connectivity-configuration.png" alt-text="[接続構成を追加する] ページのスクリーンショット。":::

    | 設定 | 値 |
    | ------- | ----- |
    | 名前 | この構成の "*名前*" を入力します。 |
    | 説明 | *省略可能* この構成で実行する内容についての説明を入力します。 |
    | トポロジ | **[ハブおよびスポーク]** トポロジを選択します。 |
    | ハブ | ハブ仮想ネットワークとして機能する仮想ネットワークを選択します。 |
    | 既存のピアリング | この構成で定義されているネットワーク グループ内の仮想ネットワーク間で、以前に作成したすべての VNet ピアリングを削除する場合は、このチェックボックスをオンにします。 |

1. 次に、 **[+ ネットワーク グループの追加]** を選択します。 

1. *[ネットワーク グループの追加]* ページで、この構成に追加するネットワーク グループを選択します。 次に、 **[追加]** を選択して保存します。

1. *[スポーク ネットワーク グループ]* の下に、ネットワーク グループ名の横に次の 3 つのオプションが表示されます。
    
    :::image type="content" source="./media/how-to-create-hub-and-spoke/spokes-settings.png" alt-text="[スポーク ネットワーク グループ] の設定のスクリーンショット。" lightbox="./media/how-to-create-hub-and-spoke/spokes-settings-expanded.png":::

    * *直接接続*: 同じリージョンのネットワーク グループ内の仮想ネットワーク間で VNet ピアリングを確立する場合は、 **[ネットワークグループ内のピアリングを有効にする]** を選択します。
    * *グローバル メッシュ*: リージョン間のネットワーク グループ内のすべての仮想ネットワークに対して VNet ピアリングを確立する場合は、 **[リージョン間のメッシュ接続を有効にする]** を選択します。
    * *ゲートウェイ*: ハブ仮想ネットワーク内に仮想ネットワーク ゲートウェイがあり、このネットワーク グループがトラフィックをオンプレミスに渡すために使用する場合は、 **[ハブをゲートウェイとして使用する]** を選択します。

    各ネットワーク グループに対して有効にする設定を選択します。

1. 最後に、 **[追加]** を選択してハブおよびスポーク接続構成を作成します。

## <a name="deploy-the-hub-and-spoke-configuration"></a>ハブおよびスポーク構成を展開する

環境内でこの構成を有効にするには、選択した仮想ネットワークが作成されたリージョンに構成をデプロイする必要があります。

1. *[設定]* の下の **[デプロイ]** を選択し、 **[構成のデプロイ]** を選択します。

1. *[構成のデプロイ]* で、次の設定を選択します。

    :::image type="content" source="./media/how-to-create-hub-and-spoke/deploy.png" alt-text="[構成のデプロイ] ページのスクリーンショット。":::

    | 設定 | 値 |
    | ------- | ----- |
    | 構成のタイプ | **[接続]** を選択します。 |
    | 構成 | 前のセクションで作成した構成の名前を選択します。 |
    | ターゲット リージョン | 構成用に選択した仮想ネットワークに適用するすべてのリージョンを選択します。 |

1. **[デプロイ]** を選択し、 **[OK]** を選択して、選択したリージョンに構成をコミットします。

1. 構成のデプロイには最大 15 から 20 分かかることがあります。デプロイの状態を確認するには、 **[更新]** ボタンを選択します。

## <a name="confirm-deployment"></a>デプロイを確認する

1. 「[適用された構成を表示する](how-to-view-applied-configurations.md)」を参照してください。

1. スポーク間の *直接接続* をテストするには、各スポーク仮想ネットワークに仮想マシンをデプロイします。 次に、仮想マシン間で ICMP 要求を開始します。

## <a name="next-steps"></a>次のステップ

- 「[セキュリティ管理規則](concept-security-admins.md)」について学習します。
- [SecurityAdmin 構成](how-to-block-network-traffic-portal.md)を使用してネットワーク トラフィックをブロックする方法を学びます。
