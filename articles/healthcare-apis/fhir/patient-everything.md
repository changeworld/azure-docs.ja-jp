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
ms.openlocfilehash: 480a909b5349d973f2d9803e1440827d94923ee6
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2021
ms.locfileid: "112322784"
---
# <a name="patient-everything-in-fhir"></a>Patient-everything in FHIR

すべての [$patient操作](https://www.hl7.org/fhir/patient-operation-everything.html) は、患者にレコード全体へのアクセス権を提供したり、プロバイダーや他のユーザーが一括データのダウンロードを実行したりするために使用されます。 この操作は、この操作が呼び出されるリソースまたはコンテキストで説明されている 1 人または複数の患者に関連する情報を返す場合に使用されます。  

## <a name="use-patient-everything"></a>patient-everything を使用する
patient-everything を呼び出す場合は、次のコマンドを使用します。

```json
GET {FHIRURL}/Patient/{ID}/$everything
```
このAzure API for FHIR、指定された患者 ID に一致する患者が見つけられるか検証されます。 結果が見つかった場合、応答は次の情報を含む型 "searchset" のバンドルになります。 
* [患者リソース](https://www.hl7.org/fhir/patient.html) 
*  Patient リソースによって直接参照されるリソース (リンクを除く) 
*  患者コンパートメント [内のリソース](https://www.hl7.org/fhir/compartmentdefinition-patient.html)
*  [Patient リソース](https://www.hl7.org/fhir/device.html) を参照するデバイス リソース。 これは 100 台のデバイスに制限されています。 患者に 100 を超えるデバイスがリンクされている場合、返されるデバイスは 100 台のみです。 

 
> [!Note]
> FHIR Server の機能ステートメントに $patient-everything のサポートがありません。これは、問題 [1989 で追跡されています](https://github.com/microsoft/fhir-server/issues/1989)。 


## <a name="patient-everything-parameters"></a>Patient-everything パラメーター
Azure API for FHIR では、次のクエリ パラメーターがサポートされています。 これらのパラメーターはすべて省略可能です。

|Query parameter (クエリ パラメーター)        |  説明|
|-----------------------|------------|
| \_type | 応答に含めるリソースの種類を指定できます。 たとえば \_ 、type=Encounter は、患者に関連 `Encounter` 付けられているリソースのみを返します。 |
| \_since | 指定された時間以降に変更されたリソースのみを返します。 |
| start | 開始日を指定すると、指定した開始日より後に、その臨床日が含むリソースが取得されます。 開始日が指定されている場合、終了日より前のすべてのレコードがスコープ内に含まれます。 |
| end | 終了日を指定すると、指定された終了日より前の医療日付のリソースが取得されます。 終了日が指定されている場合、開始日より後のすべてのレコードがスコープ内に含まれます。 |

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