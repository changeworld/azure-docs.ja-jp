---
title: Azure HDInsight クラスターで Private Link を有効にする
description: Azure Private Link を使用して、HDInsight クラスターの外部に接続する方法について学習します。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 5ed9587c0c8bdb378206db70ca459cc8cd004b45
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215561"
---
# <a name="enable-private-link-on-an-hdinsight-cluster"></a>HDInsight クラスターで Private Link を有効にする

この記事では、Azure Private Link を使用して、Microsoft バックボーン ネットワーク経由でネットワークを介して HDInsight クラスターにプライベートに接続する方法について学習します。 この記事は、パブリック接続の制限に重点を置いた [Azure HDInsight でのクラスター接続の制限](./hdinsight-restrict-public-connectivity.md)に関する記事を拡張したものです。 HDInsight クラスターとそれに依存するリソースに対して、またはその内部でパブリック接続が必要な場合、「[Azure HDInsight のネットワーク トラフィックを制御する](./control-network-traffic.md)」にあるガイドラインに従ってクラスター接続を制限することを検討してください。

Private Link は、仮想ネットワーク ピアリングが利用できない、または有効になっていないクロスネットワーク シナリオで使用できます。

> [!NOTE]
> パブリック接続を制限することは、Private Link を有効にするための前提条件であり、同じ機能とは見なさないでください。

Private Link を使用して HDInsight クラスターに接続する機能はオプションであり、既定では無効になっています。 この機能は、[Azure HDInsight でのクラスター接続の制限](./hdinsight-restrict-public-connectivity.md)に関する記事で説明されているように、`resourceProviderConnection`ネットワーク プロパティが *outbound* に設定されている場合にのみ使用できます。

`privateLink` が *enabled* に設定されている場合は、内部の [標準ロード バランサー](../load-balancer/load-balancer-overview.md) (SLB) が作成され、SLB ごとに Azure Private Link サービスがプロビジョニングされます。 Private Link サービスを使用すると、プライベート エンドポイントから HDInsight クラスターにアクセスできます。

## <a name="prerequisites"></a>前提条件

標準ロード バランサーでは、基本ロード バランサーの場合と同様、[パブリック送信 NAT](../load-balancer/load-balancer-outbound-connections.md) が自動的に提供されません。 送信パブリック HDInsight の依存関係に接続するには、独自の NAT ソリューション (NAT ゲートウェイや、[ファイアウォール](./hdinsight-restrict-outbound-traffic.md)によって提供される NAT など) を提供する必要があります。 

HDInsight クラスターから送信依存関係へのアクセスは引き続き必要です。 このような送信依存関係が許可されていない場合は、クラスターの作成に失敗する可能性があります。 

### <a name="configure-a-default-network-security-group-on-the-subnet"></a>サブネットで既定のネットワーク セキュリティ グループを構成する

HDInsight クラスターをデプロイする予定のサブネットに、ネットワーク セキュリティ グループ (NSG) を作成して追加します。 NSG は、送信接続を有効にするために必要です。

### <a name="disable-network-policies-for-the-private-link-service"></a>Private Link サービスのネットワーク ポリシーを無効にする

Private Link サービスの作成を成功させるには、明示的に[Private Link サービスのネットワーク ポリシーを無効にする](../private-link/disable-private-link-service-network-policy.md)必要があります。

### <a name="configure-a-nat-gateway-on-the-subnet"></a>サブネットで NAT Gateway を構成する

ファイアウォールまたはネットワーク仮想アプライアンス (NVA) を NAT 用に構成しない場合は、NAT Gateway を使用することを選択できます。 それ以外の場合は、次の前提条件に進みます。

作業を開始するには、仮想ネットワークの構成済みサブネットに NAT Gateway (仮想ネットワークの新しいパブリック IP アドレスを持つ) を追加します。 このゲートウェイには、仮想ネットワークの外部にトラフィックを送信する必要がある場合に、プライベート内部 IP アドレスをパブリック アドレスに変換する役割を担います。

### <a name="configure-a-firewall-optional"></a>ファイアウォールを構成する (省略可能)
基本的なセットアップを開始するには、次の操作を行います。

1. *AzureFirewallSubnet* という名前の新しいサブネットを仮想ネットワークに追加します。 
1. この新しいサブネットを使用して、新しいファイアウォールを構成し、ファイアウォール ポリシーを追加します。 
1. この新しいファイアウォールのプライベート IP アドレスを、ルート テーブルの `nextHopIpAddress` 値として使用します。 
1. このルート テーブルを、仮想ネットワークの構成済みサブネットに追加します。

ファイアウォールの設定の詳細については、「[Azure HDInsight のネットワーク トラフィックを制御する](./control-network-traffic.md)」を参照してください。

次の図は、クラスターを作成する前に必要なネットワーク構成の例を示しています。 この例では、すべての送信トラフィックが、ユーザー定義のルートによって Azure Firewall に強制的に誘導されます。 クラスターを作成する前に、ファイアウォールで必要な送信依存関係を許可する必要があります。 Enterprise セキュリティ パッケージ クラスターでは、仮想ネットワーク ピアリングによって、Azure Active Directory Domain Services へのネットワーク接続を提供できます。

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="クラスターを作成する前の Private Link 環境の図。":::

