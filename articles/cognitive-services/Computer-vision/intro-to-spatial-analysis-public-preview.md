---
title: 空間分析とは
titleSuffix: Azure Cognitive Services
description: このドキュメントでは、Azure 空間分析コンテナーの基本概念と機能について説明します。
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 10/06/2021
ms.custom: contperf-fy22q2
ms.openlocfilehash: fd160aa2ba4a626e12db638694cb6d971b19fbd2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469941"
---
# <a name="what-is-spatial-analysis"></a>空間分析とは

Computer Vision の空間分析を使用して、カメラからストリーミング ビデオを取り込み、分析情報を抽出し、他のシステムで使用されるイベントを生成できます。 このサービスでは、ビデオ内の人の存在と移動が検出されます。 これにより、空間に入ってくる人の数を数えたり、マスクやソーシャル ディスタンスのガイドラインに対するコンプライアンスを測定したりする処理を行えます。 物理空間からのビデオ ストリームを処理することで、ユーザーが物理空間をどのように使用するかを学習し、組織にとっての空間の価値を最大限に高めることができます。 

<!--This documentation contains the following types of articles:
* The [quickstarts](./quickstarts-sdk/analyze-image-client-library.md) are step-by-step instructions that let you make calls to the service and get results in a short period of time. 
* The [how-to guides](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) contain instructions for using the service in more specific or customized ways.
* The [conceptual articles](tbd) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions.-->

## <a name="what-it-does"></a>実行内容
空間分析ではビデオが取り込まれた後、ビデオ内のユーザーが検出されます。 人が検出されると、人が時間とともに動き回るようすがシステムによって追跡され、関心のある領域に人が作用するとイベントが生成されます。 すべての操作では、1 つのカメラの視野から分析情報が得られます。 

### <a name="people-counting"></a>人数カウント
この操作では、PersonCount 操作を使用して、特定のゾーン内の経時的な人数がカウントされます。 ここでは、フレーム間で人を追跡せずに、処理されるフレームごとに独立したカウントが生成されます。 この操作を使用して、空間内の人の数を推定したり、人が現れたときにアラートを生成したりすることができます。

![空間分析では、カメラの視野内の人の数がカウントされます](https://user-images.githubusercontent.com/11428131/139924111-58637f2e-f2f6-42d8-8812-ab42fece92b4.gif)

### <a name="entrance-counting"></a>入り口でのカウント
この機能では、人がエリア内にいた時間、または入り口を通過した時刻が監視されます。 この監視は、PersonCrossingPolygon 操作または PersonCrossingLine 操作を使用して実行できます。 小売のシナリオでは、これらの操作を使用して、レジの行列の待ち時間や展示に対するエンゲージメントを測定できます。 これらの操作では、他の商用ビルのシナリオで、ロビーや特定の階での歩行者を測定できます。

![空間分析ではレジの行列の待ち時間を測定します](https://user-images.githubusercontent.com/11428131/137016574-0d180d9b-fb9a-42a9-94b7-fbc0dbc18560.gif)

### <a name="social-distancing-and-facemask-detection"></a>ソーシャル ディスタンスとマスクの検出 
この機能では、人々が空間内のソーシャル ディスタンス要件にどれだけ従っているかを分析します。 PersonDistance 操作を使用すると、人が空間内を歩き回る間、システムは自動的に調整されます。 そして、人が特定の距離のしきい値 (6 フィートまたは 10 フィート) に違反したことを識別します。

![空間分析では、距離を示す人と人との間の線を表示することで、ソーシャル ディスタンス違反イベントが視覚化されます](https://user-images.githubusercontent.com/11428131/139924062-b5e10c0f-3cf8-4ff1-bb58-478571c022d7.gif)

空間分析では、人がマスクなどの顔を保護するものを装着しているかどうかを検出するようにも構成できます。 `ENABLE_FACE_MASK_CLASSIFIER` パラメーターを構成することで、PersonCount、PersonCrossingLine、PersonCrossingPolygon の操作に対してマスク分類子を有効にすることができます。

![空間分析では、人がエレベーター内でマスクをしているかどうかを分類します。](https://user-images.githubusercontent.com/11428131/137015842-ce524f52-3ac4-4e42-9067-25d19b395803.png)

## <a name="get-started"></a>はじめに

[クイックスタート](spatial-analysis-container.md)に従って、空間分析コンテナーを設定し、ビデオの分析を開始します。

## <a name="responsible-use-of-spatial-analysis-technology"></a>責任を持って空間分析テクノロジを使用する

責任を持って空間分析テクノロジを使用する方法については、[透明性に関するメモ](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext)を参照してください。 Microsoft の透明性に関するメモは、AI テクノロジがどのように機能するか、またシステムのパフォーマンスと動作に影響を及ぼすシステム所有者が行う選択について理解するのに役立ちます。 ここでは、テクノロジ、人、環境などのシステム全体について考えることの重要性に焦点が当てられています。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [クイックスタート: 空間分析コンテナー](spatial-analysis-container.md)
