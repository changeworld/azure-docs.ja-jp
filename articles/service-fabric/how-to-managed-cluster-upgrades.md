---
title: Azure Service Fabric マネージド クラスターのアップグレード
description: Azure Service Fabric マネージド クラスターをアップグレードするためのオプションについて説明します。
ms.topic: how-to
ms.date: 06/16/2021
ms.openlocfilehash: 50af042be1dc69f39e61447901d4d5f07da2a1e7
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290091"
---
# <a name="manage-service-fabric-managed-cluster-upgrades"></a>Service Fabric マネージド クラスターのアップグレードの管理

Azure Service Fabric クラスターはお客様が所有するリソースですが、一部は Microsoft によって管理されています。 ここでは、Microsoft が Azure Service Fabric マネージド クラスターを更新するタイミングとその方法を管理する方法について説明します。

## <a name="set-upgrade-mode"></a>アップグレード モードの設定

Azure Service Fabric マネージド クラスターは、Microsoft が[ウェーブ デプロイ](#wave-deployment-for-automatic-upgrades)戦略を使用してリリースしているため、自動 Service Fabric アップグレードを受け取るように既定で設定されます。 また、手動モードのアップグレードも設定でき、その場合、現在サポートされているバージョンの一覧から選択します。 これらの設定は、Azure portal の "*ファブリックのアップグレード*" コントロール、またはクラスター デプロイ テンプレートの `ClusterUpgradeMode` 設定を使用して構成できます。

## <a name="wave-deployment-for-automatic-upgrades"></a>Wave デプロイによる自動アップグレード

ウェーブ デプロイでは、テスト、ステージ、および運用のクラスターを順番にアップグレードするためのパイプラインを作成でき、それぞれが運用クラスターのアップデート前に次期の Service Fabric のバージョンを検証するための組み込みの 'ベイク時間' で区切られています。

>[!NOTE]
>既定では、クラスターはウェーブ 0 に設定されます。

自動アップグレードでウェーブ デプロイを選択するには、最初に、クラスターに割り当てるウェーブを決定します。

* **ウェーブ 0** (`Wave0`): クラスターは、新しい Service Fabric ビルドがリリースされるとすぐに更新されます。
* **ウェーブ 1** (`Wave1`): クラスターは、ウェーブ 0 の後に更新され、ベイク時間が許可されます。 これは、ウェーブ 0 から少なくとも 7 日後に発生します。
* **ウェーブ 2** (`Wave2`): クラスターは最後に更新され、さらに長いベイク時間が許可されます。 これは、ウェーブ 0 から少なくとも 14 日後に発生します。

## <a name="set-the-wave-for-your-cluster"></a>クラスターのウェーブを設定する

Azure portal の "*ファブリックのアップグレード*" コントロール、またはクラスター デプロイ テンプレートの `ClusterUpgradeMode` 設定を使用して、使用可能ないずれかのウェーブにクラスターを設定できます。

### <a name="azure-portal"></a>Azure portal

Azure portal を使用して、新しい Service Fabric クラスターを作成するとき、使用可能な自動ウェーブのいずれかを選択します。

:::image type="content" source="./media/how-to-managed-cluster-upgrades/portal-new-cluster-upgrade-waves-setting.png" alt-text="Azure portal で &quot;詳細&quot; オプションから新しいクラスターを作成するときに、使用可能ないずれかのウェーブを選択する":::

既存のクラスター リソースの **[ファブリックのアップグレード]** セクションから、使用可能な自動ウェーブを切り替えることもできます。

:::image type="content" source="./media/how-to-managed-cluster-upgrades/manage-upgrade-wave-settings.png" alt-text="Azure portal のクラスター リソースの [ファブリックのアップグレード] セクションで、いずれかの自動ウェーブを選択する":::

### <a name="resource-manager-template"></a>Resource Manager テンプレート

Resource Manager テンプレートを使用してクラスター アップグレード モードを変更するには、*Microsoft.ServiceFabric/clusters* リソース定義の `ClusterUpgradeMode` プロパティに *Automatic* または *Manual* を指定します。 手動アップグレードを選択した場合は、も現在`clusterCodeVersion`サポートされているファブリック のバージョン[に設定します。](#query-for-supported-cluster-versions)

#### <a name="manual-upgrade"></a>手動アップグレード

```json
{
"apiVersion": "2021-05-01",
"type": "Microsoft.ServiceFabric/managedClusters",
"properties": {
        "ClusterUpgradeMode": "Manual",
        "ClusterCodeVersion": "8.0.514.9590"
        }
}
```

テンプレートが正常にデプロイされると、クラスター アップグレード モードの変更が適用されます。 クラスターが手動モードの場合、クラスターのアップグレードは自動的に開始されます。

クラスター正常性ポリシー (ノードの正常性と、クラスターで実行されている全アプリケーションの正常性の組み合わせ) は、アップグレードの実行中、遵守されます。 クラスター正常性ポリシーが満たされていない場合は、アップグレードがロールバックされます。

ロールバックが発生した場合、その原因となった問題を解決し、前述の手順に従ってもう一度アップグレードを実行する必要があります。

#### <a name="automatic-upgrade-with-wave-deployment"></a>ウェーブ デプロイによる自動アップグレード

自動アップグレードとウェーブ デプロイを構成するには、`ClusterUpgradeMode` が `Automatic` に設定され、Resource Manager テンプレートで上記のウェーブ値のいずれかを使用して `upgradeWave` プロパティが定義されていることを確認するか、そのように追加します。

```json
{
"apiVersion": "2021-05-01",
"type": "Microsoft.ServiceFabric/managedClusters",
"properties": {
        "ClusterUpgradeMode": "Automatic",
        "upgradeWave": "Wave1",
        }  
}
```

更新されたテンプレートをデプロイすると、クラスターは、次のアップグレード期間およびその後にわたって、指定されたウェーブに登録されます。

## <a name="query-for-supported-cluster-versions"></a>サポートされているクラスター バージョンのクエリ

[Azure REST API](/rest/api/azure/) を使用すると、指定した場所とサブスクリプションで使用可能なすべての Service Fabric ランタイム バージョン ([clusterVersions](/rest/api/servicefabric/sfrp-api-clusterversions_list)) を一覧表示できます。

サポートされているバージョンとオペレーティング システムの詳細については、[Service Fabric バージョン](service-fabric-versions.md)に関するページでも確認できます。

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/managedclusterVersions?api-version=2021-05-01

"value": [
  {
    "id": "subscriptions/eec8e14e-b47d-40d9-8bd9-23ff5c381b40/providers/Microsoft.ServiceFabric/locations/eastus2/environments/Windows/managedClusterVersions/7.2.477.9590",
    "name": "7.2.477.9590",
    "type": "Microsoft.ServiceFabric/locations/environments/managedClusterVersions",
    "properties": {
      "supportExpiryUtc": "2021-11-30T00:00:00",
      "osType": "Windows",
      "clusterCodeVersion": "7.2.477.9590"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/locations/eastus2/environments/Windows/managedClusterVersions/8.0.514.9590",
    "name": "8.0.514.9590",
    "type": "Microsoft.ServiceFabric/locations/environments/managedClusterVersions",
    "properties": {
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "osType": "Windows",
      "clusterCodeVersion": "8.0.514.9590"
    }
  }
]

```

出力の `supportExpiryUtc` は、特定のリリースの有効期限が近づいているか、期限切れになっていることを報告します。 最新リリースには有効な日付ではなく、*9999-12-31T23:59:59.9999999* という値が設定されていますが、これは単に有効期限が設定されていないことを意味するものです。

## <a name="next-steps"></a>次の手順

* [Service Fabric マネージド クラスター構成をカスタマイズする](how-to-managed-cluster-configuration.md)
* [アプリケーションのアップグレード](service-fabric-application-upgrade.md)

<!--Image references-->
[Upgrade-Wave-Settings]: ./media/how-to-managed-cluster-upgrades/manage-upgrade-wave-settings.png
[New-Cluster-Wave-Settings]: ./media/how-to-managed-cluster-upgrades/portal-new-cluster-upgrade-waves-setting.png
