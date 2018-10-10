---
title: Azure Machine Learning Data Prep SDK でデータを読み込む - Python
description: Azure Machine Learning Data Prep SDK を使用してデータを読み込む方法について説明します。 さまざまな種類の入力データを読み込むことができ、データ ファイルの種類やパラメーターを指定することも、SDK のスマート読み取り機能を使用してファイルの種類を自動検出することもできます。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 436ff9d318dc311efe27352a8b2ac91cfb5be618
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47221330"
---
#<a name="load-and-read-data-with-azure-machine-learning"></a>Azure Machine Learning を使用したデータの読み込みと読み取り

[Azure Machine Learning Data Prep SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py) を使用して、さまざまな種類の入力データを読み込みます。 

データを読み込むには、次の 2 つの方法があります。
+ データ ファイルの種類とそのパラメーターを指定する
+ SDK のスマート読み取り機能を使用してファイルの種類を自動的に検出する

## <a name="use-text-line-data"></a>テキスト行データを使用する 
データを読み込む最も簡単な方法の 1 つは、データをテキスト行として読み込むことです。

次にサンプル コードを示します。
```python
dataflow = dprep.read_lines(path='./data/text_lines.txt')
dataflow.head(5)
```
||Line|
|----|-----|
|0|日付 \|\|  最低気温 \|\|  最大温度|
|1|2015-07-1 \|\|  -4.1 \|\|  10.0|
|2|2015-07-2 \|\|  -0.8 \|\|  10.8|
|3|2015-07-3 \|\|  -7.0 \|\|  10.5|
|4|2015-07-4 \|\|  -5.5 \|\|  9.3|

データが取り込まれた後は、完全なデータセットの pandas データフレームを取得できます。

次にサンプル コードを示します。
```python
df = dataflow.to_pandas_dataframe()
df
```
サンプル出力:
||Line|
|----|-----|
|0|日付\|\| 最低気温\|\| 最大温度|
|1|2015-07-1\|\| 4.1\|\| 10.0|
|2|2015-07-2\|\| 0.8\|\| 10.8|
|3|2015-07-3\|\| 7.0\|\| 10.5|
|4|2015-07-4\|\| 5.5\|\| 9.3|

## <a name="use-csv-data"></a>CSV データを使用する
区切り形式のファイルを読み込む場合は、解析パラメーター (区切り文字、エンコーディング、ヘッダーを使用するかどうかなど) を指定するのではなく、基礎ランタイムに推測させることができます。 この例では、ファイルの場所だけを指定してファイルを読み込みます。 

次にサンプル コードを示します。
```python
# SAS expires June 16th, 2019
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
dataflow.head(5)
```

サンプル出力:
| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0||stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|1|ALABAMA|1|101710|Hale County|10171002158| |
|2|ALABAMA|1|101710|Hale County|10171002162| |
|3|ALABAMA|1|101710|Hale County|10171002156| |
|4|ALABAMA|1|101710|Hale County|10171000588|2|

指定できるパラメーターの 1 つは、読み込むファイルでスキップする行数です。 重複する行を除外するには、次のコードを使用します。
```python
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1)
dataflow.head(5)
```

サンプル出力:
| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Hale County|10171002158|29|
|1|ALABAMA|1|101710|Hale County|10171002162|40 |
|2|ALABAMA|1|101710|Hale County|10171002156| 43|
|3|ALABAMA|1|101710|Hale County|10171000588|2|
|4|ALABAMA|1|101710|Hale County|10171000589|23 |

次は、列のデータ型を確認できます。
次にサンプル コードを示します。
```python
dataflow.head(1).dtypes

stnam                     object
fipst                     object
leaid                     object
leanm10                   object
ncessch                   object
schnam10                  object
MAM_MTH00numvalid_1011    object
dtype: object
```

残念ながら、すべての列が文字列として返されました。 既定では、Azure Machine Learning Data Prep SDK はデータ型を変更しないからです。 ここで読み込むデータ ソースはテキスト ファイルなので、SDK はすべての値を文字列として読み取ります。 ただし、この例では、数値列を数値として解析する必要があるものとします。 そのためには、current_culture に inference_arguments パラメーターを設定します。

```
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1,
                          inference_arguments=dprep.InferenceArguments.current_culture())
dataflow.head(1).dtypes

stnam                      object
fipst                     float64
leaid                     float64
leanm10                    object
ncessch                   float64
schnam10                   object
ALL_MTH00numvalid_1011    float64
dtype: object
```

複数の列が数値として正しく検出され、型が float64 に設定されました。 取り込みが完了すると、完全なデータセットの pandas データフレームを取得できます。
次にサンプル コードを示します。
```python
df = dataflow.to_pandas_dataframe()
df
```

