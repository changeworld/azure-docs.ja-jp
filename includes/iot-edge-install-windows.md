---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 94b115654d50fcc7924d86afcc9b78ddab05688d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845101"
---
## <a name="install-iot-edge"></a>IoT Edge をインストールする

このセクションでは、Windows VM または物理デバイスを IoT Edge 用に準備します。 次に、IoT Edge をインストールします。

Azure IoT Edge は、OCI と互換性のあるコンテナー ランタイムに依存します。 [Moby](https://github.com/moby/moby) (Moby ベースのエンジン) はインストール スクリプトに含まれているので、エンジンをインストールする追加手順はありません。

IoT Edge ランタイムをインストールするには:

1. PowerShell を管理者として実行します。

   PowerShell(x86) ではなく、PowerShell の AMD64 セッションを使用します。 使用しているセッションの種類がわからない場合は、次のコマンドを実行します。

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. [Deploy-IoTEdge](../articles/iot-edge/reference-windows-scripts.md#deploy-iotedge) コマンドを実行します。これにより、次のタスクが実行されます。

   * お使いの Windows コンピューターがサポートされているバージョンであることを確認します
   * コンテナー機能をオンにします
   * Moby エンジンと IoT Edge ランタイムをダウンロードします

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

3. メッセージが表示されたら、デバイスを再起動します。

デバイスに IoT Edge をインストールするときは、追加のパラメーターを使用して、プロセスを次のように変更できます。

* プロキシ サーバーを経由するようトラフィックを誘導する
* オフライン インストール用にローカル ディレクトリをインストーラーに指定する

これらの追加パラメーターの詳細については、[Windows コンテナーを使用した IoT Edge 用の PowerShell スクリプト](../articles/iot-edge/reference-windows-scripts.md)に関するページを参照してください。
