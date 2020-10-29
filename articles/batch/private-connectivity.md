---
title: Azure Batch アカウントでプライベート エンドポイントを使用する
description: プライベート エンドポイントを使用して、Azure Batch アカウントにプライベートに接続する方法について説明します。
ms.topic: how-to
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: f797dbda7888eb8ea9f5c76e3b527fb98d896ee4
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92669017"
---
# <a name="use-private-endpoints-with-azure-batch-accounts"></a>Azure Batch アカウントでプライベート エンドポイントを使用する

既定では、[Azure Batch アカウント](accounts.md)にはパブリック エンドポイントがあり、パブリックにアクセスできます。 Batch サービスでは、プライベート Batch アカウントを作成し、パブリック ネットワーク アクセスを無効にすることができます。

[Azure Private Link](../private-link/private-link-overview.md) を使用することにより、[プライベート エンドポイント](../private-link/private-endpoint-overview.md)経由で Azure Batch アカウントに接続できます。 プライベート エンドポイントは、仮想ネットワークのサブネットにある一組のプライベート IP アドレスです。 これで、プライベート IP アドレスを使用して Azure Batch アカウントへのアクセスを制限できるようになります。

Private Link を使用すると、ユーザーは、仮想ネットワーク内から、またはピアリングされた任意の仮想ネットワークから、Azure Batch アカウントにアクセスできます。 Private Link にマップされたリソースは、プライベート ピアリングを使用して、VPN または [Azure ExpressRoute](../expressroute/expressroute-introduction.md) 経由でオンプレミスからアクセスすることもできます。 [自動または手動の承認方法](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow)により、Private Link を使用して構成された Azure Batch アカウントに接続できます。

> [!IMPORTANT]
> 現在、Azure Batch でのプライベート接続は、ドイツ中部とドイツ北東部を除くすべてのパブリック リージョンでサポートされています。

この記事では、プライベート Batch アカウントを作成し、プライベート エンドポイントを使用してそれにアクセスする手順について説明します。

## <a name="azure-portal"></a>Azure portal

Azure portal を使用してプライベート Batch アカウントを作成するには、以下の手順のようにします。

1. **[リソースの作成]** ウィンドウで、 **[Batch サービス]** を選択し、 **[作成]** を選択します。
2. **[基本]** タブで、サブスクリプション、リソース グループ、リージョン、Batch アカウント名を入力し、 **[次へ: 高度]** を選択します。
3. **[詳細]** タブで、 **[Public network access]\(パブリック ネットワーク アクセス\)** を **[無効]** に設定します。
4. **[設定]** で **[プライベート エンドポイント接続]** を選択してから、 **[+ プライベート エンドポイント]** を選択します。
   :::image type="content" source="media/private-connectivity/private-endpoint-connections.png" alt-text="プライベート エンドポイント接続":::
5. **[基本]** ペインで、サブスクリプション、リソース グループ、プライベート エンドポイント リソース名、リージョンの詳細を入力または選択して、 **[次へ: リソース]** を選択します。
6. **[リソース]** ペインで、 **[リソースの種類]** を **Microsoft.Batch/batchAccounts** に設定します。 アクセスするプライベート Batch アカウントを選択し、 **[次へ: 構成]** を選択します。
   :::image type="content" source="media/private-connectivity/create-private-endpoint.png" alt-text="プライベート エンドポイント接続" というメッセージが表示されたら、 **[作成]** を選択します。

プライベート エンドポイントがプロビジョニングされたら、プライベート エンドポイントを使用して、同じ仮想ネットワーク内の VM から Batch アカウントにアクセスできます。 Azure portal から IP アドレスを表示するには、次のようにします。

1. **[すべてのリソース]** を選択します。
2. 先ほど作成したプライベート エンドポイントを検索します。
3. **[概要]** タブを選択して、DNS の設定と IP アドレスを表示します。

:::image type="content" source="media/private-connectivity/access-private.png" alt-text="プライベート エンドポイント接続":::

## <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート

[Azure Resource Manager テンプレートを使用して Batch アカウントを作成する](quick-create-template.md)ときは、次に示すように、テンプレートを変更して **publicNetworkAccess** を **Disabled** に設定します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "batchAccountName": {
      "type": "string",
      },
    "location": {
      "type": "string",
    }
  },
  "resources": [
    {
      "name": "[parameters('batchAccountName')]",
      "type": "Microsoft.Batch/batchAccounts",
      "apiVersion": "2020-03-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": []
      "properties": {
        "poolAllocationMode": "BatchService"
        "publicNetworkAccess": "Disabled"
      }
    }
  ]
}
```

## <a name="configure-dns-zones"></a>DNS ゾーンを構成する

[プライベート DNS ゾーン](../dns/private-dns-privatednszone.md)は、プライベート エンドポイントを作成したサブネット内で使用します。 各プライベート IP アドレスが DNS エントリにマップされるようにエンドポイントを構成します

プライベート エンドポイントを作成するときに、Azure の[プライベート DNS ゾーン](../dns/private-dns-privatednszone.md)と統合できます。 代わりに[カスタム ドメイン](../dns/dns-custom-domain.md)を使用する場合は、プライベート エンドポイント用に予約されているすべてのプライベート IP アドレスの DNS レコードを追加するように構成する必要があります。

## <a name="pricing"></a>価格

プライベート エンドポイントに関連するコストの詳細については、「[Azure Private Link の価格](https://azure.microsoft.com/pricing/details/private-link/)」を参照してください。

## <a name="current-limitations-and-best-practices"></a>現在の制限事項とベスト プラクティス

プライベート Batch アカウント作成するときは、次の点に注意してください。

- プライベート エンドポイント リソースは、Batch アカウントと同じサブスクリプションに作成する必要があります。
- プライベート接続を削除するには、プライベート エンドポイント リソースを削除する必要があります。
- パブリック ネットワーク アクセスを使用して Batch アカウントを作成した後は、それをプライベート アクセスのみに変更することはできません。
- プライベート DNS ゾーンの DNS レコードは、プライベート エンドポイントを削除しても、Batch アカウントのリージョンを削除しても、自動的には削除されません。 このプライベート DNS ゾーンにリンクされている新しいプライベート エンドポイントを追加する前に、DNS レコードを手動で削除する必要があります。 DNS レコードをクリーンアップしないと、プライベート エンドポイントの削除またはリージョンの削除後に追加されたリージョンへのデータの停止など、予期しないデータ プレーンの問題が発生する可能性があります

## <a name="next-steps"></a>次のステップ

- [仮想ネットワークに Batch プールを作成する](batch-virtual-network.md)方法を確認します。
- [パブリック IP アドレスのない Batch プールを作成する](batch-pool-no-public-ip-address.md)方法を確認します。
- [パブリック IP アドレスを指定して Batch プールを作成する](create-pool-public-ip.md)方法を確認します。
- [Azure Private Link](../private-link/private-link-overview.md) について確認します。
