---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3fae7fc37b010a754b7fe1dd9b6e7b092ae534e8
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131576857"
---
### <a name="iot-edge-for-linux-on-windows-installation"></a>IoT Edge for Linux on Windows のインストール

以下の最小要件を備えた Windows デバイス:

* システム要件
   * Windows 10¹/11 (Pro、Enterprise、IoT Enterprise)
   * Windows Server 2019¹/2022  
   <sub>¹ 現在のすべての累積更新プログラムがインストールされた Windows 10 および Windows Server 2019 最小ビルド 17763。</sub>

* ハードウェア要件
  * 最小空きメモリ容量: 1 GB
  * 最小空きディスク領域:10 GB

* 仮想化サポート
  * Windows 10 で Hyper-V を有効にします。 詳細については、「[Windows 10 上に Hyper-V をインストールする](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)」を参照してください。
  * Windows サーバーで、Hyper-V ロールをインストールし、既定のネットワーク スイッチを作成します。 詳細については、「[Azure IoT Edge for Linux on Windows の入れ子になった仮想化](../articles/iot-edge/nested-virtualization.md)」を参照してください。
  * 仮想マシンで、入れ子になった仮想化を構成します。 詳細については、[入れ子になった仮想化](../articles/iot-edge/nested-virtualization.md)に関するページを参照してください。

* ネットワークのサポート
  * Windows Server には既定のスイッチは付属していません。 EFLOW を Windows Server デバイスにデプロイする前に、仮想スイッチを作成する必要があります。  詳細については、[Windows 上の Linux のために仮想スイッチを作成する](../articles/iot-edge/how-to-create-virtual-switch.md)ことに関する記事を参照してください。
  * デスクトップ バージョンの Windows には、EFLOW のインストールに使用できる既定のスイッチが付属しています。 必要な場合は、独自のカスタム仮想スイッチを作成できます。

> [!TIP]
> Azure IoT Edge for Linux on Windows のデプロイで **GPU アクセラレーション対応の Linux モジュール** を使用する場合は、いくつかの構成オプションを検討する必要があります。
>
> お使いの GPU のアーキテクチャに応じて適切なドライバーをインストールする必要があり、Windows Insider Program ビルドを入手することが必要な場合があります。 構成上の必要性を特定し、それらの前提条件を満たすには、「[Azure IoT Edge for Linux on Windows 用の GPU アクセラレーション](../articles/iot-edge/gpu-acceleration.md)」を参照してください。
>
> 時間をとって、現時点で GPU アクセラレータの前提条件を満たしていることをご確認ください。 インストール中に GPU アクセラレーションを使用する場合は、インストール プロセスを再起動する必要があります。

**PowerShell** または **Windows Admin Center** を使用して、IoT Edge デバイスを管理できます。 ユーティリティごとに個別の前提条件があります。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用する場合は、次の手順に従って、Azure IoT Edge for Linux on Windows のインストールと Linux 仮想マシンのデプロイに使用するターゲット デバイスを準備してください。

1. ターゲット デバイスの実行ポリシーを `AllSigned` に設定します。 現在の実行ポリシーは、管理者特権の PowerShell プロンプトで次のコマンドを使用して確認できます。

   ```powershell
   Get-ExecutionPolicy -List
   ```

   `local machine` の実行ポリシーが `AllSigned` になっていない場合は、次のようにして実行ポリシーを設定できます。

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

Azure IoT Edge for Linux on Windows PowerShell モジュールの詳細については、[PowerShell 関数リファレンス](../articles/iot-edge/reference-iot-edge-for-linux-on-windows-functions.md)を参照してください。

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Windows Admin Center を使用する場合は、次の手順に従って Windows Admin Center をダウンロードし、Windows Admin Center Azure IoT Edge 拡張機能をインストールしてください。

   1. [Windows Admin Center のインストーラー](https://aka.ms/wacdownload)をダウンロードして実行します。 インストール ウィザードの指示に従って Windows Admin Center をインストールします。

   1. インストールが完了したら、サポートされているブラウザーを使用して Windows Admin Center を開きます。 Windows 10 上の Microsoft Edge (Windows 10 バージョン 1709 以降)、Google Chrome、Microsoft Edge Insider などのブラウザーがサポートされています。

   1. Windows Admin Center を初めて使用するときに、使用する証明書の選択を求められます。 証明書として **[Windows Admin Center Client]\(Windows Admin Center クライアント\)** を選択します。

   1. Azure IoT Edge 拡張機能をインストールします。 Windows Admin Center ダッシュボードの右上にある歯車アイコンを選択します。

      ![ダッシュボードの右上にある歯車アイコンを選択して、設定にアクセスする (PNG)。](../articles/iot-edge/media/how-to-provision-devices-at-scale-linux-on-windows-x509/select-gear-icon.png)

   1. **[Settings]\(設定\)** メニューの **[Gateway]\(ゲートウェイ\)** で **[Extensions]\(拡張機能\)** を選択します。

   1. **[Available extensions]\(利用できる拡張\)** タブの拡張機能の一覧で、**Azure IoT Edge** を見つけます。 それを選択し、拡張機能の一覧の上にある **[Install]\(インストール\)** プロンプトを選択します。

   1. インストールが完了すると、 **[Installed extensions]\(インストールされている拡張機能\)** タブのインストールされている拡張機能の一覧に Azure IoT Edge が表示されます。

---
