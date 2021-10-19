---
title: 制限付き HDInsight クラスターで Private Link を有効にする (プレビュー)
description: Azure Private Link を使用して、HDInsight クラスターの外部に接続する方法について学習します。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 9f432d37e58069decdc9a97e55d926aed3b7277f
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710006"
---
# <a name="enable-private-link-on-hdinsight-cluster-preview"></a>HDInsight クラスターで Private Link を有効にする (プレビュー)

## <a name="overview"></a>概要
この記事では、Azure Private Link を利用して、Microsoft バックボーン ネットワーク経由でネットワークを介してプライベートに HDInsight クラスターに接続する方法について学習します。 この記事は、パブリック接続の制限に重点を置いた [Azure HDInsight でのクラスター接続の制限](./hdinsight-restrict-public-connectivity.md)に関するメインの記事を拡張したものです。 HDInsight クラスターとそれに依存するリソースとの間でパブリック接続を選択する場合は、「[Azure HDInsight のネットワーク トラフィックを制御する](./control-network-traffic.md)」のガイドラインに従って、クラスターの接続を制限することを検討してください

Private Link は、VNet ピアリングが使用できない、または有効になっていないクロス VNet シナリオで利用できます。 たとえば、Azure Data Factory を Azure HDInsight と統合する場合は、コンプライアンスとセキュリティ上の理由から、プライベート ネットワーク (つまり、プライベート リンク) を介して HDInsight クラスターに Azure Data Factory を接続する必要があります。

> [!NOTE]
> パブリック接続を制限することは、Private Link を有効にするための前提条件であり、同じ機能とは見なさないでください。

Private Link はオプションの機能であり、既定では無効になっています。 この機能を使用できるのは、[Azure HDInsight でのクラスター接続の制限](./hdinsight-restrict-public-connectivity.md)に関する記事の説明のとおり、`resourceProviderConnection` ネットワーク プロパティが *outbound* に設定されている場合のみです。

`privateLink` が "*有効*" に設定されている場合は、内部 [標準ロード バランサー](../load-balancer/load-balancer-overview.md) (SLB) が作成され、SLB ごとに Azure Private Link サービスがプロビジョニングされます。 Private Link サービスを使用すると、プライベート エンドポイントから HDInsight クラスターにアクセスできます。

## <a name="prerequisites"></a>前提条件

標準ロード バランサーでは、基本ロード バランサーの場合のように[パブリック送信 NAT](../load-balancer/load-balancer-outbound-connections.md) が自動的に提供されません。 送信パブリック HDInsight 依存関係に接続するために、独自の NAT ソリューション (NAT ゲートウェイ、あるいは[ファイアウォール](./hdinsight-restrict-outbound-traffic.md)によって提供される NAT など) を提供する必要があります。 HDInsight クラスターから送信依存関係へのアクセスは引き続き必要です。 このような送信依存関係が許可されていない場合は、クラスターの作成に失敗する可能性があります。 送信接続を有効にするには、サブネットでネットワーク セキュリティ グループを構成する必要もあります。

### <a name="1--configure-a-default-network-security-group-nsg-on-the-subnet"></a>1.  サブネットで既定のネットワーク セキュリティ グループ (NSG) を構成する

HDInsight クラスターをデプロイする予定のサブネットに、ネットワーク セキュリティ グループを作成して追加します。

### <a name="2--disable-network-policies-for-private-link-service"></a>2.  プライベート リンク サービスのネットワーク ポリシーを無効にする

プライベート リンク サービスの作成を成功させるには、明示的に[プライベート リンク サービスのネットワーク ポリシーを無効にする](../private-link/disable-private-link-service-network-policy.md)必要があります。

### <a name="3--configure-a-nat-gateway-on-the-subnet"></a>3.  サブネットで NAT Gateway を構成する

NAT 用にファイアウォールまたはネットワーク仮想アプライアンス (NVA) を構成しない場合は、NAT Gateway を使用することを選択できます。それ以外の場合は、次の前提条件に進みます。

作業を開始するには、仮想ネットワークの構成されたサブネットに NAT Gateway (仮想ネットワークの新しいパブリック IP アドレスを持つ) を追加するだけです。 このゲートウェイには、仮想ネットワークの外部にトラフィックを送信する必要がある場合に、プライベート内部 IP アドレスをパブリック アドレスに変換する役割があります。

### <a name="4--using-firewall-or-network-virtual-appliance-nvas-for-nat-optional"></a>4.  NAT 用のファイアウォールまたはネットワーク仮想アプライアンス (NVA) の使用 (省略可能)
基本的な設定を開始するには、まず、仮想ネットワークに新しいサブネット "AzureFirewallSubnet" を追加します。 作成したら、このサブネットを使用して新しいファイアウォールを構成し、ファイアウォール ポリシーを追加します。 ファイアウォールが設定された後、このファイアウォールのプライベート IP をルート テーブルの `nextHopIpAddress` として使用します。 このルート テーブルを、仮想ネットワークの構成済みサブネットに追加します。
ファイアウォールの設定の詳細については、「[Azure HDInsight のネットワーク トラフィックを制御する](./control-network-traffic.md)」を参照してください

次の図は、クラスターを作成する前に必要なネットワーク構成の例を示しています。 この例では、すべての送信トラフィックが UDR を使用する Azure Firewall に強制的に適用され、クラスターを作成する前に、ファイアウォールで必要な送信依存関係が "許可される" 必要があります。 Enterprise セキュリティ パッケージ クラスターでは、Azure Active Directory Domain Services へのネットワーク接続を VNet ピアリングによって提供できます。

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="クラスターを作成する前のプライベート リンク環境の図":::

