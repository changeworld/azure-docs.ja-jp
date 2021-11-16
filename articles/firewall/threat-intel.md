---
title: Azure Firewall の脅威インテリジェンスベースのフィルター処理
description: ファイアウォール用に脅威インテリジェンスベースのフィルター処理を有効にして、既知の悪意のある IP アドレスやドメインとの間のトラフィックの警告と拒否を行うことができます。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/04/2021
ms.author: victorh
ms.openlocfilehash: 5f318eabe0b1793856de040402c82951a36e00f2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278248"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Azure Firewall の脅威インテリジェンスベースのフィルター処理

ファイアウォール用に脅威インテリジェンスベースのフィルター処理を有効にして、既知の悪意のある IP アドレスやドメインとの間のトラフィックの警告と拒否を行うことができます。 これらの IP アドレスおよびドメインのソースは Microsoft の脅威インテリジェンス フィードです。これには、Microsoft のサイバー セキュリティ チームを含む複数のソースも含まれます。 [インテリジェント セキュリティ グラフ](https://www.microsoft.com/security/operations/intelligence)は Microsoft の脅威インテリジェンスの動力となるものであり、Microsoft Defender for Cloud を含む複数のサービスによって使用されます。<br>
<br>

:::image type="content" source="media/threat-intel/firewall-threat.png" alt-text="ファイアウォールの脅威インテリジェンス" border="false":::

脅威インテリジェンスベースのフィルター処理が有効になっている場合は、NAT ルール、ネットワーク ルール、またはアプリケーション ルールの前に、関連付けられているルールが処理されます。

ルールがトリガーされるときのアラートのみを記録するかどうかと、アラートおよび拒否モードを選択できます。

既定では、脅威インテリジェンスベースのフィルター処理は、アラート モードで有効になっています。 この機能の無効化やモードの変更は、ポータルのインターフェイスがお住まいの地域で使用可能になるまで実行できません。

:::image type="content" source="media/threat-intel/threat-intel-ui.png" alt-text="脅威インテリジェンス ベースのフィルタリング ポータルのインターフェイス":::

## <a name="logs"></a>ログ

次のログの抜粋は、トリガーされたルールを示しています。

```json
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

- **送信テスト** - 環境が侵害されたことを意味するため、送信トラフィック アラートがまれに発生します。 テスト送信アラートの動作をサポートするため、アラートをトリガーするテスト用 FQDN が作成されています。 `testmaliciousdomain.eastus.cloudapp.azure.com` を送信テストに使用します。

- **テスト受信** - DNAT ルールがファイアウォールで構成されている場合、受信トラフィックにアラートが表示されることを想定できます。 これは、特定のソースのみが DNAT ルールで許可されている場合でも該当し、それ以外の場合はトラフィックが拒否されます。 Azure Firewall では、既知のすべてのポート スキャナーでアラートが発行されるわけではありません。悪意のあるアクティビティにも参加していることが知られているスキャナーのみです。

## <a name="next-steps"></a>次のステップ

- [Azure Firewall Log Analytics のサンプル](./firewall-workbook.md)を参照する
- [Azure Firewall のデプロイおよび構成](tutorial-firewall-deploy-portal.md)方法について説明する
- [マイクロソフト セキュリティ インテリジェンス レポート](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)を確認する
