---
title: Azure Kubernetes Service (AKS) クラスターに計画メンテナンスを使用する (プレビュー)
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) で計画メンテナンスを使用する方法について説明します。
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: f1e0822e77d8466b1b9796041fbdba53c3f9c91f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782911"
---
# <a name="use-planned-maintenance-to-schedule-maintenance-windows-for-your-azure-kubernetes-service-aks-cluster-preview"></a>計画メンテナンスを使用して Azure Kubernetes Service (AKS) クラスターのメンテナンス期間をスケジュールする (プレビュー)

AKS クラスターでは、定期的なメンテナンスが自動的に実行されます。 既定では、この動作はいつでも発生する可能性があります。 計画メンテナンスを使用すると、週ごとのメンテナンス期間をスケジュールして、コントロール プレーンと kube-system ポッドを VMSS インスタンスで更新し、ワークロードへの影響を最小限に抑えることができます。 スケジュールが設定されると、すべてのメンテナンスが選択した期間内に実行されます。 日にち、または特定の日の時間の範囲を指定して、クラスター上で 1 つまたは複数の週ごとの期間をスケジュールすることができます。 メンテナンス期間は Azure CLI を使用して構成します。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="limitations"></a>制限事項

計画メンテナンスを使用する場合は、次の制限が適用されます。

- AKS では、緊急または重大な計画外または事後対応のメンテナンス操作のために、これらの期間を無効にする権利を留保します。
- 現在、メンテナンス操作の実行は *ベストエフォートのみ* と見なされており、指定された期間内に発生するとは限りません。
- 更新プログラムを 7 日間を超えてブロックすることはできません。

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 拡張機能をインストールする

*aks-preview* Azure CLI 拡張機能バージョン 0.5.4 以降も必要です。 *aks-preview* Azure CLI 拡張機能は、[az extension add][az-extension-add] コマンドを使用してインストールします。 または、[az extension update][az-extension-update] コマンドを使用すると、使用可能な更新プログラムをインストールできます。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="allow-maintenance-on-every-monday-at-100am-to-200am"></a>毎週月曜日の午前 1 時から午前 2 時にメンテナンスを許可します

メンテナンス期間を追加するには、`az aks maintenanceconfiguration add` コマンドを使用します。

> [!IMPORTANT]
> 計画メンテナンスの期間は、協定世界時 (UTC) で指定します。

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

次の出力例では、メンテナンス期間が毎週月曜日の午前 1 時から午前 2 時までと表示されています。

```json
{- Finished ..
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

1 日の任意の時間にメンテナンスを行うには、*start-hour* パラメーターを省略します。 たとえば、次のコマンドでは、メンテナンス期間が毎週月曜日の終日に設定されます。

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday
```

## <a name="add-a-maintenance-configuration-with-a-json-file"></a>JSON ファイルを使用してメンテナンス構成を追加する

パラメーターを使用する代わりに、JSON ファイルを使用して、メンテナンス期間を作成することもできます。 次の内容を持つ新しいファイル `test.json` を作成します。

```json
  {
        "timeInWeek": [
          {
            "day": "Tuesday",
            "hour_slots": [
              1,
              2
            ]
          },
          {
            "day": "Wednesday",
            "hour_slots": [
              1,
              6
            ]
          }
        ],
        "notAllowedTime": [
          {
            "start": "2021-05-26T03:00:00Z",
            "end": "2021-05-30T12:00:00Z"
          }
        ]
}
```

上記の JSON ファイルでは、毎週火曜日の午前 1 時から午前 3 時、毎週水曜日の午前 1 時から午前 2 時、および午前 6 時から午前 7 時にメンテナンス期間を指定しています。 メンテナンス期間に一致していてもメンテナンスが許可されない *2021-05-26T03:00:00Z* から *2021-05-30T12:00:00Z* の例外もあります。 次のコマンドでは、`test.json` からメンテナンス期間を追加します。

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --config-file ./test.json
```

## <a name="update-an-existing-maintenance-window"></a>既存のメンテナンス期間を更新する

既存のメンテナンス構成を更新するには、`az aks maintenanceconfiguration update` コマンドを使用します。

```azurecli-interactive
az aks maintenanceconfiguration update -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

## <a name="list-all-maintenance-windows-in-an-existing-cluster"></a>既存のクラスター内のすべてのメンテナンス期間を一覧表示する

AKS クラスター内の現在のメンテナンス構成期間をすべて表示するには、`az aks maintenanceconfiguration list` コマンドを使用します。

```azurecli-interactive
az aks maintenanceconfiguration list -g MyResourceGroup --cluster-name myAKSCluster
```

次の出力では、myAKSCluster 用に 2 つのメンテナンス期間が構成されていることがわかります。 1 つの期間は月曜日の午前 1 時、別の期間は金曜日の午前 4 時です。

```json
[
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
    "name": "default",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Monday",
        "hourSlots": [
          1
        ]
      }
    ],
    "type": null
  },
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/testConfiguration",
    "name": "testConfiguration",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Friday",
        "hourSlots": [
          4
        ]
      }
    ],
    "type": null
  }
]
```

## <a name="show-a-specific-maintenance-configuration-window-in-an-aks-cluster"></a>AKS クラスター内の特定のメンテナンス構成期間を表示する

AKS クラスター内の特定のメンテナンス構成期間を表示するには、`az aks maintenanceconfiguration show` コマンドを使用します。

```azurecli-interactive
az aks maintenanceconfiguration show -g MyResourceGroup --cluster-name myAKSCluster --name default
```

次の出力例では、*既定* のメンテナンス期間が示されています。

```json
{
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

## <a name="delete-a-certain-maintenance-configuration-window-in-an-existing-aks-cluster"></a>既存の AKS クラスター内の特定のメンテナンス構成期間を削除する

AKS クラスター内の特定のメンテナンス構成期間を削除するには、`az aks maintenanceconfiguration delete` コマンドを使用します。

```azurecli-interactive
az aks maintenanceconfiguration delete -g MyResourceGroup --cluster-name myAKSCluster --name default
```

## <a name="next-steps"></a>次のステップ

- AKS クラスターのアップグレードを開始するには「[AKS クラスターのアップグレード][aks-upgrade]」を参照してください


<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
