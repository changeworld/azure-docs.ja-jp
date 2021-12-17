---
title: Azure Digital Twins Explorer
titleSuffix: Azure Digital Twins
description: Azure Digital Twins Explorer の機能と目的、およびデジタル モデル、ツイン、グラフの視覚化に役立つ場合について説明します。
author: baanders
ms.author: baanders
ms.date: 10/29/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: bbcc693123f84d5cf789f53c8961f648ce2e90bf
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131504248"
---
# <a name="azure-digital-twins-explorer-preview"></a>Azure Digital Twins Explorer (プレビュー)

この記事では、ユース ケースや機能の概要など、**Azure Digital Twins Explorer** についての情報を提供します。 各機能の使用に関する詳細な手順については、「[Azure Digital Twins Explorer を使用する](how-to-use-azure-digital-twins-explorer.md)」を参照してください。

Azure Digital Twins Explorer は、[モデル](concepts-models.md)や[ツイン グラフ](concepts-twins-graph.md)など、Azure Digital Twins インスタンス内のデータを視覚化および操作するための開発者ツールです。 

>[!NOTE]
>このツールは現在、**パブリック プレビュー** 段階にあります。

サンプル グラフに対して設定されたモデルとツインを示すエクスプローラー ウィンドウのビューを次に示します。

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png" alt-text="サンプルのモデルとツインが表示されている Azure Digital Twins Explorer のスクリーンショット。" lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png":::

ビジュアル インターフェイスは、グラフとモデルのセットの形状を探索して解釈するための優れたツールです。 また、個々のツインとリレーションシップに対して、特定の即時的な変更を行うことも可能です。

## <a name="when-to-use"></a>使用する場合

Azure Digital Twins Explorer は、ツイン グラフを探索し、グラフのコンテキストのツインとリレーションシップを変更するユーザー向けに設計されたビジュアル ツールです。

開発者は、このツールが次のシナリオで特に役立つと感じると考えられます。
* **探索**: エクスプローラーを使用して、Azure Digital Twins と、それが実際の環境を表す方法について学習します。 表示および編集できるサンプルのモデルとグラフをインポートして、このサービスを理解しておいてください。 Azure Digital Twins Explorer の使用を開始するためのガイド付き手順については、「[Azure Digital Twins Explorer を開始する](quickstart-azure-digital-twins-explorer.md)」を参照してください。
* **開発**: エクスプローラーを使用して、ツイン グラフを表示および検証します。 また、モデル、ツイン、リレーションシップの特定のプロパティの調査にも使用できます。 グラフとそのデータに即時の変更を加えます。 各機能の使い方の詳細な手順については、「[Azure Digital Twins Explorer を使用する](how-to-use-azure-digital-twins-explorer.md)」を参照してください。 

このエクスプローラーの主な目的は、グラフを視覚化して理解し、必要に応じてグラフを更新できるようにすることです。 大規模なソリューションや、繰り返したり、自動化したりする必要がある作業に対しては、代わりに [API と SDK](./concepts-apis-sdks.md) を使用して、コードを介してインスタンスと対話することを検討してください。

[!INCLUDE [digital-twins-access-explorer.md](../../includes/digital-twins-access-explorer.md)]

## <a name="features-and-capabilities"></a>特徴と機能

Azure Digital Twins Explorer はパネルに編成され、各パネルにはモデル、ツイン、リレーションシップを探索および管理するためのさまざまな機能セットがあります。

エクスプローラーのセクションは次のとおりです。
* **クエリ エクスプローラー**: ツイン グラフに対してクエリを実行し、 **[ツイン グラフ]** パネルで視覚的な結果を表示します。
* **モデル**: モデルの一覧を表示し、モデルの追加、削除、モデルの詳細の表示などのアクションを実行します。
* **ツイン**: ツインとそれに関連付けられているリレーションシップの一覧を表示します。
* **ツイン グラフ**: ツインとリレーションシップを、カスタマイズ可能なツイン グラフとして視覚化します。 ツインとリレーションシップを作成および削除し、そのプロパティを表示または編集します。
* **モデル グラフ**: モデルとそれらが相互接続されている方法を、カスタマイズ可能なモデル グラフの形式で視覚化します。

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-panels.png" alt-text="上記の各パネルが強調表示されている Azure Digital Twins Explorer のスクリーンショット。" lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-panels.png":::

各機能の使い方の詳細な手順については、「[Azure Digital Twins Explorer を使用する](how-to-use-azure-digital-twins-explorer.md)」を参照してください。 

## <a name="how-to-contribute"></a>貢献する方法

Azure Digital Twins Explorer は、コードと ドキュメントへの貢献を歓迎する **オープンソース** ツールです。 ホストされているアプリケーションは、GitHub のソース コード リポジトリから定期的にデプロイされます。

ツールのソース コードを表示し、コードに貢献する方法の詳細な手順を確認するには、GitHub リポジトリ ([digital-twins-explorer](https://github.com/Azure-Samples/digital-twins-explorer)) にアクセスしてください。

このドキュメントに投稿する手順については、「[Microsoft Docs 共同作成者ガイド](/contribute/)」にアクセスしてください。

## <a name="other-considerations"></a>その他の考慮事項

### <a name="region-support"></a>リージョンのサポート

Azure Digital Twins Explorer は、すべての[サポートされているリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)の Azure Digital Twins のすべてのインスタンスで使用できます。

ただし、パブリック プレビュー中は、処理のためのデータ送信が、インスタンスがホストされているリージョンとは異なるリージョンを介して行われる場合があります。 データ主権が懸念される状況で、データがこのようにルーティングされるのを回避するには、[オープン ソース コード](#how-to-contribute)をダウンロードして、ローカルでホストされるバージョンのエクスプローラーを自分のマシンに作成できます。

### <a name="billing"></a>課金

Azure Digital Twins Explorer は、Azure Digital Twins サービスと対話するための無料ツールです。 ツール自体は無料ですが、Azure Digital Twins サービスに要求が送信されると、使用中にコストが発生する可能性があります。これは、次の価格ガイドラインに従います:「[Azure Digital Twins の価格](https://azure.microsoft.com/pricing/details/digital-twins/)」。

## <a name="next-steps"></a>次のステップ 

Azure Digital Twins Explorer の機能の使用方法の詳細をご覧ください:「[Azure Digital Twins Explorer を使用する](how-to-use-azure-digital-twins-explorer.md)」。