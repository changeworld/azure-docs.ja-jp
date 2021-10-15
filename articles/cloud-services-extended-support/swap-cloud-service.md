---
title: Azure Cloud Services (拡張サポート) でのデプロイのスワップまたは切り替え
description: Azure Cloud Services (拡張サポート) でデプロイのスワップまたは切り替えを行う方法を説明します。
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/01/2021
ms.openlocfilehash: cd13a7f69d3085786407a405598df4bc7b8e0ef9
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129358434"
---
# <a name="swap-or-switch-deployments-in-azure-cloud-services-extended-support"></a>Azure Cloud Services (拡張サポート) でのデプロイのスワップまたは切り替え

Azure Cloud Services (拡張サポート) を使用すると、2 つの独立したクラウド サービスのデプロイ間で切り替えを行うことができます。 Azure Cloud Services (従来の) とは異なり、Azure Cloud Services (拡張サポート) の Azure Resource Manager モデルはデプロイ スロットを使用しません。 Azure Cloud Services (拡張サポート) では、クラウドサービスの新しいリリースをデプロイするときに、クラウド サービスを、Azure Cloud Services (拡張サポート) の既存のクラウド サービスと "スワップ可能" にすることができます。

デプロイをスワップした後は、新しいクラウド サービスのデプロイを使用して、新しいリリースをステージングし、テストすることができます。 実際には、スワップにより、運用リリースへとステージングされた新しいクラウド サービスが昇格されます。

> [!NOTE]
> Azure Cloud Services (従来の) のデプロイと Azure Cloud Services (拡張サポート) デプロイの間でスワップすることはできません。

クラウド サービスを別のクラウド サービスにスワップ可能にする必要があるのは、ペアの 2 つ目のクラウド サービスを初めてデプロイするときです。 ペアの 2 つ目のクラウド サービスをデプロイすると、それ以降の更新でそれを既存のクラウド サービスとスワップ可能にすることはできません。

デプロイをスワップするには、Azure Resource Manager テンプレート (ARM テンプレート)、Azure portal、または REST API を使用します。

2 つ目のクラウド サービスをデプロイすると、両方のクラウド サービスの SwappableCloudService プロパティが互いを指し示すように設定されます。 これらのクラウド サービスに対するそれ以降の更新では、このプロパティを指定する必要があります。そうでないと、SwappableCloudService プロパティを削除または更新できないことを示すエラーが返されます。

設定されると、SwappableCloudService プロパティは読み取り専用として扱われます。 これを削除したり、別の値に変更したりすることはできません。 (スワップ可能なペアの) クラウド サービスのいずれかを削除すると、残りのクラウド サービスの SwappableCloudService プロパティがクリアされます。

## <a name="arm-template"></a>ARM テンプレート

ARM テンプレートのデプロイ方法を使用して、クラウドサービスをスワップ可能にするには、`cloudServices` オブジェクトの `networkProfile` の `SwappableCloudService` プロパティを、スワップ対象のクラウド サービスの ID に設定します。

```json
"networkProfile": {
 "SwappableCloudService": {
              "id": "[concat(variables('swappableResourcePrefix'), 'Microsoft.Compute/cloudServices/', parameters('cloudServicesToBeSwappedWith'))]"
            },
        }
```

## <a name="azure-portal"></a>Azure portal

Azure portal でデプロイをスワップするには、次の手順に従います。

1. ポータル メニューで、 **[Cloud Services (拡張サポート)]** または **[ダッシュボード]** を選択します。
1. 更新するクラウド サービスを選択します。
1. クラウド サービスの **[概要]** で **[スワップ]** を選択します。

   :::image type="content" source="media/swap-cloud-service-portal-swap.png" alt-text="クラウド サービスの [スワップ] タブを示すスクリーンショット。":::

1. スワップ確認用のウィンドウでデプロイ情報を確認し、 **[OK]** を選択してデプロイをスワップします。

   :::image type="content" source="media/swap-cloud-service-portal-confirm.png" alt-text="デプロイのスワップの情報を確認するスクリーンショット。":::

変更されるのは、デプロイされたクラウド サービスの仮想 IP アドレスのみであるため、デプロイは短時間でスワップされます。

コンピューティング コストを節約するために、スワップされたクラウド サービスが想定どおりに動作していることを確認した後は、(アプリケーション デプロイのステージング環境として指定された) いずれかのクラウド サービスを削除できます。

