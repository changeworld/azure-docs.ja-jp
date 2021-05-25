---
title: Azure Service Fabric マネージド クラスターのアップグレード
description: Azure Service Fabric マネージド クラスターをアップグレードするためのオプションについて説明します。
ms.topic: how-to
ms.date: 05/10/2021
ms.openlocfilehash: c7096191e72d5645149bc1b1e28254a0c57a94b0
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2021
ms.locfileid: "109785279"
---
# <a name="manage-service-fabric-managed-cluster-upgrades"></a>Service Fabric マネージド クラスターのアップグレードの管理

Azure Service Fabric クラスターはお客様が所有するリソースですが、一部は Microsoft によって管理されています。 ここでは、Microsoft が Azure Service Fabric マネージド クラスターを更新するタイミングとその方法を管理する方法について説明します。

## <a name="set-upgrade-mode"></a>アップグレード モードの設定

Azure Service Fabric マネージド クラスターは、Microsoft が[ウェーブ デプロイ](#wave-deployment-for-automatic-upgrades)戦略を使用してリリースしているため、自動 Service Fabric アップグレードを受け取るように既定で設定されます。 また、手動モードのアップグレードも設定でき、その場合、現在サポートされているバージョンの一覧から選択します。 これらの設定は、Azure portal の "*ファブリックのアップグレード*" コントロール、またはクラスター デプロイ テンプレートの `ClusterUpgradeMode` 設定を使用して構成できます。

## <a name="wave-deployment-for-automatic-upgrades"></a>Wave デプロイによる自動アップグレード

ウェーブ デプロイでは、テスト、ステージ、および運用のクラスターを順番にアップグレードするためのパイプラインを作成でき、それぞれが運用クラスターのアップデート前に次期の Service Fabric のバージョンを検証するための組み込みの 'ベイク時間' で区切られています。

>注: 既定では、クラスターはウェーブ 0 に設定されます。

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
        "ClusterCodeVersion": "7.2.457.9590"
        }
}
```

テンプレートが正常にデプロイされると、クラスター アップグレード モードの変更が適用されます。 クラスターが手動モードの場合、クラスターのアップグレードは自動的に開始されます。

[クラスター正常性ポリシー](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction#health-policies) (ノードの正常性と、クラスターで実行されている全アプリケーションの正常性の組み合わせ) は、アップグレードの実行中、遵守されます。 クラスター正常性ポリシーが満たされていない場合は、アップグレードがロールバックされます。

ロールバックの原因となった問題を解決した後、前述の手順に従ってもう一度アップグレードを実行してください。

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

## <a name="custom-policies-for-manual-upgrades"></a>手動アップグレードのカスタム ポリシー

手動によるクラスター アップグレードのためのカスタム正常性ポリシーを指定できます。 これらのポリシーは、新しいランタイム バージョンを選択するたびに適用されます。これにより、システムによってクラスターのアップグレードが開始されます。 ポリシーをオーバーライドしていない場合、既定の設定が使用されます。

カスタム正常性ポリシーを指定したり、 **[アップグレード ポリシー]** の *[カスタム]* オプションを選択して Azure portal のクラスター リソースの **[ファブリックのアップグレード]** セクションから現在の設定を確認したりできます。

:::image type="content" source="./media/service-fabric-cluster-upgrade/custom-upgrade-policy.png" alt-text="アップグレード時にカスタム正常性ポリシーを設定するには、Azure portal のクラスター リソースの [ファブリックのアップグレード] セクションで [カスタム] アップグレード ポリシー オプションを選択します":::

## <a name="query-for-supported-cluster-versions"></a>サポートされているクラスター バージョンのクエリ

[Azure REST API](/rest/api/azure/) を使用すると、指定した場所とサブスクリプションで使用可能なすべての Service Fabric ランタイム バージョン ([clusterVersions](/rest/api/servicefabric/sfrp-api-clusterversions_list)) を一覧表示できます。

サポートされているバージョンとオペレーティング システムの詳細については、[Service Fabric バージョン](service-fabric-versions.md)に関するページでも確認できます。

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2018-02-01

"value": [
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
    "name": "5.0.1427.9490",
    "type": "Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.0.1427.9490",
      "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
    "name": "5.1.1427.9490",
    "type": " Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.1.1427.9490",
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
    "name": "4.4.1427.9490",
    "type": " Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "4.4.1427.9490",
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "environment": "Linux"
    }
  }
]
}
```

出力の `supportExpiryUtc` は、特定のリリースの有効期限が近づいているか、期限切れになっていることを報告します。 最新リリースには有効な日付ではなく、*9999-12-31T23:59:59.9999999* という値が設定されていますが、これは単に有効期限が設定されていないことを意味するものです。

## <a name="next-steps"></a>次の手順

* [Service Fabric マネージド クラスター構成をカスタマイズする](how-to-managed-cluster-configuration.md)
* [アプリケーションのアップグレード](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[Upgrade-Wave-Settings]: ./media/service-fabric-cluster-upgrade/manage-upgrade-wave-settings.png
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG