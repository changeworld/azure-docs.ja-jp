---
title: 'チュートリアル: セキュリティで保護されたハブ アンド スポーク ネットワークを作成する'
description: このチュートリアルでは、Azure Virtual Network Manager を使用してハブ アンド スポーク ネットワークを作成する方法について説明します。 次に、すべての仮想ネットワークをセキュリティ ポリシーで保護します。
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 672b1218a52ee94e226c5b7c763c8e7622b60267
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092393"
---
# <a name="tutorial-create-a-secured-hub-and-spoke-network"></a>チュートリアル: セキュリティで保護されたハブ アンド スポーク ネットワークを作成する

このチュートリアルでは、Azure Virtual Network Manager を使用してハブ アンド スポーク ネットワーク トポロジを作成します。 次に、ハブ仮想ネットワークに仮想ネットワーク ゲートウェイをデプロイして、スポーク仮想ネットワーク内のリソースが VPN を使用してリモート ネットワークと通信できるようにします。 また、ポート 80 および 443 でインターネットへの送信ネットワーク トラフィックをブロックするようにセキュリティ構成を構成します。 最後に、仮想ネットワークと仮想マシンの設定を参照して、構成が正しく適用されたことを確認します。

> [!IMPORTANT]
> 現在、Azure Virtual Network Manager は、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 複数の仮想ネットワークを作成します。
> * 仮想ネットワーク ゲートウェイをデプロイします。
> * ハブ アンド スポーク ネットワーク トポロジを作成します。
> * ポート 80 および 443 でトラフィックをブロックするセキュリティ構成を作成します。
> * 構成が適用されたことを確認します。

## <a name="prerequisite"></a>前提条件

* このチュートリアルの手順を実行する前に、まず [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) インスタンスを作成する必要があります。

## <a name="create-virtual-networks"></a>仮想ネットワークを作成する

ここでは、3 つの仮想ネットワークを作成する手順について説明します。 1 つは *米国西部* リージョンにあり、他の 2 つは *米国東部* リージョンにあります。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. **[リソースの作成]** を選択し、**仮想ネットワーク** を検索します。 **[作成]** を選択して、仮想ネットワークの構成を開始します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-vnet.png" alt-text="[仮想ネットワークの作成] ページのスクリーンショット。":::

1. *[基本]* タブで、次の情報を入力または選択します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-hub-vnet-basic.png" alt-text="ハブ アンド スポーク仮想ネットワークの [基本] タブのスクリーンショット。":::

    | 設定 | [値] |
    | ------- | ----- |
    | サブスクリプション | この仮想ネットワークをデプロイするサブスクリプションを選択します。 |
    | リソース グループ | 仮想ネットワークを格納する新しいリソース グループを選択または作成します。 このクイックスタートでは、**myAVNMResourceGroup** という名前のリソース グループを使用します。
    | 名前 | 仮想ネットワークの名前に「**VNet-A-WestUS**」と入力します。 |
    | リージョン | **[米国西部]** リージョンを選択します。 |

 1. *[IP アドレス]* タブで、以下のネットワークのアドレス空間を構成します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-hub-vnet-addresses.png" alt-text="ハブ アンド スポーク仮想ネットワークの [IP アドレス] タブのスクリーンショット。":::

    | 設定 | 値 |
    | -------- | ----- |
    | IPv4 アドレス空間 | アドレス空間として「**10.3.0.0/16**」と入力します。 |
    | サブネット名 | サブネットの名前 **default** を入力します。 |
    | サブネットのアドレス空間 | サブネットのアドレス空間 **10.0.1.0/24** を入力します。 |

1. **[確認と作成]** を選択し、 **[作成]** を選択して、仮想ネットワークをデプロイします。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-vnet-validation.png" alt-text="ハブ アンド スポーク仮想ネットワークの [検証] ページのスクリーンショット。":::

1. 手順 2-5 を繰り返して、次の情報を使用して、仮想ネットワークをさらに 2 つ同じリソース グループに作成します。

    **2 番目の仮想ネットワーク**:
    * 名前: **VNet-A-EastUS**
    * リージョン: **米国東部**
    * IPv4 アドレス空間: **10.4.0.0/16**
    * サブネット名: **default**
    * サブネット アドレス空間: **10.4.0.0/24**

    **3 番目の仮想ネットワーク**:
    * 名前: **VNet-B-EastUS**
    * リージョン: **米国東部**
    * IPv4 アドレス空間: **10.5.0.0/16**
    * サブネット名: **default**
    * サブネット アドレス空間: **10.5.0.0/24**

