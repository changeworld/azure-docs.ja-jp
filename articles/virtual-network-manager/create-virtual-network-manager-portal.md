---
title: 'クイックスタート: Azure portal を使用して Azure Virtual Network Manager でメッシュ ネットワーク トポロジを作成する'
description: このクイックスタートを使用して、Virtual Network Manager で Azure portal を使用してメッシュ ネットワーク トポロジを作成する方法について説明します。
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: template-quickstart, ignite-fall-2021
ms.openlocfilehash: 134f8b28d6505963cde1f44f0b6850643dae3436
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131421755"
---
# <a name="quickstart-create-a-mesh-network-topology-with-azure-virtual-network-manager-using-the-azure-portal"></a>クイックスタート: Azure portal を使用して Azure Virtual Network Manager でメッシュ ネットワーク トポロジを作成する

Azure portal を使用して、すべての仮想ネットワークの接続を管理することで、Azure Virtual Network Manager の使用を開始します。

このクイックスタートでは、3 つの仮想ネットワークをデプロイし、Azure Virtual Network Manager を使用してメッシュ ネットワーク トポロジを作成します。 次に、接続構成が適用されたかどうかを確認します。

> [!IMPORTANT]
> 現在、Azure Virtual Network Manager は、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="register-subscription-for-public-preview"></a>サブスクリプションをパブリック プレビューに登録する