サンプル出力:
| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|1.017100e+10|49.0|
|1|ALABAMA|Hale County|1.017100e+10|40.0|
|2|ALABAMA|Hale County|1.017100e+10|43.0|
|3|ALABAMA|Hale County|1.017100e + 10|2.0|
|4|ALABAMA|Hale County|1.017100e+10|23.0|

## <a name="use-excel-data"></a>Excel データを使用する
Azure Machine Learning Data Prep SDK には、Excel ファイルを読み込むための `read_excel` 関数が含まれています。 次にサンプル コードを示します。
```python
dataflow = dprep.read_excel(path='./data/excel.xlsx')
dataflow.head(5)
```

サンプル出力:
||Column1|Column2|Column3|Column4|Column5|Column6|Column7|Column8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|Hoba|Iron, IVB|60000000.0|Found|1920.0|http://www.lpi.usra.edu/meteor/metbull.php?cod.. |-19.58333|17.91667|
|1|Cape York|Iron, IIIAB|58200000.0|Found|1818.0|http://www.lpi.usra.edu/meteor/metbull.php?cod.. |76.13333|-64.93333|
|2|Campo del Cielo|Iron, IAB-MG|50000000.0|Found|1576.0|http://www.lpi.usra.edu/meteor/metbull.php?cod.. |-27.46667|-60.58333|
|3|Canyon Diablo|Iron, IAB-MG|30000000.0|Found|1891.0|http://www.lpi.usra.edu/meteor/metbull.php?cod.. |35.05000|-111.03333|
|4|Armanty|Iron, IIIE|28000000.0|Found|1898.0|http://www.lpi.usra.edu/meteor/metbull.php?cod.. |47.00000|88.00000|

Excel ファイルの最初のシートが読み込まれました。 読み込むシートの名前を明示的に指定することで、同じ結果を得ることができます。 代わりに、2 番目のシートを読み込むには、引数としてシートの名前を指定します。 例: 
```python
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2')
dataflow.head(5)
```

サンプル出力:
||Column1|Column2|Column3|Column4|Column5|Column6|Column7|Column8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|なし|なし|なし|なし|なし|なし|なし|なし|なし|
|1|なし|なし|なし|なし|なし|なし|なし|なし|なし|
|2|なし|なし|なし|なし|なし|なし|なし|なし|なし|
|3|RANK|タイトル|スタジオ|世界全域|国内 / %|列 1|海外 / %|列 2|年 ^|
|4|1|Avatar|Fox|2788|760.5|0.273|2027.5|0.727|2009^|5|

ご覧のように、2 番目のシート内のテーブルにはヘッダーと 3 つの空の行があります。 したがって関数の引数を変更する必要があります。次に例を示します。
```python
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2', use_header=True, skip_rows=3)
df = dataflow.to_pandas_dataframe()
df
```

サンプル出力:
||RANK|タイトル|スタジオ|世界全域|国内 / %|列 1|海外 / %|列 2|年 ^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|Avatar|Fox|2788|760.5|0.273|2027.5|0.727|2009^|
|1|2|Titanic|Par.|2186.8|658.7|0.301|1528.1|0.699|1997^|
|2|3|Marvel's The Avengers|BV|1518.6|623.4|0.41|895.2|0.59|2012|
|3|4|Harry Potter and the Deathly Hallows Part 2|WB|1341.5|381|0.284|960.5|0.716|2011|
|4|5|Frozen|BV|1274.2|400.7|0.314|873.5|0.686|2013|

## <a name="use-fixed-width-data-files"></a>固定幅データ ファイルを使用する
固定幅ファイルの場合は、オフセットのリストを指定できます。 最初の列は常にオフセット 0 から始まると想定されます。次に例を示します。
```python
dataflow = dprep.read_fwf('./data/fixed_width_file.txt', offsets=[7, 13, 43, 46, 52, 58, 65, 73])
dataflow.head(5)
```

サンプル出力:
||010000|99999|BOGUS NORWAY|NO|NO_1|ENRS|列 7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010003|99999|BOGUS NORWAY|NO|NO|ENSO||||
|1|010010|99999|JAN MAYEN|NO|JN|ENJA|+70933|-008667|+00090|
|2|010013|99999|ROST|NO|NO|||||
|3|010014|99999|SOERSTOKKEN|NO|NO|ENSO|+59783|+005350|+00500|
|4|010015|99999|BRINGELAND|NO|NO|ENBL|+61383|+005867|+03270|


