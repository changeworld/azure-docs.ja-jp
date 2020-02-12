---
title: コンテナーの Azure Monitor エージェントのデータ収集を構成する | Microsoft Docs
description: この記事では、コンテナーの Azure Monitor エージェントによる stdout/stderr および環境変数のログ収集の制御を構成する方法について説明します。
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 28b93190298ae61732ff7d2e297899af4ba0e5f2
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933015"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>コンテナーの Azure Monitor に対するエージェントのデータ収集を構成する

コンテナーの Azure Monitor では、コンテナー化されたエージェントにより、マネージド Kubernetes クラスターにデプロイされたコンテナー ワークロードからの stdout、stderr、および環境変数が収集されます。 このエクスペリエンスを制御するためのカスタム Kubernetes ConfigMaps を作成することにより、エージェントのデータ収集の設定を構成できます。 

この記事では、ConfigMap を作成し、要件に基づいてデータの収集を構成する方法を示します。

>[!NOTE]
>Azure Red Hat OpenShift では、テンプレートの ConfigMap ファイルが *openshift-azure-loggin* 名前空間に作成されます。 
>

## <a name="configmap-file-settings-overview"></a>ConfigMap ファイル設定の概要

最初から作成する必要がなく、ご自分のカスタマイズで簡単に編集できる、テンプレート ConfigMap ファイルが提供されています。 始める前に、[ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) についての Kubernetes のドキュメントを確認し、ConfigMaps を作成、構成、デプロイする方法をよく理解しておく必要があります。 そうすることで、名前空間ごとまたはクラスター全体の stderr と stdout、およびクラスター内のすべてのポッド/ノードで実行されている任意のコンテナーに対する環境変数を、フィルター処理できます。

>[!IMPORTANT]
>コンテナー ワークロードからの stdout、stderr、環境変数の収集をサポートされているエージェントの最小バージョンは、ciprod06142019 以降です。 お使いのエージェントのバージョンを確認するには、 **[ノード]** タブでノードを選択し、プロパティ ウィンドウで **[エージェント イメージ タグ]** プロパティの値に注目します。 エージェント バージョンおよび各リリースに含まれている機能の追加情報については、[エージェントのリリース ノート](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)を参照してください。

### <a name="data-collection-settings"></a>データ収集の設定

データ収集を制御するために構成できる設定を次に示します。