## <a name="rest-api"></a>REST API

[REST API](/rest/api/compute/load-balancers/swap-public-ip-addresses) を使用して Azure Cloud Services (拡張サポート) で、新しいクラウド サービスのデプロイに切り替えるには、次のコマンドと JSON 構成を使用します。

```http
POST https://management.azure.com/subscriptions/subid/providers/Microsoft.Network/locations/westus/setLoadBalancerFrontendPublicIpAddresses?api-version=2021-02-01
```

```json
{
  "frontendIPConfigurations": [
    {
      "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb1/frontendIPConfigurations/lbfe1",
      "properties": {
        "publicIPAddress": {
          "id": "/subscriptions/subid/resourceGroups/rg2/providers/Microsoft.Network/publicIPAddresses/pip2"
        }
      }
    },
    {
      "id": "/subscriptions/subid/resourceGroups/rg2/providers/Microsoft.Network/loadBalancers/lb2/frontendIPConfigurations/lbfe2",
      "properties": {
        "publicIPAddress": {
          "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/publicIPAddresses/pip1"
        }
      }
    }
  ]
}
```

## <a name="common-questions-about-swapping-deployments"></a>デプロイのスワップについてよく寄せられる質問

Azure Cloud Services (拡張サポート) でのデプロイのスワップに関してよく寄せられる質問への回答を確認してください。

### <a name="what-are-the-prerequisites-for-swapping-to-a-new-cloud-services-deployment"></a>新しいクラウド サービスのデプロイにスワップするための前提条件は何ですか。

Azure Cloud Services (拡張サポート) でデプロイを正常にスワップするには、次の 2 つの重要な前提条件を満たす必要があります。

* 一方のスワップ可能クラウド サービスに、静的 IP アドレスまたは予約済み IP アドレスを使用する場合は、他方のクラウド サービスにも予約済み IP アドレスを使用する必要があります。 これを行わなかった場合、スワップは失敗します。
* スワップが成功するためには、ロールのすべてのインスタンスが実行中である必要があります。 インスタンスの状態を確認するには、Azure portal で、新しくデプロイされたクラウド サービスの **[概要]** にアクセスするか、Windows PowerShell で `Get-AzRole` コマンドを使用します。

ゲスト OS の更新およびサービス復旧の操作により、デプロイのスワップが失敗する可能性があります。 詳細については、[クラウド サービスのデプロイメントのトラブルシューティング](../cloud-services/cloud-services-troubleshoot-deployment-problems.md)に関するページを参照してください。

### <a name="can-i-make-a-vip-swap-in-parallel-with-another-mutating-operation"></a>別の変更操作と並行して VIP スワップを実行できますか。

いいえ。 VIP スワップは、クラウド サービスで他のコンピューティング操作を開始する前に完了する必要がある、ネットワークのみの変更です。 VIP スワップの進行中にクラウド サービスの更新、削除、または自動スケール操作を開始したり、別のコンピューティング操作の進行中に VIP スワップがトリガーされたりすると、クラウド サービスが、回復不能なエラー状態になることがあります。

### <a name="does-a-swap-incur-downtime-for-my-application-and-how-should-i-handle-it"></a>スワップで、アプリケーションのダウンタイムは発生しますか。それに、どのように対応すべきですか。

クラウド サービスのスワップは、Azure ロード バランサーの構成の変更にすぎないため、通常は短時間で終了します。 ただし、スワップに 10 秒以上かかり、その結果、一時的な接続エラーが発生する場合があります。 ユーザーへのスワップの影響を抑制するには、クライアント再試行ロジックを実装することを検討してください。

## <a name="next-steps"></a>次のステップ 

* Azure Cloud Services (延長サポート) の[デプロイの前提条件](deploy-prerequisite.md)を確認します。
* Azure Cloud Services (延長サポート) に関して[よく寄せられる質問](faq.yml)を確認します。
* 次のいずれかのオプションを使用して、Azure Cloud Services (拡張サポート) クラウド サービスをデプロイします。
  * [Azure Portal](deploy-portal.md)
  * [PowerShell](deploy-powershell.md)
  * [ARM テンプレート](deploy-template.md)
  * [Visual Studio](deploy-visual-studio.md)
