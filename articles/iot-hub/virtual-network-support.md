---
title: 仮想ネットワークの Azure IoT Hub サポート
description: IoT Hub で仮想ネットワークの接続パターンを使用する方法
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 10/20/2021
ms.author: jlian
ms.openlocfilehash: bd5a88bb8fc47e5f4815aea57544d6093281999b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130258869"
---
# <a name="iot-hub-support-for-virtual-networks-with-private-link-and-managed-identity"></a>Private Link とマネージド ID を使用した仮想ネットワークの IoT Hub サポート

既定では、IoT Hub のホスト名は、インターネット経由でパブリックにルーティング可能な IP アドレスを持つパブリック エンドポイントにマップされます。 この IoT Hub パブリック エンドポイントはさまざまな顧客によって共有され、IoT デバイスはワイドエリア ネットワークやオンプレミス ネットワーク経由ですべてアクセスできます。

![IoT Hub のパブリック エンドポイント](./media/virtual-network-support/public-endpoint.png)

[メッセージのルーティング](./iot-hub-devguide-messages-d2c.md)、[ファイルのアップロード](./iot-hub-devguide-file-upload.md)、[デバイスの一括インポートとエクスポート](./iot-hub-bulk-identity-mgmt.md)などの IoT Hub 機能では、パブリック エンドポイント経由での IoT Hub から顧客所有の Azure リソースへの接続も必要になります。 IoT Hub から顧客リソースへのエグレス トラフィックは、これらの接続パスでまとめて構成されています。

自分が所有して運用する VNet を介した Azure リソース (IoT Hub を含む) への接続を制限したい場合があります。 これらの理由には以下のものが含まれます。

* パブリック インターネットへの接続の露出を防ぐことにより、IoT Hub にネットワークの分離を導入する。

* オンプレミスのネットワーク資産からのプライベート接続エクスペリエンスを有効にし、データとトラフィックが Azure バックボーン ネットワークに直接送信されるようにする。

* 機微なオンプレミス ネットワークからの抜き取り攻撃を防ぐ。 

