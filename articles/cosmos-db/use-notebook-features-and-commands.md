---
title: Azure Cosmos DB の組み込みノートブックのコマンドと機能を使用する (プレビュー)
description: Azure Cosmos DB の組み込みノートブックを使用し、組み込みのコマンドと機能で一般的な操作を実行する方法について説明します。
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: dech
ms.openlocfilehash: 61d46bbf0ccdeb5cd2e95e36e19f1aa81cfeeb48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513401"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-preview"></a>Azure Cosmos DB の組み込みノートブックのコマンドと機能を使用する (プレビュー)

Azure Cosmos DB の組み込みの Jupyter ノートブックを使用すると、Azure portal のデータを分析して視覚化することができます。 この記事では、組み込みノートブックのコマンドと機能を使用して一般的な操作を行う方法について説明します。

## <a name="install-a-new-package"></a>新しいパッケージをインストールする
Azure Cosmos アカウントのノートブック サポートを有効にしたら、新しいノートブックを開き、パッケージをインストールできます。

新しいコード セルに次のコードを挿入して実行します。このとき、``PackageToBeInstalled`` を任意の Python パッケージに変更します。
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
このパッケージは、Azure Cosmos アカウントワークスペースの任意のノートブックから使用できます。 

> [!TIP]
> お使いのノートブックにカスタム パッケージが必要な場合、[ワークスペースをリセットする](#reset-notebooks-workspace)とパッケージが削除されるため、ノートブックにセルを追加してパッケージをインストールすることをお勧めします。  

## <a name="run-a-sql-query"></a>SQL クエリを実行する

マジック コマンドの ``%%sql`` を使用し、お使いのアカウントにあるあらゆるコンテナーに対して [SQL クエリ](sql-query-getting-started.md)を実行できます。 次の構文を使用します。

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- ``{database_id}`` と ``{container_id}`` をお使いの Cosmos アカウントにあるデータベースとコンテナーの名前に変更します。 引数の ``--database`` と ``--container`` が指定されない場合、[既定のデータベースとコンテナー](#set-default-database-for-queries)でクエリが実行されます。
- Azure Cosmos DB で有効なあらゆる SQL クエリを実行できます。 クエリ テキストは新しい行に配置する必要があります。

次に例を示します。 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
セルで ```%%sql?``` を実行すると、ノートブックに SQL マジック コマンドのヘルプ ドキュメントが表示されます。

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>SQL クエリを実行し、Pandas DataFrame に出力する

[Pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe) に ``%%sql`` クエリの結果を出力できます。 次の構文を使用します。 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- ``{database_id}`` と ``{container_id}`` をお使いの Cosmos アカウントにあるデータベースとコンテナーの名前に変更します。 引数の ``--database`` と ``--container`` が指定されない場合、[既定のデータベースとコンテナー](#set-default-database-for-queries)でクエリが実行されます。
- 結果が含まれる DataFrame 変数の名前に ``{outputDataFrameVar}`` を変更します。
- Azure Cosmos DB で有効なあらゆる SQL クエリを実行できます。 クエリ テキストは新しい行に配置する必要があります。 

次に例を示します。

```bash
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```bash
df_cosmos.head(10)

    Action  ItemRevenue Country Item
0   Viewed  9.00    Tunisia Black Tee
1   Viewed  19.99   Antigua and Barbuda Flannel Shirt
2   Added   3.75    Guinea-Bissau   Socks
3   Viewed  3.75    Guinea-Bissau   Socks
4   Viewed  55.00   Czech Republic  Rainjacket
5   Viewed  350.00  Iceland Cosmos T-shirt
6   Added   19.99   Syrian Arab Republic    Button-Up Shirt
7   Viewed  19.99   Syrian Arab Republic    Button-Up Shirt
8   Viewed  33.00   Tuvalu  Red Top
9   Viewed  14.00   Cape Verde  Flip Flop Shoes
```
## <a name="upload-json-items-to-a-container"></a>JSON 項目をコンテナーにアップロードする
``%%upload`` マジックコマンドを使用して、JSON ファイルから指定された Azure Cosmos コンテナーにデータをアップロードできます。 次のコマンドを使用して項目をアップロードします。

```bash
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- ``{database_id}`` と ``{container_id}`` をお使いの Azure Cosmos アカウントにあるデータベースとコンテナーの名前に変更します。 引数の ``--database`` と ``--container`` が指定されない場合、[既定のデータベースとコンテナー](#set-default-database-for-queries)でクエリが実行されます。
- ``{url_location_of_file}`` を JSON ファイルの場所に置き換えます。 ファイルは有効な JSON オブジェクトの配列である必要があり、パブリック インターネット経由でアクセスできる必要があります。

次に例を示します。

```bash
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```bash
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```
出力統計を使用すると、項目のアップロードに使用される有効な RU/秒を計算できます。 たとえば、25000 の RU が 38 秒で使用された場合、有効な RU/秒は 25000ru/38 秒 = 658 RU/秒になります。

## <a name="set-default-database-for-queries"></a>クエリの既定のデータベースを設定する
ノートブックに既定のデータベース ```%%sql``` コマンドが使用されるように設定できます。 ```{database_id}``` をお使いのデータベースの名前に変更します。

```bash
%database {database_id}
```
セルで ```%database?``` を実行すると、ノートブックにドキュメントが表示されます。

## <a name="set-default-container-for-queries"></a>クエリの既定のコンテナーを設定する
ノートブックに既定のコンテナー ```%%sql``` コマンドが使用されるように設定できます。 ```{container_id}``` をお使いのコンテナーの名前に変更します。

```bash
%container {container_id}
```
セルで ```%container?``` を実行すると、ノートブックにドキュメントが表示されます。

## <a name="use-built-in-nteract-data-explorer"></a>組み込みの nteract データ エクスプローラーを使用する
組み込みの [nteract データ エクスプローラー](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897)を使用すると、データフレームをフィルター処理したり視覚化したりできます。 この機能を有効にするには、オプション ``pd.options.display.html.table_schema`` を ``True`` に設定し、``pd.options.display.max_rows`` を目的の値に設定します (すべての結果を表示するには、``pd.options.display.max_rows`` を ``None`` に設定します)。

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
![nteract データ エクスプローラー](media/use-notebook-features-and-commands/nteract-built-in-chart.png)

## <a name="use-the-built-in-python-sdk"></a>組み込みの Python SDK を使用する
[Azure Cosmos DB Python SDK for SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) のバージョン 4 がインストールされ、Azure Cosmos アカウントのノートブック環境に含まれます。

組み込みの ``cosmos_client`` インスタンスを使用し、あらゆる SDK 操作を実行します。 

次に例を示します。

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
「[Python SDK のサンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples)」を参照してください。 

> [!IMPORTANT]
> 組み込みの Python SDK は、SQL (Core) API アカウントでのみサポートされています。 その他の API については、API に対応する[関連 Python ドライバーをインストールする](#install-a-new-package)必要があります。 

## <a name="create-a-custom-instance-of-cosmos_client"></a>``cosmos_client`` のカスタム インスタンスを作成する
自由度を上げるために、次の目的で ``cosmos_client`` のカスタム インスタンスを作成できます。

- [接続ポリシー](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview)をカスタマイズする
- 現在のアカウントとは異なる Azure Cosmos アカウントに対して操作を実行する

[環境変数](#access-the-account-endpoint-and-primary-key-env-variables)を介して現在のアカウントの接続文字列と主キーにアクセスできます。 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Azure Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(url=os.environ["COSMOS_ENDPOINT"], credential=os.environ["COSMOS_KEY"], connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>アカウント エンドポイントと主キーの環境変数にアクセスする
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> 環境変数の ``COSMOS_ENDPOINT`` と ``COSMOS_KEY`` は SQL API にのみ適用できます。 その他の API については、お使いの Azure Cosmos アカウントの **[接続文字列]** または **[キー]** ブレードでエンドポイントとキーを見つけてください。  

## <a name="reset-notebooks-workspace"></a>ノートブック ワークスペースをリセットする
ノートブック ワークスペースをリセットし、既定の設定に戻すには、コマンド バーで **[Reset Workspace]\(ワークスペースのリセット\)** を選択します。 これでインストールされているカスタム パッケージがすべて削除され、Jupyter サーバーが再起動されます。 ノートブック、ファイル、Azure Cosmos リソースは影響を受けません。  

![ノートブック ワークスペースをリセットする](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB Jupyter ノートブック](cosmosdb-jupyter-notebooks.md)の長所について学習する
- [SQL API 用の Azure Cosmos DB Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) について学習する