## <a name="manage-private-endpoints-for-azure-hdinsight"></a>Azure HDInsight のプライベート エンドポイントを管理する

Azure HDInsight クラスターの[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用すると、仮想ネットワーク上のクライアントから [Private Link](../private-link/private-link-overview.md) を介してクラスターに安全にアクセスできるようになります。 仮想ネットワーク上のクライアントと HDInsight クラスター間のネットワーク トラフィックは、Microsoft バックボーン ネットワーク経由で送受信され、パブリック インターネットへの露出を避けることができます。

:::image type="content" source="media/hdinsight-private-link/private-endpoint-experience.png" alt-text="プライベート エンドポイント管理エクスペリエンスの図。":::

Private Link サービスのコンシューマー (Azure Data Factory など) は、次の 2 つの接続承認方法から選択できます。

* **自動**: サービスのコンシューマーに、HDInsight リソースに対する Azure ロールベースのアクセス制御 (RBAC) アクセス許可がある場合、コンシューマーは自動の承認方法を選択できます。 この場合、要求が HDInsight リソースに到達したときに、その HDInsight リソースからのアクションは必要なく、接続が自動的に承認されます。
* **手動**: サービスのコンシューマーに、HDInsight リソースに対する Azure RBAC アクセス許可がない場合、コンシューマーは手動の承認方法を選択できます。 この場合、接続要求は HDInsight リソース上で **保留中** として表示されます。 要求は、接続が確立される前に、HDInsight リソースによって手動で承認される必要があります。 

プライベート エンドポイントを管理するには、Azure portal のクラスター ビューで、 **[セキュリティとネットワーク]** の下にある **[ネットワーク]** セクションに移動します。 ここでは、すべての既存の接続、接続状態、プライベート エンドポイントの詳細を確認できます。

また、既存の接続を承認、拒否、または削除することもできます。 プライベート接続を作成するときに、接続先の HDInsight サブリソース (ゲートウェイやヘッド ノードなど) を指定できます。

次の表に、HDInsight リソースのさまざまなアクションと、その結果生じるプライベート エンドポイントの接続状態を示します。 HDInsight リソースでは、後で、コンシューマーの介入なしに、プライベート エンドポイント接続の接続状態を変更することもできます。 このアクションにより、コンシューマー側のエンドポイントの状態が更新されます。

| サービス プロバイダーのアクション | サービス コンシューマーのプライベート エンドポイントの状態 | 説明 |
| --------- | --------- | --------- |
| なし | 保留中 | 接続が手動で作成されており、プライベート リンク リソースの所有者による承認を待機しています。 |
| 承認 | Approved | 接続が自動または手動で承認され、使用する準備が整っています。 |
| Reject | 拒否 | Private Link リソースの所有者によって接続が拒否されました。 |
| [削除] | [Disconnected]\(切断済み\) | Private Link リソースの所有者によって接続が削除されました。 プライベート エンドポイントは情報が多くなり、クリーンアップのために削除する必要があります。 |

## <a name="configure-dns-to-connect-over-private-endpoints"></a>プライベート エンドポイント経由で接続するように DNS を構成する

ネットワークを設定したら、送信リソース プロバイダー接続と Private Link を有効にしたクラスターを作成できます。

プライベート クラスターにアクセスするには、Private Link の DNS 拡張機能とプライベート エンドポイントを使用できます。 `privateLink` が *enabled* に設定されている場合は、プライベート エンドポイントを作成し、プライベート DNS ゾーンを介して DNS 解決を構成できます。

Azure で管理されているパブリック DNS ゾーン `azurehdinsight.net` で作成される Private Link エントリは、次のとおりです。

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```
次の図は、ピアリングされていないか、クラスターに直接アクセスできない仮想ネットワークからクラスターにアクセスできるように構成されたプライベート DNS エントリの例を示しています。 Azure DNS プライベート ゾーンを使用して、`*.privatelink.azurehdinsight.net` の完全修飾ドメイン名 (FQDN) をオーバーライドし、クライアントのネットワーク内にあるプライベート エンドポイントの IP アドレスを解決できます。 この例では、`<clustername>.azurehdinsight.net` の構成のみを示していますが、他のクラスター エンドポイントにも拡張されます。

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="Private Link アーキテクチャの図。":::

## <a name="create-clusters"></a>クラスターの作成

次の JSON コード スニペットには、プライベート HDInsight クラスターを作成するために Azure Resource Manager テンプレートで構成する必要がある 2 つのネットワーク プロパティが含まれています。

```json
networkProperties: {
    "resourceProviderConnection": "Outbound",
    "privateLink": "Enabled"
}
```

数多くの HDInsight エンタープライズ セキュリティ機能 (Private Link など) を備えた完全なテンプレートについては、[HDInsight エンタープライズ セキュリティのテンプレート](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template)を参照してください。

PowerShell を使用してクラスターを作成するには、[例](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature)を参照してください。

Azure CLI を使用してクラスターを作成するには、[例](/cli/azure/hdinsight#az_hdinsight_create-examples)を参照してください。

## <a name="next-steps"></a>次の手順

* [Azure HDInsight 用の Enterprise セキュリティ パッケージ](enterprise-security-package.md)
* [Azure HDInsight でのエンタープライズ セキュリティの一般情報とガイドライン](./domain-joined/general-guidelines.md)