---
title: Azure Healthcare API の DICOM 適合性宣言書
description: このドキュメントでは、Azure Healthcare API の DICOM 適合性宣言書の詳細について説明します。
services: healthcare-apis
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 10/05/2021
ms.author: aersoy
ms.openlocfilehash: 32a368243ab2114799dd9ce9d2ffcdf3306115c9
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130044669"
---
# <a name="dicom-conformance-statement"></a>DICOM 適合性宣言書

> [!IMPORTANT]
> Azure Healthcare API は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

**DICOM サービス向けの Azure API** では、DICOMweb&trade; 標準のサブセットがサポートされています。 次のものがサポートされます。

* [格納 (STOW-RS)](#store-stow-rs)
* [取得 (WADO-RS)](#retrieve-wado-rs)
* [検索 (QIDO-RS)](#search-qido-rs)

また、次の非標準 API がサポートされています。

- [削除](#delete)

このサービスでは、REST API のバージョン管理も使用します。 要求を行うときにバージョンを指定する方法については、「[DICOM サービスの API バージョン管理」ドキュメント](api-versioning-dicom-service.md)を参照してください。

## <a name="store-stow-rs"></a>格納 (STOW-RS)

このトランザクションでは、POST メソッドを使用して、要求ペイロードに含まれる検査、シリーズ、およびインスタンスの表現を格納します。

| 方法 | Path               | 説明 |
| :----- | :----------------- | :---------- |
| POST   | ../studies         | インスタンスを格納します。 |
| POST   | ../studies/{study} | 特定の検査のインスタンスを格納します。 |

パラメーター `study` は、DICOM 属性 StudyInstanceUID に対応します。 これを指定した場合、所与の検査に属していないインスタンスは拒否され、`43265` の警告コードが返されます。

応答のために、以下の `Accept` ヘッダーがサポートされています。

* `application/dicom+json`

以下の `Content-Type` ヘッダーがサポートされています。

* `multipart/related; type="application/dicom"`
* `application/dicom`

> [!NOTE]
> サーバーは、既存のデータと競合する属性の強制または置換は行い **ません**。 すべてのデータは指定されたとおりに格納されます。

以下の DICOM 要素は、格納しようとしているすべての DICOM ファイル内にある必要があります。

* StudyInstanceUID
* SeriesInstanceUID
* SOPInstanceUID
* SOPClassUID
* PatientID

> [!NOTE]
> すべての識別子は、1 から 64 文字の長さにする必要があります。また、英数字または特殊文字 ("."、"-") のみを使用できます。

格納される各ファイルには、StudyInstanceUID、SeriesInstanceUID、および SopInstanceUID の一意の組み合わせが必要です。 同じ識別子のファイルが既に存在する場合は、警告コード `45070` が返されます。

DICOM ファイル サイズの制限: 既定では、DICOM ファイルのサイズは 2 GB に制限されています。

### <a name="store-response-status-codes"></a>格納の応答状態コード

| コード                         | 説明 |
| :--------------------------- | :---------- |
| 200 (OK)                     | 要求内のすべての SOP インスタンスが格納されました。 |
| 202 (承認済み)               | 要求のインスタンスの一部は格納されましたが、その他は失敗しました。 |
| 204 (コンテンツなし)             | 格納トランザクション要求で、コンテンツが指定されませんでした。 |
| 400 (Bad Request)            | 要求の形式が正しくありませんでした。 たとえば、指定された検査インスタンス識別子が、期待される UID 形式に準拠していませんでした。 |
| 401 (許可されていません)           | クライアントの認証は行われません。 |
| 403 (許可されていません)              | ユーザーが承認されていません。 |
| 406 (Not Acceptable)         | 指定された `Accept` ヘッダーはサポートされていません。 |
| 409 (Conflict)               | 格納トランザクション要求内のどのインスタンスも格納されませんでした。 |
| 415 (Unsupported Media Type) | 指定された `Content-Type` はサポートされていません。 |
| 503 (Service Unavailable)    | サービスが利用できないか、ビジー状態です。 後で再度お試しください。 |

### <a name="store-response-payload"></a>格納の応答ペイロード

応答ペイロードによって、DICOM データセットに次の要素が設定されます。

| タグ          | 名前                  | 説明 |
| :----------- | :-------------------- | :---------- |
| (0008, 1190) | RetrieveURL           | 検査の取得 URL (格納要求で StudyInstanceUID が指定され、少なくとも 1 つのインスタンスが正常に格納された場合)。 |
| (0008, 1198) | FailedSOPSequence     | 格納できなかったインスタンスのシーケンス。 |
| (0008, 1199) | ReferencedSOPSequence | 格納されたインスタンスのシーケンス。 |

`FailedSOPSequence` 内の各データセットには、以下の要素があります (格納しようとする DICOM ファイルを読み取ることができる場合)。

| タグ          | 名前                     | 説明 |
| :----------- | :----------------------- | :---------- |
| (0008, 1150) | ReferencedSOPClassUID    | 格納できなかったインスタンスの SOP クラスの一意の識別子。 |
| (0008, 1150) | ReferencedSOPInstanceUID | 格納できなかったインスタンスの SOP インスタンスの一意の識別子。 |
| (0008, 1197) | FailureReason            | このインスタンスの格納の失敗に関する理由コード。 |

`ReferencedSOPSequence` 内の各データセットには、以下の要素があります。

| タグ          | 名前                     | 説明 |
| :----------- | :----------------------- | :---------- |
| (0008, 1150) | ReferencedSOPClassUID    | 格納できなかったインスタンスの SOP クラスの一意の識別子。 |
| (0008, 1150) | ReferencedSOPInstanceUID | 格納できなかったインスタンスの SOP インスタンスの一意の識別子。 |
| (0008, 1190) | RetrieveURL              | DICOM サーバー上のこのインスタンスの取得 URL。 |

`Accept` ヘッダー `application/dicom+json` での応答の例を以下に示します。

```json
{
  "00081190":
  {
    "vr":"UR",
    "Value":["http://localhost/studies/d09e8215-e1e1-4c7a-8496-b4f6641ed232"]
  },
  "00081198":
  {
    "vr":"SQ",
    "Value":
    [{
      "00081150":
      {
        "vr":"UI","Value":["cd70f89a-05bc-4dab-b6b8-1f3d2fcafeec"]
      },
      "00081155":
      {
        "vr":"UI",
        "Value":["22c35d16-11ce-43fa-8f86-90ceed6cf4e7"]
      },
      "00081197":
      {
        "vr":"US",
        "Value":[43265]
      }
    }]
  },
  "00081199":
  {
    "vr":"SQ",
    "Value":
    [{
      "00081150":
      {
        "vr":"UI",
        "Value":["d246deb5-18c8-4336-a591-aeb6f8596664"]
      },
      "00081155":
      {
        "vr":"UI",
        "Value":["4a858cbb-a71f-4c01-b9b5-85f88b031365"]
      },
      "00081190":
      {
        "vr":"UR",
        "Value":["http://localhost/studies/d09e8215-e1e1-4c7a-8496-b4f6641ed232/series/8c4915f5-cc54-4e50-aa1f-9b06f6e58485/instances/4a858cbb-a71f-4c01-b9b5-85f88b031365"]
      }
    }]
  }
}
```

### <a name="store-failure-reason-codes"></a>格納エラーの理由コード

| コード  | 説明 |
| :---- | :---------- |
| 272   | 格納トランザクションで、操作の処理中に一般的なエラーが発生したため、インスタンスが格納されませんでした。 |
| 43264 | DICOM インスタンスが検証に失敗しました。 |
| 43265 | 指定されたインスタンスの StudyInstanceUID が、格納要求内で指定された StudyInstanceUID と一致しませんでした。 |
| 45070 | StudyInstanceUID、SeriesInstanceUID、および SopInstanceUID が同じである DICOM インスタンスが、既に格納されています。 コンテンツを更新する場合は、まず、このインスタンスを削除します。 |
| 45071 | DICOM インスタンスが別のプロセスによって作成されているか、前回の作成の試行が失敗し、クリーンアップ プロセスがクリーンアップを実行できる機会がまだありませんでした。 まずインスタンスを削除してから、もう一度作成してみてください。 |

## <a name="retrieve-wado-rs"></a>取得 (WADO-RS)

この取得トランザクションでは、格納されている検査、シリーズ、インスタンス、およびフレームを参照で取得するためのサポートが提供されます。

| 方法 | Path                                                                    | 説明 |
| :----- | :---------------------------------------------------------------------- | :---------- |
| GET    | ../studies/{study}                                                      | 検査内のすべてのインスタンスを取得します。 |
| GET    | ../studies/{study}/metadata                                             | 検査内のすべてのインスタンスについてメタデータを取得します。 |
| GET    | ../studies/{study}/series/{series}                                      | シリーズ内のすべてのインスタンスを取得します。 |
| GET    | ../studies/{study}/series/{series}/metadata                             | シリーズ内のすべてのインスタンスについてメタデータを取得します。 |
| GET    | ../studies/{study}/series/{series}/instances/{instance}                 | 1 つのインスタンスを取得します。 |
| GET    | ../studies/{study}/series/{series}/instances/{instance}/metadata        | 1 つのインスタンスのメタデータを取得します。 |
| GET    | ../studies/{study}/series/{series}/instances/{instance}/frames/{frames} | 1 つのインスタンスから 1 つまたは複数のフレームを取得します。 複数のフレームを指定するには、返される各フレームをコンマで区切ります。 例: /studies/1/series/2/instance/3/frames/4,5,6 |

### <a name="retrieve-instances-within-study-or-series"></a>検査またはシリーズ内のインスタンスを取得する

検査またはシリーズ内のインスタンスを取得するために、以下の `Accept` ヘッダーがサポートされています。


* `multipart/related; type="application/dicom"; transfer-syntax=*`
* `multipart/related; type="application/dicom";` (transfer-syntax が指定されていない場合、1.2.840.10008.1.2.1 が既定値として使用されます)
* `multipart/related; type="application/dicom"; transfer-syntax=1.2.840.10008.1.2.1`
* `multipart/related; type="application/dicom"; transfer-syntax=1.2.840.10008.1.2.4.90`

### <a name="retrieve-an-instance"></a>インスタンスを取得する

特定のインスタンスを取得するために、以下の `Accept` ヘッダーがサポートされています。

* `application/dicom; transfer-syntax=*`
* `multipart/related; type="application/dicom"; transfer-syntax=*`
* `application/dicom;` (transfer-syntax が指定されていない場合、1.2.840.10008.1.2.1 が既定値として使用されます)
* `multipart/related; type="application/dicom"` (transfer-syntax が指定されていない場合、1.2.840.10008.1.2.1 が既定値として使用されます)
* `application/dicom; transfer-syntax=1.2.840.10008.1.2.1`
* `multipart/related; type="application/dicom"; transfer-syntax=1.2.840.10008.1.2.1`
* `application/dicom; transfer-syntax=1.2.840.10008.1.2.4.90`
* `multipart/related; type="application/dicom"; transfer-syntax=1.2.840.10008.1.2.4.90`

### <a name="retrieve-frames"></a>フレームを取得する

フレームを取得するために、以下の `Accept` ヘッダーがサポートされています。

* `multipart/related; type="application/octet-stream"; transfer-syntax=*`
* `multipart/related; type="application/octet-stream";` (transfer-syntax が指定されていない場合、1.2.840.10008.1.2.1 が既定値として使用されます)
* `multipart/related; type="application/octet-stream"; transfer-syntax=1.2.840.10008.1.2.1`
* `multipart/related; type="image/jp2";` (transfer-syntax が指定されていない場合、1.2.840.10008.1.2.4.90 が既定値として使用されます)
* `multipart/related; type="image/jp2";transfer-syntax=1.2.840.10008.1.2.4.90`

### <a name="retrieve-transfer-syntax"></a>転送構文を取得する

要求された転送構文が元のファイルと異なる場合、元のファイルは要求された転送構文にコード変換されます。 コード変換を成功させるには、元のファイルが以下のいずれかの形式である必要があります。それ以外の場合、コード変換は失敗する可能性があります。

* 1.2.840.10008.1.2 (暗黙的なリトル エンディアン)
* 1.2.840.10008.1.2.1 (明示的なリトル エンディアン)
* 1.2.840.10008.1.2.2 (明示的な VR ビッグ エンディアン)
* 1.2.840.10008.1.2.4.50 (JPEG ベースライン プロセス 1)
* 1.2.840.10008.1.2.4.57 (JPEG ロスレス)
* 1.2.840.10008.1.2.4.70 (JPEG ロスレス選択値 1)
* 1.2.840.10008.1.2.4.90 (JPEG 2000 ロスレスのみ)
* 1.2.840.10008.1.2.4.91 (JPEG 2000)
* 1.2.840.10008.1.2.5 (RLE ロスレス)

サポートされていない `transfer-syntax` は、`406 Not Acceptable` になります。

### <a name="retrieve-metadata-for-study-series-or-instance"></a>メタデータを取得する (検査、シリーズ、またはインスタンス用)

検査、シリーズ、またはインスタンスのメタデータを取得するために、以下の `Accept` ヘッダーがサポートされています。

* `application/dicom+json`

メタデータを取得しても、以下の値表現の属性は返されません。

| VR 名 | 説明            |
| :------ | :--------------------- |
| OB      | その他の Byte             |
| OD      | その他の Double           |
| OF      | その他の Float            |
| OL      | その他の Long             |
| OV      | その他の 64-Bit Very Long |
| OW      | その他の Word             |
| UN      | Unknown                |

### <a name="retrieve-metadata-cache-validation-for-study-series-or-instance"></a>メタデータ キャッシュの検証を取得する (検査、シリーズ、またはインスタンス用)

キャッシュの検証は、`ETag` メカニズムを使用してサポートされています。 メタデータ要求への応答では、ETag はヘッダーの 1 つとして返されます。 この ETag はキャッシュされ、同じメタデータに対する以降の要求で `If-None-Match` ヘッダーとして追加できます。 データが存在する場合、次の 2 種類の応答が可能です。

* 前回の要求以降にデータが変更されていない。HTTP 304 (Not Modified) 応答が、応答本文なしで送信されます。
* 前回の要求以降にデータが変更された。HTTP 200 (OK) 応答が、更新された ETag と共に送信されます。 要求されたデータも、本文の一部として返されます。

### <a name="retrieve-response-status-codes"></a>取得の応答状態コード

| コード                         | 説明 |
| :--------------------------- | :---------- |
| 200 (OK)                     | 要求されたすべてのデータが取得されました。 |
| 304 (Not Modified)           | 要求されたデータは、前回の要求以降に変更されていません。 このような場合、応答本文にコンテンツは追加されません。 詳細については、上のセクション「**メタデータ キャッシュの検証を取得する (検査、シリーズ、またはインスタンス用)** 」を参照してください。 |
| 400 (Bad Request)            | 要求の形式が正しくありませんでした。 たとえば、指定された検査インスタンス識別子が、期待される UID 形式に準拠していなかったか、要求された transfer-syntax エンコードがサポートされていません。 |
| 401 (許可されていません)           | クライアントの認証は行われません。 |
| 403 (許可されていません)              | ユーザーが承認されていません。 |
| 404 (Not Found)              | 指定された DICOM リソースが見つかりませんでした。 |
| 406 (Not Acceptable)         | 指定された `Accept` ヘッダーはサポートされていません。 |
| 503 (Service Unavailable)    | サービスが利用できないか、ビジー状態です。 後で再度お試しください。 |

## <a name="search-qido-rs"></a>検索 (QIDO-RS)

DICOM オブジェクトの ID に基づくクエリ (QIDO) を使用すると、検査、シリーズ、およびインスタンスを属性で検索できます。

| 方法 | Path                                            | 説明                       |
| :----- | :---------------------------------------------- | :-------------------------------- |
| "*検査を検索する*"                                                                         |
| GET    | ../studies?...                                  | 検査を検索する                |
| "*シリーズを検索する*"                                                                          |
| GET    | ../series?...                                   | シリーズを検索する                 |
| GET    |../studies/{study}/series?...                    | 検査内のシリーズを検索する      |
| "*インスタンスを検索する*"                                                                       |
| GET    |../instances?...                                 | インスタンスを検索する              |
| GET    |../studies/{study}/instances?...                 | 検査内のインスタンスを検索する   |
| GET    |../studies/{study}/series/{series}/instances?... | シリーズ内のインスタンスを検索する  |

検索するために、以下の `Accept` ヘッダーがサポートされています。

- `application/dicom+json`

### <a name="supported-search-parameters"></a>サポートされている検索パラメーター

各クエリに対して以下のパラメーターがサポートされています。

| Key              | サポートの値              | 許可される数 | 説明 |
| :--------------- | :---------------------------- | :------------ | :---------- |
| `{attributeID}=` | {value}                       | 0...N         | クエリで属性と値の一致を検索します。 |
| `includefield=`  | `{attributeID}`<br/>`all`   | 0...N         | 応答で返される追加の属性。 パブリックとプライベート、両方のタグがサポートされています。<br/>`all` を指定した場合。 各クエリの種類でどの属性が返されるかの詳細については、「[検索の応答](#search-response)」のセクションを参照してください。<br/>{attributeID} と "all" の組み合わせが指定されている場合、サーバーは既定で "all" を使用します。 |
| `limit=`         | {value}                       | 0..1          | 応答で返される値の数を制限する整数値。<br/>値は 1 >= x <= 200 の範囲にすることができます。 既定値は 100 です。 |
| `offset=`        | {value}                       | 0..1          | {value} 件の結果をスキップします。<br/>オフセットに検索クエリの結果の数よりも大きい値が指定された場合は、204 (no content) 応答が返されます。 |
| `fuzzymatching=` | `true` \| `false`             | 0..1          | true の場合、あいまい一致が PatientName 属性に適用されます。 PatientName 値内の任意の名前部分とのプレフィックス単語一致が行われます。 たとえば、PatientName が "John^Doe" の場合、"joh"、"do"、"jo do"、"Doe"、および "John Doe" のすべてが一致します。 しかし、"ohn" は一致しません。 |

#### <a name="searchable-attributes"></a>検索可能な属性

以下の属性と検索の種類に対する検索がサポートされています。

| 属性キーワード | 検査 | 系列 | インスタンス |
| :---------------- | :---: | :----: | :------: |
| StudyInstanceUID | X | X | X |
| PatientName | X | X | X |
| PatientID | X | X | X |
| AccessionNumber | X | X | X |
| ReferringPhysicianName | X | X | X |
| StudyDate | X | X | X |
| StudyDescription | X | X | X |
| SeriesInstanceUID |  | X | X |
| モダリティ |  | X | X |
| PerformedProcedureStepStartDate |  | X | X |
| SOPInstanceUID |  |  | X |

#### <a name="search-matching"></a>検索の一致

以下の一致の種類がサポートされています。

| 検索の種類 | サポートされている属性 | 例 |
| :---------- | :------------------ | :------ |
| 範囲クエリ | StudyDate | {attributeID}={value1}-{value2}。 日付および時刻値の場合は、タグに対する両端を含む範囲がサポートされていました。 これは、`attributeID >= {value1} AND attributeID <= {value2}` にマップされます。 |
| 完全一致 | サポートされているすべての属性 | {attributeID}={value1} |
| あいまい一致 | PatientName | その値で始まる、患者名の任意の部分と一致します。 |

#### <a name="attribute-id"></a>属性 ID

タグは、クエリ パラメーターのためにさまざまな方法でエンコードできます。 [PS3.18 6.7.1.1.1](http://dicom.nema.org/medical/dicom/2019a/output/chtml/part18/sect_6.7.html#sect_6.7.1.1.1) で定義されている標準は、部分的に実装されています。 タグに対して以下のエンコードがサポートされています。

| 値            | 例          |
| :--------------- | :--------------- |
| {group}{element} | 0020000D         |
| {dicomKeyword}   | StudyInstanceUID |

インスタンスを検索するクエリの例: **../instances?Modality=CT&00280011=512&includefield=00280010&limit=5&offset=0**

### <a name="search-response"></a>検索の応答

応答は、DICOM データセットの配列になります。 リソースによっては、"*既定で*" 以下の属性が返されます。

#### <a name="default-study-tags"></a>既定の検査タグ

| タグ          | 属性名 |
| :----------- | :------------- |
| (0008, 0005) | SpecificCharacterSet |
| (0008, 0020) | StudyDate |
| (0008, 0030) | StudyTime |
| (0008, 0050) | AccessionNumber |
| (0008, 0056) | InstanceAvailability |
| (0008, 0090) | ReferringPhysicianName |
| (0008, 0201) | TimezoneOffsetFromUTC |
| (0010, 0010) | PatientName |
| (0010, 0020) | PatientID |
| (0010, 0030) | PatientBirthDate |
| (0010, 0040) | PatientSex |
| (0020, 0010) | StudyID |
| (0020, 000D) | StudyInstanceUID |

#### <a name="default-series-tags"></a>既定のシリーズ タグ

| タグ          | 属性名 |
| :----------- | :------------- |
| (0008, 0005) | SpecificCharacterSet |
| (0008, 0060) | モダリティ |
| (0008, 0201) | TimezoneOffsetFromUTC |
| (0008, 103E) | SeriesDescription |
| (0020, 000E) | SeriesInstanceUID |
| (0040, 0244) | PerformedProcedureStepStartDate |
| (0040, 0245) | PerformedProcedureStepStartTime |
| (0040, 0275) | RequestAttributesSequence |

#### <a name="default-instance-tags"></a>既定のインスタンス タグ

| タグ          | 属性名 |
| :----------- | :------------- |
| (0008, 0005) | SpecificCharacterSet |
| (0008, 0016) | SOPClassUID |
| (0008, 0018) | SOPInstanceUID |
| (0008, 0056) | InstanceAvailability |
| (0008, 0201) | TimezoneOffsetFromUTC |
| (0020, 0013) | InstanceNumber |
| (0028, 0010) | 行 |
| (0028, 0011) | 列 |
| (0028, 0100) | BitsAllocated |
| (0028, 0008) | NumberOfFrames |

includefield=all の場合は、既定の属性のほかに以下の属性も含まれます。 これは、既定の属性と、各リソース レベルでサポートされている属性の完全な一覧です。

#### <a name="other-study-tags"></a>その他の検査タグ

| タグ          | 属性名 |
| :----------- | :------------- |
| (0008, 1030) | Study Description |
| (0008, 0063) | AnatomicRegionsInStudyCodeSequence |
| (0008, 1032) | ProcedureCodeSequence |
| (0008, 1060) | NameOfPhysiciansReadingStudy |
| (0008, 1080) | AdmittingDiagnosesDescription |
| (0008, 1110) | ReferencedStudySequence |
| (0010, 1010) | PatientAge |
| (0010, 1020) | PatientSize |
| (0010, 1030) | PatientWeight |
| (0010, 2180) | Occupation |
| (0010, 21B0) | AdditionalPatientHistory |

#### <a name="other-series-tags"></a>その他のシリーズ タグ

| タグ          | 属性名 |
| :----------- | :------------- |
| (0020, 0011) | SeriesNumber |
| (0020, 0060) | Laterality |
| (0008, 0021) | SeriesDate |
| (0008, 0031) | SeriesTime |

以下の属性が返されます。

* リソース URL 内のすべての一致クエリ パラメーターと UID。
* そのリソース レベルでサポートされている IncludeField 属性。 
* ターゲット リソースがすべてのシリーズの場合は、検査レベルの属性も返されます。
* ターゲット リソースがすべてのインスタンスの場合は、検査レベルおよびシリーズ レベルの属性も返されます。
* ターゲット リソースが検査のインスタンスの場合は、シリーズ レベルの属性も返されます。

### <a name="search-response-codes"></a>検索の応答コード

クエリ API は、応答で以下のいずれかの状態コードを返します。

| コード                      | 説明 |
| :------------------------ | :---------- |
| 200 (OK)                  | 応答ペイロードには、一致するすべてのリソースが含まれます。 |
| 204 (コンテンツなし)          | 検索は正常に完了しましたが、結果は返されませんでした。 |
| 400 (Bad Request)         | クエリ コンポーネントが無効だったため、サーバーがクエリを実行できませんでした。 応答本文に、エラーの詳細が含まれています。 |
| 401 (許可されていません)        | クライアントの認証は行われません。 |
| 403 (許可されていません)           | ユーザーが承認されていません。 |
| 503 (Service Unavailable) | サービスが利用できないか、ビジー状態です。 後で再度お試しください。 |

### <a name="extra-notes"></a>追加の注意事項

* `TimezoneOffsetFromUTC` (`00080201`) を使用したクエリはサポートされていません。
* クエリ API は 413 (request entity too large) を返しません。 要求されたクエリ応答の制限が、許容される範囲を超える場合は、Bad Request が返されます。 許容される範囲内の要求内容は解決されます。
* ターゲット リソースが検査またはシリーズである場合、複数のインスタンス全体で検査またはシリーズ レベルのメタデータの一貫性がなくなる可能性があります。 たとえば、2 つのインスタンスの patientName が異なる場合があります。 この場合、最新のものが優先され、最新のデータだけを検索できます。
* ページ化された結果は、一致した "*最新の*" インスタンスを最初に返すように最適化されます。 そのため、クエリに一致するより新しいデータが追加された場合、後続のページでレコードが重複する可能性があります。
* PN VR の種類では、一致で大文字と小文字が区別されず、アクセントが区別されません。
* 他の文字列 VR の種類では、一致で大文字と小文字が区別されず、アクセントが区別されます。

## <a name="delete"></a>削除

このトランザクションは、公式な DICOMweb&trade; 標準の一部ではありません。 ここでは DELETE メソッドを使用して、格納から検査、シリーズ、およびインスタンスの表現を削除します。

| 方法 | Path                                                    | 説明 |
| :----- | :------------------------------------------------------ | :---------- |
| DELETE | ../studies/{study}                                      | 特定の検査のインスタンスをすべて削除します。 |
| DELETE | ../studies/{study}/series/{series}                      | 検査内の特定のシリーズのインスタンスをすべて削除します。 |
| DELETE | ../studies/{study}/series/{series}/instances/{instance} | シリーズ内の特定のインスタンスを削除します。 |

パラメーターの `study`、`series`、および `instance` は、それぞれが DICOM 属性 StudyInstanceUID、SeriesInstanceUID、および SopInstanceUID に対応しています。

要求の `Accept` ヘッダー、`Content-Type` ヘッダー、または本文の内容に対する制限はありません。

> [!NOTE]
> 削除トランザクションの後、削除されたインスタンスは復旧できません。

### <a name="response-status-codes"></a>応答のステータス コード

| コード                         | 説明 |
| :--------------------------- | :---------- |
| 204 (コンテンツなし)             | すべての SOP インスタンスが削除された場合。 |
| 400 (Bad Request)            | 要求の形式が正しくありませんでした。 |
| 401 (許可されていません)           | クライアントの認証は行われません。 |
| 403 (許可されていません)              | ユーザーが承認されていません。 |
| 404 (Not Found)              | 検査内に指定されたシリーズが見つからなかったか、シリーズ内に指定されたインスタンスが見つからなかった場合。 |
| 503 (Service Unavailable)    | サービスが利用できないか、ビジー状態です。 後で再度お試しください。 |

### <a name="delete-response-payload"></a>削除の応答ペイロード

応答本文は空になります。 状態コードが、返される唯一の有用な情報です。

### <a name="next-steps"></a>次の手順

DICOM サービスの詳細については、以下を参照してください 

>[!div class="nextstepaction"]
>[DICOM サービスの概要](dicom-services-overview.md)