|Key |データ型 |値 |説明 |
|----|----------|------|------------|
|`schema-version` |String (大文字と小文字が区別されます) |v1 |これは、この ConfigMap を解析するときにエージェントで使われるスキーマのバージョンです。 現在サポートされているスキーマのバージョンは、v1 です。 この値に対する変更はサポートされておらず、ConfigMap の評価時に拒否されます。|
|`config-version` |String | | ソース管理システム/リポジトリでこの構成ファイルのバージョンを追跡する機能をサポートします。 許容される最大文字数は 10 で、他のすべての文字は切り捨てられます。 |
|`[log_collection_settings.stdout] enabled =` |Boolean | true または false | stdout コンテナーのログ収集が有効かどうかを制御します。 `true` に設定した場合、stdout のログ収集に対して名前空間を除外しないと (後の `log_collection_settings.stdout.exclude_namespaces` 設定)、クラスター内のすべてのポッド/ノードのすべてのコンテナーから、stdout ログが収集されます。 ConfigMap で指定しない場合、既定値は `enabled = true` です。 |
|`[log_collection_settings.stdout] exclude_namespaces =`|String | コンマ区切りの配列 |stdout のログを収集しない Kubernetes 名前空間の配列。 この設定は、`log_collection_settings.stdout.enabled` を `true` に設定した場合にのみ有効です。 ConfigMap で指定しない場合、既定値は `exclude_namespaces = ["kube-system"]` です。|
|`[log_collection_settings.stderr] enabled =` |Boolean | true または false |stderr コンテナーのログ収集が有効かどうかを制御します。 `true` に設定した場合、stdout のログ収集に対して名前空間を除外しないと (`log_collection_settings.stderr.exclude_namespaces` 設定)、クラスター内のすべてのポッド/ノードのすべてのコンテナーから、stderr ログが収集されます。 ConfigMap で指定しない場合、既定値は `enabled = true` です。 |
|`[log_collection_settings.stderr] exclude_namespaces =` |String |コンマ区切りの配列 |stderr のログを収集しない Kubernetes 名前空間の配列。 この設定は、`log_collection_settings.stdout.enabled` を `true` に設定した場合にのみ有効です。 ConfigMap で指定しない場合、既定値は `exclude_namespaces = ["kube-system"]` です。 |
| `[log_collection_settings.env_var] enabled =` |Boolean | true または false | この設定により、クラスター内のすべてのポッド/ノードにわたる環境変数コレクションが制御されます。ConfigMap で指定されていない場合、既定値は `enabled = true` になります。 環境変数のコレクションがグローバルに有効になっている場合は、特定のコンテナーに対してそのコレクションを無効にすることができます。そのためには、Dockerfile 設定を使用して、または[ポッドの構成ファイル](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/)の **env:** セクションで、環境変数 `AZMON_COLLECT_ENV` を **False** に設定します。 環境変数のコレクションがグローバルに無効になっている場合、特定のコンテナーに対してコレクションを有効にすることはできません (つまり、コンテナー レベルで適用できる唯一のオーバーライドは、既にグローバルに有効になっている場合にコレクションを無効にすることです)。 |
| `[log_collection_settings.enrich_container_logs] enabled =` |Boolean | true または false | クラスター内のすべてのコンテナー ログを対象に ContainerLog テーブルに書き込まれるあらゆるログ レコードに関して、名前と画像のプロパティ値にデータを入力するとき、この設定によりコンテナー ログのエンリッチメントが制御されます。 ConfigMap に指定されていない場合、既定は `enabled = false` になります。 |

ConfigMaps はグローバル リストであり、エージェントに適用できる ConfigMap は 1 つだけです。 別の ConfigMaps でコレクションを上書きすることはできません。

## <a name="configure-and-deploy-configmaps"></a>ConfigMap を構成してデプロイする

ConfigMap 構成ファイルを構成してクラスターにデプロイするには、次の手順のようにします。

1. テンプレート ConfigMap の yaml ファイルを[ダウンロード](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml)し、container-azm-ms-agentconfig.yaml として保存します。 

   >[!NOTE]
   >Azure Red Hat OpenShift を使用する場合は、ConfigMap テンプレートがクラスターに既に存在しているため、この手順は必要ありません。

