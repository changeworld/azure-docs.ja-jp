---
title: Kibana を使用して Azure Data Explorer のデータを視覚化する
description: この記事では、Azure Data Explorer を Kibana のデータ ソースとして設定する方法について説明します
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fac9c78607e50dca384670bf4cc08b50f723312b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065609"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>K2Bridge オープンソース コネクタを使用して Kibana で Azure Data Explorer のデータを視覚化する

K2Bridge (Kibana-Kusto Bridge) では、データソースとして Azure Data Explorer を使用し、Kibana でそのデータを視覚化することができます。 K2Bridge は、[オープンソース](https://github.com/microsoft/K2Bridge)のコンテナー化されたアプリケーションで、Kibana インスタンスと Azure Data Explorer クラスター間のプロキシとして機能します。 この記事では、K2Bridge を使用してその接続を作成する方法について説明します。

K2Bridge では、Kibana クエリが Kusto Query Language (KQL) に変換されて、Azure Data Explorer の結果が Kibana に送り返されます。 

   ![グラフ](media/k2bridge/k2bridge-chart.png)

K2Bridge は Kibana の [Discover]\(検出\) タブをサポートしており、次のことができます。
* データを検索と探索
* 結果のフィルター処理
* 結果グリッドでのフィールドの追加または削除
* レコードの内容の表示
* 検索の保存と共有

次の図は、K2Bridge によって Azure Data Explorer にバインドされた Kibana インスタンスを示しています。 Kibana のユーザー エクスペリエンスは変更されません。

   [![Kibana ページ](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>前提条件

Kibana で Azure Data Explorer のデータを視覚化するには、事前に次の準備をしておく必要があります。

* [Helm V3](https://github.com/helm/helm#install)。これは Kubernetes パッケージ マネージャーです
* Azure Kubernetes Service (AKS) クラスター、またはその他の Kubernetes クラスター (バージョン 1.14 からバージョン 1.16 までのテストと検証が完了しています)。 AKS クラスターが必要であれば、[Azure CLI を使用した場合](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough)または [Azure portal を使用した場合](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)の AKS クラスターのデプロイに関する記事を参照してください
* [Azure Data Explorer クラスター](create-cluster-database-portal.md)。次のものが含まれます。
    * Azure Data Explorer クラスターの URL 
    * データベース名
    
* Azure Data Explorer でデータを表示することが承認された Azure AD サービス プリンシパル。次のものが含まれます。
    * クライアント ID 
    * クライアント シークレット

    "視聴者" アクセス許可を持つサービス プリンシパルを使用することをお勧めします。 それより高いアクセス許可を使用することはお勧めしません。

    * [Azure AD サービス プリンシパルにクラスターの表示アクセス許可を設定します](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal)。

    Azure AD サービス プリンシパルについて詳しくは、[Azure AD サービス プリンシパルの作成](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)に関する記事をご覧ください。

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で K2Bridge を実行する

既定では、K2Bridges の Helm チャートでは、Microsoft の Container Registry (MCR) にある公開されているイメージが参照されます。 MCR は資格情報を必要とせず、すぐに使用できます。

1. 必要な Helm チャートをダウンロードします。

1. Elasticsearch 依存関係を Helm に追加します。 
    Elasticsearch の依存関係の理由は、K2Bridge では内部の小さい Elasticsearch インスタンスを使用してメタデータ関連の要求 (インデックスパターンや保存されたクエリなど) が処理されるためです。 この内部インスタンスにはビジネス データは保存されず、実装の詳細と見なすことができます。 

    1. Elasticsearch 依存関係を Helm に追加するには:

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. K2Bridge チャートを GitHub リポジトリの charts ディレクトリから取得するには:
        1. [GitHub](https://github.com/microsoft/K2Bridge) からリポジトリを複製します。
        1. K2Bridges ルート リポジトリ ディレクトリに移動します。
        1. 次のコマンドを実行します。

            ```bash
            helm dependency update charts/k2bridge
            ```

1. K2Bridge をデプロイします。

    1. 環境に合わせて変数に正しい値を設定します。

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. (省略可能) Azure Application Insights テレメトリを有効にします。 
        Azure Application Insights を初めて使用する場合は、最初に [Application Insights リソースを作成する](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)必要があります。 [インストルメンテーション キー](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key)を変数にコピーする必要があります。 

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>K2Bridge チャートをインストールします。

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        「[構成](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md)」では、構成オプションの完全なセットを見つけることができます。

    1. コマンドの出力では、Kibana をデプロイするために実行する次の Helm コマンドが提案されます。 必要に応じて、次を実行します。

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```
        
    1. ポート フォワーディングを使用して、localhost 上の Kibana にアクセスします。 

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```
        
    1. http://127.0.0.1:5601 を参照して、Kibana に接続します。

    1. Kibana をエンド ユーザーに公開します。 そのためには複数の方法があります。 使用する方法は、ユース ケースによって大きく異なります。

        次に例を示します。

        サービスを LoadBalancer サービスとして公開します。 これを行うには、`--set service.type=LoadBalancer` パラメーターを K2Bridge Helm インストール コマンド ([上記](#install-k2bridge-chart)) に追加します。        
    
        次に、次のコマンドを実行します。
        
        ```bash
        kubectl get service -w -n k2bridge
        ```
        
        出力は次のようになります。 

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx   <pending>      5601:30128/TCP   4m24s
        ```
 
        その後、表示される生成された EXTERNAL-IP を使用し、ブラウザーで `<EXTERNAL-IP>:5601` を開いて Kibana にアクセスできます。

1. データにアクセスするためのインデックス パターンを構成します。  
新しい Kibana インスタンスで次のようにします。
     1. Kibana を開きます。
     1. [Management]\(管理\) に移動します。
     1. **[Index Patterns]\(インデックス パターン\)** を選択します。 
     1. インデックス パターンを作成します。
インデックスの名前は、テーブル名または関数名と完全に一致している必要があります。アスタリスクは使用できません。 リストから関連する行をコピーできます。

> [!Note]
> 他の Kubernetes プロバイダーで実行するには、`values.yaml` の Elasticsearch storageClassName を、プロバイダーによって提案されたものに合うように変更します。

## <a name="visualize-data"></a>データの視覚化

Azure Data Explorer が Kibana のデータ ソースとして構成されている場合は、Kibana を使用してデータを探索できます。 

1. Kibana の左側のメニューで、 **[Discover]\(検出\)** タブを選択します。

1. 左側のドロップダウン リストから、探索するデータ ソースが定義されているインデックス パターン (この場合は Azure Data Explorer テーブル) を選択します。
    
   ![インデックス パターンを選択する](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. データに時間フィルター フィールドがある場合は、時間の範囲を指定できます。 ページの右上で時間フィルターを設定します。 既定では、[Discover]\(検出\) には過去 15 分間のデータが表示されます。

   ![時間フィルター](media/k2bridge/k2bridge-time-filter.png)
    
1. 結果テーブルには、最初の 500 レコードが表示されます。 ドキュメントを展開して、JSON 形式またはテーブル形式のいずれかで、そのフィールド データを調べることができます。

   ![レコードを展開する](media/k2bridge/k2bridge-expand-record.png)

1. 既定では、結果テーブルには、ドキュメントの _source と時間フィールド (存在する場合) の列が含まれています。 左側のサイドバーでフィールド名の横にある **[add]\(追加\)** を選択することで、結果テーブルに追加する特定の列を選択できます。

   ![特定の列](media/k2bridge/k2bridge-specific-columns.png)
    
1. クエリ バーでは、次の方法でデータを検索できます。
    * 検索用語の入力
    * Lucene クエリ構文の使用。 
    次に例を示します。
        * "error" を検索して、この値を含むすべてのレコードを検索します。 
        * "status: 200" を検索して、状態値が 200 のすべてのレコードを取得します。 
    * 論理演算子 (AND、OR、NOT) の使用
    * ワイルドカードの使用 (アスタリスク "\*" または疑問符 "?")。次に例を示します。
        * クエリ `"destination_city: L*"` は、宛先の都市の値が "l" で始まるレコードと一致します (K2Bridge では大文字と小文字が区別されません)。

    ![Run query](media/k2bridge/k2bridge-run-query.png)
    
    > [!Tip]
    > 「[検索](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)」では、さらに多くの検索ルールとロジックを見つけることができます。

1. 検索結果をフィルター処理するには、ページの右サイドバーにある**フィールド リスト**を使用します。 
    フィールド リストには、次の項目が表示されます。
    * フィールドの上位 5 つの値
    * フィールドが含まれているレコードの数
    * 各値を含むレコードの割合。 
    
    >[!Tip]
    > (+) 拡大鏡アイコンを使用して、特定の値を持つすべてのレコードを検索します。
    
    ![フィールド リスト](media/k2bridge/k2bridge-field-list.png)
   
    結果テーブル内の各レコードの結果テーブル形式ビューで、(+) 拡大鏡アイコンを使用して、結果をフィルター処理することもできます。
    
     ![テーブルの一覧](media/k2bridge/k2bridge-table-list.png)
    
1. 検索の **[Save]\(保存\)** または **[Share]\(共有\)** を選択します。

     ![検索を保存する](media/k2bridge/k2bridge-save-search.png)
