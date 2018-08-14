---
title: Azure IoT Edge on Windows のシミュレート | Microsoft Docs
description: Windows のシミュレートされたデバイスに Azure IoT Edge ランタイムをインストールし、最初のモジュールをデプロイする
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 06/08/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 1a45841564b0c985662e6d2db320111fa27d1e92
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578179"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>クイック スタート: 初めての IoT Edge モジュールを Azure Portal から Windows デバイスに展開する - プレビュー

Azure IoT Edge を使用すると、すべてのデータをクラウドにプッシュしなくても、デバイスで分析とデータ処理を実行することができます。 IoT Edge チュートリアルでは、さまざまな種類のモジュールをデプロイする方法について説明しますが、まずテストするデバイスが必要になります。 

このクイック スタートでは、次の方法について説明します。

1. IoT Hub の作成
2. IoT Edge デバイスを登録する
3. IoT Edge ランタイムを開始する
4. モジュールを展開する

![チュートリアル アーキテクチャ][2]

このクイック スタートで展開するモジュールは、温度、湿度、および圧力のデータを生成するシミュレートされたセンサーです。 その他の Azure IoT Edge チュートリアルは、ここで行う作業を基盤としており、ビジネスに関する分析情報を得るためにシミュレートされたデータを分析するモジュールをデプロイします。 

>[!NOTE]
>Windows 向けの IoT Edge ランタイムは、[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)段階にあります。

アクティブな Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント][lnk-account]を作成してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは、Windows を実行しているコンピューターまたは仮想マシンを使用して、IoT デバイスがシミュレートされていることを前提としています。 仮想マシンで Windows を実行している場合は、[入れ子になった仮想化][lnk-nested]を有効にして、少なくとも 2 GB のメモリを割り当てます。 

IoT Edge デバイスに使用するマシンで、次の前提条件を準備してください。

1. サポートされている Windows バージョンを使用していることを確認します。
   * Windows 10 以降
   * Windows Server 2016 以降
2. [Docker for Windows][lnk-docker] をインストールし、実行します。
3. [Linux コンテナー](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)を使用するように Docker を構成します。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

このクイック スタートでは、最初に Azure portal で IoT ハブを作成します。
![IoT Hub を作成する][3]

このクイック スタートで作成するすべてのリソースの維持および管理に使用する IoT ハブをリソース グループ内に作成します。 **IoTEdgeResources** などの覚えやすい名前を付けます。 このクイック スタートとチュートリアルのすべてのリソースを 1 つのグループ内に配置することで、それらをまとめて管理したり、テスト終了後に簡単に削除したりできます。 

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>IoT Edge デバイスを登録する

