---
title: Anomaly Detector API とは
titleSuffix: Azure Cognitive Services
description: Anomaly Detector API のアルゴリズムを利用し、時系列データに異常検出を適用します。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 02/16/2021
ms.author: mbullwin
keywords: 異常検出, 機械学習, アルゴリズム
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 75a040b8c2b480d0c82ef2cab6a953d230f6ffb7
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371108"
---
# <a name="what-is-the-anomaly-detector-univariate-api"></a>Anomaly Detector 一変量 API とは

Anomaly Detector API では、時系列データを監視し、その中の異常を検出できます。機械学習の知識は必要ありません。 Anomaly Detector API のアルゴリズムでは、産業、シナリオ、データ量に関係なく、データに最適なモデルが自動的に特定され、適用されます。 この API では、時系列データを使用し、異常検出の境界、予想される値、異常となるデータ ポイントが判断されます。

![サービス要求のパターンの変化を検出する](./media/anomaly_detection2.png)

Anomaly Detector の使用にあたり、機械学習の経験は必要ありません。REST API によってサービスをアプリケーションやプロセスに簡単に統合できます。

このドキュメントには、次のような記事が記載されています。
* [クイックスタート](./Quickstarts/client-libraries.md)は、サービスの呼び出しと結果の取得を短時間で行えるようにする、ステップバイステップの手順です。 
* [攻略ガイド](./how-to/identify-anomalies.md)には、より具体的またはカスタマイズした方法でサービスを使用するための手順が記載されています。
* [概念の記事](./concepts/anomaly-detection-best-practices.md)では、サービスの機能と特長について詳しく説明します。
* [チュートリアル](./tutorials/batch-anomaly-detection-powerbi.md)はより長文のガイドであり、より広範なビジネス ソリューションの 1 コンポーネントとしてこのサービスを使用する方法を示すものです。

## <a name="features"></a>特徴

Anomaly Detector を利用すると、時系列データ全体で異常を自動的に検出、あるいは、異常が発生したときにリアルタイムで検出できます。

|特徴量  |説明  |
|---------|---------|
|リアルタイムの異常検出。 | 前に確認されたデータ ポイントを利用し、最新のデータ ポイントが異常であるかどうかを判断することでストリーミング データ内の異常を検出します。 この操作では、送信したデータ ポイントを利用してモデルが生成され、ターゲットのポイントが異常であるかどうかが判断されます。 生成する新しいデータ ポイントで API を呼び出すことで、作成時にデータを監視できます。 |
|バッチとして設定されたデータ全体で異常を検出します。 | 時系列データを使用し、データ全体に存在する可能性がある異常を検出します。 この操作により、時系列データ全体を使用してモデルが生成されます。各ポイントが同じモデルで分析されます。         |
|バッチとして設定されたデータ全体で変化点を検出します。 | 時系列データを使用し、データに存在する傾向の変化点を検出します。 この操作により、時系列データ全体を使用してモデルが生成されます。各ポイントが同じモデルで分析されます。    |
| データに関する追加情報を取得します。 | 予想される値、異常の境界、位置など、データに関する役に立つ詳細と観察された異常を取得します。 |
| 異常検出の境界を調整します。 | Anomaly Detector API では、異常検出の境界が自動的に作成されます。 この境界を調整することでデータの異常に対する API の感度を増減させ、データを最適化します。 |

## <a name="demo"></a>デモ

この[対話型デモ](https://aka.ms/adDemo)をご覧になり、Anomaly Detector のしくみを理解してください。
デモを実行するには、Anomaly Detector のリソースを作成し、API キーとエンドポイントを取得する必要があります。

## <a name="notebook"></a>ノートブック

Anomaly Detector API を呼び出す方法については、こちらの [ Notebook](https://aka.ms/adNotebook) をお試しください。 この Jupyter Notebook では、API 要求を送信して結果を視覚化する方法について説明しています。

Notebook を実行するには、有効な Anomaly Detector API **サブスクリプション キー** と **API エンドポイント** を取得する必要があります。 ノートブックで、有効な Anomaly Detector API サブスクリプション キーを `subscription_key` 変数に追加し、`endpoint` 変数をエンドポイントに変更します。

## <a name="workflow"></a>ワークフロー

Anomaly Detector API は RESTful Web サービスです。HTTP 要求を作成して JSON を解析できる任意のプログラミング言語から簡単に呼び出すことができます。

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

新規登録後:

1. 時系列データを取り出し、有効な JSON 形式に変換します。 最良の結果を得るために、データを準備するとき、[ベスト プラクティス](concepts/anomaly-detection-best-practices.md)を使用してください。
1. Anomaly Detector API に自分のデータを含む要求を送信します。
1. 返された JSON メッセージを解析して API 応答を処理します。

## <a name="algorithms"></a>アルゴリズム

* 使用されているアルゴリズムについては、次のテクニカル ブログを参照してください。
    * [Azure Anomaly Detector API の概要](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Azure Anomaly Detector の SR-CNN アルゴリズムの概要](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

Microsoft によって開発された SR-CNN アルゴリズムの詳細については、論文「[Microsoft での時系列の異常検出サービス](https://arxiv.org/abs/1906.03821)」(KDD 2019 採択済み) を参照してください。

> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="service-availability-and-redundancy"></a>サービスの可用性と冗長性

### <a name="is-the-anomaly-detector-service-zone-resilient"></a>Anomaly Detector サービスにゾーン回復性はありますか?

はい。 Anomaly Detector サービスは、既定ではゾーン回復性を備えています。

### <a name="how-do-i-configure-the-anomaly-detector-service-to-be-zone-resilient"></a>どのように Anomaly Detector サービスにゾーン回復性を構成しますか?

ゾーン回復性を有効にするために、顧客による構成は必要ありません。 Anomaly Detector リソースのゾーン回復性は、既定で使用できるようになっており、サービス自体によって管理されます。

## <a name="deploy-on-premises-using-docker-containers"></a>Docker コンテナーを使用してオンプレミスにデプロイする

[Anomaly Detector コンテナーを使用](anomaly-detector-container-howto.md)して、API 機能をオンプレミスにデプロイします。 Docker コンテナーを使用すると、コンプライアンス、セキュリティ、またはその他の運用上の理由から、データにより近いところでサービスを使用できます。

## <a name="join-the-anomaly-detector-community"></a>Anomaly Detector コミュニティに参加する

* [Microsoft Teams 上の Anomaly Detector Advisors グループ](https://aka.ms/AdAdvisorsJoin)に参加する

## <a name="next-steps"></a>次のステップ

* [クイック スタート: Anomaly Detector を使用して時系列データ内の異常を検出する](quickstarts/client-libraries.md)
* Anomaly Detector API [オンライン デモ](https://github.com/Azure-Samples/AnomalyDetector/tree/master/ipython-notebook)
* Anomaly Detector [REST API リファレンス](https://aka.ms/anomaly-detector-rest-api-ref)