ファイルにヘッダーが存在しない場合は、最初の行をデータとして扱う必要があります。 ヘッダー キーワード引数に `PromoteHeadersMode.NONE` を渡すことで、ヘッダーの検出を回避し、正しいデータを取得できます。 例: 
```python
dataflow = dprep.read_fwf('./data/fixed_width_file.txt',
                          offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                          header=dprep.PromoteHeadersMode.NONE)

df = dataflow.to_pandas_dataframe()
df
```

サンプル出力:

||Column1|Column2|Column3|Column4|Column5|Column6|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|BOGUS NORWAY|NO|NO_1|ENRS|列 7|Column8|Column9|
|1|010003|99999|BOGUS NORWAY|NO|NO|ENSO||||
|2|010010|99999|JAN MAYEN|NO|JN|ENJA|+70933|-008667|+00090|
|3|010013|99999|ROST|NO|NO|||||
|4|010014|99999|SOERSTOKKEN|NO|NO|ENSO|+59783|+005350|+00500|
|5|010015|99999|BRINGELAND|NO|NO|ENBL|+61383|+005867|+03270|

## <a name="use-sql-data"></a>SQL データを使用する
Azure Machine Learning Data Prep SDK では、SQL サーバーからデータを読み込むこともできます。 現在は、Microsoft SQL Server のみサポートされています。
SQL server からデータを読み込むには、接続情報を含むデータ ソース オブジェクトを作成します。 例: 
```python
secret = dprep.register_secret("[SECRET-USERNAME]", "[SECRET-PASSWORD]")

ds = dprep.MSSQLDataSource(server_name="[SERVER-NAME]",
                           database_name="[DATABASE-NAME]",
                           user_name="[DATABASE-USERNAME]",
                           password=[DATABASE-PASSWORD])
```
ご覧のとおり、`MSSQLDataSource` のパスワード パラメーターは、シークレット オブジェクトを取ります。 シークレット オブジェクトは、2 つの方法で取得できます。
-   実行エンジンに、シークレットとその値を登録します。 
-   ID のみを使用して、シークレットを作成します (実行環境にシークレット値が既に登録されている場合に便利です)。

データ ソース オブジェクトを作成したら、データの読み込みに進むことができます。 例: 
```python
dataflow = dprep.read_sql(ds, "SELECT top 100 * FROM [SalesLT].[Product]")
dataflow.head(5)
```

サンプル出力:
||ProductID|Name|ProductNumber|色|StandardCost|ListPrice|サイズ|Weight|ProductCategoryID|ProductModelID|SellStartDate|SellEndDate|DiscontinuedDate|ThumbNailPhoto|ThumbnailPhotoFileName|rowguid|ModifiedDate|
|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
|0|680|HL Road Frame - Black, 58|FR-R92B-58|黒|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00+00:00|なし|なし|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|43dd68d6-14a4-461f-9069-55309d90ea7e|2008-03-11 |0:01:36.827000+00:00|
|1|706|HL Road Frame - Red, 58|FR-R92R-58|赤|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00+00:00|なし|なし|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|9540ff17-2712-4c90-a3d1-8ce5568b2462|2008-03-11 |10:01:36.827000+00:00|
|2|707|Sport-100 Helmet, Red|HL-U509-R|赤|13.0863|34.99|なし|なし|35|33|2005-07-01 00:00:00+00:00|なし|なし|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|2e1ef41a-c08a-4ff6-8ada-bde58b64a712|2008-03-11 |10:01:36.827000+00:00|
|3|708|Sport-100 Helmet, Black|HL-U509|黒|13.0863|34.99|なし|なし|35|33|2005-07-01 00:00:00+00:00|なし|なし|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|a25a44fb-c2de-4268-958f-110b8d7621e2|2008-03-11 |10:01:36.827000+00:00|
|4|709|Mountain Bike Socks, M|SO-B909-M|白|3.3963|9.50|M|なし|27|18|2005-07-01 00:00:00+00:00|2006-06-30 00:00:00+00:00|なし|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|18f95f47-1540-4e02-8f1f-cc1bcb6828d0|2008-03-11 |10:01:36.827000+00:00|

```python
df = dataflow.to_pandas_dataframe()
df.dtypes
```

サンプル出力:
```
ProductID                                     int64
Name                                         object
ProductNumber                                object
Color                                        object
StandardCost                                float64
ListPrice                                   float64
Size                                         object
Weight                                      float64
ProductCategoryID                             int64
ProductModelID                                int64
SellStartDate             datetime64[ns, UTC+00:00]
SellEndDate                                  object
DiscontinuedDate                             object
ThumbNailPhoto                               object
ThumbnailPhotoFileName                       object
rowguid                                      object
ModifiedDate              datetime64[ns, UTC+00:00]
dtype: object
```

