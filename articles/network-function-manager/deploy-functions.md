---
title: 'チュートリアル: Azure Stack Edge にネットワーク機能をデプロイする'
titleSuffix: Azure Network Function Manager
description: このチュートリアルでは、ネットワーク機能をマネージド アプリケーションとしてデプロイする方法について説明します。
author: cherylmc
ms.service: network-function-manager
ms.topic: tutorial
ms.date: 06/16/2021
ms.author: cherylmc
ms.openlocfilehash: af82a3b51da76e181c2e5856458d553051b0c409
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2021
ms.locfileid: "113129004"
---
# <a name="tutorial-deploy-network-functions-on-azure-stack-edge-preview"></a>チュートリアル: Azure Stack Edge にネットワーク機能をデプロイする (プレビュー)

このチュートリアルでは、Azure Marketplace を使用して Azure Stack Edge にネットワーク機能をデプロイする方法について説明します。 Network Function Manager を使用すると、Azure Stack Edge 上でのシンプルなデプロイのための Azure Managed Applications エクスペリエンスを実現します。

> [!div class="checklist"]
> * [前提条件](overview.md#prereq)を確認する
> * ネットワーク機能を作成する
> * ネットワーク機能の詳細を確認する

## <a name="prerequisites"></a>前提条件

* Network Function Manager のデバイス リソースを作成していること。 これらの手順を完了していない場合は、[デバイス リソースの作成方法](create-device.md)に関するページを参照してください。
* デバイスの **[概要]** タブに、次の値が表示されていることを確認します。
  * プロビジョニングの状態 = 成功
  * デバイスの状態 = 登録済み

## <a name="create-a-network-function"></a><a name="create"></a>ネットワーク機能を作成する

1. [Azure プレビュー ポータル](https://aka.ms/AzureNetworkFunctionManager)にサインインします。
1. ネットワーク機能をデプロイする **デバイス** リソースに移動し、 **[+ ネットワーク機能の作成]** を選択します。

   :::image type="content" source="./media/deploy-functions/create-network-function.png" alt-text="[+ ネットワーク機能の作成] のスクリーンショット。" lightbox="./media/deploy-functions/create-network-function.png":::
1. ドロップダウンから、使用する **[ベンダー SKU]** を選択し、 **[作成]** をクリックします。

   :::image type="content" source="./media/deploy-functions/select.png" alt-text="ベンダー SKU のスクリーンショット。" lightbox="./media/deploy-functions/select.png":::
1. 選択した SKU に応じて、ネットワーク機能のマネージド アプリケーションの Marketplace ポータルにリダイレクトされます。
 
   各ネットワーク機能パートナーには、Azure Stack Edge にネットワーク機能をデプロイするためのさまざまな要件があります。 さらに、モバイル パケット コアや SD-WAN エッジなどの一部のネットワーク機能では、ネットワーク機能をデプロイする前に、管理、LAN、および WAN のポートを構成し、これらのポートに IP アドレスを割り当てることが必要になる場合があります。 必要なプロパティと Azure Stack Edge デバイスのネットワーク構成については、パートナーにお問い合わせください。
   
   > [!IMPORTANT]
   > 管理、LAN、または WAN 仮想ネットワーク インターフェイスの静的 IP アドレスをサポートするすべてのネットワーク機能では、特定のポートに割り当てられた IP アドレス範囲の最初の 4 つの IP アドレスを使用しないようにしてください。 これらの IP アドレスは、Azure Stack Edge サービス用の予約済み IP アドレスです。
   >

1. Marketplace ポータルの手順に従って、パートナーマネージド アプリケーションをデプロイします。 マネージド アプリケーションのプロビジョニングに成功すると、リソース グループにマネージド アプリが表示されるようになります。 ネットワーク機能リソースのベンダー プロビジョニング ステータスが [プロビジョニング済み] になっているかどうかを確認するには、管理対象リソース グループにアクセスします。 これにより、ネットワーク機能のデプロイが成功し、必要な追加設定をネットワーク機能パートナーの管理ポータルからプロビジョニングできることが確認できます。 Network Function Manager を使用した初期デプロイ後の管理エクスペリエンスについては、ネットワーク機能パートナーにお問い合わせください。

### <a name="example"></a>例

1. Marketplace で **[Fusion Core – 5G Packet Core]** オファーを検索し、 **[作成]** をクリックしてネットワーク機能の作成を開始します。

   :::image type="content" source="./media/deploy-functions/metaswitch.png" alt-text="Metaswitch のページのスクリーンショット。" lightbox="./media/deploy-functions/metaswitch.png":::
1. 基本設定を構成します。

   :::image type="content" source="./media/deploy-functions/basics-blade.png" alt-text="基本設定のスクリーンショット。" lightbox="./media/deploy-functions/basics-blade.png":::
1. マネージド ID を適用します。 詳細については、[マネージド ID](overview.md#managed-identity) に関するセクションを参照してください。

   :::image type="content" source="./media/deploy-functions/managed-identity.png" alt-text="マネージド ID のスクリーンショット。" lightbox="./media/deploy-functions/managed-identity.png":::
1. Fusion Core VM の管理、LAN、および WAN インターフェイスの IP アドレス情報を入力します。

   :::image type="content" source="./media/deploy-functions/ip-settings.png" alt-text="Fusion Core VM の管理、LAN、および WAN インターフェイスのスクリーンショット。" lightbox="./media/deploy-functions/ip-settings.png":::
1. 5G および UE テストの構成のオプション設定を入力します。

   :::image type="content" source="./media/deploy-functions/5g-settings-blade.png" alt-text="5G のスクリーンショット。" lightbox="./media/deploy-functions/5g-settings-blade.png":::

   :::image type="content" source="./media/deploy-functions/test-blade.png" alt-text="UE テストの構成のスクリーンショット。" lightbox="./media/deploy-functions/test-blade.png":::
1. 検証に合格したら、契約条件に同意します。 次に、 **[作成]** をクリックして、カスタマー リソース グループに Fusion Core Managed Application、そして管理対象リソース グループにネットワーク機能リソースを作成します。 ネットワーク機能リソースを表示するには、管理対象リソース グループ ビューで **[非表示の種類の表示]** ボックスをオンにする必要があります。

   :::image type="content" source="./media/deploy-functions/managed-app-customer.png" alt-text="カスタマー リソース グループのマネージド アプリを示すスクリーンショット。" lightbox="./media/deploy-functions/managed-app-customer.png":::

   :::image type="content" source="./media/deploy-functions/managed-resource-group.png" alt-text="管理対象リソース グループのネットワーク機能を示すスクリーンショット。" lightbox="./media/deploy-functions/managed-resource-group.png":::

## <a name="next-steps"></a>次のステップ

ベンダー ポータルに移動して、ネットワーク機能の構成を完了します。
