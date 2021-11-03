---
title: 抽出要約 API を使用してテキストを要約する
titleSuffix: Azure Cognitive Services
description: この記事では、抽出要約 API を使ってテキストを要約する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-summarization, ignite-fall-2021
ms.openlocfilehash: 21be719780b4633456ceea89c0cb6c6295480348
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092643"
---
# <a name="how-to-use-text-summarization-preview"></a>テキスト要約を使用する方法 (プレビュー)

> [!IMPORTANT] 
> 抽出要約機能は、"現状のまま"、"保証なしで" 提供されるプレビュー機能です。 そのため、抽出要約 (プレビュー) は、運用環境に実装またはデプロイして使用しないでください。 お客様は、抽出要約の使用に関するすべての責任を持ちます。 

一般に、テキストの自動要約には、抽出と抽象化の 2 つの方法があります。 この API では、抽出要約が提供されます。

抽出要約機能を使うと、元のコンテンツ内で最も重要性または関連性の高い情報をまとめて表す文を抽出することで、概要が生成されます。

この機能は、ユーザーが読むには長すぎると考えられるコンテンツを短縮するように設計されています。 抽出要約では、記事、論文、またはドキュメントが重要な文に要約されます。

API で使用される AI モデルはサービスによって提供されるため、分析対象のコンテンツを送信するだけで済みます。

## <a name="features"></a>特徴

> [!TIP]
> [クイック スタートの記事](../quickstart.md)に従って、この機能を使い始めることができます。 また、[Language Studio](../../language-studio.md) を使うと、コードを記述しなくても、要求の例を作成できます。

抽出要約 API では、自然言語処理手法を使用して、非構造化テキスト ドキュメント内の重要な文が検索されます。 これらの文がまとめられて、ドキュメントの主要なアイデアが伝えられます。

抽出要約では、抽出された文と元のドキュメント内でのその位置と共に、システム応答の一部としてランク スコアが返されます。 ランク スコアは、ドキュメントの主要なアイデアに対して文がどの程度関係していると判断されるのかの指標です。 モデルでは、各文に対して 0 から 1 (両端を含む) のスコアが与えられ、要求ごとに最も高いスコアの文が返されます。 たとえば、3 文の要約を要求した場合、サービスからはスコアが最も高い 3 つの文が返されます。

Azure Cognitive Service for Language には、[キー フレーズ抽出](./../../key-phrase-extraction/how-to/call-api.md)というもう 1 つの機能があり、重要な情報を抽出できます。 キー フレーズ抽出と抽出要約のどちらを使用するかを決めるときは、次の点を考慮します。
* キー フレーズ抽出からは語句が返されるのに対し、抽出要約からは文が返されます
* 抽出要約からは、文と共にランク スコアが返されます。 要求ごとに、ランクが上位の文が返されます

## <a name="determine-how-to-process-the-data-optional"></a>データの処理方法を決定する (省略可能)

### <a name="specify-the-text-summarization-model"></a>テキスト要約モデルを指定する

既定では、テキストで利用できる最新の AI モデルがテキスト要約によって使われます。 特定のバージョンのモデルを使うように API 要求を構成することもできます。 指定したモデルを使って、テキスト要約操作が行われます。

| サポートされているバージョン | 最新バージョン |
|--|--|
| `2021-08-01` | `2021-08-01` |

### <a name="input-languages"></a>入力言語

キー フレーズ抽出で処理されるドキュメントを送信するときに、[サポートされている言語](../language-support.md)のどれでそれが書かれているかを指定できます。 言語を指定しないと、英語がキー フレーズ抽出によって既定で使われます。 さまざまな[多言語と絵文字のエンコード](../../concepts/multilingual-emoji-support.md)をサポートするため、API からの応答でオフセットを返すことができます。 

## <a name="submitting-data"></a>データの送信

ドキュメントをテキストの文字列として API に送信します。 要求が受信されると分析が実行されます。 API は非同期なので、API 要求を送信してから、結果を受信するまでに、遅延が発生する可能性があります。  1 分間および 1 秒間に送信できる要求のサイズと数については、以下のデータ制限をご覧ください。

この機能を使うと、API の結果は、応答で示される要求取り込み時刻から 24 時間利用できます。 この時間が経過すると、結果は消去され、取得できなくなります。

`sentenceCount` パラメーターを使って、返される文の数を指定できます。既定値は `3` です。 範囲は 1 から 20 です。

また、`sortby` パラメーターを使うと、抽出された文が返されるときの順序を指定できます。値は `Offset` または `Rank` で、既定値は `Offset` です。 


