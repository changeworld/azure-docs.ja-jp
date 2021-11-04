---
title: 'チュートリアル: Azure Stack Edge にネットワーク機能をデプロイする'
titleSuffix: Azure Network Function Manager
description: このチュートリアルでは、ネットワーク機能をマネージド アプリケーションとしてデプロイする方法について説明します。
author: prmitt
ms.service: network-function-manager
ms.topic: tutorial
ms.date: 11/02/2021
ms.author: prmitt
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3ac4c4e0654e1d5f22c128c45106079916676a4f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005083"
---
# <a name="tutorial-deploy-network-functions-on-azure-stack-edge"></a>チュートリアル: Azure Stack Edge にネットワーク機能をデプロイする

このチュートリアルでは、Azure Marketplace を使用して Azure Stack Edge にネットワーク機能をデプロイする方法について説明します。 Network Function Manager を使用すると、Azure Stack Edge 上でのシンプルなデプロイのための Azure Managed Applications エクスペリエンスを実現します。

> [!div class="checklist"]
> * [前提条件](#prereq)を確認する
> * ネットワーク機能を作成する
> * ネットワーク機能の詳細を確認する

## <a name="prerequisites"></a><a name="prereq"></a>前提条件

* [前提条件と要件](requirements.md)に関する記事に記載されている前提条件がすべて満たされています。
* Network Function Manager のデバイス リソースを作成していること。 これらの手順を完了していない場合は、[デバイス リソースの作成方法](create-device.md)に関するページを参照してください。
* デバイスの **[概要]** タブに、次の値が表示されていることを確認します。
  * プロビジョニングの状態 = 成功
  * デバイスの状態 = 登録済み

## <a name="create-a-network-function"></a><a name="create"></a>ネットワーク機能を作成する

1. [Azure Portal](https://portal.azure.com) にサインインします。
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
1. マネージド ID を適用します。 詳細については、[マネージド ID](resources-permissions.md) に関するセクションを参照してください。

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
