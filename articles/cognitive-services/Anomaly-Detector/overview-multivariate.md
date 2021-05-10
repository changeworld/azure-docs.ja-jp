---
title: Anomaly Detector 多変量 API とは
titleSuffix: Azure Cognitive Services
description: 新しい Anomaly Detector パブリック プレビュー多変量 API の概要。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: 異常検出, 機械学習, アルゴリズム
ms.openlocfilehash: 63ad63cd57dce5f503e317e8c6330dca0325ba05
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107318824"
---
# <a name="multivariate-time-series-anomaly-detection-public-preview"></a>多変量時系列の異常検出 (パブリック プレビュー)

Azure Cognitive Services Anomaly Detector の最初のリリースでは、使いやすい[一変量時系列の Anomaly Detector API](overview.md) を使用して、メトリック監視ソリューションを構築できました。 Anomaly Detector 一変量では、時系列を個別に分析できるようにして、シンプルさとスケーラビリティを実現します。

新しい **多変量異常検出** API を使用すると、機械学習の知識やラベル付けされたデータがなくても、一連のメトリックから異常を検出するための高度な AI を開発者がさらに容易に統合することができます。 最大 300 の異なる信号間の依存関係や相互相関が自動的に主要な要因として考慮されるようになりました。 この新しい機能を使用すると、ソフトウェア アプリケーション、サーバー、工業用機械、宇宙船、さらにユーザーのビジネスなどの複雑なシステムを障害から予防的に保護することができます。

振動、気温、燃料圧などの 20 種類の信号を生成する、自動エンジンの 20 個のセンサーがあると想定します。これらの信号の測定値は、個別にはシステム レベルの問題についての詳細を示さない場合でも、組み合わせることでエンジンの正常性を表すことがあります。 これらの信号の相互作用が通常の範囲から外れた場合、多変量異常検出機能によって、経験豊かな専門家のように異常を検知できます。 基になる AI モデルは、ユーザーのビジネスの固有のニーズを理解できるように、データを使用してトレーニングおよびカスタマイズされます。 Anomaly Detector の新しい API を使用して、開発者は、多変量時系列の異常検出機能を、予測メンテナンス ソリューション、複雑なエンタープライズ ソフトウェア用の AIOps 監視ソリューション、またはビジネス インテリジェンス ツールに簡単に統合できるようになりました。

## <a name="when-to-use-multivariate-versus-univariate"></a>**多変量** を使用する場合と **一変量** を使用する場合

独自の履歴データに基づいて、単に個々の時系列で正常なパターンから異常を検出することが目的である場合は、一変量の異常検出 API を使用します。 例: 収益データ自体に基づいて毎日の収益異常を検出する場合、または CPU データに基づいて単に CPU スパイクを検出する場合。
- `POST /anomalydetector/v1.0/timeseries/last/detect`
- `POST /anomalydetector/v1.0/timeseries/batch/detect`
- `POST /anomalydetector/v1.0/timeseries/changepoint/detect`

![1 つの変数の変動値が青い線によってキャプチャされ、異常がオレンジの円で示された、時系列の折れ線グラフ](./media/anomaly_detection2.png)

時系列データのグループからシステム レベルの異常を検出することが目的である場合は、下の多変量異常検出 API を使用します。 特に、個々の時系列では詳細が得られず、システム レベルの問題を特定するために、すべての信号 (時系列のグループ) を総合的に確認する必要がある場合です。 例: 飛行機、油田掘削装置、衛星などの高価な物理資産があるとします。 これらの各資産には、何万もの異なる種類のセンサーがあります。 システム レベルの問題があるかどうかを判断するには、これらのセンサーからのすべての時系列信号を確認する必要があります。

- `POST /anomalydetector/v1.1-preview/multivariate/models`
- `GET /anomalydetector/v1.1-preview/multivariate/models[?$skip][&$top]`
- `GET /anomalydetector/v1.1-preview/multivariate/models/{modelId}`
- `POST/anomalydetector/v1.1-preview/multivariate/models/{modelId}/detect`
- `GET /anomalydetector/v1.1-preview/multivariate/results/{resultId}`
- `DELETE /anomalydetector/v1.1-preview/multivariate/models/{modelId}`
- `GET /anomalydetector/v1.1-preview/multivariate/models/{modelId}/export`

![振動、気温、圧力、ベロシティ、回転速度の変数の複数の時系列線グラフ。異常はオレンジで強調表示されています](./media/multivariate-graph.png)

## <a name="region-support"></a>リージョンのサポート

Anomaly Detector 多変量のパブリック プレビューは現在、米国西部 2、米国東部 2、西ヨーロッパの 3 つのリージョンでご利用いただけます。

## <a name="algorithms"></a>アルゴリズム

- [グラフ アテンション ネットワークによる多変量時系列の異常検出](https://arxiv.org/abs/2009.02040)

## <a name="join-the-anomaly-detector-community"></a>Anomaly Detector コミュニティに参加する

- [Microsoft Teams 上の Anomaly Detector Advisors グループ](https://aka.ms/AdAdvisorsJoin)に参加する

## <a name="next-steps"></a>次のステップ

- [クイックスタート](./quickstarts/client-libraries-multivariate.md)。
- [ベスト プラクティス](./concepts/best-practices-multivariate.md): この記事は、多変量 API で使用するために推奨されるパターンに関するものです。