## <a name="deploy-a-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイをデプロイする

ハブ仮想ネットワークに仮想ネットワーク ゲートウェイをデプロイします。 この仮想ネットワーク ゲートウェイは、スポークの *[ハブをゲートウェイとして使用する]* 設定に必要です。

1. **[リソースの作成]** を選択し、**仮想ネットワーク ゲートウェイ** を検索します。 **[作成]** を選択して、仮想ネットワーク ゲートウェイの構成を開始します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-gateway.png" alt-text="[仮想ネットワーク ゲートウェイの作成] ページのスクリーンショット。":::

1. *[基本]* タブで、次の設定を入力または選択します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/gateway-basics.png" alt-text="[仮想ネットワーク ゲートウェイの作成] の [基本] タブのスクリーンショット。" lightbox="./media/tutorial-create-secured-hub-and-spoke/gateway-basics-expanded.png":::

1. **[確認と作成]** を選択し、検証に成功したら **[作成]** を選択します。 仮想ネットワーク ゲートウェイのデプロイには、約 30 分かかる場合があります。 このデプロイの完了を待機している間に、次のセクションに進むことができます。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/gateway-validation.png" alt-text="[仮想ネットワーク ゲートウェイの作成] の [検証] ページのスクリーンショット。":::

## <a name="create-a-network-group"></a>ネットワーク グループを作成する

1. Azure Virtual Network Manager インスタンスに移動します。 このチュートリアルでは、[クイックスタート](create-virtual-network-manager-portal.md) ガイドを使用して作成済みであることを前提としています。

1. *[設定]* で **[ネットワーク グループ]** を選択し、 **[+ 追加]** を選択して新しいネットワーク グループを作成します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-network-group.png" alt-text="[ネットワーク グループの追加] ボタンのスクリーンショット。":::

1. *[基本]* タブで、次の情報を入力します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/network-group-basics.png" alt-text="[ネットワーク グループの作成] の [基本] タブのスクリーンショット。":::

    | 設定 | [値] |
    | ------- | ----- |
    | 名前 | ネットワーク グループ名として「**myNetworkGroupB**」と入力します。 |
    | 説明 | このネットワーク グループの説明を入力します。 |

1. **[条件付きステートメント]** タブを選択します。 *[パラメーター]* には、ドロップダウン リストから **[名前]** を選択します。 *[演算子]* で、 **[次の値を含む]** を選択します。 *[条件]* として、「**VNet-** 」と入力します。 この条件付きステートメントでは、以前に作成した 3 つの仮想ネットワークをこのネットワーク グループに追加します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/network-group-conditional.png" alt-text="[ネットワーク グループの作成] の [条件付きステートメント] タブのスクリーンショット。":::

1. 選択した仮想ネットワークを確認する必要がある場合は、 **[評価]** を選択します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/evaluate-vnet.png" alt-text="[有効な仮想ネットワーク] ページのスクリーンショット。":::

1. **[確認と作成]** を選択し、次に、検証に成功したら **[作成]** を選択します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/network-group-validation.png" alt-text="[ネットワーク グループ検証の作成] ページのスクリーンショット。":::

## <a name="create-a-hub-and-spoke-connectivity-configuration"></a>ハブ アンド スポーク接続構成を作成する

1. **[設定]** で *[構成]* を選択し、 **[+ 構成の追加]** を選択します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-configuration.png" alt-text="Network Manager の [構成の追加] ボタンのスクリーンショット。":::

1. ドロップダウン メニューで **[接続]** を選択します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/configuration-menu.png" alt-text="[構成] ドロップダウン メニューのスクリーンショット。":::

1. 接続構成に次の情報を入力して選択します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/connectivity-configuration.png" alt-text="[接続構成の追加] ページのスクリーンショット。":::

    | 設定 | [値] |
    | ------- | ----- |
    | 名前 | 構成の名前として「**HubA**」と入力します |
    | 説明 | この接続構成で実行する内容についての説明を入力します。 |
    | トポロジ | **[ハブ アンド スポーク]** を選択します。 |

