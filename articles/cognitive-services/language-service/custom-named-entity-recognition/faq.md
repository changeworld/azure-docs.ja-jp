---
title: カスタム固有表現認識 (NER) の FAQ
titleSuffix: Azure Cognitive Services
description: カスタム固有表現認識を使うときによくあるご質問について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 488d72987f97dd01842332f5d1d11739e878115d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439103"
---
# <a name="frequently-asked-questions-for-custom-named-entity-recognition"></a>カスタム固有表現認識についてよくあるご質問

Azure Cognitive Service for Language でのカスタム NER に関する概念とシナリオについてのよくあるご質問に対する回答を示します。

## <a name="how-do-i-get-started-with-the-service"></a>サービスの使用を開始するにはどうすればよいですか?

初めてのプロジェクトを手軽に作るには、[クイックスタート](./quickstart.md)に関する記事をご覧ください。詳しくは、[プロジェクトの作成方法](how-to/create-project.md)に関する記事をご覧ください。

## <a name="what-are-the-service-limits"></a>サービスの制限とは何ですか。

詳細については、[サービスの制限に関する記事](service-limits.md)をご覧ください。

## <a name="how-many-tagged-files-are-needed"></a>タグ付けされたファイルはどれくらい必要ですか?

一般に、タグ付けが正確に、一貫して、完全に行われていれば、[タグ付けされたデータ](how-to/tag-data.md)が多様で代表的であるほど、良い結果が得られます。 タグ付けされたインスタンスの数に、すべてのモデルのパフォーマンスを向上させる決まった数はありません。 パフォーマンスは、スキーマと、スキーマのあいまいさに大きく依存します。 あいまいなエンティティの種類には、より多くのタグが必要です。 パフォーマンスは、タグ付けの質にも依存します。 エンティティあたりのタグ付けされたインスタンスの推奨数は 50 です。

## <a name="training-is-taking-a-long-time-is-this-expected"></a>トレーニングに長い時間がかかりますが、これは予想されることですか?

トレーニング プロセスには長時間かかることがあります。 大まかな見積もりとして、合計の長さが 12,800,000 文字のファイルの予想されるトレーニング時間は 6 時間です。

## <a name="how-do-i-build-my-custom-model-programmatically"></a>カスタム モデルをプログラムから作成するにはどうすればよいでしょうか?

