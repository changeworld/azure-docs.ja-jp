---
title: 2 つの Azure Cloud Services 間のスワップ/切り替え (延長サポート)
description: 2 つの Azure Cloud Services 間のスワップ/切り替え (延長サポート)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: 6f96656af9afd9874cc6273a9cea9ed43e8c69cc
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294230"
---
# <a name="swapswitch-between-two-azure-cloud-services-extended-support"></a>2 つの Azure Cloud Services 間のスワップ/切り替え (延長サポート)
Cloud Services (延長サポート) を使用すると、2 つの独立したクラウド サービスのデプロイを切り替えることができます。 クラウド サービス (クラシック) とは異なり、スロットの概念は Azure Resource Manager モデルには存在しません。 クラウド サービス (延長サポート) の新しいリリースをデプロイする場合は、別の既存の クラウド サービス (延長サポート) を使用して "スワップ可能" にして、このデプロイを使用して新しいリリースのステージングとテストを行うことができます。 クラウド サービスは、(ペアの) 2 つ目のクラウド サービスをデプロイするときにのみ、別のクラウド サービスと "スワップ可能" にすることができます。 ARM テンプレートベースのデプロイ方法を使用する場合、これを行うには、Cloud Service オブジェクトのネットワーク プロファイル内の SwappableCloudService プロパティを、ペアになっているクラウド サービスの ID に設定します。 

```
"networkProfile": {
 "SwappableCloudService": {
              "id": "[concat(variables('swappableResourcePrefix'), 'Microsoft.Compute/cloudServices/', parameters('cloudServicesToBeSwappedWith'))]"
            },
```
> [!Note] 
> クラウド サービス (クラシック) とクラウド サービス (延長サポート) の間でスワップすることはできません。

2 つのクラウド サービスをアドレス指定する際に使用する URL を切り替えるには、 **[スワップ]** を使用します。これにより、新しいクラウド サービス (ステージング済み) が運用リリースに昇格します。
デプロイメントのスワップは、 [Cloud Services] ページまたはダッシュボードから実行できます。

1. [Azure ポータル](https://portal.azure.com)で、更新するクラウド サービスを選択します。 この手順により、クラウド サービス インスタンス ブレードが開きます。
2. ブレードで、 **[スワップ]** 
   :::image type="content" source="media/swap-cloud-service-1.png" alt-text="[イメージ] を選択すると、クラウド サービスのスワップ オプションが表示 "::: されます
   
3. 次のような確認ダイアログが表示されます
   
   :::image type="content" source="media/swap-cloud-service-2.png" alt-text="図はクラウド サービスのスワップを示しています":::
   
4. デプロイ情報を確認した後、 [OK] を選択してデプロイをスワップします。
変更されるのは 2 つのクラウド サービスの仮想 IP アドレス (VIP) だけであるため、スワップは直ちに実行されます。

コンピューティング コストを節約するために、スワップされたクラウド サービスが想定どおりに動作していることを確認した後、(アプリケーション デプロイのステージング環境として指定された) いずれかのクラウド サービスを削除できます。

2 つのクラウド サービス延長サポート デプロイの間で ‘スワップ’ を実行する rest API を以下に示します。
```http
POST https://management.azure.com/subscriptions/subId/providers/Microsoft.Network/locations/region/setLoadBalancerFrontendPublicIpAddresses?api-version=2020-11-01
```
```
{
  "frontendIPConfigurations": [
    {
    "id": "#LBFE1#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP2#"
    }
      }
    },
   {
    "id": "#LBFE2#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP1#"
     }
       }
    }
  ]
 }
```
## <a name="common-questions-about-swapping-deployments"></a>デプロイのスワップについてよく寄せられる質問

### <a name="what-are-the-prerequisites-for-swapping-between-two-cloud-services"></a>2 つのクラウド サービス間でスワップするための前提条件は何ですか?
クラウド サービス (延長サポート) のスワップを成功させるには、次の 2 つの重要な前提条件があります。
* スワップ可能ないずれかのクラウド サービスに静的/予約済み IP アドレスを使用する場合は、他のクラウド サービスでも予約済み IP を使用する必要があります。 これを行わなかった場合、スワップは失敗します。
* スワップを実行する前に、ロールのすべてのインスタンスを実行する必要があります。 インスタンスの状態は、Azure Portal の [概要] ブレードで確認できます。 または Windows PowerShell の Get-AzRole を使用して確認できます。

ゲスト OS の更新とサービス復旧操作もデプロイのスワップが失敗する原因となる可能性があります。 詳細については、「クラウド サービスのデプロイメントに関する問題のトラブルシューティング」を参照してください。

### <a name="can-i-perform-a-vip-swap-in-parallel-with-another-mutating-operation"></a>別の変更操作と同時に VIP スワップを実行できますか?
いいえ。 VIP スワップは、クラウド サービスで他のコンピューティング操作を実行する前に完了する必要があるネットワークのみの変更です。 VIP スワップが進行中にクラウド サービスで更新、削除、または自動スケール操作を実行する、または他のコンピューティング操作が進行中に VIP スワップをトリガーすると、クラウド サービスが望ましくない状態になり、リカバリが不可能になる場合があります。 

### <a name="does-a-swap-incur-downtime-for-my-application-how-should-i-handle-it"></a>スワップで、アプリケーションのダウンタイムは発生しますか。 どのように対応する必要がありますか。
前のセクションで説明したように、クラウド サービスのスワップは、Azure ロード バランサーの構成を変更するだけなので、通常は高速で実行されます。 ただし、場合によっては 10 数秒かかることがあり、その結果、一時的な接続エラーが発生します。 お客様への影響を制限するために、クライアント再試行ロジックの実装を検討してください。

## <a name="next-steps"></a>次のステップ 
- Cloud Services (延長サポート) の[デプロイの前提条件](deploy-prerequisite.md)を確認します。
- Cloud Services (延長サポート) に関して[よく寄せられる質問](faq.md)を確認します。
- [Azure portal](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[テンプレート](deploy-template.md)、または [Visual Studio](deploy-visual-studio.md) を使用してクラウド サービス (延長サポート) をデプロイします。
