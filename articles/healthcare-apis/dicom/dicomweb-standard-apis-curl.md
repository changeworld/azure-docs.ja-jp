---
title: cURL での DICOMweb &trade; Standard API の使用 - Azure Healthcare API
description: このチュートリアルでは、cURL で DICOMweb Standard API を使用する方法について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 07/16/2021
ms.author: aersoy
ms.openlocfilehash: afa4b294b71a3cde198001d204d4670cf8ca1ec1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787788"
---
# <a name="using-dicomwebtrade-standard-apis-with-curl"></a>cURL での DICOMWeb &trade; Standard API の使用

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

このチュートリアルでは、cURL を使用して DICOM サービスの操作を示します。

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

>[!NOTE]
>これらの各ファイルは 1 つのインスタンスを表し、同じ調査の一部です。 また、緑の四角形と赤い三角形は同じ系列の一部ですが、青い円は別の系列にあります。

## <a name="prerequisites"></a>前提条件

DICOMWeb Standard &trade; API を使用するには、DICOM サービスのインスタンスがデプロイされている必要があります。 DICOM サービスのインスタンスをまだデプロイしていない場合は、「 を使用して DICOM サービスをデプロイする[」をAzure portal。](deploy-dicom-services-in-azure.md)

DICOM サービスのインスタンスをデプロイしたら、App Service の URL を取得します。

