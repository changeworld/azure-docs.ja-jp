---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/30/2021
ms.author: juliako
ms.openlocfilehash: 559f4eccbf1eb9bd41471937b3f1d6e846812b40
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092027"
---
1. Visual Studio Code で src/edge に移動します。 作成した .env ファイルと、いくつかのデプロイ テンプレート ファイルを確認できます。

   展開テンプレートでは、いくつかのプレースホルダー値で、エッジ デバイスの配置マニフェストが参照されています。 それらの変数の値が .env ファイルに格納されています。
2. 次に、src/cloud-to-device-console-app フォルダーに移動します。 ここでは、作成した appsettings.json ファイルと、他のいくつかのファイルを確認できます。

   - operations.json - このファイルには、プログラムで実行するさまざまな操作がリストされます。
   - main.py - これは次の処理を実行するサンプル プログラムのコードです。
     - アプリ設定を読み込みます。
     - Azure Video Analyzer モジュールのダイレクト メソッドを呼び出して、トポロジの作成、パイプラインのインスタンス作成とアクティブ化を実行します。
     - **[ターミナル]** ウィンドウでパイプラインの出力、 **[出力]** ウィンドウで IoT ハブに送信されたイベントを調べるために一時停止します。
     - ライブ パイプラインを非アクティブ化し、ライブ パイプラインを削除して、トポロジを削除します。
