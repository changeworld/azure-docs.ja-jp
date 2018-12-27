---
title: Project Acoustics についてよく寄せられる質問
titlesuffix: Azure Cognitive Services
description: このページでは、ダウンロード手順やベイク プロセスなど、Project Acoustics についてよく寄せられる質問に回答します。
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.component: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: b8b5ea39c1cfb3a37a8e3009f59312728bcb0331
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900395"
---
# <a name="frequently-asked-questions"></a>よく寄せられる質問

## <a name="what-is-project-acoustics"></a>Project Acoustics とは

Project Acoustics Unity プラグインは、実行時よりも前に音波の挙動を計算する音響システムであり、静的ライティングに似ています。 複雑な波動物理学計算がクラウドで実行されるため、実行時の CPU コストを低減します。  

## <a name="where-can-i-download-the-plugin"></a>どこでプラグインをダウンロードできますか?

音響効果プラグインの評価に関心がある方は、[こちら](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u)からご登録のうえ、デザイナー プレビューにご参加ください。

## <a name="is-azure-used-at-runtime"></a>Azure は実行時に使用されますか?

いいえ、クラウド統合はシーン設定の一部としての事前計算段階の間にのみ使用されます。
 
## <a name="what-is-simulation-input"></a>シミュレーション入力とは何ですか? 

シミュレーション入力は 3D シーン、仮想環境、またはゲーム レベルです。 Project Acoustics は、滑らかな閉鎖や散乱など、音の物理的特性を厳密にモデル化した 3D 容積測定波動シミュレーションを実行します。
 
## <a name="what-is-the-runtime-cost"></a>実行時コストはどのくらいですか?

Acoustics は 1 ソース、1 フレームあたり CPU の約 0.01% を占有します。 RAM の使用量はシーンのサイズに依存し、10 ～ 100 MB の範囲です。
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>レベル ジオメトリを簡略化する必要はありますか? 三角形の数を制御しますか? メッシュを水密にしますか?

いいえ。 システムは詳細レベルのジオメトリを直接取り込みます。 内部処理のためにボクセル化されます。
 
## <a name="whats-in-the-runtime-lookup-table"></a>実行時ルックアップ テーブルの内容は何ですか?

ACE ファイルは、無数のソース/リスナー位置ペア間の音響パラメーターのテーブルです。
 
## <a name="can-it-handle-moving-sources"></a>移動するソースを扱えますか?

はい。**Microsoft Acoustics** Unity 立体化プラグインは、オーディオ処理のティックごとに、現在のソース位置とリスナー位置を使用してルックアップ テーブルを照会します。 立体化処理の DSP は、ティックごとに音響処理パラメーターを円滑に更新します。
 
## <a name="can-it-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>動的なジオメトリを扱えますか? 閉じるドアは? 吹き飛ばされる壁は?

いいえ。 音響パラメーターはゲーム レベルの静的状態に基づいて事前計算されます。 ドアのジオメトリは音響から除外し、確立された技術を使用して、破壊と移動が可能なゲーム オブジェクトの状態に基づいて閉鎖を適用することをお勧めします。
 
## <a name="does-it-handle-materials"></a>マテリアルを扱いますか?

はい。 レベル内の物理マテリアル名からマテリアルが選ばれ、吸音性を決定します。
 
## <a name="what-do-the-probes-represent"></a>"プローブ" とは何のことですか?

プローブは、可能性のあるプレイヤー位置のサンプリングです。 各プローブは、プローブ位置を起点とした、シーンの個別の波動シミュレーションを表します。 実行時に、リスナー位置の音響パラメーターが近くのプローブ位置から補間されます。
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>なぜクラウドでそんなに多くの計算を消費するのですか? どのようなメリットがあるのですか?

Project Acoustics は、アーキテクチャのすべての側面を考慮に入れ、きわめて複雑な仮想環境の場合でも正確で信頼できる音響パラメーターを提供します。 一切の手作業、動的リバーブ変動、描画ボリュームのない、滑らかな閉鎖/障害を提供します。 実行中ずっと CPU のライトが点いたままです。

## <a name="what-exactly-happens-during-baking"></a>"ベイク" の間、正確には何が起きているのですか?

システムは潜在的なプレイヤー位置を考慮して、一定間隔の "プローブ" サンプル位置を生成します。 レベルのベイクは、プローブごとの独立したタスクで構成されます: システムはプローブを中心にした直方体の "シミュレーション領域" を考慮し、その領域内で最大 25 cm の解像度で詳細な波動シミュレーションを行います。

## <a name="next-steps"></a>次の手順
* [サンプル シーン](sample-walkthrough.md)を調査する