1. *[ハブ アンド スポーク]* トポロジを選択すると、さらに多くのフィールドが表示されます。 次の設定を選択します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/hub-configuration.png" alt-text="接続構成のハブの選択を示すスクリーンショット。":::

    | 設定 | 値 |
    | -------- | ----- |
    | ハブ | ハブ仮想ネットワークとして「**VNet-A-West**」と選択します。 |
    | 既存のピアリング | このオプションは **オフ** のままとします。 |
    | スポーク ネットワーク グループ | **[ネットワーク グループの追加]** を選択し、**myNetworkGroupB** を構成に追加します。 |

1. ネットワーク グループを追加した後、次のオプションを選択します。 その後、[追加] を選択して接続構成を作成します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/network-group-options.png" alt-text="ネットワーク グループ構成の設定を示すスクリーンショット。":::

    | 設定 | 値 |
    | ------- | ----- |
    | 推移性 | **[ネットワーク グループ内のピアリングを有効にする]** のチェックボックスを選択します。 この設定により、同じリージョン内のネットワーク グループ内のスポーク仮想ネットワークが相互に直接通信できるようになります。 |
    | グローバル メッシュ | このオプションは **オフ** のままとします。 両方のスポークが同じリージョンにあるので、この設定は必要ありません。 |
    | Gateway | **[ハブをゲートウェイとして使用する]** を選択します。 |

## <a name="deploy-the-connectivity-configuration"></a>接続構成をデプロイする

