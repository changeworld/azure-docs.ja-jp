---
title: Azure API for FHIR で $export コマンドを呼び出してエクスポートを実行する
description: この記事では、$export を使用して FHIR データをエクスポートする方法について説明します
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: 74fe09895f49cc9f7c3cdf6b6c97c1624c3e9c0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91839828"
---
# <a name="how-to-export-fhir-data"></a>FHIR データをエクスポートする方法


一括エクスポート機能を使用すると、[FHIR 仕様](https://hl7.org/fhir/uv/bulkdata/export/index.html)に従って FHIR サーバーからデータをエクスポートできます。 

$export を使用する前に、Azure API for FHIR がそれを使用するように構成されていることを確認する必要があります。 エクスポート設定を構成し、Azure ストレージ アカウントを作成する方法については、[データのエクスポートの構成ページ](configure-export-data.md)を参照してください。

## <a name="using-export-command"></a>$export コマンドの使用

エクスポート用に Azure API for FHIR を構成すると、$export コマンドを使用して、サービスからデータをエクスポートできます。 データは、エクスポートの構成時に指定したストレージ アカウントに格納されます。 FHIR サーバーで $export コマンドを呼び出す方法については、[$export の仕様](https://hl7.org/Fhir/uv/bulkdata/export/index.html)に関するドキュメントを参照してください。 

Azure API for FHIR の $export コマンドには、データのエクスポート先として構成されたストレージ アカウント内のコンテナーを指定する省略可能な _\_conatiner_ パラメーターを指定します。 コンテナーが指定されている場合は、その名前を持つ新しいフォルダー内のそのコンテナーにデータがエクスポートされます。 コンテナーが指定されていない場合は、ランダムに生成された名前を持つ新しいコンテナーにエクスポートされます。 

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

## <a name="supported-scenarios"></a>サポートされるシナリオ

Azure API for FHIR を使用すると、システム、患者、およびグループ レベルでの $export がサポートされます。 グループのエクスポートの場合、関連するすべてのリソースをエクスポートしますが、グループの特性はエクスポートしません。

> [!Note] 
> リソースが複数のリソースのコンパートメントにある場合、または複数のグループに存在する場合は、$export を使用すると重複するリソースがエクスポートされます。

さらに、キュー登録中に場所ヘッダーによって返された URL を介したエクスポートの状態の確認と、実際のエクスポート ジョブのキャンセルもサポートされています。

## <a name="next-steps"></a>次の手順

この記事では、$export コマンドを使用して、FHIR リソースをエクスポートする方法について説明しました。 次は、サポートされている機能を確認します。
 
>[!div class="nextstepaction"]
>[サポートされる機能](fhir-features-supported.md)
