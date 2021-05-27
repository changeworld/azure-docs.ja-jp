---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/30/2021
ms.author: juliako
ms.openlocfilehash: b5d950aca0090067d426457cc09495fd0b8344b5
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110383878"
---
1. Visual Studio Code で src/edge に移動します。 作成した .env ファイルと、いくつかのデプロイ テンプレート ファイルを確認できます。

   展開テンプレートでは、いくつかのプレースホルダー値で、エッジ デバイスの配置マニフェストが参照されています。 それらの変数の値が .env ファイルに格納されています。
2. 次に、src/cloud-to-device-console-app フォルダーに移動します。 ここでは、作成した appsettings.json ファイルと、他のいくつかのファイルを確認できます。

   - c2d-console-app.csproj: これは Visual Studio Code 用のプロジェクト ファイルです。
   - operations.json: このファイルには、このプログラムで実行するさまざまな操作のリストが記載されています。
   - Program.cs:このサンプル プログラム コードでは次を実行します。
     - アプリ設定を読み込みます。
     - Azure Video Analyzer モジュールのダイレクト メソッドを呼び出して、トポロジの作成、パイプラインのインスタンス作成とアクティブ化を実行します。
     - **[ターミナル]** ウィンドウでパイプラインの出力、 **[出力]** ウィンドウで IoT ハブに送信されたイベントを調べるために一時停止します。
     - ライブ パイプラインを非アクティブ化し、ライブ パイプラインを削除して、トポロジを削除します。
