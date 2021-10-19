---
title: Python での DICOMweb Standard Api の使用-Azure 医療 Api
description: このチュートリアルでは、DICOMweb Standard Api と Python を使用する方法について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 07/16/2021
ms.author: aersoy
ms.openlocfilehash: c474409b2dfdbca0a921690b1871c7268bde08d1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785518"
---
# <a name="using-dicomwebtrade-standard-apis-with-python"></a>&trade;Python での DICOMWeb Standard api の使用

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

このチュートリアルでは、Python を使用して、DICOM サービスの使用方法を示します。

このチュートリアルでは、次の例の [DCM DICOM ファイル](https://github.com/microsoft/dicom-server/tree/main/docs/dcms)を使用します。

* blue-circle
* dicom-metadata.csv
* green-square
* red-triangle

 サンプルの DICOM ファイルのファイル名、studyUID、系列 Uid、および instanceUID は次のとおりです。

| ファイル | StudyUID | 系列 Uid | InstanceUID |
| --- | --- | --- | ---|
|green-square|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652|1.2.826.0.1.3680043.8.498.12714725698140337137334606354172323212|
|red-triangle|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652|1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395|
|blue-circle|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.77033797676425927098669402985243398207|1.2.826.0.1.3680043.8.498.13273713909719068980354078852867170114|

> [!NOTE]
> これらの各ファイルは1つのインスタンスを表し、同じ研究の一部です。 また、緑色の四角形と赤の三角形は同じ系列に含まれていますが、青い円は個別の系列に含まれています。

## <a name="prerequisites"></a>前提条件

DICOMWeb Standard Api を使用するには &trade; 、DICOM サービスのインスタンスがデプロイされている必要があります。 まだ DICOM サービスをデプロイしていない場合は、「 [Azure portal を使用した dicom サービスのデプロイ](deploy-dicom-services-in-azure.md)」を参照してください。

DICOM サービスのインスタンスをデプロイした後、App service の URL を取得します。

1. [Azure Portal ](https://ms.portal.azure.com/)にサインインします。
1. **最近使用** したリソースを検索し、DICOM サービスインスタンスを選択します。
1. DICOM サービスの **サービス URL** をコピーします。 
2. まだトークンを取得していない場合は、「 [Azure CLI を使用して、DICOM サービスのアクセストークンを取得](dicom-get-access-token-azure-cli.md)する」を参照してください。 

このコードでは、パブリックプレビューの Azure サービスにアクセスします。 プライベート状態情報 (PHI) をアップロードしないことが重要です。

## <a name="working-with-the-dicom-service"></a>DICOM サービスの使用

DICOMweb 標準では、 &trade; `multipart/related` DICOM 固有の accept ヘッダーを組み合わせて HTTP 要求を多用します。 他の REST ベースの Api に慣れている開発者は、多くの場合、DICOMweb 標準の扱いにくい作業を見つけ &trade; ます。 ただし、これを実行すると、簡単に使用できます。 作業を開始するには少しの知識が必要です。

### <a name="import-the-appropriate-python-libraries"></a>適切な Python ライブラリをインポートする

まず、必要な Python ライブラリをインポートします。

同期ライブラリを使用してこの例を実装することを選択しました `requests` 。 非同期サポートの場合は、 `httpx` または別の非同期ライブラリを使用することを検討してください。 さらに、 `urllib3` 要求の処理をサポートするために、から2つのサポート関数をインポートしてい `multipart/related` ます。

さらに、 `DefaultAzureCredential` Azure にログインしてトークンを取得するためにインポートしています。

```python
import requests
import pydicom
from pathlib import Path
from urllib3.filepost import encode_multipart_formdata, choose_boundary
from azure.identity import DefaultAzureCredential
```

### <a name="configure-user-defined-variables-to-be-used-throughout"></a>ユーザー定義変数を全体で使用するように構成する

{} で囲まれたすべての変数の値を独自の値に置き換えます。 さらに、構築された変数が正しいことを検証します。  たとえば、 `base_url` はサービス URL を使用して作成され、使用されている REST API のバージョンと共に追加されます。 DICOM サービスのサービス URL は次のようになり ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` ます。 Azure ポータルを使用して、DICOM サービスに移動し、サービス URL を取得することができます。 また、バージョン管理の詳細については、「 [DICOM サービスの API のバージョン管理](api-versioning-dicom-service.md) 」を参照してください。 カスタム URL を使用している場合は、その値を独自の値に上書きする必要があります。

```python
dicom_service_name = "{server-name}"
path_to_dicoms_dir = "{path to the folder that includes green-square.dcm and other dcm files}"

base_url = f"{Service URL}/v{version}"

study_uid = "1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"; #StudyInstanceUID for all 3 examples
series_uid = "1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"; #SeriesInstanceUID for green-square and red-triangle
instance_uid = "1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"; #SOPInstanceUID for red-triangle
```

### <a name="authenticate-to-azure-and-get-a-token"></a>Azure に対して認証を行い、トークンを取得する

`DefaultAzureCredential` を使用すると、さまざまな方法でトークンを取得してサービスにログインできます。 `AzureCliCredential`サービスにログインするためのトークンを取得するために、を使用します。 やなどの他の資格情報 `ManagedIdentityCredential` プロバイダー `EnvironmentCredential` も使用できます。 AzureCliCredential を使用するには、このコードを実行する前に、CLI から Azure にログインしておく必要があります。 (詳細については [、「Azure CLI を使用して、DICOM サービスのアクセストークンを取得](dicom-get-access-token-azure-cli.md) する」を参照してください)。または、CLI からログインするときに取得したトークンをコピーして貼り付けることもできます。

> [!NOTE]
> `DefaultAzureCredential` 複数の異なる資格情報オブジェクトを返します。 `AzureCliCredential`返されたコレクションの5番目の項目としてを参照します。 これは、一貫性がない可能性があります。 その場合は、行をコメント解除 `print(credential.credential)` します。 これにより、すべての項目が一覧表示されます。 正しいインデックスを検索し、Python が0から始まるインデックスを使用することを呼び出します。

> [!NOTE]
> CLI を使用して Azure にログインしていない場合、これは失敗します。 これを機能させるには、CLI から Azure にログインする必要があります。 

```python
from azure.identity import DefaultAzureCredential
credential = DefaultAzureCredential()

#print(credential.credentials) # this can be used to find the index of the AzureCliCredential
token = credential.credentials[4].get_token('https://dicom.healthcareapis.azure.com')
bearer_token = f'Bearer {token.token}'
```

### <a name="create-supporting-methods-to-support-multipartrelated"></a>サポートするサポートメソッドを作成する `multipart\related`

`Requests`ライブラリ (およびほとんどの Python ライブラリ) は、 `multipart\related` DICOMweb をサポートするようには機能しません &trade; 。 これらのライブラリにより、DICOM ファイルの操作をサポートするためにいくつかのメソッドを追加する必要があります。

`encode_multipart_related` 一連のフィールドを取得します (DICOM ケースでは、これらのライブラリは通常、パート10の dam ファイル) と、オプションのユーザー定義の境界です。 完全な本文と content_type を返します。この値は使用できます。

```python
def encode_multipart_related(fields, boundary=None):
    if boundary is None:
        boundary = choose_boundary()

    body, _ = encode_multipart_formdata(fields, boundary)
    content_type = str('multipart/related; boundary=%s' % boundary)

    return body, content_type
```

### <a name="create-a-requests-session"></a>セッションの作成 `requests`

`requests`というセッションを作成し `client` ます。これは、DICOM サービスとの通信に使用されます。


```python
client = requests.session()
```

### <a name="verify-authentication-is-configured-correctly"></a>認証が正しく構成されていることを確認する

Changefeed API エンドポイントを呼び出します。認証が成功した場合は200が返されます。

```python
headers = {"Authorization":bearer_token}
url= f'{base_url}/changefeed'

response = client.get(url,headers=headers)
if (response.status_code != 200):
    print('Error! Likely not authenticated!')
```

## <a name="uploading-dicom-instances-stow"></a>DICOM インスタンスのアップロード (STOW)

次の例では、DICOM ファイルの永続化を強調しています。

### <a name="store-instances-using-multipartrelated"></a>を使用してインスタンスを格納する `multipart/related`

この例では、1つの DICOM ファイルをアップロードする方法を示しています。また、Python の一部を使用して、DICOM ファイル (バイト) をメモリに事前読み込みします。 ファイルの配列をの fields パラメーターに渡すことによって `encode_multipart_related` 、複数のファイルを1回の投稿でアップロードできます。 完全なシリーズまたは調査の中で複数のインスタンスをアップロードするために使用されることがあります。

_住所_

* パス:../研究
* メソッド: POST
* Headers:
    * Accept: application/dicom + json
    * Content-type: マルチパート/関連;type = "application/dicom"
    * 承認: ベアラー $token "

* 本文は次のようになります。
    * Content-type: アップロードされた各ファイルのアプリケーション/dicom は、境界値で区切られます。

一部のプログラミング言語とツールは、動作が異なります。 たとえば、一部のユーザーは独自の境界を定義する必要があります。 そのためには、若干変更された Content-type ヘッダーを使用する必要がある場合があります。 次が正常に使用されました。
* Content-Type: multipart/related;type="application/dicom";boundary=ABCD1234
* Content-Type: multipart/related;boundary=ABCD1234
* Content-Type: multipart/related

```python
#upload blue-circle.dcm
filepath = Path(path_to_dicoms_dir).joinpath('blue-circle.dcm')

# Read through file and load bytes into memory 
with open(filepath,'rb') as reader:
    rawfile = reader.read()
files = {'file': ('dicomfile', rawfile, 'application/dicom')}

#encode as multipart_related
body, content_type = encode_multipart_related(fields = files)

headers = {'Accept':'application/dicom+json', "Content-Type":content_type, "Authorization":bearer_token}

url = f'{base_url}/studies'
response = client.post(url, body, headers=headers, verify=False)
```

### <a name="store-instances-for-a-specific-study"></a>特定の調査のインスタンスを格納する

この例では、指定された調査に複数の DICOM ファイルをアップロードする方法を示します。 少し Python を使用して、DICOM ファイルを (バイトとして) メモリに事前に読み込みます。  

の fields パラメーターにファイルの配列を渡して、1 つの POST に複数の `encode_multipart_related` ファイルをアップロードできます。 これは、完全なシリーズをアップロードしたり、学習したりするために使用される場合があります。 

_詳細：_
* パス：。。/study/{study}
* メソッド: POST
* Headers:
    * 同意する: application/dicom+json
    * Content-Type: multipart/related;type="application/dicom"
    * 承認: Bearer $token"
* 本文は次のようになります。
    * Content-Type: 境界値で区切られた、アップロードされた各ファイルの application/dicom


```python

filepath_red = Path(path_to_dicoms_dir).joinpath('red-triangle.dcm')
filepath_green = Path(path_to_dicoms_dir).joinpath('green-square.dcm')

# Open up and read through file and load bytes into memory 
with open(filepath_red,'rb') as reader:
    rawfile_red = reader.read()
with open(filepath_green,'rb') as reader:
    rawfile_green = reader.read()  
       
files = {'file_red': ('dicomfile', rawfile_red, 'application/dicom'),
         'file_green': ('dicomfile', rawfile_green, 'application/dicom')}

#encode as multipart_related
body, content_type = encode_multipart_related(fields = files)

headers = {'Accept':'application/dicom+json', "Content-Type":content_type, "Authorization":bearer_token}

url = f'{base_url}/studies'
response = client.post(url, body, headers=headers, verify=False)
```
### <a name="store-single-instance-non-standard"></a>単一インスタンスを格納する (非標準)

次のコード例は、1 つの DICOM ファイルをアップロードする方法を示しています。 これは、要求の本文で送信されるバイナリ バイトとして単一のファイルをアップロードする方法を簡略化する非標準の API エンドポイントです

_詳細：_
* パス：。。/studies
* メソッド: POST
* Headers:
   *  同意する: application/dicom+json
   *  Content-Type: application/dicom
   *  承認: Bearer $token"
* 本文は次のようになります。
    * バイナリ バイトとして 1 つの DICOM ファイルが含まれます。

```python
#upload blue-circle.dcm
filepath = Path(path_to_dicoms_dir).joinpath('blue-circle.dcm')

# Open up and read through file and load bytes into memory 
with open(filepath,'rb') as reader:
    body = reader.read()

headers = {'Accept':'application/dicom+json', 'Content-Type':'application/dicom', "Authorization":bearer_token}

url = f'{base_url}/studies'
response = client.post(url, body, headers=headers, verify=False)
response  # response should be a 409 Conflict if the file was already uploaded in the above request
```

## <a name="retrieve-dicom-instances-wado"></a>DICOM インスタンスの取得 (WADO)

次の例では、DICOM インスタンスの取得について説明します。

### <a name="retrieve-all-instances-within-a-study"></a>調査内のすべてのインスタンスを取得する

この例では、1 つの調査内のすべてのインスタンスを取得します。

_詳細：_
* パス：。。/study/{study}
* メソッド: GET
* Headers:
   * Accept: multipart/related;type="application/dicom";transfer-syntax=*
   * 承認: Bearer $token"

前にアップロードした 3 つの dcm ファイルはすべて同じ調査の一部なので、応答では 3 つのインスタンスすべてが返されます。 応答の状態コードが [OK] で、3 つのインスタンスすべてが返されるのを確認します。

```python
url = f'{base_url}/studies/{study_uid}'
headers = {'Accept':'multipart/related; type="application/dicom"; transfer-syntax=*', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="use-the-retrieved-instances"></a>取得したインスタンスを使用する

インスタンスはバイナリ バイトとして取得されます。 返された項目をループ処理し、バイトを で読み取り可能なファイルに変換できます `pydicom` 。


```python
import requests_toolbelt as tb
from io import BytesIO

mpd = tb.MultipartDecoder.from_response(response)
for part in mpd.parts:
    # Note that the headers are returned as binary!
    print(part.headers[b'content-type'])
    
    # You can convert the binary body (of each part) into a pydicom DataSet
    #   And get direct access to the various underlying fields
    dcm = pydicom.dcmread(BytesIO(part.content))
    print(dcm.PatientName)
    print(dcm.SOPInstanceUID)
```

### <a name="retrieve-metadata-of-all-instances-in-study"></a>調査中のすべてのインスタンスのメタデータを取得する

この要求は、1 つの調査内のすべてのインスタンスのメタデータを取得します。

_詳細：_
* パス：。。/study/{study}/metadata
* メソッド: GET
* Headers:
   * 同意する: application/dicom+json
   * 承認: Bearer $token"

前にアップロードした 3 つのファイルはすべて同じ調査の一部なので、応答は 3 つのインスタンスすべてについてメタデータ `.dcm` を返す必要があります。 応答の状態コードが [OK] で、すべてのメタデータが返されるのを確認します。

```python
url = f'{base_url}/studies/{study_uid}/metadata'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-all-instances-within-a-series"></a>系列内のすべてのインスタンスを取得する

この要求は、1 つの系列内のすべてのインスタンスを取得します。

_詳細：_
* パス：。。/study/{study}/series/{series}
* メソッド: GET
* Headers:
   * Accept: multipart/related;type="application/dicom";transfer-syntax=*
   * 承認: Bearer $token"

この系列には 2 つのインスタンス (緑の四角形と赤い三角形) が含まれるので、応答は両方のインスタンスを返す必要があります。 応答の状態コードが [OK] で、両方のインスタンスが返されるのを確認します。

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}'
headers = {'Accept':'multipart/related; type="application/dicom"; transfer-syntax=*', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-metadata-of-all-instances-in-series"></a>一連のすべてのインスタンスのメタデータを取得する

この要求は、1 つの系列内のすべてのインスタンスのメタデータを取得します。

_詳細：_
* パス：。。/study/{study}/series/{series}/metadata
* メソッド: GET
* Headers:
   * 同意する: application/dicom+json
   * 承認: Bearer $token"

この系列には 2 つのインスタンス (緑の四角形と赤い三角形) が含まれるので、応答は両方のインスタンスに対してを返す必要があります。 応答の状態コードが [OK] で、両方のインスタンスメタデータが返されるのを確認します。

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/metadata'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-a-single-instance-within-a-series-of-a-study"></a>一連の調査内で 1 つのインスタンスを取得する

この要求は、1 つのインスタンスを取得します。

_詳細：_
* パス：。。/study/{study}/series{series}/instances/{instance}
* メソッド: GET
* Headers:
   * 同意する: application/dicom;transfer-syntax=*
   * 承認: Bearer $token"

このコード例では、インスタンスの赤三角形のみを返す必要があります。 応答の状態コードが [OK] で、インスタンスが返されるのを確認します。

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances/{instance_uid}'
headers = {'Accept':'application/dicom; transfer-syntax=*', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-metadata-of-a-single-instance-within-a-series-of-a-study"></a>一連の調査内の単一インスタンスのメタデータを取得する

この要求は、1 つの調査と系列内の単一インスタンスのメタデータを取得します。

_詳細：_
* パス：。。/study/{study}/series/{series}/instances/{instance}/metadata
* メソッド: GET
* Headers:
  * 同意する: application/dicom+json
  * 承認: Bearer $token"

このコード例では、インスタンスの赤三角形のメタデータのみを返す必要があります。 応答の状態コードが [OK] で、メタデータが返されるのを確認します。

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances/{instance_uid}/metadata'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-one-or-more-frames-from-a-single-instance"></a>1 つのインスタンスから 1 つ以上のフレームを取得する

この要求は、1 つのインスタンスから 1 つ以上のフレームを取得します。

_詳細：_
* パス：。。/study/{study}/series{series}/instances/{instance}/frames/1,2,3
* メソッド: GET
* Headers:
   * 承認: ベアラー $token "
   * `Accept: multipart/related; type="application/octet-stream"; transfer-syntax=1.2.840.10008.1.2.1` (既定値) または
   * `Accept: multipart/related; type="application/octet-stream"; transfer-syntax=*` または
   * `Accept: multipart/related; type="application/octet-stream";`

このコード例では、赤い三角形からのみフレームを返す必要があります。 応答のステータスコードが OK で、フレームが返されることを検証します。

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances/{instance_uid}/frames/1'
headers = {'Accept':'multipart/related; type="application/octet-stream"; transfer-syntax=*', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

## <a name="query-dicom-qido"></a>DICOM のクエリ (QIDO)

次の例では、一意の識別子を使用して項目を検索します。 PatientName などの他の属性を検索することもできます。

サポートされている DICOM 属性については、「 [Dicom 準拠ステートメント](dicom-services-conformance-statement.md#supported-search-parameters) 」を参照してください。

### <a name="search-for-studies"></a>検査を検索する

この要求では、DICOM 属性によって1つ以上の調査が検索されます。

_住所_
* パス:..患者?StudyInstanceUID = {study}
* メソッド: GET
* Headers:
   * Accept: application/dicom + json
   * 承認: ベアラー $token "

応答に1つの調査が含まれていること、および応答コードが OK であることを検証します。

```python
url = f'{base_url}/studies'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'StudyInstanceUID':study_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-series"></a>シリーズを検索する

この要求では、DICOM 属性によって1つ以上の系列が検索されます。

_住所_
* パス:..系列?系列の Instanceuid = {series}
* メソッド: GET
* Headers:
   * Accept: application/dicom + json
   * 承認: ベアラー $token "

応答に1つの系列が含まれていること、および応答コードが OK であることを確認します。

```python
url = f'{base_url}/series'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'SeriesInstanceUID':series_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-series-within-a-study"></a>研究内のシリーズを検索する

この要求では、DICOM 属性によって1つの調査で1つ以上の系列が検索されます。

_住所_
* パス:../studies/{study}/series?系列の Instanceuid = {series}
* メソッド: GET
* Headers:
   * Accept: application/dicom + json
   * 承認: ベアラー $token "

応答に1つの系列が含まれていること、および応答コードが OK であることを確認します。

```python
url = f'{base_url}/studies/{study_uid}/series'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'SeriesInstanceUID':series_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-instances"></a>インスタンスを検索する

この要求は、DICOM 属性によって1つ以上のインスタンスを検索します。

_住所_
* パス:..選ぶ?SOPInstanceUID = {instance}
* メソッド: GET
* Headers:
   * Accept: application/dicom + json
   * 承認: ベアラー $token "

応答に1つのインスタンスが含まれていること、および応答コードが OK であることを検証します。

```python
url = f'{base_url}/instances'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'SOPInstanceUID':instance_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-instances-within-a-study"></a>スタディ内のインスタンスの検索

この要求は、DICOM 属性によって1つの調査内で1つ以上のインスタンスを検索します。

_住所_
* パス:../studies/{study}/instances?SOPInstanceUID = {instance}
* メソッド: GET
* Headers:
   * Accept: application/dicom + json
   * 承認: ベアラー $token "

応答に1つのインスタンスが含まれていること、および応答コードが OK であることを検証します。

```python
url = f'{base_url}/studies/{study_uid}/instances'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'SOPInstanceUID':instance_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-instances-within-a-study-and-series"></a>研究およびシリーズ内のインスタンスの検索

この要求は、1つの調査内で1つ以上のインスタンスを検索し、DICOM 属性によって1つの系列を検索します。

_住所_
* パス:../studies/{study}/series/{series}/instances?SOPInstanceUID = {instance}
* メソッド: GET
* Headers:
   * Accept: application/dicom + json
   * 承認: ベアラー $token "

応答に1つのインスタンスが含まれていること、および応答コードが OK であることを検証します。

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances'
headers = {'Accept':'application/dicom+json'}
params = {'SOPInstanceUID':instance_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

## <a name="delete-dicom"></a>DICOM の削除

> [!NOTE]
> 削除は、DICOM 標準の一部ではありませんが、便宜上追加されています。

204応答コードは、削除が成功したときに返されます。 項目が存在しない場合、または既に削除されている場合は、404応答コードが返されます。

### <a name="delete-a-specific-instance-within-a-study-and-series"></a>スタディとシリーズ内の特定のインスタンスを削除する

この要求は、1つのスタディと1つのシリーズ内の1つのインスタンスを削除します。

_住所_
* パス:../studies/{study}/series/{series}/instances/{instance}
* 方法: DELETE
* Headers:
   * 承認: ベアラー $token

この要求は、サーバーから赤い三角形のインスタンスを削除します。 成功した場合は、応答の状態コードにコンテンツが含まれていません。

```python
headers = {"Authorization":bearer_token}
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances/{instance_uid}'
response = client.delete(url, headers=headers) 
```

### <a name="delete-a-specific-series-within-a-study"></a>スタディ内の特定の系列を削除する

この要求では、単一のスタディ内の1つの系列 (およびすべての子インスタンス) が削除されます。

_住所_
* パス:../studies/{study}/series/{series}
* 方法: DELETE
* Headers:
   * 承認: ベアラー $token

このコード例では、サーバーからの緑色のインスタンス (系列に残されている唯一の要素) を削除します。 成功した場合、応答の状態コードはコンテンツになりません。

```python
headers = {"Authorization":bearer_token}
url = f'{base_url}/studies/{study_uid}/series/{series_uid}'
response = client.delete(url, headers=headers) 
```

### <a name="delete-a-specific-study"></a>特定のスタディを削除する

この要求によって、1つの調査 (およびすべての子シリーズとインスタンス) が削除されます。

_住所_
* パス:../studies/{study}
* 方法: DELETE
* Headers:
   * 承認: ベアラー $token

```python
headers = {"Authorization":bearer_token}
url = f'{base_url}/studies/{study_uid}'
response = client.delete(url, headers=headers) 
```

### <a name="next-steps"></a>次の手順

DICOM サービスの詳細については、以下を参照してください 

>[!div class="nextstepaction"]
>[DICOM サービスの概要](dicom-services-overview.md)
