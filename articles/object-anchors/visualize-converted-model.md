---
title: Object Anchors モデルを視覚化する方法
description: 変換されたモデルを視覚化する方法について説明します。
author: rgarcia
manager: vrivera
ms.author: rgarcia
ms.date: 05/28/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: a9140004d9f4d79cabb9890851ba5c5cb7b815f2
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111987865"
---
# <a name="how-to-visualize-an-object-anchors-model"></a>Object Anchors モデルを視覚化する方法

変換されたモデルを使用するために、そのモデルを視覚化する必要はありません。 ただし、必要に応じて、メッシュを簡単に表示して使用することもできます。

マイナー変更が 1 つ行われている [Unity アプリのクイックスタート](quickstarts/get-started-unity-hololens.md)の手順に従ってください。 サンプルのシーンをビルドする際、**AOASampleScene** を開くのではなく **VisualizeScene** を開いて、シーンのビルド リストに追加します。 次に **[ビルド設定]** で、**VisualizeScene** のチェックボックス *のみ* が隣にあることを確認します。それ以外のシーンは含めないでください。

:::image type="content" source="../../includes/media/object-anchors-quickstarts-unity/aoa-unity-build-settings-visualize.png" alt-text="ビルド設定の視覚化":::

**[ビルド]** ボタンを選択する *のではなく* **[ビルド設定]** ダイアログを閉じます。

**[階層]** パネルで **ビジュアライザー** GameObject を選択します。

:::image type="content" source="../../includes/media/object-anchors-quickstarts-unity/aoa-unity-hierarchy.png" alt-text="階層":::

**[インスペクター]** パネルで、 **[メッシュ ローダー (スクリプト)]** セクションの **Model パス** プロパティを見つけ、Object Anchors モデル ファイルへのパスをファイル拡張子も含めて入力します。

:::image type="content" source="../../includes/media/object-anchors-quickstarts-unity/aoa-unity-inspector.png" alt-text="インスペクター":::

Unity エディター上部の **[再生]** ボタンを選択し、 **[シーン]** ビューが選択されていることを確認します。

:::image type="content" source="../../includes/media/object-anchors-quickstarts-unity/aoa-unity-editor.png" alt-text="再生とシーン表示":::

[Unity のシーン ビュー ナビゲーション コントロール](https://docs.unity3d.com/Manual/SceneViewNavigation.html)を使用して、Object Anchors モデルを検査できるようになりました。

:::image type="content" source="./media/object-anchors-visualize.png" alt-text="モデルの視覚化":::
