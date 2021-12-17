---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: juliako
ms.openlocfilehash: c8d77cf500956e94610d7fab72d3758aeb767b54
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860430"
---
前提条件の一環として、サンプル コードをフォルダーにダウンロードしてあります。 サンプル ファイルを確認して編集するには、これらの手順に従います。

1. Visual Studio Code で、*src/edge* に移動します。 *.env* ファイルと、いくつかの展開テンプレート ファイルがあります。
1. *deployment.grpcyolov3icpu.template.json* は、エッジ デバイスの配置マニフェストを参照しています。 そこには、いくつかのプレースホルダー値が含まれています。 *.env* ファイルには、それらの変数の値が格納されています。
1. *src/cloud-to-device-console-app* フォルダーに移動します。 ここには、*appsettings.json* ファイルと、他にいくつかのファイルがあります。
    
    * c2d-console-app.csproj - Visual Studio Code のプロジェクト ファイルです。
    * operations.json - プログラムで実行する操作のリストです。
    * Program.cs - サンプル プログラム コードです。 このコードによって以下が行われます。
    
        * アプリ設定を読み込みます。
        * Azure Video Analyzer モジュールによって公開されているダイレクト メソッドを呼び出します。 このモジュールを使用し、そのダイレクト メソッドを呼び出すことで、ライブ ビデオ ストリームを分析できます。
        * プログラムの出力を **[ターミナル]** ウィンドウで調べたり、モジュールによって生成されたイベントを **[出力]** ウィンドウで調べたりできるように、一時停止します。
        * ダイレクト メソッドを呼び出して、リソースをクリーンアップします。
1. operations.json ファイルを編集します。
    
    * パイプラインへのリンクを変更します。<br/>` "topologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/motion-with-grpcExtension/topology.json"`
    * livePipelineSet で、前のリンクの値と一致するようにパイプライン トポロジの名前を編集します。<br/>`"topologyName" : "EVROnMotionPlusGrpcExtension"`
    * topologyDelete で、名前を編集します。<br/>`"name" : "EVROnMotionPlusGrpcExtension"`
