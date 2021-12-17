---
title: サービス タグの使用
titleSuffix: Azure Web PubSub service
description: サービス タグを使って Azure Web PubSub Service への送信トラフィックを許可する
author: ArchangelSDY
ms.service: azure-web-pubsub
ms.topic: article
ms.date: 10/21/2021
ms.author: dayshen
ms.openlocfilehash: 2422fb24fd3608d035fe374bd08dcae49321f4f2
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270090"
---
# <a name="use-service-tags-for-azure-web-pubsub-service"></a>Azure Web PubSub Service にサービス タグを使う

[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md#network-security-groups)を構成するとき、Azure Web PubSub Service に[サービス タグ](../virtual-network/network-security-groups-overview.md#service-tags)を使うことができます。 IP アドレスをハードコードしなくても、Azure Web PubSub Service エンドポイントの受信および送信ネットワーク セキュリティ規則を定義できます。

これらのサービス タグは、Azure Web PubSub Service によって管理されます。 独自のサービス タグを作成したり、既存のタグを変更したりすることはできません。 サービス タグと一致するこれらのアドレス プレフィックスの管理は Microsoft によって行われ、アドレスが変化するとサービス タグは自動的に更新されます。

> [!Note]
> 2021 年 8 月 15 日から、Azure Web PubSub Service は、受信および送信トラフィックの両方で双方向サービス タグをサポートします。

## <a name="use-service-tag-via-azure-cli"></a>Azure CLI 経由でサービス タグを使う

### <a name="configure-outbound-traffic"></a>送信トラフィックの構成

新しい送信ネットワーク セキュリティ規則を追加することで、Azure Web PubSub Service への送信トラフィックを許可できます。

```azurecli-interactive
az network nsg rule create -n <rule-name> --nsg-name <nsg-name> -g <resource-group> --priority 100 --direction Outbound --destination-address-prefixes AzureWebPubSub
```

### <a name="configure-inbound-traffic"></a>受信トラフィックの構成

[イベント ハンドラー](concept-service-internals.md#event_handler)を使っている場合は、新しい受信ネットワーク セキュリティ規則を追加することで、Azure Web PubSub Service からの受信トラフィックを許可することもできます。

```azurecli-interactive
az network nsg rule create -n <rule-name> --nsg-name <nsg-name> -g <resource-group> --priority 100 --direction Inbound --source-address-prefixes AzureWebPubSub
```

## <a name="next-steps"></a>次のステップ

- [ネットワーク セキュリティ グループ: サービス タグ](../virtual-network/network-security-groups-overview.md#security-rules)