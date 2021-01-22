---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: d07a5da3b9013700694f6c20102ef2e8c5066087
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2021
ms.locfileid: "98256562"
---
最低でも、初期トレーニング セットで、タグごとに少なくとも 30 の画像を使用することをお勧めします。 トレーニングを行ったら、追加の画像をいくつか収集し、モデルをテストすることもお勧めします。

モデルを効果的にトレーニングするには、視覚的に多様性のある画像を使用します。 以下の点で変化に富んだ画像を選択してください。
* カメラのアングル
* 照明
* background
* 見た目のスタイル
* 個人またはグループになっている被写体
* size
* type

さらに、すべてのトレーニング画像が以下の条件を満たしていることを確認します。
* .jpg、.png、.bmp、または .gif 形式
* サイズが 6 MB 未満 (予測用画像は 4 MB)
* 最短の辺が 256 ピクセル以上。これより短い画像は Custom Vision Service によって自動的にスケール アップされます

> [!NOTE]
> Trove (Microsoft Garage プロジェクト) を使用すると、トレーニング目的で画像のセットを収集して購入することができます。 画像を収集したら、それらをダウンロードした後、通常の方法で Custom Vision プロジェクトにインポートできます。 詳細については、[Trove ページ](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3)を参照してください。