---
title: Text Analytics とは
titleSuffix: Azure Cognitive Services
description: センチメント分析、キー フレーズ抽出、言語検出、エンティティのリンクのための Azure Cognitive Services の Text Analytics。
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: overview
ms.date: 10/01/2018
ms.author: ashmaka
ms.openlocfilehash: 0fe4a9f05e0f6d1abed7b906cc5cd89854885ae5
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2019
ms.locfileid: "53992857"
---
# <a name="what-is-text-analytics"></a>Text Analytics とは

Text Analytics API は、未加工のテキストに対して高度な自然言語処理を実行できるクラウドベースのサービスであり、主要な機能として感情分析、キー フレーズ抽出、言語検出、エンティティ リンク設定の 4 つを備えています。

この API を支援するのが [Microsoft Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/) のリソースです。これは機械学習と AI のアルゴリズムをクラウドに集めたものであり、開発プロジェクトですぐに利用できます。

## <a name="capabilities-in-text-analytics"></a>テキスト分析の機能

テキスト分析にはさまざまな意味がありますが、Cognitive Services の場合、Text Analytics API は次の表に示す 4 種類の分析を提供します。

| 操作| 説明 | API |
|-----------|-------------|------|
|[**感情分析**](how-tos/text-analytics-how-to-sentiment-analysis.md) | 未加工のテキストを分析し、正または負の感情を見抜くための手がかりを求めることで、ブランドまたはトピックに対して顧客がどう思っているのかを突き止めます。 この API はドキュメントごとに 0 から 1 までの感情スコアを返します。1 が最も肯定的となります。<br /> 分析モデルは、広範囲にわたるテキスト本文と Microsoft の自然言語技術を利用して事前トレーニングされています。 [一部の言語](text-analytics-supported-languages.md)については、この API はユーザーが指定したあらゆる未加工テキストを分析し、評価し、呼び出し元のアプリケーションに結果を直接返すことができます。 | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) <br /> [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package)  |
|[**キー フレーズ抽出**](how-tos/text-analytics-how-to-keyword-extraction.md) | 重要なフレーズを自動的に抽出し、重要な点を迅速に特定します。 たとえば、「食べ物はおいしくて、すばらしいスタッフがいた」というテキストを入力すると、この API は話題の中心として "食べ物" と "すばらしいスタッフ" を返します。  | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) <br /> [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) |
|[**言語検出**](how-tos/text-analytics-how-to-language-detection.md) | 最大 120 の言語に対して、入力テキストが書かれている言語を検出し、要求で送信されたドキュメントごとに 1 つの言語コードを報告します。 言語コードは、評価値の強度を示すスコアと組みになります。 | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) <br />  [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) |
|[**エンティティの認識 (プレビュー)**](how-tos/text-analytics-how-to-entity-linking.md) | テキスト内のエンティティを識別し、人、場所、組織、日付/時刻、数量、パーセンテージ、通貨、その他に分類します。 既知のエンティティも識別され、Web 上の詳細ページにリンクされます。 | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) |

## <a name="use-containers"></a>コンテナーの使用

キー フレーズの抽出、言語の検出、および感情の分析をローカルに行うには、標準化された Docker コンテナーをデータの近くにインストールして、[Text Analytics コンテナーを使用](how-tos/text-analytics-how-to-install-containers.md)します。

## <a name="typical-workflow"></a>一般的なワークフロー

ワークフローは単純です。ユーザーが分析のためにデータを送信し、コードで出力を処理します。 アナライザーはそのままの状態で利用されます。構成やカスタマイズの追加はありません。

1. [新規登録](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)して[アクセス キー](how-tos/text-analytics-how-to-access-key.md)を入手します。 要求のたびにこのキーを渡す必要があります。

