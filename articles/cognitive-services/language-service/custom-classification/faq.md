---
title: カスタム テキスト分類に関してよくあるご質問
titleSuffix: Azure Cognitive Services
description: カスタム テキスト分類 API を使用する場合によくあるご質問について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 9320fee8eb7ad44b33246f28091dfb0b4bd72bb2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434391"
---
# <a name="frequently-asked-questions"></a>よく寄せられる質問

Azure Cognitive Service for Language でのカスタム テキスト分類に関する概念とシナリオについてのよくあるご質問に対する回答を示します。

## <a name="how-do-i-get-started-with-the-service"></a>サービスの使用を開始する操作方法

初めてのプロジェクトを手軽に作るには、[クイックスタート](./quickstart.md)に関する記事をご覧ください。詳しくは、[プロジェクトの作成方法](how-to/create-project.md)に関する記事をご覧ください。

## <a name="what-are-the-service-limits"></a>サービスの制限とは何ですか。

詳細については、[サービスの制限に関する記事](service-limits.md)をご覧ください。

## <a name="which-languages-are-supported-in-this-feature"></a>この機能でサポートされている言語は何ですか?

[言語のサポート](./language-support.md)に関する記事をご覧ください。

## <a name="how-many-tagged-files-are-needed"></a>タグ付けされたファイルはどれくらい必要ですか?

一般に、タグ付けが正確に、一貫して、完全に行われていれば、[タグ付けされたデータ](how-to/tag-data.md)が多様で代表的であるほど、良い結果が得られます。 タグ付けされたクラスの数に、すべてのモデルのパフォーマンスを向上させる決まった数はありません。 パフォーマンスは、スキーマと、スキーマのあいまいさに大きく依存します。 あいまいなクラスには、より多くのタグが必要です。 パフォーマンスは、タグ付けの質にも依存します。 クラスあたりのタグ付けされたインスタンスの推奨数は 50 です。 

## <a name="training-is-taking-a-long-time-is-this-expected"></a>トレーニングに長い時間がかかりますが、これは予想されることですか?

トレーニング プロセスには時間がかかる場合があります。 大まかな見積もりとして、合計の長さが 12,800,000 文字のファイルの予想されるトレーニング時間は 6 時間です。

## <a name="how-do-i-build-my-custom-model-programmatically"></a>カスタム モデルをプログラムでビルドする操作方法