1. [Azure Portal ](https://ms.portal.azure.com/)にサインインします。
2. [最近 **使ったリソース]** を検索し、DICOM サービス インスタンスを選択します。
3. DICOM **サービスのサービス URL** をコピーします。 
4. トークンをまだ取得していない場合は、「 を使用して DICOM サービスのアクセス トークンを取得[する」をAzure CLI。](dicom-get-access-token-azure-cli.md) 

このコードでは、パブリック プレビュー Azure サービスにアクセスします。 プライベート正常性情報 (PHI) をアップロードしないのが重要です。


## <a name="working-with-the-dicom-service"></a>DICOM サービスの操作
 
DICOMweb Standard では、HTTP 要求を DICOM 固有の受け入れヘッダーと組 &trade; `multipart/related` み合わせて使用します。 他の REST ベースの API を使い慣れた開発者は、DICOMweb Standard を使い慣れた &trade; 作業に慣れ親しんだ開発者がよくいます。 ただし、それを稼働状態にした後は、簡単に使用できます。 使い始めるには少し慣れる必要があります。

cURL コマンドには、それぞれ少なくとも 1 つ(場合によっては 2 つ)、置換する必要がある変数が含まれています。 コマンドの実行を簡略化するには、次の変数を検索し、特定の値に置き換えます。

* {サービス URL}これは、次のコードでプロビジョニングした DICOM サービスにアクセスAzure portal URL です (例: ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` )。 要求を行う際は、URL の一部としてバージョンを指定してください。 詳細については、DICOM サービスの [API バージョン管理に関するドキュメント](api-versioning-dicom-service.md)を参照してください。
* {path-to-dicoms} - red-triangle.dcm ファイルを含むディレクトリへのパス ( など) `C:/dicom-server/docs/dcms`
    * スラッシュを区切り記号として使用し、末尾のスラッシュを使用せずに _ディレクトリを終了_ します。


## <a name="uploading-dicom-instances-stow"></a>DICOM インスタンスのアップロード (STOW)

### <a name="store-instances-using-multipartrelated"></a>Store-instances-using-multipart/related

この要求は、マルチパート/関連を使用して DICOM ファイルをアップロードする方法を示す予定です。 

>[!NOTE]
>DICOM サービスは、DICOM 標準よりも充実しています。 ただし、次の例は、標準に厳しく準拠する POST 要求を示しています。

_詳細：_

* パス：。。/studies
* メソッド: POST
* Headers:
    * 同意する: application/dicom+json
    * Content-Type: multipart/related;type="application/dicom"
    * 承認: ベアラー {トークン値}
* 本文は次のようになります。
    * Content-Type: 境界値で区切られた、アップロードされた各ファイルの application/dicom

一部のプログラミング言語とツールの動作は異なります。 たとえば、独自の境界を定義する必要がある場合があります。 その場合は、少し変更された Content-Type ヘッダーを使用する必要があります。 次が正常に使用されました。
* Content-Type: multipart/related;type="application/dicom";boundary=ABCD1234
* Content-Type: multipart/related;boundary=ABCD1234
* Content-Type: multipart/related

```
curl --location --request POST "{Service URL}/v{version}/studies"
--header "Accept: application/dicom+json"
--header "Content-Type: multipart/related; type=\"application/dicom\""
--header "Authorization: Bearer {token value}"
--form "file1=@{path-to-dicoms}/red-triangle.dcm;type=application/dicom"
--trace-ascii "trace.txt"
```

### <a name="store-instances-for-a-specific-study"></a>特定の調査のインスタンスを格納する

この要求では、指定された調査に関連するマルチパート/を使用して DICOM ファイルをアップロードする方法を示します。

_詳細：_
* パス：。。/study/{study}
* メソッド: POST
* Headers:
    * 同意する: application/dicom+json
    * Content-Type: multipart/related;type="application/dicom"
    * 承認: ベアラー {トークン値}
* 本文は次のようになります。
    * Content-Type: 境界値で区切られた、アップロードされた各ファイルの application/dicom

一部のプログラミング言語とツールの動作は異なります。 たとえば、独自の境界を定義する必要がある場合があります。 その場合は、少し変更された Content-Type ヘッダーを使用する必要があります。 次が正常に使用されました。

 * Content-Type: multipart/related;type="application/dicom";boundary=ABCD1234
 * Content-Type: multipart/related;boundary=ABCD1234
 * Content-Type: multipart/related

```
curl --request POST "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"
--header "Accept: application/dicom+json"
--header "Content-Type: multipart/related; type=\"application/dicom\"
--header "Authorization: Bearer {token value}"
--form "file1=@{path-to-dicoms}/blue-circle.dcm;type=application/dicom"
```

### <a name="store-single-instance"></a>ストアシングルインスタンス

> [!NOTE]
> これは、マルチパート/関連の POST を構成する必要なく、単一の DICOM ファイルをアップロードできる非標準 API です。 cURL はマルチパート/関連を処理しますが、この API を使用すると、Postman のようなツールで DICOM サービスにファイルをアップロードできます。

1 つの DICOM ファイルをアップロードするには、次の方法が必要です。

_詳細：_
* パス：。。/studies
* メソッド: POST
* Headers:
   * 同意する: application/dicom+json
   * Content-type: application/dicom
   * 承認: ベアラー {token value}
* 本文は次のようになります。
    * バイナリバイトとして1つの DICOM ファイルを格納します。

```
curl --location --request POST "{Service URL}/v{version}/studies"
--header "Accept: application/dicom+json"
--header "Content-Type: application/dicom"
--header "Authorization: Bearer {token value}"
--data-binary "@{path-to-dicoms}/green-square.dcm"
```

## <a name="retrieving-dicom-wado"></a>DICOM の取得 (WADO)

### <a name="retrieve-all-instances-within-a-study"></a>スタディ内のすべてのインスタンスを取得する

この要求は、1つのスタディ内のすべてのインスタンスを取得し、マルチパート/関連バイトのコレクションとして返します。

_住所_
* パス:../studies/{study}
* メソッド: GET
* Headers:
   * 承諾: マルチパート/関連;「= "application/dicom";」と入力します。転送-構文 = *
   * 承認: ベアラー {token value}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"
--header "Accept: multipart/related; type=\"application/dicom\"; transfer-syntax=*"
--header "Authorization: Bearer {token value}"
--output "suppressWarnings.txt"
```

この cURL コマンドでは、ダウンロードされたバイトが出力ファイル (suppressWarnings.txt) に表示されますが、これらは直接の DICOM ファイルではなく、マルチパート/関連ダウンロードのテキスト表現にすぎません。

### <a name="retrieve-metadata-of-all-instances-in-study"></a>スタディですべてのインスタンスのメタデータを取得する

この要求は、単一のスタディ内のすべてのインスタンスのメタデータを取得します。

_住所_
* パス:../studies/{study}/metadata
* メソッド: GET
* Headers:
   * Accept: application/dicom + json
   * 承認: ベアラー {token value}

この cURL コマンドでは、ダウンロードされたバイトが出力ファイル (suppressWarnings.txt) に表示されますが、これらは直接の DICOM ファイルではなく、マルチパート/関連ダウンロードのテキスト表現にすぎません。

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/metadata"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="retrieve-all-instances-within-a-series"></a>系列内のすべてのインスタンスを取得する

この要求は、1つの系列内のすべてのインスタンスを取得し、マルチパート/関連バイトのコレクションとして返します。

_住所_
* パス:../studies/{study}/series/{series}
* メソッド: GET
* Headers:
   * 承諾: マルチパート/関連;「= "application/dicom";」と入力します。転送-構文 = *
   * 承認: ベアラー {token value}

この cURL コマンドでは、ダウンロードしたバイトが出力ファイル (suppressWarnings.txt) に表示されますが、これは DICOM ファイルではなく、マルチパート/関連ダウンロードのテキスト表現にすぎません。

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"
--header "Accept: multipart/related; type=\"application/dicom\"; transfer-syntax=*"
--header "Authorization: Bearer {token value}"
--output "suppressWarnings.txt"
```

### <a name="retrieve-metadata-of-all-instances-within-a-series"></a>系列内のすべてのインスタンスのメタデータを取得する

この要求は、単一のスタディ内のすべてのインスタンスのメタデータを取得します。

_住所_
* パス:../studies/{study}/series/{series}/metadata
* メソッド: GET
* Headers:
   * Accept: application/dicom + json
   * 承認: ベアラー {token value}

```
curl --request GET "{Service URL}/v{version}/studies1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/metadata"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```
 
### <a name="retrieve-a-single-instance-within-a-series-of-a-study"></a>一連のスタディ内の1つのインスタンスを取得する

この要求は1つのインスタンスを取得し、それを DICOM 形式のバイトストリームとして返します。

_住所_
* パス:../studies/{study}/series{series}/instances/{instance}
* メソッド: GET
* Headers:
   * Accept: application/dicom;転送-構文 = *
   * 承認: ベアラー {token value}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances/1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Accept: application/dicom; transfer-syntax=*"
--header "Authorization: Bearer {token value}"
--output "suppressWarnings.txt"
```

### <a name="retrieve-metadata-of-a-single-instance-within-a-series-of-a-study"></a>一連のスタディ内の1つのインスタンスのメタデータを取得する

この要求は、1つの研究およびシリーズ内の1つのインスタンスのメタデータを取得します。

_住所_
* パス:../studies/{study}/series/{series}/instances/{instance}/metadata
* メソッド: GET
* Headers:
  * Accept: application/dicom + json
  * 承認: ベアラー {token value}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances/1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395/metadata"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="retrieve-one-or-more-frames-from-a-single-instance"></a>1つのインスタンスから1つ以上のフレームを取得する

この要求では、1つのインスタンスから1つ以上のフレームを取得し、マルチパート/関連バイトのコレクションとして返します。 複数のフレームを取得するには、フレーム番号のコンマ区切りリストを渡します。  イメージを含むすべての DICOM インスタンスには、少なくとも1つのフレームがあります。これは、多くの場合、インスタンス自体に関連付けられているイメージにすぎません。

_住所_
* パス:../studies/{study}/series{series}/instances/{instance}/frames/1、2、3
* メソッド: GET
* Headers:
   * 承諾: マルチパート/関連;type = "application/オクテット-stream";transfer-構文 = 1.2.840.10008.1.2.1 (既定値) または
   * 承諾: マルチパート/関連;type = "application/オクテット-stream";転送-構文 = * または
   * 承諾: マルチパート/関連;type = "application/オクテット-stream";
   * 承認: ベアラー {token value}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances/1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395/frames/1"
--header "Accept: multipart/related; type=\"application/octet-stream\"; transfer-syntax=1.2.840.10008.1.2.1"
--header "Authorization: Bearer {token value}"
--output "suppressWarnings.txt"
```

## <a name="query-dicom-qido"></a>DICOM のクエリ (QIDO)

次の例では、一意の識別子を使用して項目を検索します。 などの他の属性を検索することもでき `PatientName` ます。

### <a name="search-for-studies"></a>検査を検索する

この要求により、DICOM 属性によって1つ以上の調査が検索されます。

サポートされている DICOM 属性の詳細については、「 [Dicom 準拠ステートメント](dicom-services-conformance-statement.md)」を参照してください。

_住所_
* パス:..患者?StudyInstanceUID = {study}
* メソッド: GET
* Headers:
   * Accept: application/dicom + json
   * 承認: ベアラー {token value}

```
curl --request GET "{Service URL}/v{version}/studies?StudyInstanceUID=1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-series"></a>シリーズを検索する

この要求により、DICOM 属性で1つ以上の系列を検索できるようになります。

サポートされている DICOM 属性の詳細については、「 [Dicom 準拠ステートメント](dicom-services-conformance-statement.md)」を参照してください。

_住所_
* パス:..系列?系列の Instanceuid = {series}
* メソッド: GET
* Headers:
   * Accept: application/dicom + json
   * 承認: ベアラー {token value}

```
curl --request GET "{Service URL}/v{version}/series?SeriesInstanceUID=1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-series-within-a-study"></a>研究内のシリーズを検索する

この要求により、DICOM 属性によって1つの調査で1つ以上の系列を検索できるようになります。

サポートされている DICOM 属性の詳細については、「 [Dicom 準拠ステートメント](dicom-services-conformance-statement.md)」を参照してください。

_住所_
* パス:../studies/{study}/series?系列の Instanceuid = {series}
* メソッド: GET
* Headers:
   * Accept: application/dicom + json
   * 承認: ベアラー {token value}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series?SeriesInstanceUID=1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-instances"></a>インスタンスを検索する

この要求により、DICOM 属性によって1つ以上のインスタンスが検索されます。

サポートされている DICOM 属性の詳細については、「 [Dicom 準拠ステートメント](dicom-services-conformance-statement.md)」を参照してください。

_住所_
* パス:..選ぶ?SOPInstanceUID = {instance}
* メソッド: GET
* Headers:
   * Accept: application/dicom + json
   * 承認: ベアラー {token value}

```
curl --request GET "{Service URL}/v{version}/instances?SOPInstanceUID=1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-instances-within-a-study"></a>スタディ内のインスタンスの検索

この要求により、DICOM 属性によって1つの調査内で1つ以上のインスタンスを検索できます。

サポートされている DICOM 属性の詳細については、「 [Dicom 準拠ステートメント](dicom-services-conformance-statement.md)」を参照してください。

_住所_
* パス:../studies/{study}/instances?SOPInstanceUID = {instance}
* メソッド: GET
* Headers:
   * Accept: application/dicom + json
   * 承認: ベアラー {token value} 

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/instances?SOPInstanceUID=1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-instances-within-a-study-and-series"></a>研究およびシリーズ内のインスタンスの検索

この要求により、1つの調査内の1つ以上のインスタンスを DICOM 属性で検索できるようになります。

サポートされている DICOM 属性の詳細については、「 [Dicom 準拠ステートメント](dicom-services-conformance-statement.md)」を参照してください。

_住所_
* パス:../studies/{study}/series/{series}/instances?SOPInstanceUID = {instance}
* メソッド: GET
* Headers:
   * Accept: application/dicom + json
   * 承認: ベアラー {token value}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances?SOPInstanceUID=1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```


## <a name="delete-dicom"></a>DICOM の削除 

### <a name="delete-a-specific-instance-within-a-study-and-series"></a>スタディとシリーズ内の特定のインスタンスを削除する

この要求は、1つのスタディと1つのシリーズ内の1つのインスタンスを削除します。

削除は、DICOM 標準の一部ではありませんが、便宜上追加されています。

_住所_
* パス:../studies/{study}/series/{series}/instances/{instance}
* 方法: DELETE
* Headers:
   * 承認: ベアラー {token value}  

```
curl --request DELETE "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances/1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Authorization: Bearer {token value}"
```

### <a name="delete-a-specific-series-within-a-study"></a>スタディ内の特定の系列を削除する

この要求では、単一のスタディ内の1つの系列 (およびすべての子インスタンス) が削除されます。

削除は、DICOM 標準の一部ではありませんが、便宜上追加されています。

_住所_
* パス:../studies/{study}/series/{series}
* 方法: DELETE
* Headers:
   * 承認: ベアラー {token value}

```
curl --request DELETE "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"
--header "Authorization: Bearer {token value}"
```

### <a name="delete-a-specific-study"></a>特定のスタディを削除する

この要求によって、1つの調査 (およびすべての子シリーズとインスタンス) が削除されます。

削除は、DICOM 標準の一部ではありませんが、便宜上追加されています。

_住所_
* パス:../studies/{study}
* 方法: DELETE
* Headers:
   * 承認: ベアラー {token value}

```
curl--request DELETE "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498
--header "Authorization: Bearer {token value}"
```

### <a name="next-steps"></a>次の手順

DICOM サービスの詳細については、以下を参照してください

>[!div class="nextstepaction"]
>[DICOM サービスの概要](dicom-services-overview.md)