接続構成をデプロイする前に、仮想ネットワーク ゲートウェイが正常にデプロイされたことを確認します。 **[ハブをゲートウェイとして使用する]** を有効にしてハブ アンド スポーク構成をデプロイするときにゲートウェイが存在しない場合、デプロイは失敗します。 詳細については、[ハブをゲートウェイとして使用する](concept-connectivity-configuration.md#use-hub-as-a-gateway)を参照してください。

1. *[設定]* で **[デプロイ]** を選択し、 **[構成のデプロイ]** を選択します。

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployments.png" alt-text="Network Manager の [デプロイ] ページのスクリーンショット。":::

1. 構成の種類として **[接続]** を選択し、前のセクションで作成した **HubA** 構成を選択します。 次に、ターゲット リージョンとして **米国西部** および **米国東部** を選択し、 **[デプロイ]** を選択します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deploy-configuration.png" alt-text="[構成のデプロイ] ページのスクリーンショット。":::

1. **[OK]** を選択して、既存の構成を上書きしてセキュリティ管理構成をデプロイすることを確認します。 

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployment-confirmation.png" alt-text="デプロイの確認メッセージのスクリーンショット。":::

1. これで、これらのリージョンの一覧にデプロイが表示されます。 構成のデプロイが完了するまでに、約 15 分から 20 分かかる場合があります。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deployment-in-progress.png" alt-text="デプロイの一覧の進行中のデプロイのスクリーンショット。":::

## <a name="create-security-configuration"></a>セキュリティ構成を作成する

1. **[設定]** で *[構成]* をもう一度選択し、 **[+ 構成の追加]** を選択します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-security-configuration.png" alt-text="Network Manager の別の構成の追加を示すスクリーンショット。":::

1. メニューから **[SecurityAdmin]** を選択して、SecurityAdmin 構成の作成を開始します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/security-drop-down.png" alt-text="ドロップダウン メニューの SecurityAdmin のスクリーンショット。":::

1. 構成の名前「**mySecurityConfig**」を入力し、 **[+ 規則コレクションの追加]** を選択します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/security-admin-configuration.png" alt-text="[セキュリティ管理構成] ページのスクリーンショット。":::

1. 規則コレクションの名前「**myRuleCollection**」を入力し、ターゲット ネットワーク グループに「**myNetworkGroupB**」を選択します。 **[+ 規則を追加する]** を選択します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-rule-collection.png" alt-text="[規則コレクションの追加] ページのスクリーンショット。":::

1. 次の設定を入力して選択してから、 **[追加]** を選択します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-rule.png" alt-text="[規則の追加] ページのスクリーンショット。":::

1. **[保存]** を選択して、規則コレクションを構成に追加します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/save-rule-collection.png" alt-text="規則コレクションの [保存] ボタンのスクリーンショット。":::

1. **[追加]** を選択して、セキュリティ管理構成を作成します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-configuration.png" alt-text="構成を作成するための [追加] ボタンのスクリーンショット。":::

## <a name="deploy-the-security-admin-configuration"></a>セキュリティ管理構成をデプロイする

1. *[設定]* で **[デプロイ]** を選択し、 **[構成のデプロイ]** を選択します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deployments.png" alt-text="Virtual Network Manager の [セキュリティ デプロイ] ページのスクリーンショット。":::

1. 構成の種類として **[SecurityAdmin]** を選択し、前のセクションで作成した **[mySecurityConfig]** 構成を選択します。 次に、ターゲット リージョンとして **米国西部** および **米国東部** を選択し、 **[デプロイ]** を選択します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deploy-security.png" alt-text="セキュリティ構成のデプロイを示すスクリーンショット。":::

1. **[OK]** を選択して、既存の構成を上書きしてセキュリティ管理構成をデプロイすることを確認します。

    :::image type="content" source="./media/how-to-block-network-traffic-portal/confirm-security.png" alt-text="セキュリティ構成のデプロイに関する確認メッセージのスクリーンショット。":::

1. これで、選択したリージョンの一覧にデプロイが表示されます。 構成のデプロイが完了するまでに、約 15 分から 20 分かかる場合があります。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/security-in-progress.png" alt-text="デプロイの一覧の進行中のセキュリティ デプロイのスクリーンショット。":::

## <a name="verify-deployment-of-configurations"></a>構成のデプロイを確認する

### <a name="verify-from-a-virtual-network"></a>仮想ネットワークから確認する

1. **VNet-A-WestUS** 仮想ネットワークに移動し、 **[ネットワーク マネージャー]** を *[設定]* から選びます。 **HubA** 接続構成が適用されます。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vnet-connectivity-configuration.png" alt-text="仮想ネットワークに適用されている接続構成のスクリーンショット。":::

1. *[設定]* で **[ピアリング]** を選択します。 *AVNM* という名前で Virtual Network Manager によって仮想ネットワーク ピアリングが作成されます。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vnet-peerings.png" alt-text="Virtual Network Manager によって作成された仮想ネットワーク ピアリングのスクリーンショット。":::

1. **[SecurityAdmin]** タブを選択すると、この仮想ネットワークに適用されているセキュリティ管理規則が表示されます。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vnet-admin-configuration.png" alt-text="仮想ネットワークに適用されるセキュリティ管理規則のスクリーンショット。":::

### <a name="verify-from-a-vm"></a>VM から確認する

1. テスト Windows VM を **VNet-A-EastUS** にデプロイします。 

1. *VNet-A-EastUS* で作成される VM に移動し、 *[設定]* の下の **[ネットワーク]** を選択します。 **[送信ポート規則]** を選択すると、セキュリティ管理規則が適用されます。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vm-security-rules.png" alt-text="テスト VM のネットワーク セキュリティ規則のスクリーンショット。":::

1. ネットワーク インターフェイス名を選択します。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vm-network-settings.png" alt-text="テスト VM のネットワーク設定のスクリーンショット。":::

1. 次に、 *[サポート + トラブルシューティング]* で **[有効なルート]** を選択して、仮想ネットワーク ピアリングのルートを確認します。 ネクスト ホップ `VNetGlobalPeering` を使用する `10.3.0.0/16` ルートは、ハブ仮想ネットワークへのルートです。 ネクスト ホップ `ConnectedGroup` を使用する `10.5.0.0/16` ルートは、他のスポーク仮想ネットワークへのルートです。 **[推移性]** が有効になっている場合、すべてのスポーク仮想ネットワークは *ConnectedGroup* に入ります。

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/effective-routes.png" alt-text="テスト VM ネットワーク インターフェイスからの有効なルートのスクリーンショット。" lightbox="./media/tutorial-create-secured-hub-and-spoke/effective-routes-expanded.png" :::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure Virtual Network Manager が不要になった場合は、リソースを削除する前に、次のすべてに当てはまることを確認する必要があります。

* どのリージョンにも構成のデプロイはありません。
* すべての構成が削除されました。
* すべてのネットワーク グループが削除されました。

[コンポーネント削除のチェックリスト](concept-remove-components-checklist.md)を使用して、リソース グループを削除する前に、使用可能な子リソースが存在しないことを確認します。

## <a name="next-steps"></a>次のステップ

[セキュリティ管理構成](how-to-block-network-traffic-portal.md)を使用してネットワーク トラフィックをブロックする方法を学びます。
