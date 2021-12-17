---
title: サービス タグの使用
titleSuffix: Azure SignalR Service
description: サービス タグを使用して Azure SignalR サービスへの送信トラフィックを許可する
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: fdb87dab5f2dc105da28ca097f81ff83c0dce3b3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735639"
---
# <a name="use-service-tags-for-azure-signalr-service"></a>Azure SignalR Service のサービス タグの使用

[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md#network-security-groups)を構成するとき、Azure SignalR Service に[サービス タグ](../virtual-network/network-security-groups-overview.md#service-tags)を使用できます。 IP アドレスをハードコードしなくても、Azure SignalR Service エンドポイントの受信および送信ネットワーク セキュリティ規則を定義できます。

Azure SignalR Service によってこれらのサービス タグが管理されます。 独自のサービス タグを作成したり、既存のタグを変更したりすることはできません。 サービス タグと一致するこれらのアドレス プレフィックスの管理は Microsoft によって行われ、アドレスが変化するとサービス タグは自動的に更新されます。

> [!Note]
> 2021 年 8 月 15 日から、Azure SignalR Service は、受信および送信トラフィックの両方で双方向サービス タグをサポートします。

## <a name="use-service-tag-on-portal"></a>ポータルでサービス タグを使用する

### <a name="configure-outbound-traffic"></a>送信トラフィックの構成

新しい送信ネットワーク セキュリティ規則を追加することで、Azure SignalR Service への送信トラフィックを許可できます。

1. ネットワーク セキュリティ グループに移動します。

1. **[送信セキュリティ規則]** という名称の設定メニューをクリックします。

1. 上部にある **[+ 追加]** ボタンをクリックします。

1. **[宛先]** で **[サービス タグ]** を選択します。

1. **[宛先サービス タグ]** で **[AzureSignalR]** を選択します。

1. **[宛先ポート範囲]** に「**443**」と入力します。

    ![送信セキュリティ規則の作成](media/howto-service-tags/portal-add-outbound-security-rule.png)

1. 必要に応じて他のフィールドを調整します。

1. **[追加]** をクリックします。

### <a name="configure-inbound-traffic"></a>受信トラフィックの構成

アップストリームがある場合は、新しい受信ネットワーク セキュリティ規則を追加することで、Azure SignalR Service からの受信トラフィックを許可することもできます。

1. ネットワーク セキュリティ グループに移動します。

1. **[受信セキュリティ規則]** という名称の設定メニューをクリックします。

1. 上部にある **[+ 追加]** ボタンをクリックします。

1. **[ソース]** で **[サービス タグ]** を選択します。

1. **[ソース サービス タグ]** で **[AzureSignalR]** を選択します。

1. **\**を*[ソース ポート範囲]** に入力します。

   :::image type="content" alt-text="受信セキュリティ規則の作成" source="media/howto-service-tags/portal-add-inbound-security-rule.png" :::


1. 必要に応じて他のフィールドを調整します。

1. **[追加]** をクリックします。

## <a name="next-steps"></a>次のステップ

- [ネットワーク セキュリティ グループ: サービス タグ](../virtual-network/network-security-groups-overview.md#security-rules)