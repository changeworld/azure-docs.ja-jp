---
title: 'クイック スタート: 詳細な MRTK チュートリアル'
description: このクイック スタートでは、Azure Object Anchors MRTK Unity サンプル アプリケーションの詳細を確認します
author: RamonArguelles
manager: virivera
ms.author: rgarcia
ms.date: 07/12/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 515d44cc7cb40972c67424799163bc57deba9182
ms.sourcegitcommit: b59e0afdd98204d11b7f9b6a3e55f5a85d8afdec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114371568"
---
# <a name="quickstart-in-depth-mrtk-walkthrough"></a>クイック スタート: 詳細な MRTK チュートリアル

このガイドでは、[Azure Object Anchors MRTK Unity サンプル アプリケーション](get-started-unity-hololens-mrtk.md)について詳しく説明します。 これは、サンプルの設計に関する分析情報を提供することを目的としています。 このガイドを読むことで、開発者はサンプルで Azure Object Anchors の主要な概念に関する理解を早めることができます。

## <a name="project-layout"></a>プロジェクトのレイアウト

Azure Object Anchors MRTK Unity サンプル用に作成されたアセットは、`Assets\MixedReality.AzureObjectAnchors` に格納されます。 サブフォルダーは次のとおりです。

- **アイコン**
  - ユーザー向けメニューで使用されるカスタム アイコンがいくつか含まれています。
- **素材**
  - サーフェスの再構築を視覚化するためのシェーダーと素材、および ''*深度専用*'' のシェーダー (深度バッファーに書き込まれ、テキストの周りのホログラムの安定化に役立つ) が含まれています。
- **Prefabs**
  - 再利用可能な Unity `GameObjects` が含まれています。 特に、`TrackableObjectPrefab` は、Azure Object Anchors で物体が検出されたときに作成される物体を表します。
- **Profiles**
  - アプリケーションを有効にするために MRTK の最小限必要な機能を記述するカスタマイズされた MRTK プロファイルが含まれています。
- **Scenes**
  - サンプルの主要なシーンである `AOASampleTestScene` が含まれています。
- **スクリプト**
  - サンプル用に記述されたスクリプトが含まれています。

## <a name="unity-scene"></a>Unity シーン

**Mixed Reality Play Space** – 主に定型 MRTK

- <a href="/windows/mixed-reality/develop/unity/mrtk-getting-started" target="_blank">Unity 用の MRTK の概要</a>。
- カメラにアタッチされた UI があります。これには、Azure Object Anchors の全体的な状態が詳しく示されます (「`OverlayDebugText.cs`」を参照)。

**Object Mixed Reality Play Space** – 主に Azure Object Anchors に関連していますが、MRTK コントロールがいくつか使用されます。 親にアタッチされている 2 つのスクリプト (`TrackableObjectSearch` と `ObjectTracker`) は、Azure Object Anchors とのプライマリ インターフェイスを表しています。

- **Menu**
  - 主に MRTK コードですが、UI 操作は、Azure Object Anchors 機能に送信されます。
  - アタッチされた `TrackableObjectMenu` スクリプトでは、MRTK UI イベントを適切な Azure Object Anchors 呼び出しにルーティングするプライマリ ジョブを実行します。
  - <a href="/windows/mixed-reality/design/hand-menu" target="_blank">MRTK ハンド メニュー</a>。
- **WorkspaceBoundingBox**
  - 境界ボックスの制御に関連付けられた MRTK スクリプトが含まれています。
  - `ModelVis` 子物体も含まれています。これは、トリッキーな検出中の配置に役立つように検出が発生する前に、Azure Object Anchors モデルを視覚化するために使用されます。

## <a name="menu-walkthrough"></a>メニューのチュートリアル

スクリプトの説明に入る前に、まずメニュー項目を確認しましょう。 この方法で、これらのメニュー項目でスクリプトを操作する方法を参照できます。

:::image type="content" source="./media/mrtk-menus.png" alt-text="mrtk メニュー":::

下部および右のサブメニューは自動的には表示されませんが、それぞれ `Search Area Settings` と `Tracker Settings` で切り替えられます。

### <a name="main-menu"></a>メイン メニュー

- **検索の開始**
  - 指定された検索領域内で物体の検索を開始します。
- **空間マッピングの切り替え**
  - 検索中に空間マッピングを表示するか、常に空間マッピングを表示するか、空間マッピングを表示しないように切り替えます。
- **Tracker Settings\(トラッカー設定\) / Search Area Settings\(検索領域の設定\)**
  - それぞれのサブメニューを切り替えます。
- **トレースの開始/トレースの停止**
  - 診断トレースを開始または終了します。
- **Upload Tracing\(トレースのアップロード\)**
  - デバッグ分析のために診断トレースを Microsoft にアップロードします。

### <a name="tracker-settings"></a>Tracker Settings\(トラッカー設定\)

- **高い精度**
  - 有効にすると、`ObjectInstanceTrackingMode` は `HighLatencyAccuratePosition` に設定されます。
  - 無効にすると、`ObjectInstanceTrackingMode` は `LowLatencyCoarsePosition` に設定されます。
