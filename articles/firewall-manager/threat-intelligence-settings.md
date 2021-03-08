---
title: Azure Firewall の脅威インテリジェンスの構成
description: Azure Firewall ポリシー用に脅威インテリジェンスベースのフィルター処理を構成して、既知の悪意のある IP アドレスおよびドメインとの間で送受信されるトラフィックを警告および拒否する方法について説明します。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: a663c5f3bcf3492c4a9bc74fe93c6ed6a86137ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530643"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Azure Firewall の脅威インテリジェンスの構成

Azure Firewall ポリシー用に脅威インテリジェンスベースのフィルター処理を構成して、既知の悪意のある IP アドレスおよびドメインとの間で送受信されるトラフィックを警告および拒否できます。 この IP アドレスとドメインのソースは、Microsoft の脅威インテリジェンス フィードです。 [インテリジェント セキュリティ グラフ](https://www.microsoft.com/security/operations/intelligence)は、Microsoft の脅威インテリジェンスを動作させる機能です。Azure Security Center を含む複数のサービスによって使用されます。<br>

脅威インテリジェンスベースのフィルター処理が構成されている場合は、すべての NAT ルール、ネットワーク ルール、またはアプリケーション ルールの前に、関連付けられているルールが処理されます。

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="脅威インテリジェンス ポリシー":::

## <a name="threat-intelligence-mode"></a>脅威インテリジェンス モード

ルールがトリガーされたときにアラートのみをログに記録することを選択できます。または、アラートと拒否モードを選択できます。

既定では、脅威インテリジェンスベースのフィルター処理は、アラート モードで有効になっています。

## <a name="allowed-list-addresses"></a>許可リストのアドレス

許可された IP アドレスのリストを構成して、指定したアドレス、範囲、またはサブネットが脅威インテリジェンスによってフィルター処理されないようにすることができます。



## <a name="logs"></a>ログ

次のログの抜粋は、トリガーされたルールを示しています。

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>テスト

- **送信テスト** - 環境が侵害されたことを意味するため、送信トラフィック アラートがまれに発生します。 テスト送信アラートの動作をサポートするため、アラートをトリガーするテスト用 FQDN が作成されています。 **testmaliciousdomain.eastus.cloudapp.azure.com** を送信テストに使用してください。

- **テスト受信** - DNAT ルールがファイアウォールで構成されている場合、受信トラフィックにアラートが表示されることを想定できます。 これは、特定のソースのみが DNAT ルールで許可されている場合でも該当し、それ以外の場合はトラフィックが拒否されます。 Azure Firewall では、既知のすべてのポート スキャナーでアラートが発行されるわけではありません。悪意のあるアクティビティにも参加していることが知られているスキャナーのみです。

## <a name="next-steps"></a>次のステップ

- [マイクロソフト セキュリティ インテリジェンス レポート](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)を確認する