[REST API](https://aka.ms/ct-authoring-swagger) を使用して、カスタム モデルを構築できます。 オーサリング API を呼び出す方法の例については、この[クイックスタート](quickstart.md?pivots=rest-api)に従って、プロジェクトの作成と API を使用したモデルの作成を開始します。

## <a name="what-is-the-recommended-cicd-process"></a>推奨される CI/CD プロセスを教えてください

同じプロジェクト内の同じデータセットで複数のモデルをトレーニングできます。 モデルをうまくトレーニングしたら、[その評価を表示](how-to/view-model-evaluation.md)できます。 [Language Studio](https://aka.ms/languageStudio) 内でモデルを[デプロイしてテスト](quickstart.md#deploy-your-model)できます。 データに対してタグを追加または削除し、**新しい** モデルをトレーニングしてテストすることもできます。 同じプロジェクトを使用したトレーニング済みモデルの最大数については、[サービスの制限](service-limits.md)を参照してください。 新しいモデルをトレーニングする場合、データセットはトレーニング セットとテスト セットにランダムに[分割](how-to/train-model.md#data-split)されます。したがって、反映されたモデル評価の対象が同じテスト セットである保証はないので、結果は比較できません。 改善を測定できるよう、独自のテスト セットを開発し、それを使用して両方のモデルを評価することをお勧めします。

## <a name="does-a-low-or-high-model-score-guarantee-bad-or-good-performance-in-production"></a>モデル スコアの高さまたは低さは、実稼働環境でのパフォーマンスの高さや低さを保証しますか?

モデルの評価が常に包括的であるとは限りません。 次の要素に左右されます。
* **テスト セット** が小さすぎる場合、良いスコアと悪いスコアはモデルの実際のパフォーマンスを示す値にはなりません。 また、特定のエンティティ型がテスト セットに存在しない場合や出現率が低い場合、その点がモデルのパフォーマンスに影響します。
* **データの多様性** は、データが実稼働環境で予想されるテキストのシナリオや例の一部のみを対象とし、モデルが可能性があるすべてのシナリオの影響を受けなかった場合、トレーニング対象でなかったシナリオではパフォーマンスが低い可能性があります。
* **データ表現** は、モデルのトレーニングに使用されるデータセットが、実稼働環境でモデルに導入されるデータを表すものでない場合、モデルのパフォーマンスは大きく影響を受ける可能性があります。

詳細については、[データの選択とスキーマ設計](how-to/design-schema.md)に関する記事を参照してください。

## <a name="how-do-i-improve-model-performance"></a>モデルのパフォーマンスを向上させるにはどうすればよいですか?

* モデルの[混同行列](how-to/view-model-evaluation.md)をご覧ください。 特定のエンティティ型が正しく予測されないことが多い場合は、このクラスのタグ付けされたインスタンスを追加することを検討してください。 2 つのエンティティ型の予測結果が互いを表すことが多い場合、これはスキーマがあいまいであることを意味し、パフォーマンスを向上させるために、両方を 1 つのエンティティ型に統合することを検討する必要があります。

* [データ分散を調べます](how-to/improve-model.md#examine-data-distribution)。 いずれか 1 つのエンティティ型に他のエンティティ型よりもはるかに多くのタグ付けされたインスタンスがある場合、モデルはこの型に偏っている可能性があります。 他のエンティティ型にデータを追加するか、偏りのある型から例を削除します。

* [データの選択とスキーマの設計](how-to/design-schema.md)の詳細をご覧ください。

* [テスト セットを調べて](how-to/improve-model.md)、予測されたエンティティとタグ付けされたエンティティを並べて表示すると、モデルのパフォーマンスをよりよく理解し、スキーマまたはタグの変更が必要かどうかを判断できます。

## <a name="why-do-i-get-different-results-when-i-retrain-my-model"></a>モデルを再トレーニングしたときに異なる結果が得られるのはなぜですか?

* 新しいモデルをトレーニングする場合、データセットはトレーニング セットとテスト セットにランダムに[分割](how-to/train-model.md#data-split)されます。したがって、反映されたモデル評価が同じテスト セットに対する保証はないので、結果は比較できません。

* 同じモデルを再トレーニングする場合、テスト セットは変わりませんが、モデルによって行われる予測にわずかな変化が見られることがあります。 これはトレーニング済みモデルのロバストネスが十分ではないためであり、データの表現と多様性、そしてタグ付けされたデータの質の要因となります。

## <a name="how-do-i-get-predictions-in-different-languages"></a>異なる言語で予測を取得するにはどうすればよいですか?

まず、[プロジェクトの作成](how-to/create-project.md)時に多言語オプションを有効にする必要があります。または、後でプロジェクト設定ページから有効にすることもできます。 モデルをトレーニングしてデプロイした後は、[複数の言語](language-support.md#multiple-language-support)でモデルのクエリを開始できます。 言語によって結果が異なる場合があります。 任意の言語の精度を向上させるには、その言語でプロジェクトにタグ付きインスタンスを追加して、その言語のさらに多くの構文にトレーニング済みモデルを導入します。

## <a name="i-trained-my-model-but-i-cant-test-it"></a>モデルをトレーニングしましたが、テストできません

テストする前に、[モデルをデプロイする](quickstart.md#deploy-your-model)必要があります。 

## <a name="how-do-i-use-my-trained-model-for-the-prediction-api"></a>トレーニング済みのモデルを予測 API に使用するにはどうすればよいですか?

モデルをデプロイした後、[予測 API を呼び出します](how-to/call-api.md)。 詳細については、[予測 API のリファレンス](https://aka.ms/ct-runtime-swagger)をご覧ください。

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

カスタム NER は、一般データ保護規則 (GDPR) を目的とするデータ プロセッサです。 GDPR ポリシーに準拠したカスタム NER ユーザーは、[Language Studio](https://aka.ms/languageStudio) を使用するか、[REST API](https://aka.ms/ct-authoring-swagger) を使用してプログラムによって、すべてのユーザー コンテンツを表示、エクスポート、または削除するように全面的に制御できます。

データは、Azure ストレージ アカウントにのみ格納されます。 トレーニング中は、そこから読み取るためのアクセス権だけがカスタム NER に与えられます。

## <a name="how-to-clone-my-project"></a>プロジェクトをクローンする方法を教えてください

プロジェクトをクローンするためには、エクスポート API  を使用してプロジェクト アセットをエクスポートし、それを新しいプロジェクトにインポートする必要があります。 両方の操作については、[REST API](https://aka.ms/ct-authoring-swagger) リファレンスを参照してください。

## <a name="next-steps"></a>次のステップ

* [カスタム NER の概要](overview.md)
* [クイック スタート](quickstart.md)
