---
title: 'クイックスタート: 新しい HoloLens Unity アプリを作成する'
description: このクイックスタートでは、Object Anchors を使用する新しい HoloLens Unity アプリを作成する方法について説明します。
author: RamonArguelles
manager: virivera
ms.author: rgarcia
ms.date: 06/23/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: a83606d76beb4fd9cead19293636b9e5348d4990
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124777566"
---
# <a name="quickstart-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-object-anchors"></a>クイックスタート: Azure Object Anchors を使用する新しい HoloLens Unity アプリを作成する詳細な手順

このクイックスタートでは、[Azure Object Anchors](../overview.md) を使用した新しい HoloLens Unity アプリを作成する方法について説明します。 Azure Object Anchors は、3D アセットを HoloLens の物体認識 Mixed Reality エクスペリエンスを実現する AI モデルに変換するマネージド クラウド サービスです。 作業を終えると、Unity で作成された HoloLens アプリで、物理世界のオブジェクトを検出できるようになります。

## <a name="prerequisites"></a>前提条件

このクイック スタートを実行するには、以下が必要です。

* [Unity HoloLens](get-started-unity-hololens.md) または [MRTK を使用した Unity HoloLens](get-started-unity-hololens-mrtk.md) のクイックスタートでの、すべての前提条件。
* <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2020.3.8f1 以降を使用している Unity Hub</a>

## <a name="getting-started"></a>作業の開始

最初に、プロジェクトと Unity シーンを設定します。

1. Unity Hub を開始します。
1. **[New]\(新規\)** を選択し、Unity 2020.3.8f1 以降を選択します。
1. **[3D]** が選択されていることを確認します。
1. プロジェクトに名前を付け、保存 **場所** を入力します。
1. **［作成］** を選択します
1. **Unity Editor** が開いたら、 **[File]\(ファイル\)**  >  **[Save As]\(名前を付けて保存\)** を使用して、空の既定のシーンを新しいファイルに保存します。
1. **Scenes** フォルダーを選択し、新しいシーンに **Main** という名前を付けて、 **[Save]\(保存\)** ボタンを押します。

## <a name="configure-as-uwp"></a>UWP として構成する

1. **[File]\(ファイル\) -> [Building Settings]\(ビルド設定\)** を選択します。
1. **[ユニバーサル Windows プラットフォーム]** を選択し、 **[Switch Platform]\(プラットフォームの切り替え\)** を選択します。
1. 最初にいくつかのコンポーネントをダウンロードする必要がある旨のメッセージが Unity Editor に表示された場合は、それらをダウンロードしてインストールします。

## <a name="install-mixed-reality-feature-tool-feature-packages"></a>Mixed Reality Feature Tool の機能パッケージをインストールする

1. <a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">Mixed Reality Feature Tool</a> のドキュメントに従ってツールを設定し、使用方法を確認してください。
1. **[Platform Support]\(プラットフォーム サポート\)** セクションで、**Mixed Reality OpenXR Plugin** 機能パッケージのバージョン 1.0.0 以降を Unity プロジェクト フォルダーにインストールします。
1. **[Azure Mixed Reality Services]** セクションで、**Microsoft Azure Object Anchors** 機能パッケージを Unity プロジェクト フォルダーにインストールします。
1. **Unity Editor** に戻ります。 **Mixed Reality Feature Tool** の機能パッケージがインストールされる間、数分かかることがあります。
1. 新しい入力システムを有効にするかどうかを確認するダイアログが表示されます。
1. **[はい]** ボタンを選択します。
1. インストール プロセスが完了すると、Unity が自動的に再起動します。

## <a name="configure-openxr"></a>OpenXR を構成する

1. 引き続き **[Build Settings]\(ビルド設定\)** ウィンドウで作業します。
1. **[Player settings...]\(プレイヤー設定\)** ボタンを選択します。
1. **[Project Settings]\(プロジェクト設定\)** ウィンドウが開きます。
1. **[XR Plug-in Management]** エントリを選択します。
1. 「<a href="/windows/mixed-reality/develop/unity/xr-project-setup#configuring-xr-plugin-management-for-openxr" target="_blank">OpenXR の XR Plugin Management の構成</a>」のドキュメントに従って、 **[Plug-in Providers]\(プラグイン プロバイダー\)** の一覧で **[OpenXR]** と **[Microsoft HoloLens feature set]\(Microsoft HoloLens 機能セット\)** を設定します。

