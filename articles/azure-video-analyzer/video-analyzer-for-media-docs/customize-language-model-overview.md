---
title: Azure Video Analyzer for Media (旧 Video Indexer) で言語モデルをカスタマイズする - Azure
titleSuffix: Azure Video Analyzer for Media
description: この記事では、Azure Video Analyzer for Media (旧 Video Indexer) における言語モデルについて、およびそのカスタマイズ方法について概要を示します。
services: azure-video-analyzer
author: anikaz
manager: johndeu
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: ca79d7d541d137a299fd61431df9410c6822be04
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121374"
---
# <a name="customize-a-language-model-with-video-analyzer-for-media"></a>Video Analyzer for Media で言語モデルをカスタマイズする

Azure Video Analyzer for Media (旧 Video Indexer) では、Microsoft [Custom Speech Service](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/) との統合を通して自動音声認識がサポートされています。 適応テキスト、つまりエンジンを適応させたいボキャブラリのドメインに属するテキストをアップロードすることで、言語モデルをカスタマイズできます。 モデルをトレーニングすると、適応テキスト内に現れる新しい単語が認識され、既定の発音が想定されて、言語モデルが、ありそうな新しい一連の単語を学習することになります。 カスタム言語は、英語、スペイン語、フランス語、ドイツ語、イタリア語、簡体中国語、日本語、ロシア語、ポルトガル語、ヒンズー語、および韓国語がサポートされています。 

例として、(Azure Kubernetes サービスのコンテキストにおける) "Kubernetes" のように、非常に限られた意味を持つ単語を見てみましょう。 その単語は Video Analyzer for Media にとって新しいものなので、単語は "コミュニティ" として認識されます。 これが "Kubernetes" と認識されるようにモデルをトレーニングする必要があります。 その他の場合、単語は存在していても、言語モデルはそれらの単語が特定のコンテキスト内に現れることを予期していません。 たとえば、"コンテナー サービス" は、特殊化されていない言語モデルが特定の単語のセットとして認識する 2 語のシーケンスではありません。

テキスト ファイルの一覧で、コンテキストのない単語をアップロードするオプションが用意されています。 これは部分的な適応と考えられます。 または、より適切な適応のために、コンテンツに関連するドキュメントや文章から成るテキスト ファイルをアップロードできます。

このトピックの「[次のステップ](#next-steps)」 セクションに示したトピックで説明されているように、Video Analyzer for Media の API または Web サイトを使用して、カスタム言語モデルを作成し、編集することができます。

## <a name="best-practices-for-custom-language-models"></a>カスタム言語モデルのベスト プラクティス

Video Analyzer for Media では単語の組み合わせの確率に基づいて学習が行われるので、最適な学習を行うには、以下のようにします。

* 実際に話されるとおりの文例を十分に提供します。
* 各行には 1 つの文だけを配置します。多くてはいけません。 そのようにしないと、システムでは複数の文にわたる確率が学習されます。
* 1 つの単語を文として配置し、特定の単語を他の単語よりも優先して学習させてもかまいませんが、システムが最適な学習を行うのは完全な文からです。
* 新しい単語や頭字語を導入するときには、可能であれば、できるだけ多くの使用例を完全な文で提供し、システムにできるだけ多くのコンテキストを提供します。
* いくつかの適応オプションを試して、どのような結果が得られるかを確認します。
* まったく同じ文を複数回繰り返すのは避けてください。 それが、残りの入力に対して偏りを生む可能性があります。
* 一般的でない記号 (~、#、@、%、&) は、破棄されるため含まれないようにします。 それらが出現する文も破棄されます。
* 数十万の文など、大きすぎる入力を供給するとブースティングの効果が薄まるため、そうしないようにします。

## <a name="next-steps"></a>次のステップ

[API を使用して言語モデルをカスタマイズする](customize-language-model-with-api.md)

[Web サイトを使用して言語モデルをカスタマイズする](customize-language-model-with-website.md)
