---
title: Service Fabric クラスターのアップグレードの管理
description: Service Fabric クラスター ランタイムをいつどのように更新するかを管理する
ms.topic: how-to
ms.date: 03/26/2021
ms.openlocfilehash: 98c3300e5cc51c32d894397839879e25190d979b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731169"
---
# <a name="manage-service-fabric-cluster-upgrades"></a>Service Fabric クラスターのアップグレードの管理

Azure Service Fabric クラスターはユーザーが所有するリソースですが、一部が Microsoft によって管理されています。 ここでは、Microsoft が Azure Service Fabric クラスターを更新するタイミングとその方法を管理する方法について説明します。

クラスターのアップグレードの概念とプロセスの背景については、「[Azure Service Fabric クラスターのアップグレードと更新](service-fabric-cluster-upgrade.md)」を参照してください

## <a name="set-upgrade-mode"></a>アップグレード モードの設定

Microsoft からリリースされる Service Fabric の自動アップグレードを受信するようにクラスターを設定することができます。また、クラスターのアップグレード モードを設定して、現在サポートされているバージョンの一覧から手動で選択することもできます。 これを行うには、Azure portal の"*ファブリック アップグレード モード*" 制御を使用するか、クラスター デプロイ テンプレートの `upgradeMode` 設定から行うことができます。

### <a name="azure-portal"></a>Azure portal

Azure portal を使用して、新しい Service Fabric クラスターを作成するときに自動または手動アップグレードのいずれかを選択します。

:::image type="content" source="media/service-fabric-cluster-upgrade/portal-new-cluster-upgrade-mode.png" alt-text="[詳細設定] オプションから Azure portal に新しいクラスターを作成するときに、自動または手動アップグレードのいずれかを選択する":::

既存のクラスター リソースの **[ファブリックのアップグレード]** セクションから、自動または手動アップグレードを切り替えることもできます。

:::image type="content" source="./media/service-fabric-cluster-upgrade/fabric-upgrade-mode.png" alt-text="Azure portal のクラスター リソースの [ファブリックのアップグレード] セクションで、[自動] または [手動] アップグレードを選択する":::

### <a name="manual-upgrades-with-azure-portal"></a>Azure portal を使用した手動アップグレード

手動アップグレード オプションを選択した場合、アップグレードを開始するには、使用可能なバージョンをドロップダウンから選択し、"*保存*" するだけです。 そこから、クラスターのアップグレードがすぐに開始されます。

[クラスター正常性ポリシー](#custom-policies-for-manual-upgrades) (ノードの正常性と、クラスターで実行されている全アプリケーションの正常性の組み合わせ) は、アップグレードの実行中、遵守されます。 クラスター正常性ポリシーが満たされていない場合は、アップグレードがロールバックされます。

ロールバックの原因となった問題を解決した後、前述の手順に従ってもう一度アップグレードを実行してください。

### <a name="resource-manager-template"></a>Resource Manager テンプレート

Resource Manager テンプレートを使用してクラスター アップグレード モードを変更するには、*Microsoft.ServiceFabric/clusters* リソース定義の `upgradeMode` プロパティに *Automatic* または *Manual* を指定します。 手動アップグレードを選択した場合は、も現在`clusterCodeVersion`サポートされているファブリック のバージョン[に設定します。](#query-for-supported-cluster-versions)

:::image type="content" source="./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG" alt-text="スクリーンショットには、構造を反映するようにインデントされたプレーン テキストのテンプレートが示されています。'clusterCodeVersion' と 'upgradeMode' のプロパティーが強調表示されています。":::

テンプレートが正常にデプロイされると、クラスター アップグレード モードの変更が適用されます。 クラスターが手動モードの場合、クラスターのアップグレードは自動的に開始されます。

[クラスター正常性ポリシー](#custom-policies-for-manual-upgrades) (ノードの正常性と、クラスターで実行されている全アプリケーションの正常性の組み合わせ) は、アップグレードの実行中、遵守されます。 クラスター正常性ポリシーが満たされていない場合は、アップグレードがロールバックされます。

ロールバックの原因となった問題を解決した後、前述の手順に従ってもう一度アップグレードを実行してください。

## <a name="wave-deployment-for-automatic-upgrades"></a>自動アップグレードのウェーブ デプロイ

自動アップグレード モードでは、クラスターでウェーブ デプロイを有効にするオプションがあります。 ウェーブ デプロイでは、テスト、ステージ、および運用のクラスターを順番にアップグレードするためのパイプラインを作成でき、それぞれが運用クラスターのアップデート前に次期の Service Fabric のバージョンを検証するための組み込みの 'ベイク時間' で区切られています。

### <a name="enable-wave-deployment"></a>ウェーブ デプロイを有効にする

> [!NOTE]
> ウェーブ デプロイを行うには、*Microsoft.ServiceFabric/clusters* リソースの `2020-12-01-preview` (またはそれ以降) の API バージョンが必要です。

自動アップグレードでウェーブ デプロイを有効にするには、まず、クラスターに割り当てるウェーブを決定します。

* **ウェーブ 0** (`Wave0`): クラスターは、新しい Service Fabric ビルドがリリースされるとすぐに更新されます。 テストまたは開発クラスターが対象です。
* **ウェーブ 1** (`Wave1`): クラスターは、新しいビルドがリリースされてから 1 週間 (7 日) 後に更新されます。 運用前またはステージング クラスターを対象としています。
* **ウェーブ 2** (`Wave2`): クラスターは、新しいビルドがリリースされてから 2 週間 (14 日) 後に更新されます。 運用クラスターが対象です。

次に、上記のいずれかのウェーブの値を使用して、クラスター リソース テンプレートに `upgradeWave` プロパティを追加します。 クラスター リソースの API のバージョンが `2020-12-01-preview` またはそれ以降であることを確認してください。

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "fabricSettings": [...],
        "managementEndpoint": ...,
        "nodeTypes": [...],
        "provisioningState": ...,
        "reliabilityLevel": ...,
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
       ...
```

更新されたテンプレートをデプロイすると、クラスターは、次のアップグレード期間およびその後にわたって、指定されたウェーブに登録されます。

クラスターのアップグレードが失敗した場合のヘルプへのリンクを含む[電子メール通知を登録](#register-for-notifications)できます。

### <a name="register-for-notifications"></a>通知を登録する

クラスターのアップグレードが失敗したときに通知されるよう、登録することができます。 指定したメール アドレスに、アップグレードの失敗に関する詳細情報とヘルプへのリンクが記載されたメールが送信されます。

> [!NOTE]
> アップグレード エラーの通知を受信するのに、ウェーブ デプロイに登録する必要はありません。

通知を受け取るよう登録するには、クラスター リソース テンプレートに `notifications` セクションを追加し、通知を受信する 1 つ以上のメール アドレス (*receivers*) を指定します。

```json
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
        "notifications": [
        {
            "isEnabled": true,
            "notificationCategory": "WaveProgress",
            "notificationLevel": "Critical",
            "notificationTargets": [
            {
                "notificationChannel": "EmailUser",
                "receivers": [
                    "devops@contoso.com"
                ]
            }]
        }]
```

更新したテンプレートをデプロイすると、アップグレード エラー通知に登録されます。

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

* [Service Fabric のアップグレードの管理](service-fabric-cluster-upgrade-version-azure.md)
* [Service Fabric クラスターの設定](service-fabric-cluster-fabric-settings.md)のカスタマイズ
* [クラスターのスケール インとスケール アウト](service-fabric-cluster-scale-in-out.md)
* [アプリケーションのアップグレード](service-fabric-application-upgrade.md)


<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