1. [プレビュー機能](https://portal.azure.com/#blade/Microsoft_Azure_Resources/PreviewFeaturesBlade)のページにアクセスします。

1. 「**AllowAzureNetworkManager**」を検索します。

1. *[AllowAzureNetworkManager]* の横にあるチェックボックスをオンにし、 **[+ Register]\(+ 登録\)** を選択します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/registration.png" alt-text="プレビュー機能の登録ページのスクリーンショット。":::

## <a name="create-virtual-network-manager"></a>Virtual Network Manager を作成する

1. **[+ リソースの作成]** を選択し、 **[ネットワーク マネージャー]** を検索します。 次に、 **[作成]** を選択して、Azure Virtual Network Manager の設定を開始します。

1. *[基本]* タブで、次の情報を入力または選択します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/network-manager-basics.png" alt-text="ネットワーク マネージャーの基本ページの作成のスクリーンショット。":::

    | 設定 | 値 |
    | ------- | ----- |
    | サブスクリプション | Azure Virtual Network Manager をデプロイするサブスクリプションを選択します。 |
    | リソース グループ | Azure Virtual Network Manager を格納するリソース グループを選択または作成します。 この例では、以前に作成した **myAVNMResourceGroup** を使用します。
    | 名前 | この Azure Virtual Network Manager インスタンスの名前を入力します。 この例では、**myAVNM** という名前を使用します。 |
    | リージョン | このデプロイのリージョンを選択します。 Azure Virtual Network Manager は、任意のリージョンの仮想ネットワークを管理できます。 選択されたリージョンは、Virtual Network Manager インスタンスを配置する場所を指定します。 |
    | 説明 | *(省略可能)* この Azure Virtual Network Manager インスタンスと、このインスタンスが管理するタスクの説明を指定します。 |
    | [スコープ](concept-network-manager-scope.md#scope) | Azure Virtual Network Manager で管理できるスコープを定義します。
    | [機能](concept-network-manager-scope.md#features) | Azure Virtual Network Manager で有効にする機能を選択します。 使用できる機能は、 *[接続性]* 、 *[SecurityAdmin]* 、または *[すべて選択]* です。 </br> 接続 - スコープ内の仮想ネットワーク間に完全なメッシュまたはハブとスポークのネットワーク トポロジを作成する機能を有効にします。 </br> SecurityAdmin - グローバル ネットワーク セキュリティ規則を作成する機能を有効にします。 |

1. **[確認と作成]** を選択し、次に、検証に成功したら **[作成]** を選択します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/network-manager-validation.png" alt-text="ネットワーク マネージャー リソースを作成するための検証ページのスクリーンショット。":::

## <a name="create-three-virtual-networks"></a>3 つの仮想ネットワークを作成する

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. **[リソースの作成]** を選択し、**仮想ネットワーク** を検索します。 **[作成]** を選択して、仮想ネットワークの構成を開始します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/create-vnet.png" alt-text="仮想ネットワーク作成ページのスクリーンショット。":::

1. *[基本]* タブで、次の情報を入力または選択します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/create-mesh-vnet-basic.png" alt-text="仮想ネットワーク作成の基本ページのスクリーンショット。":::

    | 設定 | 値 |
    | ------- | ----- |
    | サブスクリプション | この仮想ネットワークをデプロイするサブスクリプションを選択します。 |
    | リソース グループ | 仮想ネットワークを格納する新しいリソース グループを選択または作成します。 このクイックスタートでは、**myAVNMResourceGroup** という名前の新しいリソース グループを使用します。
    | 名前 | 仮想ネットワークの名前に「**VNetA**」を入力します。 |
    | リージョン | **[米国西部]** を選択します。 |

1. **[次へ: IP アドレス >]** を選択し、次のネットワークのアドレス空間を構成します。

     :::image type="content" source="./media/create-virtual-network-manager-portal/create-mesh-vnet-ip.png" alt-text="仮想ネットワーク作成の IP アドレス ページのスクリーンショット。":::

    | 設定 | 値 |
    | -------- | ----- |
    | IPv4 アドレス空間 | 10.0.0.0/16 |
    | サブネット名 | default |
    | サブネットのアドレス空間 | 10.0.0.0/24 |

1. **[確認と作成]** を選択し、次に、検証に成功したら **[作成]** を選択し、仮想ネットワークをデプロイします。

    :::image type="content" source="./media/create-virtual-network-manager-portal/vnet-validation.png" alt-text="仮想ネットワークを作成するための検証ページのスクリーンショット。":::

1. 手順 2 から 5 を繰り返して、次の情報を含む仮想ネットワークあと 2 つ作成します。

    | 設定 | 値 |
    | ------- | ----- |
    | サブスクリプション | 手順 3 で選択したものと同じサブスクリプションを選択します。 |
    | リソース グループ | **myAVNMResourceGroup** を選択します。 |
    | 名前 | 2 番目の仮想ネットワークに「**VNetB**」、3 番目の仮想ネットワークに「**VNetC**」と入力します。 |
    | リージョン | リソース グループを選択すると、リージョンが選択されます。 |
    | VNetB IP アドレス | IPv4 アドレス空間: 10.1.0.0/16 </br> サブネット名: 既定 </br> サブネット アドレス空間: 10.1.0.0/24|
    | VNetC IP アドレス | IPv4 アドレス空間: 10.2.0.0/16 </br> サブネット名: 既定 </br> サブネットのアドレス空間: 10.2.0.0/24|

## <a name="create-a-network-group"></a>ネットワーク グループを作成する

1. 作成した Azure Virtual Network Manager インスタンスにアクセスします。

1. **[設定]** の *[ネットワーク グループ]* を選択し、 **[+ 追加]** を選択します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-network-group.png" alt-text="ネットワーク グループの追加ボタンのスクリーンショット。":::

1. *[ネットワーク グループを追加する]* ページで、ネットワーク グループの **[名前]** を入力します。 この例では、**myNetworkGroup** という名前を使用します。 **[次へ: 静的グループ メンバー >]** を選択して、ネットワーク グループへの仮想ネットワークの追加を開始します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/network-group-basics.png" alt-text="ネットワーク グループ作成の基本タブのスクリーンショット。":::

1. *[静的グループ メンバー]* タブで、 **[+ 仮想ネットワークの追加]** を選択します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-virtual-networks-button.png" alt-text="[仮想ネットワークの追加] ボタンのスクリーンショット。":::

1. *[仮想ネットワークの追加]* ページで、前に作成した 3 つすべての仮想ネットワーク (VNetA、VNetB、VNetC) を選択します。 次に、 **[追加]** を選択して、選択内容をコミットします。

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-virtual-networks.png" alt-text="ネットワーク グループへの仮想ネットワークの追加ページのスクリーンショット。":::

1. **[確認と作成]** を選択し、次に、検証に成功したら **[作成]** を選択します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/review-create.png" alt-text="新しいネットワーク グループの [確認と作成] ボタンのスクリーンショット。":::

1. *[ネットワーク グループ]* ページに、新しいネットワーク グループが追加されているのが表示されます。

    :::image type="content" source="./media/create-virtual-network-manager-portal/network-groups-list.png" alt-text="新しいネットワーク グループが追加された [ネットワーク グループ] ページのスクリーンショット。":::

## <a name="create--a-connectivity-configuration"></a>接続構成を作成する

1. **[設定]** の下の *[構成]* を選択し、 **[+ 構成の追加]** を選択します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-configuration.png" alt-text="Network Manager の [構成の追加] ボタンのスクリーンショット。":::

1. ドロップダウン メニューから **[接続]** を選択して、接続構成の作成を開始します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/configuration-menu.png" alt-text="構成ドロップダウン メニューのスクリーンショット。":::

1. *[接続構成を追加する]* ページで、次の情報を入力または選択します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/connectivity-configuration.png" alt-text="[接続構成を追加する] ページのスクリーンショット。":::

    | 設定 | 値 |
    | ------- | ----- |
    | 名前 | この接続構成の名前を入力します。 |
    | 説明 | *(省略可能)* この接続構成に関する説明を入力します。 |
    | トポロジ | この構成で作成するトポロジの種類を選択します。 この例では、**メッシュ** トポロジを使用します。 |

1. *メッシュ* トポロジを選択すると、 **[グローバル メッシュ]** と **[ネットワーク グループ]** オプションが表示されます。 *グローバル メッシュ* は、すべての仮想ネットワークが同じリージョン内にあるので、この設定には必要ありません。 **[+ Add network groups]\(ネットワーク グループの追加\)**  を選択し、前のセクションで作成したネットワーク グループを選択します。 **[選択]** をクリックして、ネットワーク グループを構成に追加します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-network-group-configuration.png" alt-text="接続構成にネットワーク グループを追加するスクリーンショット。":::

1. **[追加]** を選択して、構成を作成します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/create-connectivity-configuration.png" alt-text="接続構成の作成のスクリーンショット。":::

1. *[構成]* ページに新しい接続構成が追加されているのが表示されます。

    :::image type="content" source="./media/create-virtual-network-manager-portal/connectivity-configuration-list.png" alt-text="接続構成の一覧のスクリーンショット。":::

## <a name="deploy-the-connectivity-configuration"></a>接続構成をデプロイする

環境に構成を適用するには、デプロイによって構成をコミットする必要があります。 仮想ネットワークがデプロイされている **[米国西部]**  リージョンに構成をデプロイする必要があります。

1. *[設定]* の下の **[デプロイ]** を選択し、 **[構成の配置]** を選択します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployments.png" alt-text="Network Manager のデプロイ ページのスクリーンショット。":::

1. 次の設定を選択します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/deploy-configuration.png" alt-text="構成のデプロイ ページのスクリーンショット。":::

    | 設定 | 値 |
    | ------- | ----- |
    | 構成のタイプ | デプロイする構成の種類を選択します。 この例では、 **[接続]** 構成をデプロイします。 |
    | 構成 | 前のセクションから作成した **myConnectivityConfig** 構成を選択します。 |
    | ターゲット リージョン | この構成をデプロイするリージョンを選択します。 すべての仮想ネットワークが **[米国西部]** リージョンに作成されたので、そのリージョンが選択されています。 |

1. **[デプロイ]** を選択し、 **[OK]** を選択して、既存の構成を上書きするかどうかを確認します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployment-confirmation.png" alt-text="デプロイの確認メッセージのスクリーンショット。":::

1. これで、選択したリージョンの一覧にデプロイが表示されます。 構成のデプロイが完了するには、約 15 分から 20 分かかる場合があります。

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployment-in-progress.png" alt-text="構成のデプロイが進行中の状態のスクリーンショット。":::

## <a name="confirm-configuration-deployment"></a>構成のデプロイを確認する

1. *[デプロイメント]* ページの **[更新]** を選択し、コミットした構成の更新状態を確認します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployment-status.png" alt-text="更新されたデプロイ状態の更新ボタンのスクリーンショット。":::

1. **VNetA** 仮想ネットワークに移動し、 **[ネットワーク マネージャー]** を *[設定]* から選びます。 仮想ネットワークに関連付けられている Azure Virtual Network Manager を使用してデプロイした構成が表示されます。

    :::image type="content" source="./media/create-virtual-network-manager-portal/vnet-configuration-association.png" alt-text="VNetA 仮想ネットワークに関連付けられている接続構成のスクリーンショット。":::

1. **VNetB** と **VNetC** でも同様に確認できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure Virtual Network Manager が不要になった場合は、リソースを削除する前に、次のすべてに当てはまることを確認する必要があります。

* 構成は、どのリージョンにもデプロイされていません。
* すべての構成が削除されました。
* すべてのネットワーク グループが削除されました。

1. リージョンからすべての構成を削除するには、 **[なし]** 構成をターゲット リージョンにデプロイします。 **[デプロイ]** を選択し、 **[OK]** を選択して確認します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/none-configuration.png" alt-text="[なし] 接続構成のデプロイのスクリーンショット。":::

1. 構成を削除するには、Azure Virtual Network Manager の左側のウィンドウから *[設定]* の **[構成]** を選びます。 削除する構成の横にあるチェック ボックスをオンにし、リソース ページの上部にある **[削除]** を選択します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/delete-configuration.png" alt-text="接続構成の [削除] ボタンのスクリーンショット。":::

1. ネットワーク グループを削除するには、Azure Virtual Network Manager の左側のウィンドウから *[設定]* の **[ネットワーク グループ]** を選びます。 削除するネットワーク グループの横にあるチェック ボックスをオンにし、リソース ページの上部にある **[削除]** を選択します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/delete-network-group.png" alt-text="ネットワーク グループの [削除] ボタンのスクリーンショット。":::

1. すべてのネットワーク グループが削除されたら、一覧から Azure Virtual Network Manager を右クリックし、 **[削除]** を選択してリソースを削除できます。

    :::image type="content" source="./media/create-virtual-network-manager-portal/delete-network-manager.png" alt-text="Azure Virtual Network Manager の [削除] ボタンのスクリーンショット。":::

1. リソース グループを削除するには、リソース グループを見つけて、 **[リソース グループの削除]** を選択します。 リソース グループの名前を入力して削除を確定し、 **[削除]** を選択します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/delete-resource-group.png" alt-text="リソース グループの [削除] ボタンのスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

Azure Virtual Network Manager を作成したら、セキュリティ管理者の構成を使用してネットワーク トラフィックをブロックする方法についての学習に進みます。

> [!div class="nextstepaction"]
> [セキュリティ管理者規則を使用してネットワーク トラフィックをブロックする](how-to-block-network-traffic-portal.md)
