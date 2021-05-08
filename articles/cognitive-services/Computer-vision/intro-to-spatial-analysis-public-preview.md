---
title: 空間分析とは
titleSuffix: Azure Cognitive Services
description: このドキュメントでは、Computer Vision 空間分析コンテナーの基本概念と機能について説明します。
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.openlocfilehash: 5aa34ce15d96112450a7c15debcd92312c1d9e19
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284544"
---
# <a name="what-is-spatial-analysis"></a>空間分析とは

空間分析サービスを使用すると、組織は特定の領域内での人の動きと存在を理解することで、物理的な空間の価値を最大限に高めることができます。 CCTV または監視カメラからビデオを取り込み、AI 操作を実行してビデオ ストリームから分析情報を抽出し、他のシステムで使用するイベントを生成することができます。 AI 操作では、カメラ ストリームからの入力を使用して、空間に入ってくるユーザーの人数を数えたり、マスクやソーシャル ディスタンスのガイドラインに対するコンプライアンスを測定したりする処理を行えます。

<!--This documentation contains the following types of articles:
* The [quickstarts](./quickstarts-sdk/analyze-image-client-library.md) are step-by-step instructions that let you make calls to the service and get results in a short period of time. 
* The [how-to guides](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) contain instructions for using the service in more specific or customized ways.
* The [conceptual articles](tbd) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions.-->

## <a name="what-it-does"></a>実行内容

空間分析の中核となるすべての操作は、パイプライン上に構築されています。このパイプラインでは、ビデオを取り込み、ビデオ内のユーザーを検出し、時間の経過に伴って動き回るユーザーを追跡し、ユーザーが関心領域を操作したときにイベントを生成します。

## <a name="spatial-analysis-features"></a>空間分析の機能

| 機能 | 定義 |
|------|------------|
| **人物検出** | このコンポーネントでは、"人物がこの画像のどこにいるか" という質問に答えます。 画像内の人間を見つけ、それぞれの人物の場所を示す境界ボックスが、人物追跡コンポーネントに渡されます。 |
| **人物追跡** | このコンポーネントでは、人物がカメラの前を動き回るときに時間の経過に伴って人物検出が接続されます。 これには、人が一般的にどのように動くかに関するテンポラル ロジックと、人物の全体的な外観に関する基本情報が使用されます。 複数のカメラにまたがる人物の追跡は行われません。 ある人物が約 1 分を超えて視野に入らず、その後カメラの視野に再び入ると、システムではこれが新しい人物として認識されます。 人物追跡では、複数のカメラ間で個人が一意に識別されることはありません。 顔認識や歩行追跡は使用されません。 |
| **マスクの検出** | このコンポーネントでは、カメラの視野にある人物の顔の位置が検出され、マスクの存在が識別されます。 AI 操作ではビデオから画像がスキャンされます。顔が検出されると、サービスによって顔の周りに境界ボックスが表示されます。 物体検出機能を使用して、境界ボックス内にマスクがあるかどうかが識別されます。 マスクの検出では、複数の顔を区別したり、顔の属性を予測または分類したり、顔認識を行ったりすることはありません。 |
| **関心領域** | これは、入力ビデオ フレーム内のユーザー定義のゾーンまたは線です。 ある人物がビデオのこの領域を操作すると、システムによってイベントが生成されます。 たとえば、PersonCrossingLine 操作の場合、ビデオ内には線が定義されています。 人物がその線を越えると、イベントが生成されます。 |
| **Event** | イベントは、空間分析の主要な出力です。 各操作は、定期的に (毎分 1 回など)、または特定のトリガーが生じたときのどちらかで、特定のイベントを生成します。 このイベントには、入力ビデオで起きたことに関する情報は含まれますが、画像やビデオは含まれません。 たとえば、PeopleCount 操作では、人々の数が変化するたびに (トリガー)、または 1 分ごとに (定期的に)、更新された数を含んだイベントを生成できます。 |

## <a name="get-started"></a>作業開始

### <a name="public-preview-gating"></a>パブリック プレビューの限定

空間分析向けに設計されたシナリオで空間分析を使用してもらえるように、Microsoft では、お客様がアプリケーション プロセスを通じてこのテクノロジを利用できるようにしています。 空間分析にアクセスするには、最初にオンラインの受付フォームに入力する必要があります。 [アプリケーションはこちらから開始します](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)。

空間分析パブリック プレビューへのアクセスは、Microsoft の資格基準、審査プロセス、およびこの制限プレビュー期間中での限られた数のお客様に対するサポートの可用性に基づき、Microsoft の独自の判断によって決定されます。 パブリック プレビューでは、Microsoft と重要な関係を持ち、推奨のユース ケースと、責任ある AI へのコミットメントに従った追加シナリオに関して Microsoft と協力することに関心があるお客様を求めています。

### <a name="follow-a-quickstart"></a>クイックスタートの実施

空間分析へのアクセスが許可されたら、[クイックスタート](spatial-analysis-container.md)に従って、コンテナーを設定し、ビデオの分析を開始します。

## <a name="responsible-use-of-spatial-analysis-technology"></a>責任を持って空間分析テクノロジを使用する

責任を持って空間分析テクノロジを使用する方法については、[透明性に関するメモ](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext)を参照してください。 Microsoft の透明性に関するメモは、AI テクノロジの機能のしくみ、システムのパフォーマンスと動作に影響を与えるシステム所有者の選択肢、およびテクノロジ、人、環境などのシステム全体について検討することの重要性を理解するためのものです。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [クイックスタート: 空間分析コンテナー](spatial-analysis-container.md)''''''''''''