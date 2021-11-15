---
title: Azure IoT Device Provisioning Service (DPS) のパブリック ネットワーク アクセスを管理する
description: Azure IoT Device Provisioning Service (DPS) のパブリック ネットワーク アクセスを無効および有効にする方法に関するドキュメント
ms.author: v-stharr
author: anastasia-ms
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/18/2021
ms.openlocfilehash: 74c22d802c022d51a1ef8b4aa231cc92661d582b
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858432"
---
# <a name="manage-public-network-access-for-your-iot-device-provisioning-service"></a>IoT Device Provisioning Service のパブリック ネットワーク アクセスを管理する

[VNet 内の DPS のプライベート エンドポイント](virtual-network-support.md)へのアクセスを制限するには、パブリック ネットワーク アクセスを無効にします。 そうするには、Azure portal または `publicNetworkAccess` API を使用します。 ポータルまたは `publicNetworkAccess` API を使用してパブリック アクセスを許可することもできます。

## <a name="turn-off-public-network-access-using-the-azure-portal"></a>Azure portal を使用してパブリック ネットワーク アクセスを無効にする

パブリック ネットワーク アクセスを無効にするには:

1. [Azure portal](https://portal.azure.com) にサインインします。
2. 左側のメニューまたはポータル ページで、 **[すべてのリソース]** を選択します。
3. ご利用の Device Provisioning Service を選択します。
4. 左側の **[設定]** メニューで *[ネットワーク]* を選択します。
5. **[パブリック ネットワーク アクセス]** で *[無効]* を選択します
6. **[保存]** を選択します。

    :::image type="content" source="media/iot-dps-public-network-access/disable-public-access.png" alt-text="パブリック ネットワーク アクセスを無効にしている Azure portal を示す画像" :::

パブリック ネットワーク アクセスを有効にするには:

1. **[すべてのネットワーク]** を選択します。
2. **[保存]** を選択します。

## <a name="access-the-dps-after-disabling-the-public-network-access"></a>パブリック ネットワーク アクセスを無効にした後に DPS にアクセスする

パブリック ネットワーク アクセスを無効にした後、DPS インスタンスには、[Azure プライベート リンクを使用して、その VNet プライベート エンドポイントから](virtual-network-support.md)のみアクセスできます。 この制限には、Azure portal によるアクセスも含まれます。

## <a name="dps-endpoint-ip-address-and-ports-after-disabling-public-network-access"></a>パブリック ネットワーク アクセスを無効にした後の DPS エンドポイント、IP アドレス、ポート

DPS はマルチテナントのサービスとしてのプラットフォーム (PaaS) であるため、異なる顧客がコンピューティング、ネットワーク、ストレージのハードウェア リソースの同じプールを共有します。 DPS のホスト名は、インターネット経由でパブリックにルーティング可能な IP アドレスを持つパブリック エンドポイントにマップされます。 異なる顧客がこの DPS パブリック エンドポイントを共有します。ワイドエリア ネットワークとオンプレミス ネットワーク内の IoT デバイスは、すべてこの DPS パブリック エンドポイントにアクセスできます。 

パブリック ネットワーク アクセスの無効化は、特定の DPS リソースに適用され、分離が保証されます。 パブリック パスを使用して他のカスタマー リソースに対してサービスをアクティブな状態に保つために、パブリック エンドポイントは解決可能な状態のままで、IP アドレスは検出可能で、ポートは開いたままになります。 テナント間の完全な分離を実現するために、Microsoft では複数のセキュリティ層が統合されるため、これは問題ではありません。 詳細については、「[Azure Public Cloud での分離](../security/fundamentals/isolation-choices.md#tenant-level-isolation)」を参照してください。

## <a name="ip-filter"></a>IP フィルター

パブリック ネットワーク アクセスが無効になっている場合、すべての [IP フィルター](../iot-dps/iot-dps-ip-filtering.md)規則は無視されます。 これは、パブリック インターネットからのすべての IP がブロックされるためです。 IP フィルターを使用するには、 **[Selected IP ranges]\(選択された IP 範囲\)** オプションを使用します。

### <a name="turn-on-all-network-ranges"></a>すべてのネットワーク範囲を有効にする

すべてのネットワーク範囲を有効にするには:

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. 左側のメニューまたはポータル ページで、 **[すべてのリソース]** を選択します。
3. ご利用の Device Provisioning Service を選択します。
4. 左側の **[設定]** メニューで *[ネットワーク]* を選択します。
5. **[パブリック ネットワーク アクセス]** で *[すべてのネットワーク]* を選択します
6. **[保存]** を選択します。