## <a name="use-azure-data-lake-storage"></a>Azure Data Lake Storage を使用する
SDK が Azure Data Lake Storage にアクセスするために必要な OAuth トークンを取得するには、2 つの方法があります。
-   ユーザーの Azure CLI ログインの最近のログイン セッションからアクセス トークンを取得する
-   サービス プリンシパル (SP) と証明書をシークレットとして使用する

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>最近の Azure CLI セッションからアクセス トークンを使用する
ローカル コンピューターで、次のコマンドを実行します。

> [!NOTE] 
> ユーザー アカウントが複数の Azure テナントのメンバーである場合は、AAD URL ホスト名の形式でテナントを指定する必要があります。


例: 
```azurecli
az login
az account show --query tenantId
dataflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dataflow.head(5) head
```
### <a name="create-a-service-principal-with-the-azure-cli"></a>Azure CLI でサービス プリンシパルを作成する
Azure CLI を使用してサービス プリンシパルと対応する証明書を作成することができます。 この特定のサービス プリンシパルはリーダーとして構成され、その範囲は Azure Data Lake Storage アカウントの 'dpreptestfiles' のみに縮小されています  例: 
```azurecli
az account set --subscription "Data Wrangling development"
az ad sp create-for-rbac -n "SP-ADLS-dpreptestfiles" --create-cert --role reader --scopes /subscriptions/35f16a99-532a-4a47-9e93-00305f6c40f2/resourceGroups/dpreptestfiles/providers/Microsoft.DataLakeStore/accounts/dpreptestfiles
```
このコマンドは、`appId` と証明書ファイルへのパス (通常はホーム フォルダー内) を発行します。 .crt ファイルには、パブリック証明書と秘密キーの両方が PEM 形式で格納されています。

サムプリントを抽出する:
```
openssl x509 -in adls-dpreptestfiles.crt -noout -fingerprint
```

Azure Data Lake Storage ファイル システムの ACL を構成するには、ユーザーの objectId を使用するか、この例ではサービス プリンシパルを使用します。 例: 
```azurecli
az ad sp show --id "8dd38f34-1fcb-4ff9-accd-7cd60b757174" --query objectId
```

Azure Data Lake Storage ファイル システムの `Read` および `Execute` アクセスを構成するには、フォルダーとファイルの ACL を個別に構成する必要があります。 これは、基礎となる HDFS ACL モデルが継承をサポートしていないためです。 例: 
```azurecli
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r-x" --path /
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r--" --path /farmers-markets.csv
```
```
certThumbprint = 'C2:08:9D:9E:D1:74:FC:EB:E9:7E:63:96:37:1C:13:88:5E:B9:2C:84'
certificate = ''
with open('./data/adls-dpreptestfiles.crt', 'rt', encoding='utf-8') as crtFile:
    certificate = crtFile.read()

servicePrincipalAppId = "8dd38f34-1fcb-4ff9-accd-7cd60b757174"
```
### <a name="acquire-an-oauth-access-token"></a>OAuth アクセス トークンを取得する
MSFT テナントで認証コンテキストを作成し、OAuth アクセス トークンを取得するには、`adal` パッケージ (`pip install adal` 経由) を使用します。 ADLS の場合、トークン要求のリソースは、他の多くの Azure リソースとは異なり、'https://datalake.azure.net' 用でなければなりません。

```python
import adal
from azureml.dataprep.api.datasources import DataLakeDataSource

ctx = adal.AuthenticationContext('https://login.microsoftonline.com/microsoft.onmicrosoft.com')
token = ctx.acquire_token_with_client_certificate('https://datalake.azure.net/', servicePrincipalAppId, certificate, certThumbprint)
dataflow = dprep.read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', accessToken=token['accessToken']))
dataflow.to_pandas_dataframe().head()
```

||FMID|MarketName|Web サイト|street|city|County|
|----|------|-----|----|----|----|----|
|0|1012063|Caledonia Farmers Market Association - Danville|https://sites.google.com/site/caledoniafarmers.. ||Danville|Caledonia|
|1|1011871|Stearns Homestead Farmers' Market|http://Stearnshomestead.com |6975 Ridge Road|Parma|Cuyahoga|
|2|1011878|100 Mile Market|http://www.pfcmarkets.com |507 Harrison St|Kalamazoo|Kalamazoo|
|3|1009364|106 S. Main Street Farmers Market|http://thetownofsixmile.wordpress.com/ |106 S. Main Street|Six Mile|||
|4|1010691|10th Steet Community Farmers Market|http://agrimissouri.com/mo-grown/grodetail.php.. |10th Street and Poplar|Lamar|Barton|

## <a name="use-smart-reading"></a>"スマート読み取り" を使用する

SDK のスマート読み取り機能を使用してファイルの種類を自動的に検出します。
