---
title: オントロジとは
titleSuffix: Azure Digital Twins
description: デジタル ツインのオントロジ、Azure Digital Twins での使用方法、およびこれらの DTDL オントロジを使用して特定の業界のコンテキストでモデリングを行う方法について説明します。
author: baanders
ms.author: baanders
ms.date: 10/21/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 56ce0815bdcb59e64f6a61cce4d4fb6172abea20
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501630"
---
# <a name="what-is-an-ontology"></a>オントロジとは 

この記事では、業界オントロジの概念と、Azure Digital Twins のコンテキスト内でそれらを使用する方法について説明します。

Azure Digital Twins ソリューションのボキャブラリは、ご自身の環境内に存在するエンティティの種類を示す、[モデル](concepts-models.md)を使用して定義されます。

自分のソリューションが特定の業界に関連している場合、独自のモデル セットをゼロから作成するよりも、既に存在するその業界用の一連のモデルから開始する方が簡単で効率的な場合があります。 これらの既存のモデル セットを **オントロジ** と呼びます。

一般的に、オントロジとは、特定の分野 (建造物、IoT システム、スマート シティ、エネルギー グリッド、Web コンテンツなど) の一連のモデルです。 オントロジは、次のことを実現できるため、ツイン グラフのスキーマとしてよく使用されます。
* ソフトウェア コンポーネント、ドキュメント、クエリ ライブラリなどの調和。
* 概念モデリングとシステム開発への投資の削減
* セマンティック レベルでのデータ相互運用性の向上
* ゼロから開始したり、"車輪の再発明" をしたりせずに、ベスト プラクティスを再利用

この記事では、Azure Digital Twins のモデルにオントロジを使用する理由と、その方法について説明します。 また、現在使用できるオントロジとそのためのツールについても説明します。

## <a name="using-ontologies-for-azure-digital-twins"></a>Azure Digital Twins のオントロジを使用する

オントロジは、デジタル ツイン ソリューションの出発点として最適です。 これらには、デジタル ツイン グラフの設計、作成、解析のための、一連の分野固有のモデルとエンティティ間のリレーションシップが含まれます。 オントロジを使用すると、ソリューション開発者は、実証済みの出発点からデジタル ツイン ソリューションを開始し、ビジネス上の問題の解決に専念できます。 また、Microsoft が提供するオントロジは、簡単に拡張できるように設計されているため、自分のソリューションに合わせてカスタマイズできます。 

また、オントロジはソリューション間で共通のボキャブラリを提供できるため、自分のソリューションでこれらのオントロジを使用すると、異なるパートナーおよびベンダー間でのシームレスな統合のためにそれらを設定できます。

Azure Digital Twins のモデルは [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) で表されているため、Azure Digital Twins で使用されるオントロジも DTDL で記述されています。 

## <a name="strategies-for-integrating-ontologies"></a>オントロジを統合するための戦略

DTDL を使用した業界標準のオントロジを統合するための戦略は 3 つ考えられます。 自分のニーズに応じて最適なものを選択できます。

| 戦略 | 説明 | リソース |
| --- | --- | --- |
| **採用** | 広く採用されている業界標準に基づいて構築されたオープンソースの DTDL オントロジーを使用して、ソリューションを開始できます。 これらのモデル セットは、すぐに使用することも、カスタマイズされたソリューションのために独自の追加を加えて拡張することもできます。 | [業界標準のオントロジの採用](concepts-ontologies-adopt.md)<br><br>[オントロジの拡張](concepts-ontologies-extend.md) |
| **CONVERT** | 別の標準形式で表されている既存のモデルが既にある場合は、それらを DTDL に変換して Azure Digital Twins で使用できます。 | [オントロジの変換](concepts-ontologies-convert.md)<br><br>[オントロジの拡張](concepts-ontologies-extend.md) |
| **作成者** | インスピレーションとして適用可能な業界標準を使用して、独自のカスタム DTDL モデルをゼロからいつでも作成できます。 | [DTDL モデル](concepts-models.md) |

### <a name="using-ontology-strategies-in-a-model-development-path"></a>モデル開発パスでのオントロジ戦略の使用

オントロジを Azure Digital Twins に統合するためにどのような戦略を選択した場合でも、下の完全なパスに従って、オントロジを DTDL モデルとして作成し、アップロードできます。

1. 最初に、[Azure Digital Twins での DTDL モデリング](concepts-models.md)を確認し、理解します。
1. 上で選択したオントロジ統合戦略を進めます。オントロジに基づいて、モデルを[採用](concepts-ontologies-adopt.md)、[変換](concepts-ontologies-convert.md)、または[作成](concepts-models.md)します。
    1. 必要に応じて、オントロジを[拡張](concepts-ontologies-extend.md)して、自分のニーズに合わせてカスタマイズします。
1. モデルを[検証](how-to-parse-models.md)して、それが有効な DTDL ドキュメントであることを確認します。
1. [API](how-to-manage-model.md#upload-models) または [Azure Digital Twins のモデル アップローダー](https://github.com/Azure/opendigitaltwins-tools/tree/master/ADTTools#uploadmodels)のようなサンプルを使用して、完成したモデルを Azure Digital Twins にアップロードします。

この一連の記事を読むと、Azure Digital Twins インスタンスでモデルを使用する方法が手順を追って説明されています。 

>[!TIP]
> [Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md) または [Azure Digital Twins Model Visualizer](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer) を使用して、オントロジ内のモデルを視覚化できます。

## <a name="next-steps"></a>次のステップ

オントロジの採用、変換、作成の戦略について詳しく確認します。
* [業界標準のオントロジの採用](concepts-ontologies-adopt.md)
* [オントロジの変換](concepts-ontologies-convert.md)
* [DTDL モデルを管理する](how-to-manage-model.md)

または、「[デジタル ツインとツイン グラフ](concepts-twins-graph.md)」でモデルを使用してデジタル ツインを作成する方法について確認してください。