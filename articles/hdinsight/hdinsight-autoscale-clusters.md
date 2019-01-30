---
title: Azure HDInsight クラスターを自動的にスケール調整する
description: HDInsight の自動スケール機能を使用してクラスターを自動的にスケール調整する
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: hrasheed
ms.openlocfilehash: 043c83e2039d87b1650ba17f770ce16a2ad2c13d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811164"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Azure HDInsight クラスターを自動的にスケール調整する

Azure HDInsight のクラスター自動スケール機能は、事前に定義された範囲内の負荷に基づいて、クラスター内のワーカー ノードの数を自動的にスケールアップおよびスケールダウンします。 新しい HDInsight クラスターの作成中に、ワーカー ノードの最小数と最大数を設定できます。 自動スケールはその後、分析負荷のリソース要件を監視し、それに応じてワーカー ノードの数をスケールアップまたはスケールダウンします。 この機能に対して追加料金は発生しません。

## <a name="getting-started"></a>Getting Started (概要)

### <a name="create-cluster-with-azure-portal"></a>Azure Portal を使用してクラスターを作成する

> [!Note]
> 自動スケールは現在、Azure HDInsight Hive、MapReduce、および Spark クラスター バージョン 3.6 でのみサポートされています。

「[Azure Portal を使用した HDInsight の Linux ベースのクラスターの作成](hdinsight-hadoop-create-linux-clusters-portal.md)」の手順に従い、手順 5 の「**クラスター サイズ**」に達したら、下に示すように **[Worker node Autoscale (preview)] (ワーカー ノードの自動スケール (プレビュー))** を選択します。 

![[Worker node Autoscale (preview)] (ワーカー ノードの自動スケール (プレビュー)) オプションを有効にする](./media/hdinsight-autoscale-clusters/worker-node-autoscale-option.png)

このオプションをオンにすると、次の内容を指定できます。

* ワーカー ノードの初期の数
* ワーカー ノードの最小数
* ワーカー ノードの最大数

ワーカー ノードの初期の数には、最小数から最大数までの数を指定する必要があります。 この値によって、クラスターが作成されるときのその初期サイズが定義されます。 ワーカー ノードの最小数は 1 以上である必要があります。

ノードの種類ごとに VM の種類を選択すると、クラスター全体の概算のコスト範囲を表示できるようになります。 その後、これらの設定を予算に合わせて調整できます。

サブスクリプションには、リージョンごとに容量のクォータがあります。 ヘッド ノードのコアの総数とワーカー ノードの最大数の合計が容量のクォータを超えることはできません。 ただし、このクォータはソフト制限です。それを簡単に増やすためのサポート チケットをいつでも作成できます。

> [!Note]
> 合計のコア クォータ制限を超えた場合は、'最大ノードがこのリージョン内の使用可能なコア数を超えました。別のリージョンを選択するか、またはサポートに連絡してクォータを増やしてください。' というエラー メッセージが表示されます。

### <a name="create-cluster-with-an-resource-manager-template"></a>Resource Manager テンプレートを使用してクラスターを作成する

Resource Manager テンプレートを使用して HDInsight クラスターを作成する場合は、"computeProfile" "worker node" セクションに次の設定を追加する必要があります。

```json
{                            
    "name": "workernode",
    "targetInstanceCount": 4,
    "autoscale": {
        "minInstanceCount": 2,
        "maxInstanceCount": 10
    },
    "hardwareProfile": {
        "vmSize": "Standard_D13_V2"
    },
    "osProfile": {
        "linuxOperatingSystemProfile": {
            "username": "[parameters('sshUserName')]",
            "password": "[parameters('sshPassword')]"
        }
    },
    "virtualNetworkProfile": null,
    "scriptActions": []
}
```

### <a name="enable-and-disabling-autoscale-for-a-running-cluster"></a>実行中のクラスターの自動スケールの有効化および無効化

実行中のクラスターの自動スケールの有効化は、プライベート プレビュー中はサポートされません。 クラスターの作成中に有効にする必要があります。

自動スケールの無効化や実行中のクラスターの自動スケール設定の変更は、プライベート プレビューではサポートされません。 これらの設定を削除または変更するには、クラスターを削除し、新しいクラスターを作成する必要があります。

## <a name="monitoring"></a>監視

クラスター メトリックの一部としてクラスターのスケールアップおよびスケールダウンの履歴を表示できます。 過去の 1 日、1 週間、またはそれより長い期間中のすべてのスケール アクションを一覧表示できます。

## <a name="how-it-works"></a>動作のしくみ

### <a name="metrics-monitoring"></a>メトリックの監視

自動スケールはクラスターを継続的に監視し、次のメトリックを収集します。

1. **保留中の CPU の合計**: すべての保留中のコンテナーの実行を開始するために必要なコアの総数。
2. **保留中のメモリの合計**: すべての保留中のコンテナーの実行を開始するために必要なメモリの合計 (MB 単位)。
3. **空き CPU の合計**: アクティブなワーカー ノード上のすべての未使用のコアの合計。
4. **空きメモリの合計**: アクティブなワーカー ノード上の未使用のメモリの合計 (MB 単位)。
5. **ノードごとの使用済みメモリ**: ワーカー ノード上の負荷。 10 GB のメモリが使用されているワーカー ノードは、使用済みメモリが 2 GB のワーカー ノードより多くの負荷がかかっていると見なされます。
6. **ノードごとのアプリケーション マスターの数**: ワーカー ノード上で実行されているアプリケーション マスター (AM) コンテナーの数。 2 つの AM コンテナーをホストしているワーカー ノードは、ホストしている AM コンテナーがないワーカー ノードより重要であると見なされます。

上のメトリックは 60 秒ごとにチェックされます。 自動スケールは、これらのメトリックに基づいてスケールアップおよびスケールダウンの決定を行います。

### <a name="cluster-scale-up"></a>クラスターのスケールアップ

次の条件が検出されると、自動スケールはスケールアップ要求を発行します。

* 保留中の CPU の合計が空き CPU の合計を超えて 1 分以上が経過した。
* 保留中のメモリの合計が空きメモリの合計を超えて 1 分以上が経過した。

現在の CPU とメモリの要件を満たすには N 個の新しいワーカー ノードが必要であることを計算した後、N 個の新しいワーカー ノードを要求してスケールアップ要求を発行します。

### <a name="cluster-scale-down"></a>クラスターのスケールダウン

次の条件が検出されると、自動スケールはスケールダウン要求を発行します。

* 保留中の CPU の合計が空き CPU の合計を下回って 10 分以上が経過した。
* 保留中のメモリの合計が空きメモリの合計を下回って 10 分以上が経過した。

ノードごとの AM コンテナーの数および現在の CPU とメモリの要件に基づいて、自動スケールは、どのノードが削除の潜在的な候補であるかを指定して N 個のノードを削除するための要求を発行します。 既定では、1 サイクルで 2 つのノードが削除されます。

## <a name="next-steps"></a>次の手順

* クラスターを手動でスケール調整するためのベスト プラクティスについては、[スケール調整のベスト プラクティス](hdinsight-scaling-best-practices.md)に関するページを参照してください。
