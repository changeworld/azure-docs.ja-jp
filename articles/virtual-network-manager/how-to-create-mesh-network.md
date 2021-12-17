---
title: Azure Virtual Network Manager を使用してメッシュ ネットワーク トポロジを作成する (プレビュー)
description: Azure Virtual Network Manager を使用してメッシュ ネットワーク トポロジを作成する方法について説明します。
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fea577a1aa3d2a26d218a7d7c97140d5f58661f2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017180"
---
# <a name="create-a-mesh-network-topology-with-azure-virtual-network-manager-preview"></a>Azure Virtual Network Manager を使用してメッシュ ネットワーク トポロジを作成する (プレビュー)

この記事では、Azure Virtual Network Manager を使用してメッシュ ネットワーク トポロジを作成する方法について説明します。 この構成を使用すると、同じネットワーク グループ内の同じリージョンのすべての仮想ネットワークで相互に通信できます。 接続構成でグローバル メッシュ設定を有効にすると、リージョン間の接続を有効にすることができます。

> [!IMPORTANT]
> 現在、*Azure Virtual Network Manager* は、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳細については、「[**Microsoft Azure プレビューの追加使用条件**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

* [メッシュ](concept-connectivity-configuration.md#mesh-network-topology) ネットワーク トポロジに関する記事を参照してください。
* [Azure Virtual Network Manager インスタンス](create-virtual-network-manager-portal.md#create-virtual-network-manager)を作成します。
* メッシュ構成で使用する仮想ネットワークを特定するか、新しい[仮想ネットワーク](../virtual-network/quick-create-portal.md)を作成します。

## <a name="create-a-network-group"></a>ネットワーク グループを作成する

このセクションでは、ハブ アンド スポーク ネットワーク トポロジに使用する仮想ネットワークを含むネットワーク グループを作成する方法について説明します。

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

## <a name="create-a-mesh-connectivity-configuration"></a>メッシュ接続構成を作成する

このセクションでは、前のセクションで作成したネットワーク グループを使用してメッシュ構成を作成する方法について説明します。

1. **[設定]** の下の *[構成]* を選択し、 **[+ 構成の追加]** を選択します。

    :::image type="content" source="./media/how-to-create-hub-and-spoke/configuration-list.png" alt-text="[構成] 一覧のスクリーンショット。":::

1. ドロップダウン メニューで **[接続]** を選択します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/configuration-menu.png" alt-text="[構成] ドロップダウン メニューのスクリーンショット。":::

1. *[接続構成を追加する]* ページで、次の情報を入力または選択します。

    :::image type="content" source="./media/how-to-create-mesh-network/connectivity-configuration.png" alt-text="[接続構成を追加する] ページのスクリーンショット。":::

    | 設定 | 値 |
    | ------- | ----- |
    | 名前 | この構成の "*名前*" を入力します。 |
    | 説明 | *省略可能* この構成で実行する内容についての説明を入力します。 |
    | トポロジ | **メッシュ** トポロジを選択します。 |
    | グローバル メッシュ | 同じネットワーク グループ内の仮想ネットワーク間のリージョン間接続を有効にするには、このオプションを選択します。 |

1. 次に、 **[+ ネットワーク グループの追加]** を選択します。 

1. *[ネットワーク グループの追加]* ページで、この構成に追加するネットワーク グループを選択します。 次に、 **[追加]** を選択して保存します。

1. 再度 **[追加]** を選択して、メッシュ接続構成を作成します。

## <a name="deploy-the-mesh-configuration"></a>メッシュ構成をデプロイする

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

1. 仮想ネットワーク間の接続をテストするには、テスト仮想マシンを各仮想ネットワークにデプロイし、それらの間で ICMP 要求を開始します。

## <a name="next-steps"></a>次のステップ

- 「[セキュリティ管理規則](concept-security-admins.md)」について学習します。
- [SecurityAdmin 構成](how-to-block-network-traffic-portal.md)を使用してネットワーク トラフィックをブロックする方法を学びます。
