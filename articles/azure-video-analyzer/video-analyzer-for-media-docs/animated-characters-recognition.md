---
title: Azure Video Analyzer for Media (旧称 Video Indexer) を使用したアニメーション化されたキャラクターの検出
description: 'このトピックでは、Azure Video Analyzer for Media (旧称: Video Indexer) を使用して、アニメーション化されたキャラクターを検出する方法について説明します。'
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: d12b7a1911bb0c63d7f546a8b7a30e0287a05759
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610600"
---
# <a name="animated-character-detection-preview"></a>アニメーション化されたキャラクターの検出 (プレビュー)

Azure Video Analyzer for Media (旧称: Video Indexer) は、[Cognitive Services Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) との統合により、アニメーション化されたコンテンツでキャラクターの検出、グループ化、認識をサポートします。 この機能は、ポータルと API の両方で使用できます。

特定のアニメーション モデルを使用してアニメーション ビデオをアップロードすると、Video Analyzer for Media では、キーフレームの抽出、それらのフレーム内のアニメーション化されたキャラクターの検出、類似したキャラクターのグループ化、最適なサンプルの選択が行われます。 次に、グループ化されたキャラクターが Custom Vision に送信され、トレーニングされたモデルに基づいてキャラクターが識別されます。 

モデルのトレーニングを開始する前に、キャラクターが名前なしで検出されます。 名前を追加してモデルをトレーニングすると、Video Analyzer for Media によってキャラクターが認識され、それに応じて名前が付けられます。

## <a name="flow-diagram"></a>フロー図

次の図は、アニメーション化されたキャラクターの検出プロセスのフローを示しています。

![フロー図](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>アカウント

Video Analyzer for Media アカウントの種類に応じて、異なる機能セットを使用できます。 アカウントを Azure に接続する方法の詳細については、[Azure に接続された Video Analyzer for Media アカウントの作成](connect-to-azure.md)に関する記事を参照してください。

* 試用版アカウント: Video Analyzer for Media では、モデルを作成し、そのモデルをお使いの Video Analyzer for Media アカウントに接続するために、内部の Custom Vision アカウントが使用されます。 
* 有料アカウント: Custom Vision アカウントを自分の Video Analyzer for Media アカウントに接続します (まだアカウントをお持ちでない場合は、最初にアカウントを作成する必要があります)。

### <a name="trial-vs-paid"></a>試用版と有料版

|機能|試用版|有料|
|---|---|---|
|Custom Vision アカウント|Video Analyzer for Media によって、バックグラウンドで管理されます。 |Custom Vision アカウントが Video Analyzer for Media に接続されます。|
|アニメーション モデルの数|1 つ|アカウントあたり最大 100 個のモデル (Custom Vision の制限)。|
|モデルのトレーニング|Video Analyzer for Media では、既存のキャラクターの例に加えて、新しいキャラクターに対してモデルがトレーニングされます。|アカウント所有者は、変更を行う準備ができたらモデルをトレーニングします。|
|Custom Vision の詳細オプション|Custom Vision ポータルへのアクセス権がありません。|ご自身で、モデルを Custom Vision ポータルで調整することができます。|

## <a name="use-the-animated-character-detection-with-portal--and-api"></a>ポータルおよび API でアニメーション化されたキャラクターの検出を使用する

詳細については、「[ポータルおよび API でアニメーション化されたキャラクターの検出を使用する](animated-characters-recognition-how-to.md)」を参照してください。

## <a name="limitations"></a>制限事項

* 現時点では、"アニメーション ID" 機能は東アジア リージョンでサポートされていません。
* ビデオの品質が低い場合、ビデオ内でサイズが小さいキャラクターや遠くに見えるキャラクターは正しく識別されない可能性があります。
* アニメーション化されたキャラクター セットごとにモデルを使用することをお勧めします (アニメーション化された系列ごと、など)。

## <a name="next-steps"></a>次のステップ

[Video Analyzer for Media の概要](video-indexer-overview.md)
