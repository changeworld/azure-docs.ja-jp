---
title: FHIR サービスの匿名化データ (プレビュー) をエクスポートする
description: この記事では、匿名化エクスポートを設定して使用する方法について説明します。
author: ranvijaykumar
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 9/28/2020
ms.author: ranku
ms.openlocfilehash: a7757830dfb75f7ad111913ee4a8ea41926db600
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2021
ms.locfileid: "132867376"
---
# <a name="exporting-de-identified-data-preview"></a>匿名化データをエクスポートする (プレビュー)

> [!Note] 
> 匿名化エクスポートの使用時の結果は、入力されたデータや、顧客が選択した関数などの要因によってさまざまです。 Microsoft では、匿名化エクスポートの出力を評価したり、お客様のユース ケースやコンプライアンスのニーズにおいて受け入れ可能かどうかを判断したりすることはできません。 匿名化エクスポートにより、法律、規制、またはコンプライアンスの特定の要件が満たされるとは保証されません。

$export コマンドを使用して、FHIR サーバーから匿名化データをエクスポートすることもできます。 [FHIR 匿名化ツール](https://github.com/microsoft/FHIR-Tools-for-Anonymization)の匿名化エンジンが使用され、クエリ パラメーターで匿名化構成の詳細が取得されます。 独自の匿名化構成ファイルを作成することも、HIPAA Safe Harbor メソッドの[サンプル構成ファイル](https://github.com/microsoft/Tools-for-Health-Data-Anonymization/blob/master/docs/FHIR-anonymization.md#sample-configuration-file)を出発点として使用することもできます。 

## <a name="configuration-file"></a>構成ファイル

匿名化エンジンには、HIPAA セーフハーバーメソッドの要件を満たすためのサンプル構成ファイルが付属しています。 構成ファイルは、、、、およびの4つのセクションを含む JSON ファイルです `fhirVersion` `processingErrors` `fhirPathRules` `parameters` 。 
* `fhirVersion` 匿名化エンジンの FHIR バージョンを指定します。
* `processingErrors` 匿名化中に発生する可能性のある処理エラーに対して実行するアクションを指定します。 必要に応じて、例外を _発生させ_ たり、 _保持_ したりすることができます。
* `fhirPathRules` 使用する匿名化メソッドを指定します。 規則は、構成ファイルでの出現順に実行されます。
* `parameters`_Fhirpathrules_ に指定されている匿名化動作の規則を設定します。

R4 のサンプル構成ファイルを次に示します。

```json
{
  "fhirVersion": "R4",
  "processingError":"raise",
  "fhirPathRules": [
    {"path": "nodesByType('Extension')", "method": "redact"},
    {"path": "Organization.identifier", "method": "keep"},
    {"path": "nodesByType('Address').country", "method": "keep"},
    {"path": "Resource.id", "method": "cryptoHash"},
    {"path": "nodesByType('Reference').reference", "method": "cryptoHash"},
    {"path": "Group.name", "method": "redact"}
  ],
  "parameters": {
    "dateShiftKey": "",
    "cryptoHashKey": "",
    "encryptKey": "",
    "enablePartialAgesForRedact": true
  }
}
```

構成ファイルのこれら4つのセクションの詳細については、 [こちら](https://github.com/microsoft/Tools-for-Health-Data-Anonymization/blob/master/docs/FHIR-anonymization.md#configuration-file-format)を参照してください。
## <a name="using-export-command-for-the-de-identified-data"></a>特定されていないデータに対して $export コマンドを使用する
 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

> [!Note] 
> 現在、FHIR サービスでは、システム レベルでの匿名化エクスポートのみがサポートされています ($export)。

|Query parameter (クエリ パラメーター)            | 例 |選択肢| 説明|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.json|匿名化エクスポートで必須 |構成ファイルの名前です。 構成ファイル形式については、[ここ](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format)を参照してください。 このファイルは、エクスポート先として構成されている場所と同じ Azure ストレージ アカウント内の **匿名化** という名前のコンテナー内に保存する必要があります。 |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|匿名化エクスポートで任意|これは構成ファイルの Etag です。 Azure Storage Explorer を使用して、BLOB プロパティから Etag を取得できます。|

> [!IMPORTANT]
> 生エクスポートと匿名化エクスポートはどちらも、エクスポート構成の一部として指定されたのと同じ Azure ストレージ アカウントに書き込まれます。 別の匿名化構成に対応する別のコンテナーを使用し、コンテナー レベルでユーザー アクセスを管理することをお勧めします。
