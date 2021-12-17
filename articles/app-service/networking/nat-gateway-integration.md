---
title: NAT ゲートウェイの統合 - Azure App Service | Microsoft Docs
description: NAT ゲートウェイを Azure App Service と統合する方法について説明します。
services: app-service
author: madsd
ms.assetid: 0a84734e-b5c1-4264-8d1f-77e781b28426
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 08/04/2021
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: e5a6f2fe52a9b74f74047a5a9512e40bb267161f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130245286"
---
# <a name="virtual-network-nat-gateway-integration"></a>Virtual Network NAT ゲートウェイの統合

NAT ゲートウェイは、完全に管理された回復性があるサービスで、1 つ以上のサブネットに関連付けることができ、インターネットに接続されたすべての送信トラフィックがゲートウェイ経由でルーティングされるようにします。 App Service では、NAT ゲートウェイを使用できる 2 つの重要なシナリオがあります。 

NAT ゲートウェイにより、インターネットに接続された送信トラフィックに対して予測可能な静的パブリック IP が提供されます。 また、同じパブリック アドレスとポートの組み合わせへのコンカレント接続数が多いシナリオでは、使用可能な [SNAT ポート](../troubleshoot-intermittent-outbound-connection-errors.md)も大幅に増加します。

詳細と価格については、 [NAT ゲートウェイの概要](../../virtual-network/nat-gateway/nat-overview.md)に関する記事を参照してください。

:::image type="content" source="./media/nat-gateway-integration/nat-gateway-overview.png" alt-text="図には、Azure Virtual Network 内で NAT ゲートウェイに送信されるインターネット トラフィックが示されています。":::

> [!Note] 
> App Service での NAT ゲートウェイの使用は、リージョンの VNet 統合に依存しているため、 **[Standard]** 、 **[Premium]** 、 **[PremiumV2]** または **[PremiumV3]** App Service プランが必要です。

## <a name="configuring-nat-gateway-integration"></a>NAT ゲートウェイ統合の構成

App Service との NAT ゲートウェイ統合を構成するには、次の手順を実行する必要があります。

* 「[アプリを Azure 仮想ネットワークと統合する](../overview-vnet-integration.md)」の説明に従って、アプリとのリージョン VNet 統合を構成します
* VNet 統合に対して [[Route All]\(すべてルーティング\)](../overview-vnet-integration.md#routes) が有効になっていることを確認します。これにより、インターネットにバインドされたトラフィックは VNet 内のルートの影響を受けるようになります。
* パブリック IP を使用して NAT ゲートウェイをプロビジョニングし、VNet 統合サブネットに関連付けます。

ポータルを使用して NAT ゲートウェイを設定します。

1. App Service ポータルの **[ネットワーク]** UI にアクセスし、[Outbound Traffic]\(送信トラフィック\) セクションで [VNet 統合] を選択します。 アプリがサブネットと統合されていて、 **[Route All]\(すべてルーティング\)** が有効になっていることを確認します。
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-route-all-enabled.png" alt-text="VNet 統合に対して有効になっている [Route All]\(すべてルーティング\) のスクリーンショット。":::
1. Azure portal メニュー上または **[ホーム]** ページから **[リソースの作成]** を選択します。 **[新規作成]** ウィンドウが表示されます。
1. "NAT ゲートウェイ" を検索し、結果の一覧から選択します。
1. **[基本]** 情報を入力し、アプリが配置されているリージョンを選択します。
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-create-basics.png" alt-text="NAT ゲートウェイの作成の [基本] タブのスクリーンショット。":::
1. **[Outbound IP]\(送信 IP\)** タブで、パブリック IP アドレスを新規に作成するか、既存のものを選択します。
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-create-outbound-ip.png" alt-text="NAT ゲートウェイの作成の [Outbound IP]\(送信 IP\) タブのスクリーンショット。":::
1. **[サブネット]** タブで、VNet 統合に使用するサブネットを選択します。
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-create-subnet.png" alt-text="NAT ゲートウェイの作成の [サブネット] タブのスクリーンショット。":::
1. 必要に応じてタグを入力し、NAT ゲートウェイを **作成** します。 NAT ゲートウェイがプロビジョニングされたら、 **[リソース グループに移動]** をクリックし、新しい NAT ゲートウェイを選択します。 [Outbound IP]\(送信 IP\) ブレードで、インターネットに接続された送信トラフィックに対してアプリで使用されるパブリック IP を確認できます。
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-public-ip.png" alt-text="NAT ゲートウェイ ポータルの [Outbound IP]\(送信 IP\) ブレードのスクリーンショット。"::: 

CLI を使用して環境を構成する場合は、これらのコマンドが重要です。 前提条件として、VNet 統合が構成された Web アプリを作成する必要があります。

**[Route All]\(すべてルーティング\)** が VNet 統合に対して構成されていることを確認します (*注*: 必要な最小の `az version` は 2.27 です)。

```azurecli-interactive
az webapp config set --resource-group [myResourceGroup] --name [myWebApp] --vnet-route-all-enabled
```

パブリック IP と NAT ゲートウェイを作成します。

```azurecli-interactive
az network public-ip create --resource-group [myResourceGroup] --name myPublicIP --sku standard --allocation static

az network nat gateway create --resource-group [myResourceGroup] --name myNATgateway --public-ip-addresses myPublicIP --idle-timeout 10
```

NAT ゲートウェイを VNet 統合サブネットに関連付けます。

```azurecli-interactive
az network vnet subnet update --resource-group [myResourceGroup] --vnet-name [myVnet] --name [myIntegrationSubnet] --nat-gateway myNATgateway
```

## <a name="scaling-nat-gateway"></a>NAT ゲートウェイのスケーリング

同じ仮想ネットワーク内の複数のサブネット間で同じ NAT ゲートウェイを使用して、複数のアプリや App Service プランで NAT ゲートウェイを使用することができます。

NAT ゲートウェイでは、パブリック IP アドレスとパブリック IP プレフィックスの両方がサポートされます。 NAT ゲートウェイでは、個々の IP アドレスとプレフィックスに対して最大 16 個の IP アドレスをサポートできます。 各 IP アドレスによって 64,000 ポート (SNAT ポート) が割り当てられ、最大 1M 個の使用可能ポートが許可されます。 NAT ゲートウェイの[スケーリングのセクション](../../virtual-network/nat-gateway/nat-gateway-resource.md#scaling)で詳細を確認してください。

## <a name="next-steps"></a>次のステップ
NAT ゲートウェイの詳細については、[NAT ゲートウェイのドキュメント](../../virtual-network/nat-gateway/nat-overview.md)を参照してください。

VNet 統合の詳細については、[VNet 統合のドキュメント](../overview-vnet-integration.md)を参照してください。