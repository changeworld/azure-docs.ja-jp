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
ms.openlocfilehash: d5644ecbc6b76ab2fc1ec44239fc14c52ab5fa73
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092289"
---
# <a name="what-is-custom-named-entity-recognition-ner-preview"></a>カスタム固有表現認識 (NER) とは (プレビュー)

カスタムの NER は、 [Microsoft チューリング ](https://msturing.org/about)テクノロジ上に構築された[Azure Cognitive Service for Language](../overview.md)内のカスタム機能の一部として提供されています。 この機能を使用すると、ユーザーはカスタム AI モデルを作成して、コントラクトや財務ドキュメントなどの非構造化テキストからドメイン固有のエンティティを抽出できます。 開発者は、カスタムの NER プロジェクトを作成することによって、データのタグ付けを繰り返し行うことができ、モデル パフォーマンスをトレーニング、評価、および改善してから、利用できるようにすることができます。 

モデルの構築とカスタマイズを簡単にするために、サービスには、[Language studio](https://aka.ms/languageStudio) を使用してアクセスできるカスタム Web ポータルが用意されています。 この[クイックスタート](quickstart.md)の手順に従って、サービスを簡単に開始できます。 

このドキュメントには、次の種類の記事が含まれています。

* [クイックスタート](quickstart.md)は、サービスへの要求の実行方法を説明する概要手順です。
* [概念](concepts/evaluation-metrics.md)では、サービスの機能と特徴について説明します。
* [攻略ガイド](how-to/tag-data.md)には、より具体的またはカスタマイズした方法でサービスを使用するための手順が記載されています。

## <a name="example-usage-scenarios"></a>使用シナリオの例を示します。

### <a name="information-extraction"></a>情報の抽出

金融機関や法務部門の多くは、銀行明細書、法的契約書、銀行のフォームなど、数千の複雑な非構造化テキストのデータを 1 日単位で抽出して正規化します。 これらのフォームを手動で処理する代わりに、カスタムの NER がこのプロセスを自動化するのに役立ちます。

### <a name="knowledge-mining-to-enhanceenrich-semantic-search"></a>セマンティック検索を強化するためのナレッジ マイニング

検索は、ユーザーにテキスト コンテンツを表示するすべてのアプリの基本となるものです。一般的なシナリオには、カタログやドキュメントの検索、小売商品の検索、データ サイエンスのための知識マイニングなどがあります。さまざまな業界にわたる多くの企業では、構造化および非構造化ドキュメントの両方を含む、プライベートで異種なコンテンツに対して豊富な検索エクスペリエンスを構築しようと検討しています。 パイプラインの一部として、開発者は、業界に関連するテキストからエンティティを抽出するために、カスタムの NER を使用できます。 これらのエンティティを使用してファイルのインデックス作成を強化することで、よりカスタマイズされた検索エクスペリエンスを実現できます。 

### <a name="audit-and-compliance"></a>監査とコンプライアンス

組織のポリシーを監査して適用するために、手動で長いテキスト ファイルを使用する代わりに、カスタムの NER を使用して、自動化されたソリューションを構築し、必要なビジネス ルールを適用できます。 

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
