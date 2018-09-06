---
title: Text Analytics の概要 - Azure Cognitive Services | Microsoft Docs
description: センチメント分析、キー フレーズ抽出、言語検出、エンティティのリンクのための Azure Cognitive Services の Text Analytics。
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 8/30/2018
ms.author: ashmaka
ms.openlocfilehash: 71af2bcbf58279681bbea66d4f76e951a2efce59
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2018
ms.locfileid: "43341488"
---
# <a name="what-is-text-analytics"></a>Text Analytics とは

Text Analytics サービスは、未加工の非構造化テキストに対して高度な自然言語処理機能を提供しています。 主にセンチメント分析、キーフレーズ抽出、言語検出、エンティティのリンクという 4 つの機能があります。

## <a name="analyze-sentiment"></a>センチメントを分析する

未加工のテキストを分析し、正または負の感情を見抜くための手がかりを求めることで、ブランドまたはトピックに対して顧客がどう思っているのかを[突き止めます](how-tos/text-analytics-how-to-sentiment-analysis.md)。 この API はドキュメントごとに 0 から 1 までの感情スコアを返します。1 が最も肯定的となります。<br />
分析モデルは、広範囲にわたるテキスト本文と Microsoft の自然言語技術を利用して事前トレーニングされています。 [一部の言語](text-analytics-supported-languages.md)では、指定した未加工のテキストを API で分析してスコアを付けることができます。

## <a name="extract-key-phrases"></a>キー フレーズを抽出する

[キー フレーズを自動的に抽出](how-tos/text-analytics-how-to-keyword-extraction.md)し、重要な点を迅速に特定します。 たとえば、「食べ物はおいしくて、すばらしいスタッフがいた」というテキストを入力すると、Text Analytics サービスは話題の中心として "食べ物" と "すばらしいスタッフ" を返します。

## <a name="detect-language"></a>言語を検出する

最大 120 の言語に対して、入力テキストが書かれている言語を[検出](how-tos/text-analytics-how-to-language-detection.md)し、要求で送信されたドキュメントごとに 1 つの言語コードを報告します。 言語コードは、評価値の強度を示すスコアと組みになります。

## <a name="identify-linked-entities-preview"></a>リンクされているエンティティを特定する (プレビュー)

テキストに含まれる既知のエンティティを[特定](how-tos/text-analytics-how-to-entity-linking.md)し、Web 上の詳細ページにリンクします。 エンティティ リンク設定では、区別可能なエンティティ、動詞、その他の単語形式として使用されている言葉が認識され、あいまいさが取り除かれます。

## <a name="typical-workflow"></a>一般的なワークフロー

ワークフローは単純です。ユーザーが分析のためにデータを送信し、コードで出力を処理します。 アナライザーはそのままの状態で利用されます。構成やカスタマイズの追加はありません。

1. [新規登録](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)して[アクセス キー](how-tos/text-analytics-how-to-access-key.md)を入手します。 要求のたびにこのキーを渡す必要があります。

2. 未加工の非構造化テキストとしてデータを含む[要求を JSON 形式で作成します](how-tos/text-analytics-how-to-call-api.md#json-schema)。

3. 新規登録時に確立されたエンドポイントに、呼び出したい API (センチメント分析、キー フレーズ抽出、言語検出、エンティティの特定) を付加して要求を投稿します。

4. 応答をローカルでストリームまたは保存します。 要求に基づき、結果は感情スコア、抽出されたキー フレーズのコレクション、または言語コードになります。

ID をベースに出力は 1 つの JSON ドキュメントとして返されます。投稿したテキスト ドキュメントごとに結果が返されます。 その結果を分析、視覚化、または分類し、実用的な情報を得ることができます。

Text Analytics サービスによって実行される操作は、ステートレスです。 データはユーザーのアカウントに保存されません。

<a name="data-limits"></a>

## <a name="specifications"></a>仕様

### <a name="supported-languages"></a>サポートされている言語

[Text Analytics でサポートされている言語](text-analytics-supported-languages.md)に関するページを参照してください。

### <a name="data-limits"></a>データ制限

Text Analytics サービスのエンドポイントはすべて、未加工のテキスト データを受け取ります。 現在の上限はドキュメントあたり 5,000 文字です。それより大きなドキュメントを分析する必要がある場合は小さなまとまりに分割できます。 分割ではなく、上限を増やす必要がある場合は[お問い合わせください](https://azure.microsoft.com/overview/sales-number/)。要件について説明いたします。

| 制限 | 値 |
|------------------------|---------------|
| 1 つのドキュメントの最大サイズ | `String.Length` で測定される 5,000 文字。 |
| 要求全体の最大サイズ | 1 MB |
| 1 件の要求での最大ドキュメント数 | 1,000 ドキュメント |

1 分あたりの呼び出し回数上限は 100 回です。 1 回の呼び出しで大量のドキュメントを送信できます (最大 1,000 ドキュメント)。

### <a name="unicode-encoding"></a>Unicode エンコーディング

Text Analytics サービスでは、テキストの表現と文字数の計算に Unicode エンコーディングが使用されます。 UTF-8 または UTF-16 のいずれかで要求を送信できますが、文字数に測定可能な違いはありません。 `String.Length` を利用して文字数を取得する場合、データ サイズの測定に使用されているものと同じ方法を使用することになります。

## <a name="next-steps"></a>次の手順

まず、[対話型デモ](https://azure.microsoft.com/services/cognitive-services/text-analytics/)をお試しください。 テキスト入力 (最大 5,000 文字) を貼り付け、言語の検出 (最大 120)、センチメント スコアの算出、キー フレーズの抽出、リンクされているエンティティの特定を行うことができます。 新規登録の必要はありません。

Text Analytics サービスを直接呼び出す準備ができたら、次の手順を実行します。

+ [新規登録](how-tos/text-analytics-how-to-signup.md)してアクセス キーを入手し、[API の呼び出し](how-tos/text-analytics-how-to-call-api.md)手順を確認します。

+ [こちらのクイック スタート](quickstarts/csharp.md)は C# で記述された REST API 呼び出しのチュートリアルです。 テキストの送信方法、分析の選択方法、最小コードで結果を表示する方法について学習してください。

+ [API リファレンス ドキュメント](//go.microsoft.com/fwlink/?LinkID=759346)には、REST API に関する技術文書があります。 ドキュメントには呼び出しが埋め込まれているため、各ドキュメント ページから API を呼び出すことができます。

+ [こちらの外部/コミュニティ コンテンツ](text-analytics-resource-external-community.md)紹介ページには、Text Analytics を他のツールやテクノロジと共に使用する方法を実演したブログ投稿や動画の一覧があります。

## <a name="see-also"></a>関連項目

 [Cognitive Services のドキュメント ページ](https://docs.microsoft.com/azure/cognitive-services/)
