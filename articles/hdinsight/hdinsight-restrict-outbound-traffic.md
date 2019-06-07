---
title: Azure HDInsight クラスターの送信ネットワーク トラフィック制限の構成
description: Azure HDInsight クラスターの送信ネットワーク トラフィック制限を構成する方法について説明します。
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 05/13/2019
ms.openlocfilehash: 44b6f099b5b17329976b9fec3c0ac38b5e394221
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978013"
---
# <a name="configure-outbound-network-traffic-restriction-for-azure-hdinsight-clusters-preview"></a>Azure HDInsight クラスターの送信ネットワーク トラフィック制限の構成 (プレビュー)

この記事では、Azure Firewall を使用して HDInsight クラスターからの送信トラフィックをセキュリティで保護するための手順を説明します。 次の手順では、既存のクラスター用に Azure Firewall を構成することを前提としています。 新しいクラスターをファイアウォールの背後にデプロイする場合は、最初に HDInsight クラスターとサブネットを作成してから、このガイドの手順に従います。

## <a name="background"></a>バックグラウンド

Azure HDInsight クラスターは、通常は独自の仮想ネットワークにデプロイされます。 クラスターには、その仮想ネットワークの外部にある、正常に機能するためにネットワーク アクセスを必要とするサービスへの依存関係があります。

受信トラフィックを必要とする依存関係は複数あります。 受信管理トラフィックはファイアウォール デバイスを介して送信できません。 このトラフィックのソース アドレスは既知であり、[こちら](hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip)で公開されています。 また、その情報を使用してネットワーク セキュリティ グループ (NSG) ルールを作成し、クラスターへの受信トラフィックをセキュリティで保護することもできます。

HDInsight の送信トラフィックの依存関係は、ほぼすべて、背後に静的 IP アドレスがない FQDN を使用して定義されています。 静的アドレスがないということは、ネットワーク セキュリティ グループ (NSG) を使用してクラスターからの送信トラフィックをロックできないことを意味します。 アドレスは頻繁に変わるので、現在の名前解決に基づいてルールを設定し、それを使用して NSG ルールを設定することができません。

