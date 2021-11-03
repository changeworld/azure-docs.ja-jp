---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/07/2021
ms.author: juliako
ms.openlocfilehash: d86ad88e122909225b5b64861623ce9b5b2df8af
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030045"
---
前提条件の一環として、サンプル コードをフォルダーにダウンロードしてあります。 サンプル ファイルを確認して編集するには、これらの手順に従います。

1. Visual Studio Code で、**src/edge** に移動します。 *.env* ファイルと、いくつかの展開テンプレート ファイルがあります。

    展開テンプレートは、エッジ デバイスの配置マニフェストを参照しています。 そこには、いくつかのプレースホルダー値が含まれています。 .env ファイルには、それらの変数の値が格納されています。
1. **src/cloud-to-device-console-app** フォルダーに移動します。 ここには、*appsettings.json* ファイルと、他にいくつかのファイルがあります。

    * **c2d-console-app.csproj** - Visual Studio Code のプロジェクト ファイルです。
    * **operations.json** - プログラムで実行する操作のリストです。
    * **Program.cs** - サンプル プログラム コードです。 このコードによって以下が行われます。

        * アプリ設定を読み込みます。
        * Video Analyzer エッジ モジュールによって公開されている[ダイレクト メソッド](../../../direct-methods.md)を呼び出します。
        * プログラムの出力を **[ターミナル]** ウィンドウで調べたり、モジュールによって生成されたイベントを **[出力]** ウィンドウで調べたりできるように、一時停止します。
        * ダイレクト メソッドを呼び出して、リソースをクリーンアップします。
1. **operations.json** ファイルを編集します。
    * パイプラインへのリンクを変更します。 <br/>`"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-motion-file-sink/topology.json" `
    * livePipelineSet で、前のリンクの値と一致するようにパイプライン トポロジの名前を編集します。 <br/>`"topologyName" : "EVRToFilesOnMotionDetection" `
    * PipelineTopologyDelete の下で、名前を編集します。 <br/>`"name": "EVRToFilesOnMotionDetection" `
