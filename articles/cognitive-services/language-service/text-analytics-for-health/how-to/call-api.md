---
title: Text Analytics for Health を呼び出す方法
titleSuffix: Azure Cognitive Services
description: Text Analytics for Health を使用して、構造化されていない臨床テキストから医療情報を抽出してラベル付けする方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: d94e07b312bda98c6317e8a2b020510ffda800f4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092179"
---
# <a name="how-to-use-text-analytics-for-health"></a>Text Analytics for Health を使用する方法

> [!IMPORTANT] 
> Text Analytics for Health は、"現状のまま"、"保証なしで" 提供される機能です。 Text Analytics for Health は、医療デバイス、臨床サポート、診断ツール、または他のテクノロジ (病気や他の状況の診断、治療、軽減、取り扱い、防止での使用が意図されているもの) として使用することを意図されたり、使用できるようにされているものではなく、そのような目的でのこの機能の使用に対して、マイクロソフトからはライセンスや権利は付与されません。 この機能は、専門的な医療のアドバイスや医学的意見、診断、治療、または医療専門家による医学的判断に代わるものとして実装またはデプロイするために設計されたり、それを意図されたりしたものではなく、そのようには使用しないでください。 お客様は、Text Analytics for Health の使用に関するすべての責任を持ちます。 お客様は [UMLS Metathesaurus 使用許諾契約書付録](https://www.nlm.nih.gov/research/umls/knowledge_sources/metathesaurus/release/license_agreement_appendix.html)または将来の同等のリンクに対して設定された条項に基づいて、使用する予定のすべてのソース ボキャブラリを個別にライセンスする必要があります。 お客様は、地理的または他の適用される制限を含め、これらのライセンス条項に準拠する責任を負います。


Text Analytics for Health を使うと、医師のメモ、退院要約、臨床ドキュメント、電子健康記録などの非構造化テキストから関連する医療情報を抽出して、ラベルを付けることができます。  このサービスを利用するには、次の 2 つの方法があります。 

* Web ベースの API とクライアント ライブラリ (非同期)
* [Docker コンテナー](use-containers.md) (同期)

## <a name="features"></a>特徴

Text Analytics for Health により、英語のテキストに対して固有表現認識 (NER)、関係抽出、エンティティ否定、エンティティ リンク設定が実行されて、臨床および生物医学に関する構造化されていないテキストに含まれる分析情報が明らかにされます。 サポートされているすべてのエンティティの一覧については、Text Analytics for Health によって返される[エンティティのカテゴリ](../concepts/health-entity-categories.md)を参照してください。 信頼度スコアについては、[透明性に関するメモ](/legal/cognitive-services/text-analytics/transparency-note#general-guidelines-to-understand-and-improve-performance?context=/azure/cognitive-services/text-analytics/context/context)をご覧ください。 

> [!TIP]
> [クイックスタートの記事](../quickstart.md)に従って、この機能を使い始めることができます。 また、[Language Studio](../../language-studio.md) を使うと、コードを記述しなくても要求の例を作成できます。

## <a name="determine-how-to-process-the-data-optional"></a>データの処理方法を決定する (省略可能)

### <a name="specify-the-text-analytics-for-health-model"></a>Text Analytics for Health のモデルを指定する

既定状態の Text Analytics for Health では、利用できる最新の AI モデルがテキストで使われます。 モデルの特定のバージョンを使うように API 要求を構成することもできます。 指定したモデルを使って、Text Analytics for Health によって提供される操作が実行されます。

| サポートされているバージョン | 最新バージョン |
|--|--|
| `2021-05-15` | `2021-05-15`   |

### <a name="text-analytics-for-health-container"></a>Text Analytics for Health コンテナー

[Text Analytics for Health コンテナー](use-containers.md)では、REST API やクライアント ライブラリとは異なるモデルのバージョン管理が使われます。 コンテナー イメージごとに使用できるモデルのバージョンは 1 つだけです。

| エンドポイント                        | コンテナー イメージ タグ                     | モデル バージョン |
|---------------------------------|-----------------------------------------|---------------|
| `/entities/health`              | `3.0.016230002-onprem-amd64` (最新)            | `2021-05-15`  |
| `/entities/health`              | `3.0.015370001-onprem-amd64`            | `2021-03-01`  |
| `/entities/health`              | `1.1.013530001-amd64-preview`           | `2020-09-03`  |
| `/entities/health`              | `1.1.013150001-amd64-preview`           | `2020-07-24`  |
| `/domains/health`               | `1.1.012640001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |

### <a name="input-languages"></a>入力言語

現在、Text Analytics for Health で[サポート](../language-support.md)されているのは英語のみです。 

## <a name="submitting-data"></a>データの送信

API 要求を送信するには、言語リソースのエンドポイントとキーが必要です。

> [!NOTE]
> Azure portal で言語リソースのキーとエンドポイントを確認できます。 それらは、リソースの **[Key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 

要求が受信されると分析が実行されます。 1 分間および 1 秒間に送信できる要求のサイズと数については、以下のデータ制限をご覧ください。

REST API またはクライアント ライブラリを使って要求を送信すると、結果は非同期的に返されます。 Docker コンテナーを使っている場合は、同期的に返されます。  


## <a name="getting-results-from-the-feature"></a>機能からの結果の取得

API 要求と、Text Analytics for Health に送信するデータに応じて、次のように取得されます。

[!INCLUDE [Text Analytics for health features](../includes/features.md)]


この機能を非同期的に使うと、API の結果は、応答で示される要求取り込み時刻から 24 時間利用できます。 この時間が経過すると、結果は消去され、取得できなくなります。

## <a name="data-limits"></a>データ制限

> [!NOTE]
> * 制限を超えるドキュメントを分析する必要がある場合は、テキストを小さなチャンクに分割してから API に送信することができます。 最良の結果を得るには、テキストを文単位で分割してください。
> * ドキュメントとは、テキスト文字の 1 つの文字列です。  

| 制限 | 値 |
|------------------------|---------------|
| 1 つのドキュメントの最大サイズ | [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements) によって計測された 5,120 文字。 |
| 要求全体の最大サイズ | 1 MB |
| 要求あたりのドキュメントの最大数 | Web ベース API の場合は 10、コンテナーの場合は 1000 です。 |

ドキュメントが文字制限を超えた場合、最大サイズを超えるドキュメントは API によって処理されず、無効ドキュメント エラーが返されます。 API 要求に複数のドキュメントが含まれている場合、API は、それらが文字数制限内であれば処理を続行します。

### <a name="rate-limits"></a>転送率の制限

レート制限は[価格レベル](https://aka.ms/unifiedLanguagePricing)によって異なります。 これらの制限は、API の両方のバージョンで同じです。 これらのレート制限は、設定されたレート制限がない Text Analytics for Health コンテナーには適用されません。

| レベル          | 1 秒あたりの要求数 | 1 分あたりの要求数 |
|---------------|---------------------|---------------------|
| S/マルチサービス | 1000                | 1000                |
| F0         | 100                 | 300                 |

## <a name="see-also"></a>関連項目

* [Text Analytics for Health の概要](../overview.md)
* [Text Analytics for Health のエンティティのカテゴリ](../concepts/health-entity-categories.md)
