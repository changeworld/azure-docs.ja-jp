---
title: 患者-すべて
description: この記事では、患者のすべての操作を使用する方法について説明します。
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/03/2021
ms.author: cavoeg
ms.openlocfilehash: 3aaed02f7de7e8a4495415b15d893fd7b357fc63
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595011"
---
# <a name="using-patient-everything-in-fhir-service"></a>FHIR サービスのすべての $patient の使用

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

[$patient-everything](https://www.hl7.org/fhir/patient-operation-everything.html) 操作は、患者にレコード全体へのアクセス権を付与するため、またはプロバイダーや他のユーザーがデータの一括ダウンロードを実行できるようにするために使用されます。 この操作は、この操作が呼び出されるリソースまたはコンテキストで記述された 1 人以上の患者に関連するすべての情報を返すために使用されます。  

## <a name="use-patient-everything"></a>patient-everything を使用する
patient-everything を呼び出すには、次のコマンドを使用します。

```json
GET {FHIRURL}/Patient/{ID}/$everything
```
Azure の医療 Api (FHIR サービスと呼ばれます) の FHIR サービスは、指定された患者 ID に一致する患者を見つけることができるかどうかを検証します。 結果が見つかった場合、応答は次の情報を含む "searchset" という種類のバンドルになります。 
* [患者リソース](https://www.hl7.org/fhir/patient.html) 
* 患者リソースによって直接参照されているリソース (リンクを除く) 
* [患者のコンパートメント](https://www.hl7.org/fhir/compartmentdefinition-patient.html)内のリソース
* 患者リソースを参照する[デバイス リソース](https://www.hl7.org/fhir/device.html)。 これは 100 台のデバイスに制限されています。 患者に 100 台を超えるデバイスがリンクされている場合は、100 台のみが返されます。 


## <a name="patient-everything-parameters"></a>patient-everything のパラメーター
FHIR サービスでは、次のクエリ パラメーターがサポートされています。 これらのパラメーターはすべて省略可能です。

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

これらの通話ごとに患者が見つかった場合は、対応するリソースので200の応答が返され `Bundle` ます。

## <a name="next-step"></a>次のステップ
これで、patient-everything 操作を使用する方法がわかったので、次は検索ガイドの概要の検索オプションの詳細について学習できます。

>[!div class="nextstepaction"]
>[FHIR 検索の概要](overview-of-search.md)