2. カスタマイズで ConfigMap yaml ファイルを編集し、stdout、stderr、または環境変数を収集します。 Azure Red Hat OpenShift の ConfigMap yaml ファイルを編集している場合は、最初にコマンド `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` を実行して、テキスト エディターでファイルを開きます。

    - stdout のログ収集から特定の名前空間を除外するには、次の例を使ってキー/値を構成します: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`。
    
    - 特定のコンテナーからの環境変数の収集を無効にするには、キー/値 `[log_collection_settings.env_var] enabled = true` を設定して変数の収集をグローバルに有効にした後、[こちら](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container)の手順に従って特定のコンテナーの構成を完了します。
    
    - クラスター全体で stderr のログ収集を無効にするには、次の例を使ってキー/値を構成します: `[log_collection_settings.stderr] enabled = false`。

3. Azure Red Hat OpenShift 以外のクラスターの場合は、Azure Red Hat OpenShift 以外のクラスターで kubectl コマンド `kubectl apply -f <configmap_yaml_file.yaml>` を実行して、ConfigMap を作成します。 
    
    例: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Azure Red Hat OpenShift では、エディターの変更内容を保存します。

構成の変更が有効になるまでに数分かかる場合があり、クラスター内のすべての omsagent ポッドが再起動されます。 すべての omsagent ポッドが同時に再起動されるのではなく、ローリング再起動で行われます。 再起動が完了すると、次のような結果を含むメッセージが表示されます: `configmap "container-azm-ms-agentconfig" created`。

## <a name="verify-configuration"></a>構成の確認

Azure Red Hat OpenShift 以外のクラスターに構成が正常に適用されたことを確認するには、次のコマンドを使って、エージェント ポッドからのログを確認します: `kubectl logs omsagent-fdf58 -n=kube-system`。 omsagent ポッドからの構成エラーがある場合は、出力で次のようなエラーが示されます。

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

構成変更の適用に関連するエラーも確認できます。 構成変更の追加のトラブルシューティングを実行するには、次のオプションを使用できます。

- エージェント ポッド ログから。同じ `kubectl logs` コマンドを使用。 

    >[!NOTE]
    >このコマンドは、Azure Red Hat OpenShift クラスターには適用できません。
    > 

- ライブ ログから。 ライブ ログでは、次のようなエラーが示されます。

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- Log Analytics ワークスペースの **KubeMonAgentEvents** テーブルから。 データは、構成エラーに関する*エラー*の重大度と共に、1 時間ごとに送信されます。 エラーがない場合、テーブルのエントリには "*情報*" の重大度のデータが含まれ、エラーは報告されません。 **[タグ]** プロパティには、エラーが発生したポッドとコンテナー ID に関する詳細情報のほか、過去 1 時間の最初の発生、最後の発生、および発生回数も含まれます。

- Azure Red Hat OpenShift では、openshift-azure-logging のログ収集が有効になっているかどうかを確認するために、**ContainerLog** テーブルを検索して omsagent ログを確認します。

Azure Red Hat OpenShift 以外のクラスターで ConfigMap のエラーを修正した後、次のコマンドを実行して yaml ファイルを保存し、更新された ConfigMap を適用します: `kubectl apply -f <configmap_yaml_file.yaml`。 Azure Red Hat OpenShift の場合は、次のコマンドを実行して、更新された ConfigMap を編集して保存します。

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>更新された ConfigMap を適用する

Azure Red Hat OpenShift 以外のクラスターに ConfigMap を既にデプロイしてあり、それをより新しい構成で更新したい場合は、前に使用した ConfigMap ファイルを編集してから、前と同じコマンド `kubectl apply -f <configmap_yaml_file.yaml` を使って適用できます。 Azure Red Hat OpenShift の場合は、次のコマンドを実行して、更新された ConfigMap を編集して保存します。

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

構成の変更が有効になるまでに数分かかる場合があり、クラスター内のすべての omsagent ポッドが再起動されます。 すべての omsagent ポッドが同時に再起動されるのではなく、ローリング再起動で行われます。 再起動が完了すると、次のような結果を含むメッセージが表示されます: `configmap "container-azm-ms-agentconfig" updated`。

## <a name="verifying-schema-version"></a>スキーマのバージョンを確認する

サポートされている構成スキーマのバージョンは、omsagent ポッドのポッド注釈 (schema-versions) として利用できます。 次の kubectl コマンドでそれらを確認できます: `kubectl describe pod omsagent-fdf58 -n=kube-system`

注釈 schema-versions を含む次のような出力が表示されます。

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>次のステップ

- Azure Monitor for containers には、定義済みの一連のアラートは含まれません。 [Azure Monitor for containers を使用したパフォーマンス アラートの作成](container-insights-alerts.md)に関するページを読んで、CPU やメモリの使用率が高い場合に推奨アラートを作成し、DevOps や運用プロセスまたは手順をサポートする方法について学習します。

- AKS またはハイブリッド クラスターと実行中のワークロードの正常性とリソース使用率を収集するための監視を有効にしたうえで、コンテナーの Azure Monitor を[使用する方法](container-insights-analyze.md)について学習します。

- [ログ クエリの例](container-insights-log-search.md#search-logs-to-analyze-data)を表示して、事前定義されたクエリや例を確認し、クラスターのアラート、視覚化、または分析のために評価やカスタマイズを行います。
