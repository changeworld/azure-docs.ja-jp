---
title: Azure Functions を使用した HDInsight 上の Kafka へのデータの送信 | Microsoft Docs
description: Azure 関数を使用して HDInsight 上の Kafka にデータを書き込む方法を説明します。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2018
ms.author: larryfr
ms.openlocfilehash: 6c462f9fe5e152c82be1a2b8643ee35d260a90f6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="use-kafka-on-hdinsight-from-an-azure-function-app"></a>Azure 関数アプリから HDInsight 上の Kafka を使用する

HDInsight 上の Kafka にデータを送信する Azure 関数アプリを作成する方法について説明します。

[Azure Functions](https://docs.microsoft.com/azure/azure-functions/) はサーバーレス コンピューティング サービスです。 インフラストラクチャを明示的にプロビジョニングまたは管理することなく、オンデマンドでコードを実行できます。

[Apache Kafka](https://kafka.apache.org) はオープン ソースの分散ストリーム プラットフォームで、リアルタイムのストリーミング データ パイプラインとアプリケーションの構築に使用できます。 Kafka は、名前付きデータ ストリームへの公開および購読ができる、メッセージ キューと同様のメッセージ ブローカー機能も提供しています。 HDInsight での Kafka の使用により、管理された、拡張性の高い、高可用性のサービスが Microsoft Azure クラウドで提供されます。

HDInsight 上の Kafka では、パブリック インターネットの API は提供されません。 Kafka からのデータを発行または使用するには、Azure Virtual Network を使用して Kafka クラスターに接続する必要があります。 Azure Functions では、Virtual Network ゲートウェイを介して HDInsight 上の Kafka にデータをプッシュするパブリック エンドポイントを作成するための便利な手段が提供されます。

> [!NOTE]
> このドキュメントでは、Azure Functions が HDInsight 上の Kafka と通信できるようにするために必要な手順を中心に説明します。 例自体は、構成が機能していることを示すための基本的な Kafka のプロデューサーにすぎません。

## <a name="prerequisites"></a>前提条件

* Azure 関数アプリ。 詳細については、[最初の関数の作成](../../azure-functions/functions-create-first-azure-function.md)に関するドキュメントをご覧ください。

* Azure Virtual Network。 Azure Functions と連携させるには、仮想ネットワークで次の IP 範囲のいずれかを使用する必要があります。

    * 10.0.0.0-10.255.255.255
    * 172.16.0.0-172.31.255.255
    * 192.168.0.0-192.168.255.255

    詳細については、「[アプリを Azure 仮想ネットワークに統合する](../../app-service/web-sites-integrate-with-vnet.md)」をご覧ください。

* HDInsight クラスター上の Kafka。 HDInsight クラスター上に Kafka を作成する方法については、[Kafka クラスターの作成](apache-kafka-get-started.md)に関するドキュメントをご覧ください。

    > [!IMPORTANT]
    > クラスターの構成中に、__詳細設定__の手順を使用して、HDInsight で使用される Azure Virtual Network とサブネットを選択する必要があります。 前の手順で作成した仮想ネットワークとサブネットを選択します。

    Kafka と仮想ネットワークについて詳しくは、[仮想ネットワークを介した Kafka との接続](apache-kafka-connect-vpn-gateway.md)に関するドキュメントをご覧ください。

## <a name="architecture"></a>アーキテクチャ

HDInsight 上の Kafka は、Azure Virtual Network に含まれます。 ポイント対サイト ゲートウェイを使用することで、Azure Functions は仮想ネットワークと通信できます。 このネットワーク トポロジのダイアグラムを次の図に示します。

![HDInsight に接続する Azure Functions のアーキテクチャ](./media/apache-kafka-azure-functions/kafka-azure-functions.png)

## <a name="configure-kafka"></a>Kafka の構成

このセクションでは、Kafka クラスターに Azure 関数からのデータを受け入れさせるための準備について説明します。 次の構成操作が含まれます。

* IP の提供
* Kafka ブローカーの IP アドレスの収集
* Kafka トピックの作成

### <a name="configure-kafka-for-ip-advertising"></a>IP を提供するように Kafka を構成する

既定では、Zookeeper は、Kafka ブローカーのドメイン名をクライアントに返します。 この構成はDNS サーバーがないと機能しません。これは、クライアント (Azure Functions) が仮想ネットワークの名前を解決できないためです。 このように構成する場合は、次の手順を実行して、ドメイン名ではなく IP アドレスを提供するように Kafka を構成します。

1. Web ブラウザーを使用し、https://CLUSTERNAME.azurehdinsight.net にアクセスします。 __CLUSTERNAME__ を HDInsight クラスター上の Kafka の名前に置き換えます。

    プロンプトが表示されたら、クラスターの HTTPS ユーザー名とパスワードを入力します。 クラスターの Ambari Web UI が表示されます。

2. Kafka に関する情報を表示するには、左にある一覧から __[Kafka]__ を選択します。

    ![Kafka が強調表示されているサービスの一覧](./media/apache-kafka-azure-functions/select-kafka-service.png)

3. Kafka 構成を表示するには、上部中央の __[Configs (構成)]__ を選択します。

    ![Kafka の構成リンク](./media/apache-kafka-azure-functions/select-kafka-config.png)

4. __kafka-env__ 構成を検索するには、右上の __[Filter (フィルター)]__ フィールドに「`kafka-env`」と入力します。

    ![kafka-env の Kafka 構成](./media/apache-kafka-azure-functions/search-for-kafka-env.png)

5. IP アドレスを提供するように Kafka を構成するには、次のテキストを __kafka-env-template__ フィールドの最後に追加します。

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Kafka がリッスンするインターフェイスを構成するには、右上の __[Filter (フィルター)]__ フィールドに「`listeners`」と入力します。

7. すべてのネットワーク インターフェイスをリッスンするように Kafka を構成するには、__[listeners (リスナー)]__ フィールドの値を `PLAINTEXT://0.0.0.0:9092`に変更します。

8. 構成を保存するには、__[Save (保存)]__ ボタンを使用します。 変更を説明するテキスト メッセージを入力します。 変更が保存されたら、__[OK]__ を保存します。

    ![構成を保存するボタン](./media/apache-kafka-azure-functions/save-button.png)

9. Kafka の再起動時にエラーが発生しないようにするため、__[Service Actions (サービス アクション)__] ボタンを使用して __[Turn On Maintenance Mode (メンテナンス モードの有効化)]__ を選択します。 [OK] を選択して、この操作を完了します。

    ![[Turn On Maintenance Mode (メンテナンス モードの有効化)] が強調表示されているサービス アクション](./media/apache-kafka-azure-functions/turn-on-maintenance-mode.png)

10. Kafka を再起動するには、__[Restart (再起動)]__ ボタンをクリックし、__[Restart All Affected (影響を受けるものをすべて再起動)]__ を選択します。 再起動を確認し、操作が完了したら __[OK]__ ボタンを使用します。

    ![[Restart All Affected (影響を受けるものをすべて再起動)] が強調表示されている [Restart (再起動)] ボタン](./media/apache-kafka-azure-functions/restart-button.png)

11. メンテナンス モードを無効にするには、__[Service Actions (サービス アクション)]__ ボタンをクリックし、__[Turn Off Maintenance Mode (メンテナンス モードの無効化)]__ を選択します。 **[OK]** を選択して、この操作を完了します。

### <a name="get-the-kafka-broker-ip-address"></a>Kafka ブローカーの IP アドレスの取得

次のいずれかの方法により、Kafka クラスター内のノードの完全修飾ドメイン名 (FQDN) と IP アドレスを取得します。

```powershell
$resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

$clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

$nodes = @()
foreach($nic in $clusterNICs) {
    $node = new-object System.Object
    $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
    $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
    $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
    $nodes += $node
}
$nodes | sort-object Type
```

```azurecli
az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
```

このコマンドでは、仮想ネットワークを含む Azure リソース グループの名前を `<resourcegroupname>` で表しています。

返される名前の一覧から、workernode の IP アドレスを選択します。 ノードの内部完全修飾ドメイン名は文字 `wn` で始まります。 この IP アドレスは、関数が Kafka と通信するために使用されます。

### <a name="create-a-kafka-topic"></a>Kafka トピックの作成

Kafka では、__トピック__にデータが格納されます。 Azure 関数から Kafka にデータを送信する前に、関数を作成する必要があります。

トピックを作成するには、[Kafka クラスターの作成](apache-kafka-get-started.md)に関するドキュメントの手順に従います。

## <a name="create-a-function-that-sends-to-kafka"></a>Kafka に送信する関数の作成

> [!NOTE]
> このセクションの手順では、[kafka-node](https://www.npmjs.com/package/kafka-node) パッケージを使用して Kafka にデータを発行する JavaScript の関数を作成します。

1. 新しい __[WebHook + API]__ 関数を作成し、言語として __[JavaScript]__ を選択します。 新しい関数の作成について詳しくは、[最初の関数の作成](../../azure-functions/functions-create-first-azure-function.md#create-function)に関するドキュメントをご覧ください。

2. 関数の本体として次のコードを使用します。

    ```javascript
    var kafka = require('kafka-node');

    // The topic and a Kafka broker host for your HDInsight cluster
    var topic = 'test';
    var brokerHost = '10.1.0.7:9092';
    // Create the client
    var client= new kafka.KafkaClient({kafkaHost: brokerHost});

    module.exports = function (context, req) {
        // Create the producer
        var producer = new kafka.Producer(client, {requireAcks: 1});

        context.log('JavaScript HTTP trigger function processed a request.');

        if (req.query.name || (req.body && req.body.name)) {
            var name = req.query.name || req.body.name
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: "Hello " + name
            };
            // Create the payload, using the name as the body
            var payloads = [
                    { topic: topic, messages: [name]}
            ];
            context.log("calling producer.send");
            // Send the data to Kafka
            producer.send(payloads, function(err, data) {
                if(err) {
                    context.log(err);
                } else {
                    context.log(data);
                }
            });
        }
        else {
            context.res = {
                status: 400,
                body: "Please pass a name on the query string or in the request body"
            };
        }
        context.done();
    };
    ```

    `'test'` を、HDInsight クラスター上に作成した Kafka トピックの名前に置き換えます。

    `10.1.0.7` を、以前に取得した IP アドレスに置き換えます。 `:9092` の値はそのままにします。 9092 は Kafka がリッスンするポートです。

    __[保存]__ ボタンを押して、変更を保存します。

    ![エディターと保存ボタン](./media/apache-kafka-azure-functions/function-editor.png)

3. 関数エディターの右側から、__[ファイルの表示]__ を選択します。 __[+ 追加]__ を選択して、`package.json` という名前の新しいファイルを追加します。 このファイルは `kafka-node` パッケージへの依存関係を指定するために使用されます。

    ![ファイルの追加](./media/apache-kafka-azure-functions/add-files.png)

4. 新しいファイルを編集するには、__[ファイルの表示]__ の一覧から `package.json` を選択します。 ファイルの内容として、次のテキストを使用します。

    ```json
    {
    "name": "kafkatest",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "author": "",
    "license": "ISC",
    "dependencies": {
            "kafka-node": "^2.4.1"
        }
    }
    ```

    __[保存]__ ボタンを押して、変更を保存します。

5. `kafka-node` パッケージをインストールするには、「[Azure Functions の JavaScript 開発者向けガイド](../../azure-functions/functions-reference-node.md#node-version-and-package-management)」の「_Node のバージョンとパッケージの管理_」セクションを利用します。

    > [!NOTE]
    > kafka-node パッケージをインストールすると、いくつかのエラーが発生する場合があります。 これらのエラーは無視してかまいません。

## <a name="run-the-function"></a>関数の実行

関数エディターの右側から、__[テスト]__ を選択します。 テストは既定の設定のままにして、__[実行]__ を選択します。 テストを実行すると、`name` パラメーターが関数に渡されます。 このパラメーターには値 `Azure` が含まれます。これは関数が Kafka に挿入したものです。

![テスト ダイアログのスクリーンショット](./media/apache-kafka-azure-functions/function-test-dialog.png)

テストの実行中に関数によって記録された情報を表示するには、ページの下部にある __[ログ]__ を選択します。 テストを再実行してログ情報を生成します。

![関数のログ出力の例](./media/apache-kafka-azure-functions/function-log.png)

## <a name="verify-the-data-is-in-kafka"></a>データが Kafka にあることの確認

データが Kafka トピックに到着したことを確認するには、[Kafka クラスターの作成](apache-kafka-get-started.md#produce-and-consume-records)に関するドキュメントの「_レコードの生成および消費_」セクションの情報を使用します。 `kafka-console-consumer` はトピックからデータを読み取り、トピックに格納されたメッセージの一覧を表示します。

## <a name="next-steps"></a>次の手順

次のリンクを使用することで、HDInsight で Apache Kafka を使用する方法を知ることができます。

* [HDInsight での Kafka の使用](apache-kafka-get-started.md)

* [MirrorMaker を使用した HDInsight での Kafka のレプリカの作成](apache-kafka-mirroring.md)

* [HDInsight での Kafka に Apache Storm を使用する](../hdinsight-apache-storm-with-kafka.md)

* [HDInsight での Kafka に Apache Spark を使用する](../hdinsight-apache-spark-with-kafka.md)

* [Azure Virtual Network 経由で Kafka に接続する](apache-kafka-connect-vpn-gateway.md)
