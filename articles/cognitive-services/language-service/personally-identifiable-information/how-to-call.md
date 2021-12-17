---
title: 個人を特定できる情報 (PII) を検出する方法
titleSuffix: Azure Cognitive Services
description: この記事では、テキストから PII と健康情報 (PHI) を抽出し、特定可能な情報を検出する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-pii, ignite-fall-2021
ms.openlocfilehash: 93f232233da0310881539441fdb296ac4ae39b62
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092361"
---
# <a name="how-to-detect-and-redact-personally-identifying-information-pii"></a>個人を特定できる情報 (PII) を検出して編集する方法

PII 機能を使用すると、非構造化テキストを評価し、事前に定義された複数のカテゴリにわたって、テキスト内の機密情報 (PII) と健康情報 (PHI) を抽出することができます。

## <a name="determine-how-to-process-the-data-optional"></a>データの処理方法を決定する (省略可能)

### <a name="specify-the-pii-detection-model"></a>PII 検出モデルを指定する

既定では、テキストで利用可能な最新の AI モデルがこの機能で使われます。 特定のバージョンのモデルを使うように API 要求を構成することもできます。 指定したモデルを使って、NER と PII の操作が実行されます。

| サポートされているバージョン | 最新バージョン |
|--|--|
| `2019-10-01`, `2020-02-01`, `2020-04-01`,`2020-07-01`, `2021-01-15`  | `2021-01-15`   |

### <a name="input-languages"></a>入力言語

処理されるドキュメントを送信するときに、[サポートされている言語](language-support.md)のどれでそれが書かれているかを指定できます。 言語を指定しないと、英語がキー フレーズ抽出によって既定で使われます。 さまざまな[多言語と絵文字のエンコード](../concepts/multilingual-emoji-support.md)をサポートするため、API からの応答でオフセットを返すことができます。 

## <a name="submitting-data"></a>データの送信

要求が受信されると分析が実行されます。 分単位および秒単位で送信できる要求のサイズと数については、以下のデータ制限に関するセクションをご覧ください。

PII 検出機能の同期的な使用はステートレスです。 データはアカウントに保存されず、結果がすぐに応答で返されます。

これらの機能を非同期的に使うと、API の結果は、応答で示される要求取り込み時刻から 48 時間利用できます。 この時間が経過すると、結果は消去され、取得できなくなります。

API では、指定されたドキュメント言語について、[定義されたエンティティのカテゴリ](concepts/entity-categories.md)の検出が試みられます。 検出されて返されるエンティティを指定する場合は、オプションの `piiCategories` パラメーターを適切なエンティティ カテゴリと共に使用します。 このパラメーターを使用すると、ドキュメントの言語に対して既定で有効になっていないエンティティを検出することもできます。 次の URL の例では、既定の英語エンティティと一緒に英語のテキストに出現している可能性があるフランスの運転免許証番号を検出しようとしています。

> [!TIP]
> エンティティ カテゴリを指定するときに `default` を含めなかった場合、指定したエンティティ カテゴリのみが返されます。

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.5/entities/recognition/pii?piiCategories=default,FRDriversLicenseNumber`

## <a name="getting-pii-results"></a>PII の結果の取得

PII 検出から結果を取得するときは、結果をアプリケーションにストリーミングしたり、ローカル システム上のファイルに出力を保存したりできます。 API の応答には、[認識されたエンティティ](concepts/entity-categories.md)と、そのカテゴリ、サブカテゴリ、信頼度スコアなどが含まれます。 PII エンティティが編集されたテキスト文字列も返されます。

## <a name="data-limits"></a>データ制限

> [!NOTE]
> * 制限を超えるドキュメントを分析する必要がある場合は、テキストを小さなチャンクに分割してから API に送信することができます。 
> * ドキュメントとは、テキスト文字の 1 つの文字列です。  

| 制限 | 値 |
|------------------------|---------------|
| 1 つのドキュメントの最大サイズ (同期) | [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements) によって計測された 5,120 文字。  |
| 要求ごとの最大文字数 (非同期)  | 送信ドキュメント全体で 125,000 文字。[StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements) によって計測されます。  |
| 要求全体の最大サイズ | 1 MB。 |
| 要求あたりのドキュメントの最大数 | 5 |

ドキュメントが文字数制限を超える場合、API の動作は使っている機能により異なります。

* 非同期:
  * いずれかのドキュメントが最大サイズを超えている場合、API は要求全体を拒否し、`400 bad request` エラーを返します。
* 同期:  
  * API は、最大サイズを超えるドキュメントの処理は行わず、無効なドキュメント エラーを返します。 API 要求に複数のドキュメントが含まれている場合、API は、それらが文字数制限内であれば処理を続行します。

1 つの要求で送信できるドキュメントの最大数を超えると、HTTP 400 エラー コードが生成されます。

### <a name="rate-limits"></a>転送率の制限

レート制限は[価格レベル](https://aka.ms/unifiedLanguagePricing)によって異なります。

| レベル          | 1 秒あたりの要求数 | 1 分あたりの要求数 |
|---------------|---------------------|---------------------|
| S/マルチサービス | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |

## <a name="next-steps"></a>次のステップ

[固有表現認識の概要](overview.md)
