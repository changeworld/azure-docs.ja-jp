---
title: '例: Project Acoustics'
titlesuffix: Azure Cognitive Services
description: このチュートリアルでは、Project Acoustics の Unity サンプル シーン (デスクトップや VR へのデプロイを含む) について説明します。
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.component: acoustics
ms.topic: sample
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: f5ea565e68579dfad601d1037daeb4113e3daa43
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901160"
---
# <a name="unity-sample-walkthrough"></a>Unity サンプルのチュートリアル
これは Project Acoustics サンプルのチュートリアルです。 Project Acoustics の詳細については、[Project Acoustics の概要](what-is-acoustics.md)に関するページを参照してください。 Project Acoustics パッケージを既存の Unity プロジェクトに追加する方法については、[ファースト ステップ ガイド](getting-started.md)を使用してください。

## <a name="requirements-for-running-the-sample-project"></a>サンプル プロジェクトを実行するための要件
* .NET 4.x スクリプト ランタイム バージョンを使用する Unity 2018.2+
* Windows 64 ビット Unity エディター
* サンプルが Windows デスクトップ、UWP、および Android ターゲット (ヘッドマウント ディスプレイ (HMD) を含む) をサポートしていること
* ベイク プロセスに必要な Azure Batch サブスクリプション

## <a name="sample-project-setup"></a>サンプル プロジェクトのセットアップ
**MicrosoftAcoustics.Sample.unitypackage** をダウンロードしてインポートします。 インポートでは、**立体化**や**スクリプト ランタイム バージョン**を含むプロジェクト設定が、プラグインの要件を満たすように更新されます。 これが完了すると、スクリプト ランタイム バージョンを **.NET 4.x と同等**に変更することに関する **AcousticsGeometry.cs** からのエラーが Unity コンソールに表示されます。 この設定の変更はパッケージ インポートの一部として実行されますが、有効にするには Unity の再起動が必要です。 ここで Unity を再起動します。

## <a name="running-the-sample"></a>サンプルの実行
このサンプルには、デモ シーン **Assets/AcousticsDemo/ProjectAcousticsDemo.unity** が含まれています。 このシーンには 3 つの音源があります。 既定では、1 つの音源のみが再生中で、他の 2 つは一時停止しています。 これらは **[階層]** の **[Sound Sources]\(音源\)** の下にあります。 汎用のナビゲーション スクリプトの作成に役立つように、Main Camera は CameraHolder オブジェクトの子です。 

![階層ビュー](media/SampleHierarchyView.png)

このシーンは既にベイクされており、**階層**内の **MicrosoftAcoustics** プレハブに関連付けられた ACE ファイルが含まれています。 

Unity エディターの再生ボタンをクリックして、このシーンがどのように聞こえるかを確認します。 デスクトップで、W、A、S、D、およびマウスを使用して場所を移動します。 音響があるシーンとないシーンがどのように聞こえるかを比較するには、オーバーレイ テキストが赤色で表示されるまで **R** ボタンを押して、「Acoustics: Disabled (音響: 無効)」と言います。 その他のコントロールのキーボード ショートカットを表示するには、**F1** を押します。 すべてのコントロールは、右クリックして実行するアクションを選択し、左クリックしてアクションを実行することによって使用できます。

## <a name="targeting-other-platforms"></a>他のプラットフォームをターゲットにする
このサンプルには、Windows デスクトップ、UWP、Windows Mixed Reality、Android、および Oculus Go 上で実行するための設定が含まれています。 既定では、このプロジェクトは Windows デスクトップ用に構成されています。 VR プラットフォームをターゲットにするには、プレーヤー設定 (**[編集] > [プロジェクト設定] > [プレーヤー]**) に移動し、**[XR Settings] (XR 設定)** を見つけて、**[Virtual Reality Supported] (サポートされている仮想現実)** チェックボックスにチェックを入れます。

![VR を有効にする](media/VRSupport.png)  

VR ヘッドセットを PC に接続します。 **[ファイル] > [Build Settings] (ビルド設定)** に移動し、**[Build and Run] (ビルドおよび実行)** をクリックしてサンプルを VR ヘッドセットにデプロイします。 ヘッドセットのモーション コントローラーを使用してシーン内を移動するか、またはキーボード上の W、A、S、D を使用してみてください。    
Android および Oculus Go をターゲットにするには、**[Build Settings] (ビルド設定)** メニューから [Android] を選択します。 **[Switch Target] (ターゲットの切り替え)**、**[Build and Run] (ビルドおよび実行)** の順にクリックします。 これにより、接続された Android デバイスにサンプル シーンがデプロイされます。 Android 用の Unity の開発については、[Unity のドキュメント](https://docs.unity3d.com/Manual/android-GettingStarted.html)を参照してください。

![Android をターゲットにする](media/TargetAndroid.png)  

## <a name="next-steps"></a>次の手順
* 独自のベイク用の[Azure アカウントを作成する](create-azure-account.md)
* [設計プロセス](design-process.md)を調査する

