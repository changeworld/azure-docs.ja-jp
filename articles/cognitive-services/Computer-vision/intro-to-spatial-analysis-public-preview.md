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
ms.openlocfilehash: 9553c7e177bdc78b071d6ed68725879e46c9ef5e
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668449"
---
# <a name="what-is-spatial-analysis"></a>空間分析とは

空間分析は、組織が特定の領域内における人の動きと存在を理解することで、物理的な空間の価値を最大限に高めることができる AI サービスです。 CCTV または監視カメラからビデオを取り込み、ビデオ ストリームから分析情報を抽出し、他のシステムで使用するイベントを生成することができます。 このサービスでは、カメラ ストリームからの入力を使用して、空間に入ってくる人の数を数えたり、マスクやソーシャル ディスタンスのガイドラインに対するコンプライアンスを測定したりする処理を行えます。

<!--This documentation contains the following types of articles:
* The [quickstarts](./quickstarts-sdk/analyze-image-client-library.md) are step-by-step instructions that let you make calls to the service and get results in a short period of time. 
* The [how-to guides](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) contain instructions for using the service in more specific or customized ways.
* The [conceptual articles](tbd) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions.-->

## <a name="what-it-does"></a>実行内容

空間分析の中核となる操作は、システム上に構築されています。このシステムでは、ビデオを取り込み、ビデオ内の人物を検出し、動き回る人物を経時的に追跡し、人物が関心領域に関わったときにイベントを生成します。

## <a name="spatial-analysis-features"></a>空間分析の機能

| 機能 | 定義 |
|------|------------|
| **人物検出** | このコンポーネントでは、"人物がこの画像のどこにいるか" という質問に答えます。 画像内に人物を見つけ、それぞれの人物の場所を示す境界ボックスの座標が、**人物追跡** コンポーネントに渡されます。 |
| **人物追跡** | このコンポーネントでは、人物がカメラの前を動き回るときに時間の経過に伴って人物検出が接続されます。 これには、人が一般的にどのように動くかに関するテンポラル ロジックと、人物の全体的な外観に関する基本情報が使用されます。 複数のカメラにまたがる人物の追跡は行われません。 ある人物が約 1 分を超えて視野に入らず、その後、視野に再び入ると、システムではこれが新しい人物として認識されます。 人物追跡では、複数のカメラ間で個人が一意に識別されることはありません。 顔認識や歩行追跡は使用されません。 |
| **マスクの検出** | このコンポーネントでは、カメラの視野にある人物の顔の位置が検出され、マスクの存在が識別されます。 AI 操作ではビデオから画像がスキャンされます。顔が検出されると、サービスによって顔の周りに境界ボックスが表示されます。 物体検出機能を使用して、境界ボックス内にマスクがあるかどうかが識別されます。 マスクの検出では、複数の顔を区別したり、顔の属性を予測または分類したり、顔認識を行ったりすることはありません。 |
| **関心領域** | このコンポーネントは、入力ビデオ フレーム内のユーザー定義のゾーンまたは線です。 ある人物がビデオのこの領域を操作すると、システムによってイベントが生成されます。 たとえば、**PersonCrossingLine** 操作の場合、ビデオ フレーム内には線が定義されています。 人物がその線を越えると、イベントが生成されます。 |
| **Event** | イベントは、空間分析の主要な出力です。 各操作は、定期的に (毎分 1 回など)、または特定のトリガーが生じたときのどちらかで、特定のイベントを生成します。 このイベントには、入力ビデオで起きたことに関する情報は含まれますが、画像やビデオは含まれません。 たとえば、**PeopleCount** 操作では、人々の数が変化するたびに (トリガー)、または 1 分ごとに (定期的に)、更新された数を含んだイベントを生成できます。 |

## <a name="get-started"></a>はじめに

[クイックスタート](spatial-analysis-container.md)に従って、空間分析コンテナーを設定し、ビデオの分析を開始します。

## <a name="responsible-use-of-spatial-analysis-technology"></a>責任を持って空間分析テクノロジを使用する

責任を持って空間分析テクノロジを使用する方法については、[透明性に関するメモ](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext)を参照してください。 Microsoft の透明性に関するメモは、AI テクノロジの機能のしくみ、システムのパフォーマンスと動作に影響を与えるシステム所有者の選択肢、およびテクノロジ、人、環境などのシステム全体について検討することの重要性を理解するためのものです。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [クイックスタート: 空間分析コンテナー](spatial-analysis-container.md)
