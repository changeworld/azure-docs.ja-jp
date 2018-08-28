---
title: 音響効果の概要 - Cognitive Services
description: VR や従来型の画面を対象にしたプロジェクトは、Project Acoustics Unity プラグインによって閉塞、反響、立体化の効果を利用することができます。
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: overview
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 08c6d8b8e229aabcc05fdfde2d8ffadf3a6346c2
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2018
ms.locfileid: "40181006"
---
# <a name="what-is-project-acoustics"></a>Project Acoustics とは
VR や従来型の画面を対象にしたプロジェクトは、Project Acoustics Unity プラグインによって閉塞、反響、立体化の効果を利用することができます。 物理学に基づく波形シミュレーションにデザイナーの意図を重ねるゲーム音響効果を設計する手段となります。

## <a name="why-use-acoustics-in-virtual-environments"></a>仮想環境で音響効果を使う理由
人間は、視聴覚の刺激を用いて周囲の状況を把握します。 仮想世界では、空間オーディオと音響効果を組み合わせることによってユーザーの臨場感を高めます。 ここで説明する音響効果ツールは、仮想世界を解析することによって、臨場感のある音響シミュレーションを作成すると共に、シミュレーション後の設計プロセスを支援します。 この解析は、周囲環境の各サーフェスについて、ジオメトリと素材の両方を対象とします。 シミュレーションには、到達方向 (ポータリング)、リバーブ出力、減衰時間、閉塞効果、障害物効果などのパラメーターが含まれます。

## <a name="how-does-this-approach-to-acoustics-work"></a>音響効果に対する実際のアプローチ
このシステムの拠り所となるのは、仮想世界のオフライン計算です。解析を実行時に行った場合よりも複雑なシミュレーションが可能となります。 このオフライン計算によって、音響パラメーターのルックアップ テーブルが生成されます。 実行時にパラメーターに適用される規則は、デザイナーが指定します。 これらの規則を調整することで、きわめて現実感のある効果が得られ、さまざまなバックグラウンド オーディオ サウンドで強い感情強度と臨場感に溢れるシーンが可能となります。

## <a name="design-process-comparison"></a>設計プロセスの比較
Project Acoustics プラグインは、Unity のシーンにおける音響効果に関して新しい設計プロセスを支援します。 この新しい設計プロセスを説明するために、現在、音響効果に広く用いられているアプローチと比較してみましょう。

### <a name="typical-approach-to-acoustics-today"></a>音響効果に対して現在用いられている一般的なアプローチ
現在、音響効果に用いられている一般的なアプローチでは、リバーブ ボリュームを自分で描画します。

![デザイン ビュー](media/reverbZonesAltSPace2.png)

そのうえで、各ゾーンのパラメーターを調整します。

![デザイン ビュー](media/TooManyReverbParameters.png)

最後に、レイトレーシング ロジックを追加することによって、シーン全体で閉塞と障害物の適切なフィルタリング、そしてポータリングのためのパス検索ロジックを確保します。 このコードでは、実行時のコストが大きくなる場合があります。 また、コーナー周辺では滑らかさに問題があるほか、不規則な形状のシーンではエッジ ケースも存在します。

### <a name="an-alternative-approach-with-physics-based-design"></a>物理学に基づく設計を使った代替アプローチ
Project Acoustics の Unity プラグインに用意されているアプローチでは、静的なシーンの図形や素材を指定することができます。 シーンはボクセル化され、レイトレーシングがプロセスで使用されないため、単純化した音響メッシュも完璧な音響メッシュも与える必要がありません。 リバーブ ボリュームでシーンをマーク アップする必要もありません。 このプラグインは、シーンをクラウドにアップロードし、そこで物理学に基づく波形シミュレーションを使用します。 その結果はルックアップ テーブルとしてプロジェクトに統合され、それに変更を加えることで、美的効果やゲームプレイ上の効果を引き出すことができます。

![デザイン ビュー](media/GearsWithVoxels.jpg)

## <a name="requirements"></a>必要条件
* Unity 2018.2 以上 (音響効果の焼き付け) および Unity 5.2 以上 (サウンド設計とデプロイ)
* Windows 64 ビット Unity エディター
* Azure Batch サブスクリプション (音響効果の焼き付け)
* Unity スクリプト ランタイムは ".NET 4.x 相当" に設定する

## <a name="platform-support"></a>プラットフォームのサポート
* Windows デスクトップ (x86 および AMD64)
* Windows UWP (x86、AMD64、ARM)
* Android (x86 および ARM64)

## <a name="download"></a>[ダウンロード]
音響効果プラグインの評価に関心がある方は、[こちら](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u)からご登録のうえ、デザイナー プレビューにご参加ください。

## <a name="next-steps"></a>次の手順
* [設計プロセス](design-process.md)の詳細を確認する。
* [実際の Unity プロジェクトに音響効果を統合](getting-started.md)してみる。