新しく作成された IoT Hub に IoT Edge デバイスを登録します。
![デバイスを登録する][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>IoT Edge ランタイムをインストールして開始する

Azure IoT Edge ランタイムを IoT Edge デバイスにインストールして開始します。 
![デバイスを登録する][5]

IoT Edge ランタイムはすべての IoT Edge デバイスに展開されます。 これは 3 つのコンポーネントで構成されます。 **IoT Edge セキュリティ デーモン**は、Edge デバイスが起動するたびに開始され、IoT Edge エージェントを起動してデバイスをブートストラップします。 **IoT Edge エージェント**は、IoT Edge ハブなど、IoT Edge デバイス上のモジュールの展開と監視を容易にします。 **IoT Edge ハブ**は、IoT Edge デバイス上のモジュール間、およびデバイスと IoT ハブの間の通信を管理します。 

>[!NOTE]
>現時点では、このセクションのインストール手順は手動で行います。インストール スクリプトを現在作成中です。 

このセクションの手順では、Linux コンテナーを使用して IoT Edge ランタイムを構成します。 Windows コンテナーを使用する場合は、「[Install Azure IoT Edge runtime on Windows to use with Windows containers (Windows コンテナーを使用するために Azure IoT Edge ランタイムを Windows にインストールする)](how-to-install-iot-edge-windows-with-windows.md)」を参照してください。

### <a name="download-and-install-the-iot-edge-service"></a>IoT Edge サービスをダウンロードしてインストールする

1. IoT Edge デバイスで、PowerShell を管理者として実行します。

2. IoT Edge サービス パッケージをダウンロードします。

   ```powershell
   Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
   Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
   Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
   rmdir C:\ProgramData\iotedge\iotedged-windows
   $sysenv = "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment"
   $path = (Get-ItemProperty -Path $sysenv -Name Path).Path + ";C:\ProgramData\iotedge"
   Set-ItemProperty -Path $sysenv -Name Path -Value $path
   ```

3. vcruntime をインストールします。

  ```powershell
  Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
  .\vc_redist.exe /quiet /norestart
  ```

4. IoT Edge サービスを作成して開始します。

   ```powershell
   New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
   Start-Service iotedge
   ```

5. IoT Edge サービスが使用するポートのファイアウォール例外を追加します。

   ```powershell
   New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
   ```

6. **iotedge.reg** という名前の新しいファイルを作成し、テキスト エディターで開きます。 

7. 次の内容を追加し、ファイルを保存します。 

   ```input
   Windows Registry Editor Version 5.00
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
   "CustomSource"=dword:00000001
   "EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
   "TypesSupported"=dword:00000007
   ```

8. エクスプローラーでそのファイルに移動し、ダブルクリックして Windows レジストリに変更をインポートします。 

### <a name="configure-the-iot-edge-runtime"></a>IoT Edge ランタイムを構成する 

新しいデバイスを登録したときにコピーした IoT Edge デバイスの接続文字列を使用してランタイムを構成します。 次に、ランタイム ネットワークを構成します。 

1. `C:\ProgramData\iotedge\config.yaml` にある IoT Edge 構成ファイルを開きます。 このファイルは保護されているため、メモ帳などのテキスト エディターを管理者として実行し、そのエディターを使用してファイルを開きます。 

2. **provisioning** セクションを探し、**device_connection_string** の値を、IoT Edge デバイスの詳細からコピーした文字列に更新します。 

3. 管理者用 PowerShell ウィンドウで、IoT Edge デバイスのホスト名を取得し、出力をコピーします。 

   ```powershell
   hostname
   ```

4. 構成ファイルで、**Edge device hostname** セクションを探します。 **hostname** の値を、PowerShell からコピーしたホスト名に更新します。

3. 管理者用 PowerShell ウィンドウで、IoT Edge デバイスの IP アドレスを取得します。 

   ```powershell
   ipconfig
   ```

4. 出力の **vEthernet (DockerNAT)** セクションにある **IPv4 Address** の値をコピーします。 

5. **IOTEDGE_HOST** という環境変数を作成し、*\<ip_address\>* を IoT Edge デバイスの IP アドレスに置き換えます。 

  ```powershell
  [Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://<ip_address>:15580")
  ```

  再起動と再起動の間で環境変数を維持します。

  ```powershell
  SETX /M IOTEDGE_HOST "http://<ip_address>:15580"
  ```

6. `config.yaml` ファイルで **Connect settings** セクションを探します。 **management_uri** と **workload_uri** の値を、IP アドレスと前のセクションで開いたポートに更新します。 **\<GATEWAY_ADDRESS\>** を、コピーした DockerNAT の IP アドレスに置き換えます。

   ```yaml
   connect: 
     management_uri: "http://<GATEWAY_ADDRESS>:15580"
     workload_uri: "http://<GATEWAY_ADDRESS>:15581"
   ```

7. **Listen settings** セクションを探し、**management_uri** と **workload_uri** に同じ値を追加します。 

   ```yaml
   listen:
     management_uri: "http://<GATEWAY_ADDRESS>:15580"
     workload_uri: "http://<GATEWAY_ADDRESS>:15581"
   ```

8. **Moby Container Runtime settings** セクションを探し、**network** の値がコメント解除されて、**azure-iot-edge** に設定されていることを確認します。

   ```yaml
   moby_runtime:
     docker_uri: "npipe://./pipe/docker_engine"
     network: "azure-iot-edge"
   ```
   
9. 構成ファイルを保存します。 

10. PowerShell で、IoT Edge サービスを再起動します。

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

### <a name="view-the-iot-edge-runtime-status"></a>IoT Edge ランタイムの状態を確認する

ランタイムが正常にインストールされ、構成されていることを確認します。

1. IoT Edge サービスの状態を確認します。

   ```powershell
   Get-Service iotedge
   ```

2. サービスのトラブルシューティングが必要な場合は、サービス ログを取得します。 

   ```powershell
   # Displays logs from today, newest at the bottom.

   Get-WinEvent -ea SilentlyContinue `
    -FilterHashtable @{ProviderName= "iotedged";
      LogName = "application"; StartTime = [datetime]::Today} |
    select TimeCreated, Message |
    sort-object @{Expression="TimeCreated";Descending=$false} |
    format-table -autosize -wrap
   ```

3. IoT Edge デバイス上で実行されているすべてのモジュールを表示します。 初めてサービスが開始されたので、**edgeAgent** モジュールが実行されていることのみが確認できます。 edgeAgent モジュールは既定で実行され、デバイスにデプロイする追加モジュールのインストールと起動に役立ちます。 

   ```powershell
   iotedge list
   ```

   ![ご自身のデバイス上の 1 つのモジュールを表示する](./media/quickstart/iotedge-list-1.png)

## <a name="deploy-a-module"></a>モジュールを展開する

Azure IoT Edge デバイスをクラウドから管理し、IoT Hub に利用統計情報を送信するモジュールを展開します。
![デバイスを登録する][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>生成されたデータを表示する

このクイック スタートでは、新しい IoT Edge デバイスを作成し、そこに IoT Edge ランタイムをインストールしました。 その後、Azure Portal を使用して、IoT Edge モジュールをプッシュし、デバイス自体を変更せずにモジュールをデバイスで実行しました。 この場合は、プッシュしたモジュールによって、チュートリアルで使用できる環境データが作成されます。 

IoT Edge デバイスで、クラウドからデプロイされたモジュールが実行されていることを確認します。 

```powershell
iotedge list
```

   ![ご利用のデバイスで 3 つのモジュールを表示する](./media/quickstart/iotedge-list-2.png)

tempSensor モジュールからクラウドに送信されているメッセージを確認します。 

```powershell
iotedge logs tempSensor -f
```

  ![モジュールからのデータを表示する](./media/quickstart/iotedge-logs.png)

[Visual Studio Code 用の Azure IoT Toolkit の拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)を使用して、IoT ハブが受け取ったメッセージを表示することもできます。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

IoT Edge のチュートリアルに進む場合は、このクイック スタートで登録および設定したデバイスを使用できます。 それ以外の場合は、作成した Azure リソースを削除し、デバイスから IoT Edge ランタイムを削除することができます。 

### <a name="delete-azure-resources"></a>Azure リソースを削除する

新しいリソース グループで仮想マシンと IoT ハブを作成した場合、そのグループと関連するすべてのリソースを削除できます。 リソース グループ内に残したいリソースがある場合は、クリーン アップする個々のリソースのみを削除します。 

リソース グループを削除するには、次の手順を実行します。 

1. [Azure ポータル](https://portal.azure.com) にサインインし、 **[リソース グループ]** をクリックします。
2. **[名前でフィルター処理してください]** ボックスに、IoT ハブが含まれているリソース グループの名前を入力します。 
3. 結果一覧のでリソース グループの **[...]** をクリックし、**[リソース グループの削除]** をクリックします。
4. リソース グループの削除の確認を求めるメッセージが表示されます。 確認のためにリソース グループの名前を再度入力し、**[削除]** をクリックします。 しばらくすると、リソース グループとそこに含まれているすべてのリソースが削除されます。

### <a name="remove-the-iot-edge-runtime"></a>IoT Edge ランタイムを削除する

今後のテストに IoT Edge デバイスを使用する予定があるものの、IoT ハブを使用していない間は tempSensor モジュールからのデータの送信を停止したいという場合は、次のコマンドを使用して、IoT Edge サービスを停止します。 

   ```powershell
   Stop-Service iotedge -NoWait
   ```

テストを再開する準備ができたら、サービスを再起動することができます。

   ```powershell
   Start-Service iotedge
   ```

デバイスからインストールを削除するには、次のコマンドを使用します。  

IoT Edge ランタイムを削除します。

   ```powershell
   cmd /c sc delete iotedge
   rm -r c:\programdata\iotedge
   ```

IoT Edge ランタイムが削除されると、作成したコンテナーは停止されますが、デバイスには残っています。 すべてのコンテナーを表示します。

   ```powershell
   docker ps -a
   ```

IoT Edge ランタイムによってデバイス上に作成されたコンテナーを削除します。 tempSensor コンテナーを別の名前で呼ぶ場合は、名前を変更します。 

   ```powershell
   docker rm -f tempSensor
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、新しい IoT Edge デバイスを作成し、Azure IoT Edge クラウド インターフェイスを使用してコードをデバイスにデプロイしました。 その環境に関する生データを生成するテスト デバイスができあがりました。 

これで、引き続き他のチュートリアルを実行し、Azure IoT Edge が、エッジでこのデータをビジネスに関する分析情報に変えるうえで、どのように役立つかを確認する準備が整いました。

> [!div class="nextstepaction"]
> [Azure Function を使用してセンサー データをフィルター処理する](tutorial-deploy-function.md)

<!-- Images -->
[2]: ./media/quickstart/install-edge-full.png
[3]: ./media/quickstart/create-iot-hub.png
[4]: ./media/quickstart/register-device.png
[5]: ./media/quickstart/start-runtime.png
[6]: ./media/quickstart/deploy-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python]: https://www.python.org/downloads/
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-install-iotcore]: how-to-install-iot-core.md
[lnk-account]: https://azure.microsoft.com/free
