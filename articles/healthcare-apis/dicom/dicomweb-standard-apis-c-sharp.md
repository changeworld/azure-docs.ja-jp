---
title: C# での DICOMweb Standard API の &trade; 使用 - Azure Healthcare API
description: このチュートリアルでは、C# で DICOMweb Standard API を使用する方法について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 08/03/2021
ms.author: aersoy
ms.openlocfilehash: 2594ff27dd7e4bae5c5463c32a5d4ee3d481cfe8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787648"
---
# <a name="using-dicomwebtrade-standard-apis-with-c"></a>C での DICOMweb &trade; Standard API の使用#

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

このチュートリアルでは、C# を使用して DICOM サービスの操作を示します。

このチュートリアルでは、次のサンプル [.dcm DICOM ファイル を使用します](https://github.com/microsoft/dicom-server/tree/main/docs/dcms)。

* blue-circle.dcm
* dicom-metadata.csv
* green-square.dcm
* red-triangle.dcm

サンプル DICOM ファイルのファイル名、studyUID、seriesUID、instanceUID は次のとおりです。

| ファイル | StudyUID | SeriesUID | InstanceUID |
| --- | --- | --- | ---|
|green-square.dcm|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652|1.2.826.0.1.3680043.8.498.12714725698140337137334606354172323212|
|red-triangle.dcm|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652|1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395|
|blue-circle.dcm|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.77033797676425927098669402985243398207|1.2.826.0.1.3680043.8.498.13273713909719068980354078852867170114|

> [!NOTE]
> これらの各ファイルは 1 つのインスタンスを表し、同じ調査の一部です。 また、緑の四角形と赤い三角形は同じ系列の一部ですが、青い円は別の系列にあります。

## <a name="prerequisites"></a>前提条件

DICOMweb Standard &trade; API を使用するには、DICOM サービスのインスタンスがデプロイされている必要があります。 DICOM サービスのインスタンスをまだデプロイしていない場合は、「 を使用して DICOM サービスをデプロイする[」をAzure portal。](deploy-dicom-services-in-azure.md)

DICOM サービスのインスタンスをデプロイした後、App Service の URL を取得します。

1. [Azure Portal ](https://ms.portal.azure.com/)にサインインします。
1. [最近 **使ったリソース]** を検索し、DICOM サービス インスタンスを選択します。
1. DICOM **サービスのサービス URL** をコピーします。 要求を行う際は、URL の一部としてバージョンを指定してください。 詳細については、DICOM サービスの [API バージョン管理に関するドキュメント](api-versioning-dicom-service.md)を参照してください。

アプリケーションで、次のパッケージをインストールNuGetします。

*  [DICOM クライアント](https://microsofthealthoss.visualstudio.com/FhirServer/_packaging?_a=package&feed=Public&package=Microsoft.Health.Dicom.Client&protocolType=NuGet)

*  [fo-dicom](https://www.nuget.org/packages/fo-dicom/)

## <a name="create-a-dicomwebclient"></a>DicomWebClient を作成する

DICOM サービスをデプロイした後、DicomWebClient を作成します。 次のコード スニペットを実行して、このチュートリアルの残りの部分で使用する DicomWebClient を作成します。 前に説明したように、NuGetパッケージの両方がインストールされていることを確認します。 トークンをまだ取得していない場合は、「 を使用して DICOM サービスのアクセス トークンを取得[する」をAzure CLI。](dicom-get-access-token-azure-cli.md)

```c#
string webServerUrl ="{Your DicomWeb Server URL}"
var httpClient = new HttpClient();
httpClient.BaseAddress = new Uri(webServerUrl);
IDicomWebClient client = new DicomWebClient(httpClient);
client.HttpClient.DefaultRequestHeaders.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", “{Your token value}”); 
```
DicomWebClient を使用して、Store、Retrieve、Search、Delete の各操作を実行できます。

## <a name="store-dicom-instances-stow"></a>DICOM インスタンスの格納 (STOW)

作成した DicomWebClient を使用して、DICOM ファイルを格納できます。

### <a name="store-single-instance"></a>単一インスタンスを格納する

1 つのインスタンスを格納すると、1 つの DICOM ファイルをアップロードする方法が示されます。

_詳細：_

* POST /studies

```c#
DicomFile dicomFile = await DicomFile.OpenAsync(@"{Path To blue-circle.dcm}");
DicomWebResponse response = await client.StoreAsync(new[] { dicomFile });
```

### <a name="store-instances-for-a-specific-study"></a>特定の調査のインスタンスを格納する

特定の調査のストア インスタンスは、指定された調査に DICOM ファイルをアップロードする方法を示しています。

_詳細：_

* POST /study/{study}

```c#
DicomFile dicomFile = await DicomFile.OpenAsync(@"{Path To red-triangle.dcm}");
DicomWebResponse response = await client.StoreAsync(new[] { dicomFile }, "1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420");
```

チュートリアルの次の部分に進む前に、前の方法のいずれかを使用して `green-square.dcm` ファイルをアップロードします。

## <a name="retrieving-dicom-instances-wado"></a>DICOM インスタンスの取得 (WADO)

次のコード スニペットは、前に作成した DicomWebClient を使用して各取得クエリを実行する方法を示しています。

次の変数は、残りの例全体で使用されます。

```c#
string studyInstanceUid = "1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"; //StudyInstanceUID for all 3 examples
string seriesInstanceUid = "1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"; //SeriesInstanceUID for green-square and red-triangle
string sopInstanceUid = "1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"; //SOPInstanceUID for red-triangle
```

### <a name="retrieve-all-instances-within-a-study"></a>調査内のすべてのインスタンスを取得する

調査内のすべてのインスタンスを取得すると、1 つの調査内のすべてのインスタンスが取得されます。

_詳細：_

* GET /study/{study}

```c#
DicomWebResponse response = await client.RetrieveStudyAsync(studyInstanceUid);
```

前にアップロードした 3 つの dcm ファイルはすべて同じ調査の一部なので、応答では 3 つのインスタンスすべてが返されます。 応答の状態コードが [OK] で、3 つのインスタンスすべてが返されるのを確認します。

### <a name="use-the-retrieved-instances"></a>取得したインスタンスを使用する

次のコード スニペットは、取得されたインスタンスにアクセスする方法を示しています。 また、インスタンスの一部のフィールドにアクセスする方法と、それを dcm ファイルとして保存する方法も示します。

```c#
DicomWebAsyncEnumerableResponse<DicomFile> response = await client.RetrieveStudyAsync(studyInstanceUid);
await foreach (DicomFile file in response)
{
    string patientName = file.Dataset.GetString(DicomTag.PatientName);
    string studyId = file.Dataset.GetString(DicomTag.StudyID);
    string seriesNumber = file.Dataset.GetString(DicomTag.SeriesNumber);
    string instanceNumber = file.Dataset.GetString(DicomTag.InstanceNumber);

    file.Save($"<path_to_save>\\{patientName}{studyId}{seriesNumber}{instanceNumber}.dcm");
}
```

### <a name="retrieve-metadata-of-all-instances-in-study"></a>調査中のすべてのインスタンスのメタデータを取得する

この応答は、1 つの調査内のすべてのインスタンスのメタデータを取得します。

_詳細：_

* GET /study/{study}/metadata

```c#
DicomWebResponse response = await client.RetrieveStudyMetadataAsync(studyInstanceUid);
```

前にアップロードした 3 つの dcm ファイルはすべて同じ調査の一部なので、応答は 3 つのインスタンスすべてについてメタデータを返す必要があります。 応答の状態コードが [OK] で、すべてのメタデータが返されるのを確認します。

### <a name="retrieve-all-instances-within-a-series"></a>系列内のすべてのインスタンスを取得する

この応答は、1 つの系列内のすべてのインスタンスを取得します。

_詳細：_

* GET /study/{study}/series/{series}


```c#
DicomWebResponse response = await client.RetrieveSeriesAsync(studyInstanceUid, seriesInstanceUid);
```

この系列には 2 つのインスタンス (緑の四角形と赤い三角形) が含まれるので、応答は両方のインスタンスを返す必要があります。 応答の状態コードが [OK] で、両方のインスタンスが返されるのを確認します。

### <a name="retrieve-metadata-of-all-instances-within-a-series"></a>系列内のすべてのインスタンスのメタデータを取得する

この応答は、1 つの調査内のすべてのインスタンスのメタデータを取得します。

_詳細：_

* GET /study/{study}/series/{series}/metadata

```c#
DicomWebResponse response = await client.RetrieveSeriesMetadataAsync(studyInstanceUid, seriesInstanceUid);
```

この系列には 2 つのインスタンス (緑の四角形と赤い三角形) が含まれるので、応答は両方のインスタンスのメタデータを返す必要があります。 応答の状態コードが OK であり、メタデータの両方のインスタンスが返されるのを確認します。

### <a name="retrieve-a-single-instance-within-a-series-of-a-study"></a>一連の調査内で 1 つのインスタンスを取得する

この要求は、1 つのインスタンスを取得します。

_詳細：_

* GET /study/{study}/series{series}/instances/{instance}

```c#
DicomWebResponse response = await client.RetrieveInstanceAsync(studyInstanceUid, seriesInstanceUid, sopInstanceUid);
```

この応答は、インスタンスの赤三角形のみを返す必要があります。 応答の状態コードが [OK] で、インスタンスが返されるのを確認します。

### <a name="retrieve-metadata-of-a-single-instance-within-a-series-of-a-study"></a>一連の調査内の単一インスタンスのメタデータを取得する

この要求は、1 つの調査と系列内の単一インスタンスのメタデータを取得します。

_詳細：_

* GET /study/{study}/series/{series}/instances/{instance}/metadata

```c#
DicomWebResponse response = await client.RetrieveInstanceMetadataAsync(studyInstanceUid, seriesInstanceUid, sopInstanceUid);
```

この応答では、インスタンスの赤三角形のメタデータのみを返す必要があります。 応答のステータスコードが OK であること、およびメタデータが返されることを検証します。

### <a name="retrieve-one-or-more-frames-from-a-single-instance"></a>1つのインスタンスから1つ以上のフレームを取得する

この要求は、1つのインスタンスから1つ以上のフレームを取得します。

_住所_

* /Studies/{study}/series/{series}/instances/{instance}/frames/{frames} の取得

```c#
DicomWebResponse response = await client.RetrieveFramesAsync(studyInstanceUid, seriesInstanceUid, sopInstanceUid, frames: new[] { 1 });

```

この応答では、赤い三角形からのみフレームが返されます。 応答のステータスコードが OK で、フレームが返されることを検証します。

## <a name="query-dicom-qido"></a>DICOM のクエリ (QIDO)

> [!NOTE]
> サポートされている DICOM 属性については、「 [Dicom 準拠ステートメント](dicom-services-conformance-statement.md#supported-search-parameters) 」を参照してください。

### <a name="search-for-studies"></a>検査を検索する

この要求では、DICOM 属性によって1つ以上の調査が検索されます。

_住所_

* 研究にご利用いただけますか?StudyInstanceUID = {study}

```c#
string query = $"/studies?StudyInstanceUID={studyInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

応答に1つの調査が含まれていること、および応答コードが OK であることを検証します。

### <a name="search-for-series"></a>シリーズを検索する

この要求では、DICOM 属性によって1つ以上の系列が検索されます。

_住所_

* /シリーズを取得しますか?系列の Instanceuid = {series}

```c#
string query = $"/series?SeriesInstanceUID={seriesInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

応答に1つの系列が含まれていること、および応答コードが OK であることを検証します。

### <a name="search-for-series-within-a-study"></a>研究内のシリーズを検索する

この要求では、DICOM 属性によって1つの調査で1つ以上の系列が検索されます。

_住所_

* /Studies/{study}/series を取得しますか?系列の Instanceuid = {series}

```c#
string query = $"/studies/{studyInstanceUid}/series?SeriesInstanceUID={seriesInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

応答に1つの系列が含まれていること、および応答コードが OK であることを検証します。

### <a name="search-for-instances"></a>インスタンスを検索する

この要求は、DICOM 属性によって1つ以上のインスタンスを検索します。

_住所_

* インスタンスを取得しますか?SOPInstanceUID = {instance}

```c#
string query = $"/instances?SOPInstanceUID={sopInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

応答に1つのインスタンスが含まれていること、および応答コードが OK であることを検証します。

### <a name="search-for-instances-within-a-study"></a>スタディ内のインスタンスの検索

この要求は、DICOM 属性によって1つの調査内で1つ以上のインスタンスを検索します。

_住所_

* /Studies/{study}/instances を取得しますか?SOPInstanceUID = {instance}

```c#
string query = $"/studies/{studyInstanceUid}/instances?SOPInstanceUID={sopInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

応答に1つのインスタンスが含まれていること、および応答コードが OK であることを検証します。

### <a name="search-for-instances-within-a-study-and-series"></a>研究およびシリーズ内のインスタンスの検索

この要求は、1つの調査内で1つ以上のインスタンスを検索し、DICOM 属性によって1つの系列を検索します。

_住所_

* /Studies/{study}/series/{series}instances を取得しますか?SOPInstanceUID = {instance}

```c#
string query = $"/studies/{studyInstanceUid}/series/{seriesInstanceUid}/instances?SOPInstanceUID={sopInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

応答に1つのインスタンスが含まれていること、および応答コードが OK であることを検証します。

## <a name="delete-dicom"></a>DICOM の削除

> [!NOTE]
> 削除は、DICOM 標準の一部ではありませんが、便宜上追加されています。

### <a name="delete-a-specific-instance-within-a-study-and-series"></a>スタディとシリーズ内の特定のインスタンスを削除する

この要求は、1つのスタディと1つのシリーズ内の1つのインスタンスを削除します。

_住所_

* /Studies/{study}/series/{series}/instances/{instance} の削除

```c#
string sopInstanceUidRed = "1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395";
DicomWebResponse response = await client.DeleteInstanceAsync(studyInstanceUid, seriesInstanceUid, sopInstanceUidRed);
```

これにより、サーバーから赤い三角形のインスタンスが削除されます。 成功した場合は、応答の状態コードにコンテンツが含まれていません。

### <a name="delete-a-specific-series-within-a-study"></a>スタディ内の特定の系列を削除する

この要求では、単一のスタディ内の1つの系列 (およびすべての子インスタンス) が削除されます。

_住所_

* /Studies/{study}/series/{series} の削除

```c#
DicomWebResponse response = await client.DeleteSeriesAsync(studyInstanceUid, seriesInstanceUid);
```

この応答は、サーバーからの緑色のインスタンス (系列に残されている唯一の要素) を削除します。 成功した場合、応答状態コードにはコンテンツが含まれません。

### <a name="delete-a-specific-study"></a>特定のスタディを削除する

この要求によって、1つの調査 (およびすべての子シリーズとインスタンス) が削除されます。

_住所_

* /Studies/{study} の削除

```c#
DicomWebResponse response = await client.DeleteStudyAsync(studyInstanceUid);
```

この応答は、サーバーからの青い円インスタンス (系列に残されている唯一の要素) を削除します。 成功した場合は、応答の状態コードにコンテンツが含まれていません。

### <a name="next-steps"></a>次の手順

DICOM サービスの詳細については、以下を参照してください

>[!div class="nextstepaction"]
>[DICOM サービスの概要](dicom-services-overview.md)
