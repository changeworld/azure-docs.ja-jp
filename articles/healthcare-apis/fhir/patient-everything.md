---
title: patient-everything を使用してAzure API for FHIR
description: この記事では、この記事で patient-everything 操作を使用する方法について説明Azure API for FHIR
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 06/04/2021
ms.author: cavoeg
ms.openlocfilehash: fee544c318df318e09c75ebaf18fc0837cb4af0d
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2021
ms.locfileid: "111562735"
---
# <a name="patient-everything-in-fhir"></a>Patient-everything in FHIR

すべての [$patient操作は](https://www.hl7.org/fhir/patient-operation-everything.html) 、患者にレコード全体へのアクセス権を提供したり、プロバイダーや他のユーザーが一括データのダウンロードを実行したりするために作成されました。 この操作は、この操作が呼び出されるリソースまたはコンテキストで説明されている 1 人または複数の患者に関連する情報を返す場合に使用されます。  

## <a name="use-patient-everything"></a>patient-everything を使用する
patient-everything を呼び出す場合は、次のコマンドを使用します。

```json
GET {FHIRURL}/Patient/{ID}/$everything
```
このAzure API for FHIR、指定された患者 ID に一致する患者が見つけられるか検証されます。 結果が見つかった場合、応答は次の情報を含む型 "searchset" のバンドルになります。 
* [患者リソース](https://www.hl7.org/fhir/patient.html) 
*  Patient リソースによって直接参照されるリソース (リンクを除く) 
*  患者のコンパートメント内の [リソース](https://www.hl7.org/fhir/compartmentdefinition-patient.html)
*  [患者リソース](https://www.hl7.org/fhir/device.html) を参照するデバイス リソース  

 
> [!Note]
> $patientすべては、Cosmos DB によってサポートされているオープン ソース FHIR Server で利用できます。Azure API for FHIR 年 7 月 1 日より前に使用できます。 FHIR Server の機能ステートメントに $patient-everything のサポートがありません。これは、問題 [1989 で追跡されています](https://github.com/microsoft/fhir-server/issues/1989)。 


## <a name="patient-everything-parameters"></a>Patient-everything パラメーター
Azure API for FHIR では、次のクエリ パラメーターがサポートされています。 これらのパラメーターはすべて省略可能です。

|Query parameter (クエリ パラメーター)        |  説明|
|-----------------------|------------|
| \_type | 応答に含めるリソースの種類を指定できます。 たとえば \_ 、type=Encounter は、患者に関連 `Encounter` 付けられているリソースのみを返します。 |
| \_since | 指定された時間以降に変更されたリソースのみを返します。 |
| start | 開始日を指定すると、指定した開始日より後に臨床日があるリソースが取得されます。 開始日が指定されている場合、終了日より前のすべてのレコードがスコープ内に含まれます。 |
| end | 終了日を指定すると、指定した終了日より前の臨床日があるリソースが取り込されます。 終了日が指定されている場合、開始日より後のすべてのレコードがスコープ内に含まれます。 |

> [!Note]
> 特定の患者の ID を指定する必要があります。 すべての患者のすべてのデータが必要な場合は、「」を[参照$export。](export-data.md) 


## <a name="examples-of-patient-everything"></a>$patientの例 

次に示すのは、$patientすべての操作の使用例です。 

2010 $patient 2020 年の間に患者の "すべて" に対してクエリを実行するには、次の呼び出しを使用します。 

```json
GET {FHIRURL}/Patient/{ID}/$everything?start=2010&end=2020
``` 

すべてのデータ$patientを使用して患者の観察と遭遇を照会するには、次の呼び出しを使用します。 
```json
GET {FHIRURL}/Patient/{ID}/$everything_type=Observation,Encounter 
```

$patient-everything を使用して、2021-05-27T05:00:00Z 以降の患者の "すべて" に対してクエリを実行するには、次の呼び出しを使用します。 

```json
GET {FHIRURL}/Patient/{ID}/$everything?_since=2021-05-27T05:00:00Z 
```

これらの呼び出しごとに患者が見つかった場合は、対応するリソースのバンドルを含む 200 応答が返されます。

## <a name="next-step"></a>次のステップ
patient-everything 操作の使い方がわかったので、検索ガイドの概要に関するより多くの検索オプションについて学習できます。

>[!div class="nextstepaction"]
>[FHIR 検索の概要](overview-of-search.md)