- **緩い垂直方向の配置**
  - 有効にすると、`AllowedVerticalOrientationInDegrees` は 10 度に設定されます。 この機能を使用すると、傾斜している物体を検出できます。
  - 無効にすると、`AllowedVerticalOrientationInDegrees` は 0 度に設定されます。
- **スケール変更を許可する**
  - 有効にすると、`MaxScaleChange` は 0.1 に設定されます。 この機能を使用すると、Azure Object Anchors では、スケール調整を追跡する HoloLens に基づいて物体のスケールを調整できます。
  - 無効にすると、`MaxScaleChange` は 0 に設定されます。
- **[カバレッジの比率] スライダー**
  - 物体検出で一致と見なすために必要なカバレッジの比率を調整します。 値を低くすると、検出の頻度が増えます。 この機能は、検出が困難な物体には望ましい場合がありますが、偽陽性物体検出が増えることもあります。

### <a name="search-area-settings"></a>Search Area Settings\(検索領域の設定\)

- **検索領域をロックする**
  - 有効にすると、ユーザーは検索領域を変更できなくなります。
- **検索領域の自動調整**
  - 有効にすると、スクリプトで検索領域を移動して検出プロセスを絞り込めるようになります。
- **Cycle Mesh\(メッシュの切り替え\)**
  - 検出可能な `.ou` 物体のメッシュと、メッシュなしを切り替えます。

## <a name="scripts"></a>スクリプト

- **AutonomousSearchArea.cs**
  - このスクリプトは `WorkspaceBoundingBox` にアタッチされます。 スクリプトにより、`WorkspaceBoundingBox` の自動スケーリングおよび配置が試行されます。 `Auto-Adjust Search Area` が有効になっている場合に有効になります。
- **ObjectAnchorsSubscription.cs**
  - このスクリプトでは、診断データをアップロードするために必要な情報をラップします。
- **ObjectTracker.cs**
  - このスクリプトでは、Unity と Azure Object Anchors SDK の検出側面をつなぎます。
- **ObjectTrackerDiagnostics.cs**
  - このスクリプトでは、Azure Object Anchors SDK の診断機能を管理します。
- **OverlayDebugText.cs**
  - このスクリプトはメイン カメラにアタッチされます。 サンプルと Azure Object Anchors の全体的な状態をユーザーに示す役割があります。
- **PositionDebugInfo.cs**
  - このスクリプトでは単に、検出された物体にアタッチされたデバッグ テキストを強制的にユーザーに示します。
- **SearchAreaController.cs**
  - このスクリプトでは、Azure Object Anchors で物体を検索する必要がある場所を示すために使用される `WorkspaceBoundingBox` の状態を管理します。
- **SearchAreaModelVisualization.cs**
  - このスクリプトでは、`Search Area Settings` の `Cycle Mesh` 機能を有効にします。
- **SpatialMappingController.cs**
  - このスクリプトでは、`Main Menu` の `Toggle Spatial Mapping` の操作に基づいて、空間マッピングを有効にする必要があるタイミングを管理します。
- **TextToSpeech.cs**
  - このスクリプトではテキストを受け取り、音声に変換します。
- **TrackableObjectData.cs**
  - このスクリプトは、追跡できる物体のデータを表します。
- **TrackableObjectDataLoader.cs**
  - このスクリプトでは、`.ou` ファイルを読み込んで `TrackableObjectData` 項目に変換する作業を行います。
- **TrackableObjectMenu.cs**
  - ほとんどのユーザー操作は、UI からこのスクリプトに流れてから適切なスクリプトに流れます。 たとえば、`TrackableObjectMenu` には、`SpatialMappingController` にルーティングされる `ToggleSpatialMapping` があります。
- **TrackableObjectSearch.cs**
  - このスクリプトでは、検索領域のいくつかの簡単な管理を行います。 特に、ユーザーがタップしてスペースに入ると、検索ボックスがそのユーザーの前に配置される操作です。 また、フル レンダリングにより物体が遮られるのを防ぐため、検索中または物体が検出されている間は、検索ボックス内でレンダリングしないようにします。
- **TrackedObject.cs**
  - `TrackableObjectPrefab` のプライマリ スクリプト。 Azure Object Anchors によって検出された物体の視覚化状態を保持します。
- **TrackedObjectData.cs**
  - このスクリプトには、Azure Object Anchors によって現在追跡されている物体に関する情報が含まれています。

## <a name="prefabs"></a>Prefabs

- **TrackableObjectPrefab**
  - AzureObjectAnchors によって物体が検出されたときに作成されるプレハブ。 このプレハブの子である、`LogicalCenter` は物体の中心を表し、検出された物体に子コンテンツをアタッチしようとする際の適切な開始点です。 サンプルで表示される情報テキストは、この論理的な中心にアタッチされます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [概念: SDK の概要](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [FAQ](../faq.md)

> [!div class="nextstepaction"]
> [変換 SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)
