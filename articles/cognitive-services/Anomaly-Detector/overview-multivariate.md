---
title: Anomaly Detector 多変量 API とは
titleSuffix: Azure Cognitive Services
description: 新しい Anomaly Detector 多変量 API プレビューの概要。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: 異常検出, 機械学習, アルゴリズム
ms.openlocfilehash: 7f8e9c26de1cceb6c4dec514e21d36b9843bb44e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128588164"
---
# <a name="multivariate-time-series-anomaly-detection-preview"></a>多変量時系列の異常検出 (プレビュー)

新しい **多変量異常検出** API を使用すると、機械学習の知識やラベル付けされたデータがなくても、一連のメトリックから異常を検出するための高度な AI を開発者がさらに容易に統合することができます。 最大 300 の異なる信号間の依存関係や相互相関が自動的に主要な要因として考慮されるようになりました。 この新しい機能を使用すると、ソフトウェア アプリケーション、サーバー、工業用機械、宇宙船、さらにユーザーのビジネスなどの複雑なシステムを障害から予防的に保護することができます。

![回転、光学フィルター、圧力、ベアリングなどの変数を示す複数の時系列線グラフは、異常値がオレンジ色でハイライトされます](./media/multivariate-graph.png)

回転、燃圧、ベアリングなどの 20 種類の信号を生成する、自動車エンジンの 20 個のセンサーがあると想定します。それらの信号を個別に読み取っても、システム レベルの問題についてはあまりわからないかもしれませんが、それらを総合するとエンジンの健全性を表すことができます。 これらの信号の相互作用が通常の範囲から外れた場合、多変量異常検出機能によって、経験豊かな専門家のように異常を検知できます。 基になる AI モデルは、ユーザーのビジネスの固有のニーズを理解できるように、データを使用してトレーニングおよびカスタマイズされます。 Anomaly Detector の新しい API を使用して、開発者は、多変量時系列の異常検出機能を、予測メンテナンス ソリューション、複雑なエンタープライズ ソフトウェア用の AIOps 監視ソリューション、またはビジネス インテリジェンス ツールに簡単に統合できるようになりました。

## <a name="when-to-use-multivariate-versus-univariate"></a>**多変量** を使用する場合と **一変量** を使用する場合

独自の履歴データに基づいて、単に個々の時系列で正常なパターンから異常を検出することが目的である場合は、一変量異常検出 API を使用します。 たとえば、収益データ自体に基づいて毎日の収益異常を検出する場合、または CPU データに基づいて単に CPU スパイクを検出する場合です。

時系列データのグループからシステム レベルの異常を検出することが目的である場合は、多変量異常検出 API を使用します。 特に、個々の時系列では詳細が得られず、システム レベルの問題を特定するために、すべての信号 (時系列のグループ) を総合的に確認する必要がある場合です。 たとえば、飛行機、油田掘削装置、衛星などの高価な物理資産があるとします。 これらの各資産には、何万もの異なる種類のセンサーがあります。 システム レベルの問題があるかどうかを判断するには、これらのセンサーからのすべての時系列信号を確認する必要があります。

## <a name="notebook"></a>ノートブック

Anomaly Detector API (多変量) を呼び出す方法については、こちらの [Notebook](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/API%20Sample/Multivariate%20API%20Demo%20Notebook.ipynb) をお試しください。 この Jupyter Notebook では、API 要求を送信して結果を視覚化する方法について説明しています。

Notebook を実行するには、有効な Anomaly Detector API **サブスクリプション キー** と **API エンドポイント** を取得する必要があります。 ノートブックで、有効な Anomaly Detector API サブスクリプション キーを `subscription_key` 変数に追加し、`endpoint` 変数をエンドポイントに変更します。

## <a name="region-support"></a>リージョンのサポート

Anomaly Detector 多変量のプレビューは現在、東南アジア、オーストラリア東部、カナダ中部、北ヨーロッパ、西ヨーロッパ、米国東部、米国東部 2、米国中南部、米国西部 2、英国南部の 10 か所の Azure リージョンで提供されています。

## <a name="algorithms"></a>アルゴリズム

使用されているアルゴリズムについては、次のテクニカル ドキュメントを参照してください。

* ブログ: [多変量異常検出の紹介](https://techcommunity.microsoft.com/t5/azure-ai/introducing-multivariate-anomaly-detection/ba-p/2260679)
* 論文: [Multivariate time series Anomaly Detection via Graph Attention Network (グラフ アテンション ネットワークによる多変量時系列の異常検出)](https://arxiv.org/abs/2009.02040)


> [!VIDEO https://www.youtube.com/embed/FwuI02edclQ]


## <a name="join-the-anomaly-detector-community"></a>Anomaly Detector コミュニティに参加する

- [Microsoft Teams 上の Anomaly Detector Advisors グループ](https://aka.ms/AdAdvisorsJoin)に参加する

## <a name="next-steps"></a>次のステップ

- [チュートリアル](./tutorials/learn-multivariate-anomaly-detection.md): この記事は、多変量 API の使用方法を説明するエンドツーエンドのチュートリアルです。
- [クイックスタート](./quickstarts/client-libraries-multivariate.md)。
- [ベスト プラクティス](./concepts/best-practices-multivariate.md): この記事は、多変量 API で使用するために推奨されるパターンに関するものです。