|パラメーターの値  |説明  |
|---------|---------|
|順位    | サービスによって決定された入力ドキュメントとの関連性に従って文を並べ替えます。        |
|Offset    | 入力ドキュメントで文が出現する元の順序を維持します。        |

## <a name="getting-text-summarization-results"></a>テキストの要約結果を取得する

言語検出から結果を取得するときは、結果をアプリケーションにストリーミングしたり、ローカル システム上のファイルに出力を保存したりできます。

次に示すのは、要約のために送信するコンテンツの例です。これは、Microsoft ブログ記事「[統合 AI のために包括的な表現](https://www.microsoft.com/research/blog/a-holistic-representation-toward-integrative-ai/)」を使って抽出されたものです。 この記事は一例にすぎず、API ははるかに長い入力テキストを受け入れることができます。 詳しくは、データの制限に関するセクションをご覧ください。
 
*"At Microsoft, we have been on a quest to advance AI beyond existing techniques, by taking a more holistic, human-centric approach to learning and understanding. As Chief Technology Officer of Azure AI Cognitive Services, I have been working with a team of amazing scientists and engineers to turn this quest into a reality. In my role, I enjoy a unique perspective in viewing the relationship among three attributes of human cognition: monolingual text (X), audio or visual sensory signals, (Y) and multilingual (Z). At the intersection of all three, there’s magic—what we call XYZ-code as illustrated in Figure 1—a joint representation to create more powerful AI that can speak, hear, see, and understand humans better. We believe XYZ-code will enable us to fulfill our long-term vision: cross-domain transfer learning, spanning modalities and languages. The goal is to have pretrained models that can jointly learn representations to support a broad range of downstream AI tasks, much in the way humans do today. Over the past five years, we have achieved human performance on benchmarks in conversational speech recognition, machine translation, conversational question answering, machine reading comprehension, and image captioning. These five breakthroughs provided us with strong signals toward our more ambitious aspiration to produce a leap in AI capabilities, achieving multisensory and multilingual learning that is closer in line with how humans learn and understand. I believe the joint XYZ-code is a foundational component of this aspiration, if grounded with external knowledge sources in the downstream AI tasks."*

抽出要約 API は、要求を受信した時点で API バックエンド用のジョブを作成することにより実行されます。 ジョブが成功すると、API の出力が返されます。 出力は 24 時間取得できます。 この時間が過ぎると、出力は消去されます。 多言語と絵文字のサポートにより、応答にはテキスト オフセットが含まれる場合があります。 詳細については[オフセットの処理方法](../../concepts/multilingual-emoji-support.md)に関するページを参照してください。

上記の例を使うと、API から次のような要約された文が返される可能性があります。

*"At Microsoft, we have been on a quest to advance AI beyond existing techniques, by taking a more holistic, human-centric approach to learning and understanding."*

*"In my role, I enjoy a unique perspective in viewing the relationship among three attributes of human cognition: monolingual text (X), audio or visual sensory signals, (Y) and multilingual (Z)."*

*"At the intersection of all three, there’s magic—what we call XYZ-code as illustrated in Figure 1—a joint representation to create more powerful AI that can speak, hear, see, and understand humans better."*


## <a name="data-limits"></a>データ制限

このセクションでは、Text Summarization API に送信できるデータのサイズとレートの制限について説明します。 価格は、データ制限またはレート制限による影響を受けないことに注意してください。 価格は、言語リソースの[価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)に従います。

> [!NOTE]
> * 制限を超えるドキュメントを分析する必要がある場合は、テキストを小さなチャンクに分割してから API に送信することができます。 
> * ドキュメントとは、テキスト文字の 1 つの文字列です。  

| 制限 | 値 |
|------------------------|---------------|
| 要求あたりの最大文字数  | 送信ドキュメント全体で 125,000 文字。[StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements) によって計測されます。 |
| 要求あたりのドキュメントの最大数 | 25 |

ドキュメントが制限を超えると、要求内のいずれかのドキュメントが最大サイズを超えている場合、API によってその全体が拒否されて、`400 bad request` エラーが返されます。

### <a name="rate-limits"></a>転送率の制限

レート制限は[価格レベル](https://aka.ms/unifiedLanguagePricing)によって異なります。

| レベル          | 1 秒あたりの要求数 | 1 分あたりの要求数 |
|---------------|---------------------|---------------------|
| S/マルチサービス | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |

## <a name="see-also"></a>関連項目

* [テキストの概要作成の概要](../overview.md)