* [プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用して確立された Azure 全体の接続パターンに従う。

この記事では、IoT Hub へのイングレス接続に [Azure Private Link](../private-link/private-link-overview.md) を使用し、IoT Hub から他の Azure リソースへのエグレス接続に信頼された Microsoft サービスの例外を使用して、これらの目標を達成する方法について説明します。

## <a name="ingress-connectivity-to-iot-hub-using-azure-private-link"></a>Azure Private Link を使用した IoT Hub へのイングレス接続

プライベート エンドポイントは、Azure リソースに到達可能な、顧客所有の VNet 内に割り当てられたプライベート IP アドレスです。 Azure Private Link を使用すると、IoT Hub 用にプライベート エンドポイントを設定して、IoT Hub のパブリック エンドポイントにトラフィックを送信しなくても、VNet 内のサービスが IoT Hub に到達できるようにすることができます。 同様に、オンプレミスのデバイスでは [仮想プライベート ネットワーク (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) または [ExpressRoute](https://azure.microsoft.com/services/expressroute/) ピアリングを使用して、VNet と (プライベート エンドポイント経由で) IoT Hub に接続することができます。 その結果、[IoT Hub の IP フィルター](./iot-hub-ip-filtering.md)または[パブリック ネットワーク アクセスの切り替え](iot-hub-public-network-access.md)を使用することで、IoT Hub のパブリック エンドポイントへの接続を制限または完全にブロックすることができます。 この方法では、デバイスのプライベート エンドポイントを使用して、ハブへの接続を維持します。 この設定の主な対象は、オンプレミス ネットワーク内のデバイスです。 ワイドエリア ネットワークにデプロイされているデバイスには、この設定はお勧めできません。

![IoT Hub 仮想ネットワークのエグレス](./media/virtual-network-support/virtual-network-ingress.png)

続行する前に、確実に次の前提条件が満たされているようにしてください。

* プライベート エンドポイントが作成されるサブネットを持つ [Azure VNet を作成している](../virtual-network/quick-create-portal.md)。

* オンプレミス ネットワークの内部で動作するデバイスの場合は、[仮想プライベート ネットワーク (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md)、または Azure VNET への [ExpressRoute](https://azure.microsoft.com/services/expressroute/) プライベート ピアリングを設定する。

### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>IoT Hub のイングレス用のプライベート エンドポイントを設定する

プライベート エンドポイントは、IoT Hub デバイス API (device-to-cloud メッセージなど) とサービス API (デバイスの作成や更新など) で機能します。

1. Azure portal で、 **[ネットワーク]** 、 **[プライベート アクセス]** の順に選択し、 **[+ プライベート エンドポイントの作成]** オプションをクリックします。

    :::image type="content" source="media/virtual-network-support/private-link.png" alt-text="IoT Hub 用にプライベート エンドポイントを追加する場所を示すスクリーンショット" border="true":::

1. サブスクリプション、リソース グループ、名前、およびリージョンを指定して、新しいプライベート エンドポイントを作成します。 理想としては、プライベート エンドポイントをハブと同じリージョンに作成することをお勧めします。

1. **[次へ: リソース]** をクリックし、IoT Hub リソースのサブスクリプションを指定し、リソースの種類として **"Microsoft.Devices/IotHubs"** 、**リソース** として IoT Hub の名前、ターゲット サブリソースとして **iotHub** を選択します。

1. **[次へ: 構成]** をクリックし、プライベート エンドポイントの作成先となる仮想ネットワークとサブネットを指定します。 必要に応じて、Azure プライベート DNS ゾーンと統合するオプションを選択します。

1. **[次へ: タグ]** をクリックし、必要に応じて、リソースのタグを指定します。

1. **[確認および作成]** をクリックして、プライベート リンク リソースを作成します。

### <a name="built-in-event-hub-compatible-endpoint"></a>組み込みのイベント ハブ互換エンドポイント 

[組み込みのイベント ハブ互換エンドポイント](iot-hub-devguide-messages-read-builtin.md)には、プライベート エンドポイント経由でもアクセスできます。 プライベート リンクを構成すると、組み込みエンドポイントの追加のプライベート エンドポイント接続が表示されます。 FQDN に `servicebus.windows.net` が含まれているものです。

:::image type="content" source="media/virtual-network-support/private-built-in-endpoint.png" alt-text="それぞれ IoT Hub プライベート リンクが指定されている、2 つのプライベート エンドポイントを示す画像":::

IoT Hub の [IP フィルター](iot-hub-ip-filtering.md)によって、必要に応じて、組み込みのエンドポイントへのパブリック アクセスを制御できます。 

IoT Hub へのパブリック ネットワーク アクセスを完全にブロックするには、[パブリック ネットワーク アクセスをオフにする](iot-hub-public-network-access.md)か、IP フィルターを使用してすべての IP をブロックし、組み込みのエンドポイントに規則を適用するオプションを選択します。

### <a name="pricing-for-private-link"></a>Private Link の価格

料金の詳細については、「[Azure Private Link の料金](https://azure.microsoft.com/pricing/details/private-link)」をご覧ください。

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>IoT Hub から他の Azure リソースへのエグレス接続

IoT Hub は、リソースのパブリック エンドポイントを経由した[メッセージのルーティング](./iot-hub-devguide-messages-d2c.md)、[ファイルのアップロード](./iot-hub-devguide-file-upload.md)、[デバイスの一括インポートとエクスポート](./iot-hub-bulk-identity-mgmt.md)のために Azure BLOB ストレージ、イベント ハブ、Service Bus のリソースに接続できます。 リソースを VNet にバインドすると、リソースへの接続が既定でブロックされます。 その結果、この構成では、IoT Hub がリソースにデータを送信できなくなります。 この問題を解決するには、**信頼された Microsoft サービス** オプションを使用して、IoT Hub リソースからストレージ アカウント、イベント ハブ、または Service Bus のリソースへの接続を有効にします。 

他のサービスが IoT ハブを信頼された Microsoft サービスとして検出できるようにするには、ハブでマネージド ID を使用する必要があります。 マネージド ID がプロビジョニングされたら、カスタム エンドポイントにアクセスするために、ハブのマネージド ID に Azure RBAC アクセス許可を付与する必要があります。 [IoT Hub でのマネージド ID のサポート](./iot-hub-managed-identity.md)に関する記事に従って、Azure RBAC アクセス許可でマネージド ID をプロビジョニングし、カスタム エンドポイントを IoT Hub に追加します。 ファイアウォールの構成が整っている場合は、信頼された Microsoft ファースト パーティの例外をオンにして、IoT Hub がカスタム エンドポイントにアクセスできるようにしてください。

### <a name="pricing-for-trusted-microsoft-service-option"></a>信頼された Microsoft サービス オプションの価格
信頼された Microsoft ファースト パーティ サービスの例外機能は、無料でご利用いただけます。 プロビジョニング済みのストレージ アカウント、Event Hubs、または Service Bus リソースの料金は個別に適用されます。
## <a name="next-steps"></a>次のステップ

IoT Hub の機能の詳細について、下記のリンク使用してください。

* [メッセージ ルーティング](./iot-hub-devguide-messages-d2c.md)
* [ファイルのアップロード](./iot-hub-devguide-file-upload.md)
* [デバイスの一括デインポートとエクスポート](./iot-hub-bulk-identity-mgmt.md)