## <a name="set-capabilities"></a>機能を設定する

1. 引き続き **[Project Settings]\(プロジェクト設定\)** ウィンドウで作業します。
1. **[Player]\(プレイヤー\)** エントリを選択します。
1. **[Player Settings]\(プレイヤー設定\)** の **[Inspector Panel]\(インスペクター パネル\)** で、 **[Universal Windows Platform settings]\(ユニバーサル Windows プラットフォーム設定\)** アイコンを確実に選択します。
1. **[Publishing Settings]\(公開設定\)** の [Capabilities]\(機能\) セクションで、**InternetClientServer**、**WebCam**、および **SpatialPerception** を確実に選択します。

## <a name="set-up-the-project-settings"></a>プロジェクト設定をセットアップする

1. 引き続き **[Project Settings]\(プロジェクト設定\)** ウィンドウで作業します。
1. **[Quality]\(品質\)** エントリを選択します。
1. **ユニバーサル Windows プラットフォーム** ロゴの下の列で、 **[Default]\(既定値\)** 行の矢印を選択し、 **[Very Low]\(非常に低い\)** を選択します。 **[ユニバーサル Windows プラットフォーム]** 列のボックスと **[Very Low]\(非常に低い\)** 行が緑色の場合、設定が適切に適用されていることがわかります。
1. **[Project Settings]\(プロジェクト設定\)** と **[Build Settings]\(ビルド設定\)** のウィンドウを閉じます。
1. <a href="/windows/mixed-reality/develop/unity/xr-project-setup#optimization" target="_blank">最適化</a>に関するドキュメントに従って、HoloLens 2 に推奨されるプロジェクト設定を適用します。

## <a name="set-up-the-main-virtual-camera"></a>メインの仮想カメラを設定する

1. **[Hierarchy]\(階層\)** ペインで、 **[Main Camera]\(メイン カメラ\)** を選択します。
1. **[Inspector]\(インスペクター\)** で、変換座標を **0、0、0** に設定します。
1. **[Clear Flags]\(フラグをクリア\)** プロパティを探して、ドロップダウンを **[Skybox]\(スカイボックス\)** から **[Solid Color]\(ソリッド カラー\)** に変更します。
1. **[Background]\(背景\)** フィールドを選択してカラー ピッカーを開きます。
1. **R、G、B、A** を **0** に設定します。
1. **[Clipping Planes Near]\(ニア クリップ面\)** プロパティを 0.1 に変更します。
1. **[Add Component]\(コンポーネントの追加\)** を選択し、**Tracked Pose Driver** を検索して追加します。
1. **[File]\(ファイル\)**  >  **[Save]\(保存\)** を選択します。

## <a name="trying-it-out"></a>試してみる

問題ないことをテストするために、**Unity** でアプリをビルドし、**Visual Studio** でそれをデプロイします。 そのためには、「<a href="/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio" target="_blank">**MR Basics 100: Getting started with Unity (MR の基本 100: Unity の概要)」** コース</a>の 6 章に従ってください。 Unity のスタート画面、そしてクリアな表示が示されます。

## <a name="create-your-script"></a>スクリプトを作成する

1. **[Project]\(プロジェクト\)** ウィンドウで、**Assets** フォルダーの下に新しいフォルダー (**Scripts**) を作成します。
1. そのフォルダーを右クリックして、 **[Create]\(作成\) >** **[C# Script]\(C# スクリプト\)** の順に選択します。 **ObjectSearch** という名前を指定します。
1. **[GameObject]\(ゲームオブジェクト\)**  ->  **[Create Empty]\(空のオブジェクトの作成\)** の順に移動します。
1. それを選択し、 **[Inspector]\(インスペクター\)** でその名前を **GameObject** から **Object Observer** に変更します。
1. **[Add Component]\(コンポーネントの追加\)** を選択し、**ObjectSearch** スクリプトを検索して追加します。
1. **[File]\(ファイル\)**  >  **[Save]\(保存\)** を選択します。

## <a name="start-implementing-your-app"></a>アプリの実装を開始する

Object Anchors Runtime SDK を使用して **ObjectSearch** スクリプトへの自作のコードの追加を開始する準備ができました。 [SDK の概要](../concepts/sdk-overview.md)を参照して基本を学習し、サンプル コードを使用して試してみることができます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [概念: SDK の概要](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [FAQ](../faq.md)

> [!div class="nextstepaction"]
> [変換 SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)
