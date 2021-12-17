---
title: Azure API for FHIR 内で patient-everything を使用する
description: この記事では、Azure API for FHIR 内で patient-everything 操作を使用する方法について説明します
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 06/04/2021
ms.author: cavoeg
ms.openlocfilehash: 83c38f2add15481bab844f1c21a142644b546c0c
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123429210"
---
# <a name="patient-everything-in-fhir"></a>FHIR の patient-everything

[$patient-everything](https://www.hl7.org/fhir/patient-operation-everything.html) 操作は、患者にレコード全体へのアクセス権を付与するため、またはプロバイダーや他のユーザーがデータの一括ダウンロードを実行できるようにするために使用されます。 この操作は、この操作が呼び出されるリソースまたはコンテキストで記述された 1 人以上の患者に関連するすべての情報を返すために使用されます。  

## <a name="use-patient-everything"></a>patient-everything を使用する
patient-everything を呼び出すには、次のコマンドを使用します。

```json
GET {FHIRURL}/Patient/{ID}/$everything
```
Azure API for FHIR により、指定した患者 ID に一致する患者を見つけることができるかどうかが検証されます。 結果が見つかった場合、応答は次の情報を含む "searchset" という種類のバンドルになります。 
* [患者リソース](https://www.hl7.org/fhir/patient.html) 
*  患者リソースによって直接参照されているリソース (リンクを除く) 
*  [患者のコンパートメント](https://www.hl7.org/fhir/compartmentdefinition-patient.html)内のリソース
*  患者リソースを参照する[デバイス リソース](https://www.hl7.org/fhir/device.html)。 これは 100 台のデバイスに制限されています。 患者に 100 台を超えるデバイスがリンクされている場合は、100 台のみが返されます。 


## <a name="patient-everything-parameters"></a>patient-everything のパラメーター
Azure API for FHIR では、次のクエリ パラメーターがサポートされています。 これらのパラメーターはすべて省略可能です。

|Query parameter (クエリ パラメーター)        |  説明|
|-----------------------|------------|
| \_type | 応答にどの種類のリソースを含めるかを指定できます。 たとえば、\_type=Encounter と入力した場合に返されるのは`Encounter`、その患者に関連付けられているリソースだけです。 |
| \_since | 指定された時間以降に変更されたリソースのみを返します。 |
| start | 開始日を指定すると、臨床日が指定した開始日よりも後であるリソースが取得されます。 開始日が指定されていない場合は、終了日より前のすべてのレコードがスコープ内にあります。 |
| end | 終了日を指定すると、臨床日が指定した終了日よりも前であるリソースが取得されます。 終了日が指定されていない場合は、開始日より後のすべてのレコードがスコープ内にあります。 |

> [!Note]
> 特定の患者の ID を指定する必要があります。 すべての患者のすべてのデータが必要な場合は、「[$export](../data-transformation/export-data.md)」を参照してください。 


## <a name="examples-of-patient-everything"></a>$patient-everything の例 

$patient-everything 操作を使用する例を次に示します。 

$patient-everything を使用して、2010 年から 2020 年までの患者の "すべて" を照会するには、次の呼び出しを使用します。 

```json
GET {FHIRURL}/Patient/{ID}/$everything?start=2010&end=2020
``` 

$patient-everything を使用して、患者の観察と発生を照会するには、次の呼び出しを使用します。 
```json
GET {FHIRURL}/Patient/{ID}/$everything?_type=Observation,Encounter 
```

$patient-everything を使用して、2021-05-27T05:00:00Z 以降の患者の "すべて" を照会するには、次の呼び出しを使用します。 

```json
GET {FHIRURL}/Patient/{ID}/$everything?_since=2021-05-27T05:00:00Z 
```

これらの各呼び出しで患者が見つかると、対応するリソースのバンドルを含む 200 個の応答が返されます。

## <a name="next-step"></a>次のステップ
これで、patient-everything 操作を使用する方法がわかったので、次は検索ガイドの概要の検索オプションの詳細について学習できます。

>[!div class="nextstepaction"]
>[FHIR 検索の概要](overview-of-search.md)