2. 未加工の非構造化テキストとしてデータを含む[要求を JSON で表します](how-tos/text-analytics-how-to-call-api.md#json-schema)。

3. 任意のリソース (感情分析、キー フレーズ抽出、言語検出、エンティティ識別) を付加し、新規登録時に確立されたエンドポイントに要求を投稿します。

4. 応答をローカルでストリームまたは保存します。 要求に基づき、結果は感情スコア、抽出されたキー フレーズのコレクション、または言語コードになります。

ID をベースに出力は 1 つの JSON ドキュメントとして返されます。投稿したテキスト ドキュメントごとに結果が返されます。 その後、結果を分析、視覚化、または分類し、実用的な情報を得ることができます。

データはユーザーのアカウントに保存されません。 Text Analytics API で実行された操作はステートレスです。つまり、入力したテキストが処理され、結果がすぐに返されます。

<a name="supported-languages"></a>

## <a name="supported-languages"></a>サポートされている言語

このセクションは見つけやすいように別の記事に移されました。 このコンテンツについては、「[Supported languages in Text Analytics API](text-analytics-supported-languages.md)」 (Text Analytics API でサポートされている言語) を参照してください。

<a name="data-limits"></a>

## <a name="data-limits"></a>データ制限

Text Analytics API のエンドポイントはすべて、未加工のテキスト データを受け取ります。 現在の上限はドキュメントあたり 5,000 文字です。それより大きなドキュメントを分析する必要がある場合は小さなまとまりに分割できます。 分割ではなく、上限を増やす必要がある場合は[お問い合わせください](https://azure.microsoft.com/overview/sales-number/)。要件について説明いたします。

| 制限 | 値 |
|------------------------|---------------|
| 1 つのドキュメントの最大サイズ | `String.Length` で測定される 5,000 文字。 |
| 要求全体の最大サイズ | 1 MB |
| 1 件の要求での最大ドキュメント数 | 1,000 ドキュメント |

1 分あたりの呼び出し回数上限は 100 回です。 1 回の呼び出しで大量のドキュメントを送信できることにご注目ください (最大 1,000 ドキュメント)。

## <a name="unicode-encoding"></a>Unicode エンコーディング

Text Analytics API では、テキストの表現と文字数の計算に Unicode エンコーディングが使用されます。 要求は UTF-8 と UTF-16 の両方で提出できます。文字数に測定可能な違いはありません。 Unicode コードポイントは文字の長さのヒューリスティックとして使用され、テキスト分析データを制限するという目的では同等と見なされます。 [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) を利用して文字数を取得する場合、データ サイズの測定に使用しているものと同じ方法を使用することになります。

## <a name="next-steps"></a>次の手順

まず、[対話型デモ](https://azure.microsoft.com/services/cognitive-services/text-analytics/)をお試しください。 テキスト入力 (最大 5,000 文字) を貼り付け、言語を検出したり (最大 120)、感情スコアを算出したり、キー フレーズを抽出したりできます。 新規登録の必要ありません。

API を直接呼び出す準備ができたら:

+ [新規登録](how-tos/text-analytics-how-to-signup.md)してアクセス キーを入手し、[API の呼び出し](how-tos/text-analytics-how-to-call-api.md)手順を確認します。

+ [こちらのクイック スタート](quickstarts/csharp.md)は C# で記述された REST API 呼び出しのチュートリアルです。 テキストの送信方法、分析の選択方法、最小コードで結果を表示する方法について学習してください。

+ [API リファレンス ドキュメント](//go.microsoft.com/fwlink/?LinkID=759346)には、API に関する技術文書があります。 ドキュメントには呼び出しが埋め込まれています。各ドキュメント ページから API を呼び出すことができます。

+ [こちらの外部/コミュニティ コンテンツ](text-analytics-resource-external-community.md)紹介ページには、Text Analytics を他のツールやテクノロジと共に使用する方法を実演したブログ投稿や動画の一覧があります。

## <a name="see-also"></a>関連項目

 [Cognitive Services のドキュメント ページ](https://docs.microsoft.com/azure/cognitive-services/)