送信アドレスをセキュリティで保護する解決策は、ドメイン名に基づいて送信トラフィックを制御できるファイアウォール デバイスを使用することです。 Azure Firewall では、宛先の FQDN または [FQDN タグ](https://docs.microsoft.com/azure/firewall/fqdn-tags)に基づいて送信 HTTP および HTTPS トラフィックを制限できます。

## <a name="configuring-azure-firewall-with-hdinsight"></a>HDInsight に合わせて Azure Firewall を構成する

Azure Firewall を使用して既存の HDInsight からのエグレスをロックダウンする手順の概要は、次のとおりです。
1. サービス エンドポイントを有効にします。
1. ファイアウォールを作成します。
1. ファイアウォールにアプリケーション ルールを追加します
1. ファイアウォールにネットワーク ルールを追加します。
1. ルーティング テーブルを作成します。

### <a name="enable-service-endpoints"></a>サービス エンドポイントを有効にします

ファイアウォールをバイパスする (たとえば、データ転送のコストを節約する) 場合は、HDInsight サブネット上の SQL とストレージのサービス エンドポイントを有効にすることができます。 Azure SQL に対するサービス エンドポイントを有効にした場合、お使いのクラスターの Azure SQL の依存関係もすべて、サービス エンドポイントで構成する必要があります。

適切なサービス エンドポイントを有効にするには、次の手順を完了します。

1. Azure portal にサインインして、HDInsight クラスターがデプロイされている仮想ネットワークを選択します。
1. **[設定]** で、 **[サブネット]** を選択します。
1. クラスターがデプロイされているサブネットを選択します。
1. サブネットの設定を編集する画面の **[サービス エンドポイント]**  >  **[サービス]** ドロップダウン ボックスで、 **[Microsoft.SQL]** または **[Microsoft.Storage]** をクリックします。
1. ESP クラスターを使用する場合は、 **[Microsoft.AzureActiveDirectory]** サービス エンドポイントも選択する必要があります。
1. **[Save]** をクリックします。

### <a name="create-a-new-firewall-for-your-cluster"></a>クラスター用の新しいファイアウォールを作成します

1. クラスターが存在する仮想ネットワークに **AzureFirewallSubnet** という名前のサブネットを作成します。 
1. 次のチュートリアルの手順を使用して、新しいファイアウォール「**Test-FW01**」を作成します。「[チュートリアル: Deploy and configure Azure Firewall using the Azure portal (チュートリアル: Azure portal を使用して Azure Firewall のデプロイと構成を行う)](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall)」を参照してください。
1. Azure portal で新しいファイアウォールを選択します。 **[設定]**  >  **[アプリケーション ルール コレクション]**  >  **[アプリケーション ルール コレクションの追加]** で **[ルール]** をクリックします。

    ![タイトル:アプリケーション ルール コレクションを追加する](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

### <a name="configure-the-firewall-with-application-rules"></a>アプリケーション ルールを使用してファイアウォールを構成する

クラスターで重要な通信を送受信できるようにするアプリケーション ルール コレクションを作成します。

Azure portal で新しいファイアウォール「**Test-FW01**」を選択します。 **[設定]**  >  **[アプリケーション ルール コレクション]**  >  **[アプリケーション ルール コレクションの追加]** で **[ルール]** をクリックします。

**[アプリケーション ルール コレクションの追加]** 画面で、次の手順を完了します。

1. **[名前]** 、 **[優先順位]** を入力し、 **[アクション]** ドロップダウン メニューで **[許可]** をクリックします。
1. 次のルールを追加します。
    1. HDInsight と Windows Update のトラフィックを許可するルール:
        1. **[FQDN タグ]** セクションで、 **[名前]** を入力し、 **[ソース アドレス]** を「`*`」に設定します。
        1. **[FQDN タグ]** ドロップダウン メニューで **[HDInsight]** と **[WindowsUpdate]** を選択します。
    1. Windows ログイン アクティビティを許可するルール:
        1. **[ターゲットの FQDN]** セクションで、 **[名前]** を入力し、 **[ソース アドレス]** を「`*`」に設定します。
        1. **[プロトコル:ポート]** に「`https:443`」、 **[ターゲットの FQDN]** に「`login.windows.net`」と入力します。
    1. SQM 利用統計情報を許可するルール:
        1. **[ターゲットの FQDN]** セクションで、 **[名前]** を入力し、 **[ソース アドレス]** を「`*`」に設定します。
        1. **[プロトコル:ポート]** に「`https:443`」、 **[ターゲットの FQDN]** に「`sqm.telemetry.microsoft.com`」と入力します。
    1. クラスターが WASB によってサポートされていて、上のサービス エンドポイントを使用していない場合は、WASB のルールを追加します。
        1. **[ターゲットの FQDN]** セクションで、 **[名前]** を入力し、 **[ソース アドレス]** を「`*`」に設定します。
        1. wasb:// または wasbs:// のどちらを使用しているかに応じて **[プロトコル:ポート]** に「`http`」または [https] と入力し、 **[ターゲットの FQDNS]** にストレージ アカウント url を入力します。
1. **[追加]** をクリックします。

![タイトル:アプリケーション ルール コレクションの詳細を入力する](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

### <a name="configure-the-firewall-with-network-rules"></a>ネットワーク ルールを使用してファイアウォールを構成する

HDInsight クラスターを正しく構成するネットワーク ルールを作成します。

> [!Important]
> このセクションで説明したようにネットワーク ルールを使用してファイアウォールで SQL サービス タグを使用するか、[サービス エンドポイントに関するセクション](#enable-service-endpoints)で説明したように SQL サービス エンドポイントを使用するかを選択できます。 ネットワーク ルールで SQL タグを使用する場合は、SQL トラフィックのログを記録して監査することができます。 サービス エンドポイントを使用すると、SQL トラフィックはファイアウォールをバイパスします。

1. Azure portal で新しいファイアウォール「**Test-FW01**」を選択します。
1. **[設定]**  >  **[ネットワーク ルール コレクション]**  >  **[ネットワーク ルール コレクションの追加]** で **[ルール]** をクリックします。
1. **[ネットワーク ルール コレクションの追加]** 画面で、 **[名前]** 、 **[優先順位]** を入力し、 **[アクション]** ドロップダウン メニューで **[許可]** をクリックします。
1. 次のルールを作成します。
    1. クラスターで NTP を使用してクロック同期を実行できるようにするネットワーク ルール。
        1. **[ルール]** セクションで、 **[名前]** を入力し、 **[プロトコル]** ドロップダウンで **[任意]** を選択します。
        1. **[ソース アドレス]** と **[宛先アドレス]** を「`*`」に設定します。
        1. **[宛先ポート]** を「123」に設定します。
    1. Enterprise セキュリティ パッケージ (ESP) を使用している場合は、ESP クラスター用に AAD DS との通信を許可するネットワーク ルールを追加します。
        1. ドメイン コントローラーの 2 つの IP アドレスを決定します。
        1. **[ルール]** セクションの次の行で、 **[名前]** を入力し、 **[プロトコル]** ドロップダウンで **[任意]** を選択します。
        1. **[ソース アドレス]** を「`*`」に設定します。
        1. **[宛先アドレス]** にドメイン コントローラーのすべての IP アドレスをコンマで区切って入力します。
        1. **[宛先ポート]** を「`*`」に設定します。
    1. Azure Data Lake Storage を使用している場合は、ADLS Gen1 と Gen2 に関する SNI の問題に対処するネットワーク ルールを追加することができます。 このオプションでは、大量のデータ読み込みのコストを引き上げる可能性のあるファイアウォールにトラフィックがルーティングされますが、トラフィックはログに記録され、監査可能になります。
        1. Data Lake Storage アカウントの IP アドレスを決定します。 `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` などの powershell コマンドを使用して、FQDN を IP アドレスに解決できます。
        1. **[ルール]** セクションの次の行で、 **[名前]** を入力し、 **[プロトコル]** ドロップダウンで **[任意]** を選択します。
        1. **[ソース アドレス]** を「`*`」に設定します。
        1. **[宛先アドレス]** にストレージ アカウントの IP アドレスを入力します。
        1. **[宛先ポート]** を「`*`」に設定します。
    1. Windows のライセンス認証用のキー管理サービスとの通信を可能にするネットワーク ルール。
        1. **[ルール]** セクションの次の行で、 **[名前]** を入力し、 **[プロトコル]** ドロップダウンで **[任意]** を選択します。
        1. **[ソース アドレス]** を「`*`」に設定します。
        1. **[宛先アドレス]** を「`*`」に設定します。
        1. **[宛先ポート]** を「`1688`」に設定します。
    1. Log Analytics を使用している場合は、Log Analytics ワークスペースとの通信を可能にするネットワーク ルールを作成します。
        1. **[ルール]** セクションの次の行で、 **[名前]** を入力し、 **[プロトコル]** ドロップダウンで **[任意]** を選択します。
        1. **[ソース アドレス]** を「`*`」に設定します。
        1. **[宛先アドレス]** を「`*`」に設定します。
        1. **[宛先ポート]** を「`12000`」に設定します。
    1. SQL トラフィックのログを記録して監査できるようにする、SQL のサービス タグを構成します。
        1. **[ルール]** セクションの次の行で、 **[名前]** を入力し、 **[プロトコル]** ドロップダウンで **[任意]** を選択します。
        1. **[ソース アドレス]** を「`*`」に設定します。
        1. **[宛先アドレス]** を「`*`」に設定します。
        1. **[サービス タグ]** ドロップダウンで **[Sql]** を選択します。
        1. **[宛先ポート]** を「`1433,11000-11999,14000-14999`」に設定します。
1. **[追加]** をクリックして、ネットワーク ルール コレクションの作成を完了します。

![タイトル:アプリケーション ルール コレクションの詳細を入力する](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

### <a name="create-and-configure-a-route-table"></a>ルート テーブルを作成して構成する

次のエントリを使用してルート テーブルを作成します。

1. **インターネット**を次ホップとする[こちらの必要な HDInsight 管理 IP アドレスの一覧](../hdinsight/hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip)にある 7 つのアドレス:
    1. すべてのリージョンのすべてのクラスター用の 4 つの IP アドレス
    1. クラスターが作成されるリージョンに固有の 2 つの IP アドレス
    1. Azure の再帰リゾルバーの 1 つのアドレス
1. 次ホップが Azure Firewall プライベート IP アドレスである、IP アドレス 0.0.0.0/0 の 1 つの仮想アプライアンス ルート。

たとえば、"米国中部" の米国リージョンに作成したクラスターのルート テーブルを構成するには、次の手順を使用します。

1. Azure ポータルにサインインします。
1. Azure ファイアウォール「**Test-FW01**」を選択します。 **[概要]** ページに表示されている **[プライベート IP アドレス]** をコピーします。 この例では、**同じアドレス 10.1.1.4** を使用します
1. 新しいルート テーブルを作成します。
1. **[設定]** で、 **[ルート]** をクリックします。
1. **[追加]** をクリックして、次の表の IP アドレスのルートを作成します。

| ルート名 | アドレス プレフィックス | 次ホップの種類 | 次ホップ アドレス |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | インターネット | NA |
| 23.99.5.239 | 23.99.5.239/32 | インターネット | NA |
| 168.61.48.131 | 168.61.48.131/32 | インターネット | NA |
| 138.91.141.162 | 138.91.141.162/32 | インターネット | NA |
| 13.67.223.215 | 13.67.223.215/32 | インターネット | NA |
| 40.86.83.253 | 40.86.83.253/32 | インターネット | NA |
| 168.63.129.16 | 168.63.129.16/32 | インターネット | NA |
| 0.0.0.0 | 0.0.0.0/0 | 仮想アプライアンス | 10.1.1.4 |

![タイトル:ルート テーブルの作成](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-route-table.png)

ルート テーブルの構成を完了します。

1. **[設定]** で **[サブネット]** をクリックし、次に **[関連付け]** をクリックすることで、作成したルート テーブルを HDInsight サブネットに割り当てます。
1. **[サブネットの関連付け]** 画面で、クラスターが作成された仮想ネットワークと、ファイアウォールで使用するために作成した **[AzureFirewallSubnet]** を選択します。
1. Click **OK**.

![タイトル:ルート テーブルの作成](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-route-table-associate-subnet.png)

## <a name="edge-node-application-traffic"></a>エッジノードのアプリケーション トラフィック

上記の手順で、クラスターが問題なく動作するようになります。 ただし、該当する場合は、エッジノードで実行されているカスタムのアプリケーションに対応する依存関係を構成する必要があります。

アプリケーションの依存関係を特定し、Azure Firewall またはルート テーブルに追加する必要があります。

非対称ルーティングの問題を回避するために、アプリケーションのトラフィック用にルートを作成する必要があります。

アプリケーションに他の依存関係がある場合は、それらを Azure Firewall に追加する必要があります。 その他すべてに関する HTTP/HTTPS トラフィックとネットワークのルールを許可するようにアプリケーション ルールを作成します。

## <a name="logging"></a>ログの記録

Azure Firewall は、いくつかの異なるストレージ システムにログを送信できます。 ファイアウォールのログ記録の構成手順については、次のチュートリアルの手順に従ってください。「[チュートリアル: 「Azure Firewall のログとメトリックを監視する](../firewall/tutorial-diagnostics.md)」を参照してください。

ログ記録のセットアップを完了した後、Log Analytics にデータをログ記録する場合は、次のようなクエリでブロックされたトラフィックを表示することができます。

```
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Azure Firewall を Azure Monitor ログと統合すると、アプリケーションのすべての依存関係がわからないときに初めてアプリケーションを動作させる場合に役立ちます。 Azure Monitor ログについて詳しくは、「[Azure Monitor でログ データを分析する](../azure-monitor/log-query/log-query-overview.md)」をご覧ください

## <a name="configure-another-network-virtual-appliance"></a>別のネットワーク仮想アプライアンスの構成

>[!Important]
> 次の情報が必要なのは、Azure Firewall 以外のネットワーク仮想アプライアンス (NVA) を構成する場合**のみ**です。

前の手順では、HDInsight クラスターからの送信トラフィックを制限するために Azure Firewall を構成することができます。 Azure Firewall は自動的に、多くの一般的な重要なシナリオのトラフィックを許可するように構成されます。 別のネットワーク仮想アプライアンスを使用する場合は、さまざまな追加機能を手動で構成する必要があります。 ネットワーク仮想アプライアンスを構成する場合は、次の点に注意してください。

* サービス エンドポイント対応のサービスは、サービス エンドポイントを使用して構成する必要があります。
* IP アドレスの依存関係が HTTP/S 以外のトラフィック (TCP トラフィックと UDP トラフィックの両方) に対応しています。
* FQDN HTTP/HTTPS エンドポイントは、NVA デバイスに配置することができます。
* ワイルドカード HTTP/HTTPS エンドポイントは、さまざまな修飾子に応じて変わる場合がある依存関係です。
* 作成したルート テーブルを HDInsight サブネットに割り当てます。

### <a name="service-endpoint-capable-dependencies"></a>サービス エンドポイント対応の依存関係

| **エンドポイント** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>IP アドレスの依存関係

| **エンドポイント** | **詳細** |
|---|---|
| \*:123 | NTP クロック チェック。 トラフィックは、ポート 123 上の複数のエンドポイントでチェックされます |
| [こちら](hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip)で発行された IP | これらは HDInsight サービスです |
| ESP クラスター用の AAD DS プライベート IP |
| \*:KMS Windows ライセンス認証の場合は 16800 |
| \*Log Analytics の場合は 12000 |

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS の依存関係

>[!Important]
> 以下の一覧には、いくつかの最も重要な FQDN のみを示しています。 NVA を構成するための FQDN の完全な一覧は、[このファイルで](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json)取得できます。

| **エンドポイント**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                |
| ocsp.digicert.com:80                                                |

## <a name="next-steps"></a>次の手順

* [Azure HDInsight 仮想ネットワーク アーキテクチャ](hdinsight-virtual-network-architecture.md)
