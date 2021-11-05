---
title: Azure IoT Hub のパブリック ネットワーク アクセスの管理
description: IoT ハブのパブリック ネットワーク アクセスを無効および有効にする方法に関するドキュメント
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/07/2021
ms.openlocfilehash: b77fa4d3e82114c4b52edca250aada9837157782
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068289"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>IoT ハブのパブリック ネットワーク アクセスの管理

[VNet 内の IoT ハブのプライベート エンドポイント](virtual-network-support.md)のみにアクセスを制限するには、パブリック ネットワーク アクセスを無効にします。 そうするには、Azure portal または `publicNetworkAccess` API を使用します。 ポータルまたは `publicNetworkAccess` API を使用してパブリック アクセスを許可することもできます。

## <a name="turn-off-public-network-access-using-the-azure-portal"></a>Azure portal を使用してパブリック ネットワーク アクセスを無効にする

1. [Azure portal](https://portal.azure.com) に移動します
2. IoT Hub に移動します。 **[リソース グループ]** に移動し、適切なグループを選択して、ご自分の IoT Hub を選択します。
3. 左側のメニューで **[ネットワーキング]** を選択します。
4. [Allow public network access to]\(パブリック ネットワーク アクセスを許可する\) で、 **[無効]** を選択します
5. **[保存]** を選択します。

:::image type="content" source="./media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="パブリックネットワークアクセスを無効にする Azure portal を示すスクリーンショット。":::

パブリック ネットワーク アクセスを有効にするには、 **[すべてのネットワーク]** 、 **[保存]** の順に選択します。

### <a name="accessing-the-iot-hub-after-disabling-the-public-network-access"></a>パブリック ネットワーク アクセスを無効にした後の IoT Hub へのアクセス

パブリック ネットワーク アクセスを無効にした後、IoT Hub には、[その VNet プライベート エンドポイントから Azure プライベート リンクを使用して](virtual-network-support.md)アクセスする必要があります。 この制限には、Azure portal からのアクセスも含まれます。なぜなら、IoT Hub サービスの API 呼び出しは、お使いのブラウザーと資格情報を使用して直接行われるからです。

### <a name="iot-hub-endpoint-ip-address-and-ports-after-disabling-public-network-access"></a>パブリック ネットワーク アクセスを無効にした後の IoT Hub エンドポイント、IP アドレス、およびポート

IoT Hub はマルチテナントのサービスとしてのプラットフォーム (PaaS) であるため、異なる顧客がコンピューティング、ネットワーク、およびストレージのハードウェア リソースの同じプールを共有します。 IoT Hub のホスト名は、インターネット経由でパブリックにルーティング可能な IP アドレスを持つパブリック エンドポイントにマップされます。 この IoT Hub パブリック エンドポイントはさまざまな顧客によって共有され、IoT デバイスはワイドエリア ネットワークやオンプレミス ネットワーク経由ですべてアクセスできます。 

特定の IoT Hub リソースでは、パブリック ネットワーク アクセスの無効化が強制され、分離が保証されます。 パブリック パスを使用して他のカスタマー リソースに対してサービスをアクティブな状態に保つために、パブリック エンドポイントは解決可能な状態のままで、IP アドレスは検出可能で、ポートは開いたままになります。 テナント間の完全な分離を実現するために、Microsoft では複数のセキュリティ層が統合されるため、これは問題ではありません。 詳細については、「[Azure Public Cloud での分離](../security/fundamentals/isolation-choices.md#tenant-level-isolation)」を参照してください。

### <a name="ip-filter"></a>IP フィルター

パブリック ネットワーク アクセスが無効になっている場合、すべての [IP フィルター](iot-hub-ip-filtering.md)規則は無視されます。 これは、パブリック インターネットからのすべての IP がブロックされるためです。 IP フィルターを使用するには、 **[Selected IP ranges]\(選択された IP 範囲\)** オプションを使用します。

### <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>組み込みのイベント ハブ互換エンドポイントのバグの修正

IoT Hub には、IoT Hub へのパブリック ネットワーク アクセスが無効になっている場合でも、[組み込みのイベント ハブ互換性エンドポイント](iot-hub-devguide-messages-read-builtin.md)がパブリック インターネット経由でアクセス可能であり続けるというバグがあります。 このバグの詳細とお問い合わせについては、[IoT Hub に対するパブリック ネットワーク アクセスの無効化による組み込みのイベント ハブ エンドポイントへのアクセスの無効化](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix)に関するページを参照してください。

## <a name="turn-on-network-access-using-azure-portal"></a>Azure portal を使用してネットワーク アクセスを有効にする

1. [Azure portal](https://portal.azure.com) にアクセスします。
2. IoT Hub に移動します。 **[リソース グループ]** に移動し、適切なグループを選択して、ご自分のハブを選択します。
3. 左側のメニューで **[ネットワーキング]** を選択します。
4. [Allow public network access to]\(パブリック ネットワーク アクセスを許可する\) で、 **[Selected IP ranges]\(選択された IP 範囲\)** を選びます。
5. 開いている **[IP フィルター]** ダイアログで、 **[クライアント IP アドレスを追加する]** を選択し、名前とアドレス範囲を入力します。
6. **[保存]** を選択します。 ボタンが淡色表示されている場合は、お使いのクライアント IP アドレスが IP フィルターとして既に追加されていることをご確認ください。

:::image type="content" source="./media/iot-hub-publicnetworkaccess/turn-on-public-network-access.png" alt-text="パブリックネットワークアクセスを有効にする Azure portal を示すスクリーンショット。":::

### <a name="turn-on-all-network-ranges"></a>すべてのネットワーク範囲を有効にする

1. IoT Hub に移動します。 **[リソース グループ]** に移動し、適切なグループを選択して、ご自分のハブを選択します。
1. 左側のメニューで **[ネットワーキング]** を選択します。
1. [Allow public network access to]\(パブリック ネットワーク アクセスを許可する\) で、 **[すべてのネットワーク]** を選択します。
1. **[保存]** を選択します。

### <a name="check-iot-hub-access-using-cloud-shell"></a>Cloud Shell を使用して IoT ハブ アクセスを確認する

Azure Cloud Shell を使用して IoT ハブ アクセスを確認することができます。 すべてのネットワーク範囲が有効になっていることを確認してから、以下のコマンドを発行します。 "SubscriptionName" をご利用のサブスクリプションの名前に、"MyIoTHub" をご利用のハブの名前に置き換えます。

```azurecli
  az account set -s "SubscriptionName"
  az iot hub device-identity list --hub-name "MyIoTHub"
```

```azurepowershell
  Set-AzContext -Name "SubscriptionName"
  Get-AzIoTHubDevice -IotHubName "MyIoTHub"
```
### <a name="troubleshooting"></a>トラブルシューティング

ご利用の IoT ハブへのアクセス時にトラブルが生じた場合は、ネットワーク構成に問題があるおそれがあります。 たとえば、[IoT デバイス] ページへのアクセス試行時に次のエラー メッセージが表示される場合は、 **[ネットワーク]** ページで、パブリック ネットワーク アクセスが無効になっているか、または選択した IP 範囲に制限されているかを確認します。

```
  Unable to retrieve devices. Please ensure that your network connection is online and network settings allow connections from your IP address.
```

IoT ハブにアクセスするには、担当の IT 管理者にアクセス許可を要求して、ご利用の IP アドレスを IP アドレス範囲に追加してもらうか、またはすべてのネットワークへのパブリック ネットワーク アクセスを有効にしてもらってください。 その問題が解決しない場合は、ご利用のローカル ネットワーク設定を確認するか、担当のローカル ネットワーク管理者にご連絡いただき、IoT Hub への接続の修正を依頼してください。 たとえば、ローカル ネットワーク内のプロキシによって、IoT Hub へのアクセスが妨害される場合があります。

上記のコマンドが機能しない場合、またはすべてのネットワーク範囲を有効にできない場合は、Microsoft サポートにお問い合わせください。
