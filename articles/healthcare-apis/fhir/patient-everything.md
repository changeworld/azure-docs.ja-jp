---
title: Azure API for FHIR ですべてのものを使用する
description: この記事では、Azure API for FHIR で患者のすべての操作を使用する方法について説明します。
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 06/04/2021
ms.author: cavoeg
ms.openlocfilehash: b02a2b582130c439298653d7c2a0512edbb8a23b
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111756367"
---
# <a name="patient-everything-in-fhir"></a>患者-FHIR のすべての情報

[$Patient すべて](https://www.hl7.org/fhir/patient-operation-everything.html)の操作が作成されました。これにより、レコード全体にアクセスしたり、プロバイダーや他のユーザーが一括データのダウンロードを実行したりできるようになります。 この操作は、この操作が呼び出されるリソースまたはコンテキストで記述された1つまたは複数の患者に関連するすべての情報を返すために使用されます。  

## <a name="use-patient-everything"></a>患者をすべて使用する
患者をすべて呼び出すには、次のコマンドを使用します。

```json
GET {FHIRURL}/Patient/{ID}/$everything
```
Azure API for FHIR は、指定された患者 ID に一致する患者を見つけることができるかどうかを検証します。 結果が見つかった場合、応答は "searchset" という種類のバンドルになり、次の情報が表示されます。 
* [患者のリソース](https://www.hl7.org/fhir/patient.html) 
*  患者のリソースによって直接参照されているリソース (リンクを除く) 
*  患者の[コンパートメント](https://www.hl7.org/fhir/compartmentdefinition-patient.html)内のリソース
*  患者のリソースを参照する[デバイスリソース](https://www.hl7.org/fhir/device.html)。 これは、100のデバイスに制限されていることに注意してください。 患者に100個を超えるデバイスがリンクされている場合は、100だけが返されます。 

 
> [!Note]
> $patient-すべてが、Cosmos DB によってサポートされているオープンソースの FHIR サーバーで利用でき、7月1日までの Azure API for FHIR で利用できるようになります。 FHIR サーバーの機能ステートメントで $patient のサポートが不足しています。これについては、「問題 [1989](https://github.com/microsoft/fhir-server/issues/1989)」を参照してください。 


## <a name="patient-everything-parameters"></a>患者のすべてのパラメーター
Azure API for FHIR では、次のクエリ パラメーターがサポートされています。 これらのパラメーターはすべて省略可能です。

|Query parameter (クエリ パラメーター)        |  説明|
|-----------------------|------------|
| \_type | 応答に含めるリソースの種類を指定できます。 たとえば、「=」と入力すると、 \_ `Encounter` その患者に関連付けられているリソースのみが返されます。 |
| \_since | は、指定された時間以降に変更されたリソースのみを返します。 |
| start | 開始日を指定すると、指定した開始日よりも臨床日があるリソースが取得されます。 開始日が指定されていない場合は、終了日より前のすべてのレコードがスコープ内にあります。 |
| end | 終了日を指定すると、指定した終了日よりも前の臨床日があるリソースが取得されます。 終了日が指定されていない場合、開始日より後のすべてのレコードがスコープ内にあります。 |

> [!Note]
> 特定の患者の ID を指定する必要があります。 すべての患者のすべてのデータが必要な場合は、「 [$export](export-data.md)」を参照してください。 


## <a name="examples-of-patient-everything"></a>$Patient の例-すべて 

$Patient すべての操作を使用するその他の例を次に示します。 

$Patient すべてを使用して、2010と2020の患者の "すべて" を照会するには、次の呼び出しを使用します。 

```json
GET {FHIRURL}/Patient/{ID}/$everything?start=2010&end=2020
``` 

$Patient すべてを使用して患者の観測と発生を照会するには、次の呼び出しを使用します。 
```json
GET {FHIRURL}/Patient/{ID}/$everything_type=Observation,Encounter 
```

$Patient すべてを使用して、2021-05-27T05:00: 00Z 以降の患者の "すべて" を照会するには、次の呼び出しを使用します。 

```json
GET {FHIRURL}/Patient/{ID}/$everything?_since=2021-05-27T05:00:00Z 
```

これらの通話ごとに患者が見つかると、対応するリソースのバンドルを含む200の応答が返されます。

## <a name="next-step"></a>次のステップ
これで、患者のすべての操作を使用する方法がわかりました。次は、検索ガイドの概要の検索オプションの詳細について説明します。

>[!div class="nextstepaction"]
>[FHIR 検索の概要](overview-of-search.md)