## <a name="manage-private-endpoints-for-azure-hdinsight"></a>Azure HDInsight のプライベート エンドポイントを管理する

Azure HDInsight クラスターの[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用すると、仮想ネットワーク (VNet) 上のクライアントから[プライベート リンク](../private-link/private-link-overview.md)を介してクラスターに安全にアクセスできるようになります。 VNet 上のクライアントと HDInsight クラスター間のネットワーク トラフィックが、Microsoft バックボーン ネットワーク経由で送受信され、パブリック インターネットへの露出を避けることができます。

:::image type="content" source="media/hdinsight-private-link/private-endpoint-experience.png" alt-text="プライベート エンドポイント管理エクスペリエンスの図":::

Private Link サービスのコンシューマー (Azure Data Factory など) は、次の 2 つの接続承認方法から選択できます。
* **自動**: サービスのコンシューマーに HDInsight リソースに対する Azure RBAC アクセス許可がある場合、コンシューマーは自動の承認方法を選択できます。 この場合、要求が HDInsight リソースに到達したときに、その HDInsight リソースからのアクションは必要なく、接続が自動的に承認されます。
* **手動**: 一方、サービスのコンシューマーに HDInsight リソースに対する Azure RBAC アクセス許可がない場合、コンシューマーは手動の承認方法を選択できます。 この場合、接続要求は HDInsight リソース上で保留中として表示されます。 要求は、接続を確立する前に、HDInsight リソースによって手動で承認される必要があります。 

プライベート エンドポイントを管理するには、Azure Portal のクラスター ビューで、[セキュリティとネットワーク] の下にある [ネットワーク (プレビュー)] セクションに移動します。 ここでは、すべての既存の接続、接続状態、およびプライベート エンドポイントの詳細を表示できます。
既存の接続を承認、拒否、または削除することもできます。 プライベート接続を作成するときに、接続先の HDInsight サブリソース (ゲートウェイやヘッドノードなど) を 指定することもできます。

以下の表に、HDInsight リソースのさまざまなアクションと、その結果であるプライベート エンドポイントの接続状態を示します。 HDInsight リソースでは、コンシューマーの介入なしに、プライベート エンドポイント接続の接続状態を後で変更することもできます。 このアクションにより、コンシューマー側のエンドポイントの状態が更新されます。

| サービス プロバイダーのアクション | サービスのコンシューマーのプライベート エンドポイントの状態 | 説明 |
| --------- | --------- | --------- |
| なし | 保留中 | 接続が手動で作成されており、プライベート リンク リソースの所有者による承認を待機しています。 |
| 承認 | Approved | 接続が自動または手動で承認され、使用する準備が整っています。 |
| Reject | 拒否 | プライベート リンク リソースの所有者によって接続が拒否されました。 |
| [削除] | [Disconnected]\(切断済み\) | プライベート リンク リソースの所有者によって接続が削除されました。プライベート エンドポイントは情報が多くなり、クリーンアップのために削除する必要があります。 |

## <a name="configure-dns-to-connect-over-private-endpoints"></a>プライベート エンドポイント経由で接続するように DNS を構成する

ネットワークを設定したら、次の図に示すように、送信リソース プロバイダー接続とプライベート リンクが有効になっているクラスターを作成できます。
プライベート クラスターにアクセスするために、Private Link の DNS 拡張機能とプライベート エンドポイントを使用できます。 `privateLink` 設定が有効に設定されている場合は、プライベート エンドポイントを作成し、プライベート DNS ゾーンを使用して DNS 解決を構成できます。
Azure で管理されているパブリック DNS ゾーン `azurehdinsight.net` で作成される Private Link エントリは、次のとおりです。

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```
次の図は、ピアリングされていないか、クラスターに直接アクセスできない仮想ネットワークからクラスターにアクセスできるように構成されたプライベート DNS エントリの例を示しています。 Azure プライベート ゾーンを使用して `*.privatelink.azurehdinsight.net` FQDN を上書きし、クライアントのネットワークでプライベート エンドポイント IP アドレスを解決できます。
これは `<clustername>.azurehdinsight.net` に対してのみ表示されますが、他のクラスター エンドポイントにも拡張されます。

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="プライベート リンク アーキテクチャの図":::

## <a name="how-to-create-clusters"></a>クラスターを作成する方法
### <a name="use-arm-template-properties"></a>ARM テンプレートのプロパティの使用

次の JSON コード スニペットには、プライベート HDInsight クラスターを作成するために ARM テンプレートで構成する必要がある 2 つのネットワーク プロパティが含まれています。

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound"
}
```

数多くの HDInsight エンタープライズ セキュリティ機能 (Private Link など) を備えた完全なテンプレートについては、[HDInsight エンタープライズ セキュリティのテンプレート](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template)を参照してください。

### <a name="use-azure-powershell"></a>Azure PowerShell の使用

PowerShell を使用する場合は、[こちら](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature)の例を参照してください。

### <a name="use-azure-cli"></a>Azure CLI の使用
Azure CLI を使用する場合は、[こちら](/cli/azure/hdinsight#az_hdinsight_create-examples)の例を参照してください。

## <a name="next-steps"></a>次の手順

* [Azure HDInsight 用の Enterprise セキュリティ パッケージ](enterprise-security-package.md)
* [Azure HDInsight でのエンタープライズ セキュリティの一般情報とガイドライン](./domain-joined/general-guidelines.md)