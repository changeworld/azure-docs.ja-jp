---
title: Azure Cognitive Service for Language (プレビュー) のカスタム固有表現認識 (NER) とは
titleSuffix: Azure Cognitive Services
description: カスタム固有表現認識 (NER) の使用方法を説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: bac6068c02ea4f253176a65061d11604104c2bd5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439046"
---
# <a name="what-is-custom-named-entity-recognition-ner-preview"></a>カスタム固有表現認識 (NER) とは (プレビュー)

カスタム NER は、[Azure Cognitive Service for Language](../overview.md) で提供されている機能の 1 つです。 これは、機械学習インテリジェンスを適用してカスタム NER タスク用のカスタム モデルを構築できるようにする、クラウドベースの API サービスです。

カスタム NER は、[Azure Cognitive for Language](../overview.md) 内のカスタム機能の一部として提供されています。 この機能を使用すると、ユーザーはカスタム AI モデルを作成して、コントラクトや財務ドキュメントなどの非構造化テキストからドメイン固有のエンティティを抽出できます。 開発者は、カスタムの NER プロジェクトを作成することによって、データのタグ付けを繰り返し行うことができ、モデル パフォーマンスをトレーニング、評価、および改善してから、利用できるようにすることができます。 タグ付けされたデータの品質は、モデルのパフォーマンスに大きく影響します。 モデルの構築とカスタマイズを簡単にするために、サービスには、[Language Studio](https://aka.ms/languageStudio) からアクセスできるカスタム Web ポータルが用意されています。 この[クイックスタート](quickstart.md)の手順に従って、サービスを簡単に開始できます。 
 
このドキュメントには、次の種類の記事が含まれています。

* [クイックスタート](quickstart.md)は、サービスへの要求の実行方法を説明する概要手順です。
* [概念](concepts/evaluation-metrics.md)では、サービスの機能と特徴について説明します。
* [攻略ガイド](how-to/tag-data.md)には、より具体的またはカスタマイズした方法でサービスを使用するための手順が記載されています。

## <a name="example-usage-scenarios"></a>使用シナリオの例を示します。

### <a name="information-extraction"></a>情報の抽出

金融機関や法務部門の多くは、銀行明細書、法的契約書、銀行のフォームなど、数千の複雑な非構造化テキストのデータを 1 日単位で抽出して正規化します。 これらのフォームを手動で処理する代わりにカスタム NER を使用すると、このプロセスを自動化し、コスト、時間、労力を節約できます。

### <a name="knowledge-mining-to-enhanceenrich-semantic-search"></a>セマンティック検索を強化するためのナレッジ マイニング

検索は、ユーザーにテキスト コンテンツを表示するすべてのアプリの基本となるものです。一般的なシナリオには、カタログやドキュメントの検索、小売商品の検索、データ サイエンスのための知識マイニングなどがあります。さまざまな業界にわたる多くの企業では、構造化および非構造化ドキュメントの両方を含む、プライベートで異種なコンテンツに対して豊富な検索エクスペリエンスを構築しようと検討しています。 パイプラインの一部として、開発者は、業界に関連するテキストからエンティティを抽出するために、カスタムの NER を使用できます。 これらのエンティティを使用してファイルのインデックス作成を強化することで、よりカスタマイズされた検索エクスペリエンスを実現できます。 

### <a name="audit-and-compliance"></a>監査とコンプライアンス

金融や法律関連企業の IT 部門は、ポリシーを監査して適用するために、非常に長いテキスト ファイルを手動で確認するのではなく、カスタム NER を使用して、自動化されたソリューションを構築できます。 これらのソリューションは、コンプライアンス ポリシーを適用し、構造化および非構造化コンテンツを処理するナレッジ マイニング パイプラインに基づいて必要なビジネス ルールを設定するのに役立ちます。

## <a name="application-development-lifecycle"></a>アプリケーション開発ライフサイクル

通常、カスタムの NER を使用するには、いくつかの異なる手順が必要です。 

:::image type="content" source="../custom-classification/media/development-lifecycle.png" alt-text="開発ライフサイクル" lightbox="../custom-classification/media/development-lifecycle.png":::

1. **スキーマを定義する**: データを認識し、抽出するエンティティを識別します。 あいまいさを回避します。

2. **データにタグを付ける**: データのタグ付けはモデルのパフォーマンスを判断する際の重要な要素です。 正確かつ、一貫性があるように、完全にタグ付けします。
    1. **正確にタグを付ける**: 各エンティティを適切な型に常にタグ付けします。 抽出するデータのみを含め、タグに不要なデータを含めないでください。
    2. **一貫性があるようにタグ付けをする**: 同一のエンティティは、すべてのファイルで同じタグを持つ必要があります。
    3. **完全にタグ付けする**: すべてのファイル内のエンティティのすべてのインスタンスにタグを付ける。

3. **モデルのトレーニング**: ご使用のモデルは、タグが付けられたデータから学習を開始します。

4. **モデルの評価の詳細を表示する**: トレーニングが完了したら、モデルの評価の詳細とそのパフォーマンスを表示します。

5. **モデルの改善**: モデルの評価の詳細を確認した後、モデルを改善する方法を学習できます。

6. **モデルのデプロイ**: モデルをデプロイするとは、モデルを使用できるようにすることです。

7. **エンティティの抽出**: エンティティ抽出タスクにカスタム モデルを使用します。

## <a name="next-steps"></a>次のステップ

* [クイックスタートに関する記事](quickstart.md)を使用して、カスタム テキスト分類の使用を開始します。  

* アプリケーション開発ライフサイクルを進める過程で、[用語集](glossary.md)を参照して、この機能のドキュメント全体で使用される用語の詳細を確認してください。 

* [リージョンの可用性](service-limits.md#regional-availability)などの情報については、「[サービスの制限](service-limits.md)」を必ずご覧ください。