[REST API](https://aka.ms/ct-authoring-swagger) を使用して、カスタム モデルを構築できます。 オーサリング API を呼び出す方法の例については、この[クイックスタート](quickstart.md?pivots=rest-api)に従って、プロジェクトの作成と API を使用したモデルの作成を開始します。 


## <a name="what-is-the-recommended-cicd-process"></a>推奨される CI/CD プロセスは何ですか?

同じプロジェクト内の同じデータセットで複数のモデルをトレーニングできます。 モデルをうまくトレーニングしたら、[その評価を表示](how-to/view-model-evaluation.md)できます。 [Language Studio](https://aka.ms/languageStudio) 内でモデルを[デプロイしてテスト](quickstart.md#deploy-your-model)できます。 データに対してタグを追加または削除し、**新しい** モデルをトレーニングしてテストすることもできます。 [サービスの制限](service-limits.md)を表示して、同じプロジェクトのトレーニング済みモデルの最大数について学習します。 新しいモデルをトレーニングすると、データセットはトレーニング セットとテスト セットにランダムに[分割](how-to/train-model.md#data-splits)されます。 そのため、モデルの評価が同じテスト セットで実行される保証はないので、結果は比較できません。 改善を測定できるよう、独自のテスト セットを開発し、それを使用して両方のモデルを評価する方法をお勧めします。

## <a name="does-a-low-or-high-model-score-guarantee-bad-or-good-performance-in-production"></a>低いモデル スコアまたは高いモデル スコアは、実稼働環境でのパフォーマンスの低下または良好なパフォーマンスを保証しますか?

モデルの評価が常に包括的とは限りない場合があります。 これは、次に依存します。 
* **テスト セット** が小さすぎる場合、良いスコアと悪いスコアはモデルの実際のパフォーマンスを示す値にはなりません。 また、テスト セットで特定のクラスが欠落しているか、表されていない場合は、モデルのパフォーマンスに影響します。
* **データの多様性** は、データが実稼働環境で予想されるテキストのシナリオや例の一部のみを対象とし、モデルが可能性があるすべてのシナリオの影響を受けなかった場合、トレーニング対象でなかったシナリオではパフォーマンスが低い可能性があります。
* **データ表現** は、モデルのトレーニングに使用されるデータセットが、実稼働環境でモデルに導入されるデータを表すものでない場合、モデルのパフォーマンスは大きく影響を受ける可能性があります。

詳細については、[データの選択とスキーマ設計](how-to/design-schema.md)に関する記事を参照してください。

## <a name="how-do-i-improve-model-performance"></a>モデルのパフォーマンスを向上させるにはどうすればよいですか?

* モデル[混同行列](how-to/view-model-evaluation.md)を表示します。特定のクラスが誤って分類される頻度が高い場合は、このクラスにタグ付けされたインスタンスを追加する方法を検討してください。 2 つのクラスが互いに分類される頻度が高い場合は、スキーマがあいまいな場合は、両方を 1 つのクラスにマージしてパフォーマンスを向上させるのを検討してください。

*  [データ分散を調べ](how-to/improve-model.md#examine-data-distribution-from-language-studio)ます。クラスの 1 つが他のクラスよりもはるかに多くのタグ付けされたインスタンスを持っている場合、モデルはこのクラスに偏っている可能性があります。 他のクラスにデータを追加するか、ほとんどの例を支配クラスから削除します。 

* データの選択とスキーマの設計の詳細は、[ここ](how-to/design-schema.md)で参照できます。

* [テスト セットを調べて](how-to/improve-model.md)、予測されたクラスとタグ付けされたクラスを並べて表示すると、モデルのパフォーマンスをよりよく理解し、スキーマまたはタグの変更が必要かどうかを判断できます。

## <a name="when-i-retrain-my-model-i-get-different-results-why-is-this"></a>モデルを再トレーニングすると、異なる結果が得られますが、なぜですか?

* 新しいモデルをトレーニングする場合、データセットはトレーニング セットとテスト セットにランダムに[分割](how-to/train-model.md#data-splits)されます。したがって、反映されたモデル評価が同じテスト セットに対する保証はないので、結果は比較できません。

* 同じモデルを再トレーニングする場合、テスト セットは同じですが、モデルによって行われた予測のわずかな変化に気付く場合があります。 これは、トレーニング済みのモデルが十分に堅牢でないためです。これは、データの代表的な明確さ、およびタグ付けされたデータの品質の要因です。 

## <a name="how-do-i-get-predictions-in-different-languages"></a>異なる言語で予測を取得するにはどうすればよいですか?

まず、[プロジェクトの作成](how-to/create-project.md)時に多言語オプションを有効にする必要があります。または、後でプロジェクト設定ページから有効にすることもできます。 モデルをトレーニングしてデプロイした後は、[複数の言語](language-support.md#multiple-language-support)でモデルのクエリを開始できます。 言語によって結果が異なる場合があります。 任意の言語の精度を向上させるには、その言語でプロジェクトにタグ付きインスタンスを追加して、その言語のさらに多くの構文にトレーニング済みモデルを導入します。

## <a name="i-trained-my-model-but-i-cant-test-it"></a>モデルをトレーニングしましたが、テストできません

テストする前に、[モデルをデプロイする](quickstart.md#deploy-your-model)必要があります。 

## <a name="how-do-i-use-my-trained-model-to-make-predictions"></a>トレーニング済みのモデルを予測にどのように使用する操作方法

モデルをデプロイした後、[予測 API を呼び出します](how-to/call-api.md)。 詳細については、[予測 API のリファレンス](https://aka.ms/ct-runtime-swagger)をご覧ください。

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

カスタム テキスト分類は、データ処理 (GDPR) 一般データ保護規則データ プロセッサです。 GDPR ポリシーに準拠したカスタム分類ユーザーは、[Language Studio](https://aka.ms/languageStudio) を使用するか、[REST API](https://aka.ms/ct-authoring-swagger) を使用してプログラムによって、すべてのユーザー コンテンツを表示、エクスポート、または削除するように全面的に制御できます。

データは、Azure Storage アカウントにのみ格納されます。 カスタム分類は、トレーニング中にそこから読み取るアクセス権のみを持っています。

## <a name="how-to-clone-my-project"></a>プロジェクトを複製する方法

プロジェクトを複製するには、エクスポート API を使用してプロジェクト アセットをエクスポートし、新しいプロジェクトにインポートする必要があります。 両方の操作については、[REST API](https://aka.ms/ct-authoring-swagger) リファレンスを参照してください。

## <a name="next-steps"></a>次のステップ

* [カスタム テキスト分類の概要](overview.md)
* [クイック スタート](quickstart.md)
