---
title: DPS を使用して Azure IoT Edge の自動プロビジョニングを行う - Windows | Microsoft Docs
description: Windows マシン上でシミュレートされたデバイスを使用して、デバイス プロビジョニング サービスで Azure IoT Edge の自動デバイス プロビジョニングをテストします
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e149886e1ade80d7751f58eb1f77031c4e432b75
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39307945"
---
# <a name="create-and-provision-a-simulated-tpm-edge-device-on-windows"></a>Windows 上のシミュレートされた TPM Edge デバイスの作成とプロビジョニング

Azure IoT Edge デバイスは、[Device Provisioning Service](../iot-dps/index.yml) を使用して、Edge に対応していないデバイスと同じように自動プロビジョニングできます。 自動プロビジョニングの処理に慣れていない場合は、「[自動プロビジョニングの概念](../iot-dps/concepts-auto-provisioning.md)」を読んでから先に進んでください。 

この記事では、シミュレートされた Edge デバイスの自動プロビジョニングを次の手順でテストする方法を示します。 

* IoT Hub Device Provisioning Service (DPS) のインスタンスを作成する。
* ハードウェアのセキュリティ用のシミュレートされたトラステッド プラットフォーム モジュール (TPM) を使用して、Windows マシン上にシミュレートされたデバイスを作成する。
* デバイスの個別登録を作成する。
* IoT Edge ランタイムをインストールし、デバイスを IoT Hub に接続する。

## <a name="prerequisites"></a>前提条件

* Windows 開発マシン。 この記事では、Windows 10 を使用します。 
* アクティブな IoT Hub。 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Azure IoT Hub Device Provisioning Service を設定する

Azure 内に IoT Hub Device Provisioning Service の新しいインスタンスを作成し、それを自分の IoT Hub にリンクします。 手順については、[IoT Hub DPS の設定](../iot-dps/quick-setup-auto-provision.md)に関する記事を参照してください。

Device Provisioning Service を実行した後、概要ページから **[ID スコープ]** の値をコピーします。 この値は、IoT Edge ランタイムを構成するときに使用します。 

## <a name="simulate-a-tpm-device"></a>TPM デバイスのシミュレート

Windows 開発マシン上でシミュレートされた TPM デバイスを作成します。 デバイスの**登録 ID** と**保証キー**を取得し、それらを使用して DPS に個別の登録エントリを作成します。 

DPS 内に登録を作成するときに、**デバイス ツインの初期状態**を宣言する機会があります。 デバイス ツインでは、ソリューションで必要な任意のメトリック (リージョン、環境、場所、デバイスの種類など) によってデバイスをグループ化するためのタグを設定できます。 これらのタグは、[自動展開](how-to-deploy-monitor.md)を作成するために使用されます。 

シミュレートされたデバイスの作成に使用する SDK の言語を選択し、個々の登録を作成するまでの手順に従います。 

個々の登録を作成するときに、**[有効にする]** を選択して、この仮想マシンが **IoT Edge デバイス**であることを宣言します。

シミュレートされたデバイスと個々の登録ガイド: 
* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

個別登録を作成したら、**登録 ID** の値を保存します。 この値は、IoT Edge ランタイムを構成するときに使用します。 

## <a name="install-the-iot-edge-runtime"></a>IoT Edge ランタイムをインストールする

IoT Edge ランタイムはすべての IoT Edge デバイスに展開されます。 そのコンポーネントはコンテナー内で実行されるため、デバイスに追加のコンテナーを展開して、Edge でコードを実行できるようにすることができます。 Windows を実行しているデバイスでは、Windows コンテナーまたは Linux コンテナーを使用することができます。 使用するコンテナーの種類を選択し、手順に従います。 IoT Edge ランタイムの構成が、手動プロビジョニングではなく、自動プロビジョニングになっていることを確認してください。 

手順に従って、前のセクションのシミュレートされた TPM を実行しているデバイスに IoT Edge ランタイムをインストールします。 

これらの記事の手順を実行する前に、DPS の **ID スコープ**とデバイスの**登録 ID** を確認します。 

* [Windows コンテナー](how-to-install-iot-edge-windows-with-windows.md)
* [Linux コンテナー](how-to-install-iot-edge-windows-with-linux.md)

## <a name="create-a-tpm-environment-variable"></a>TPM の環境変数を作成する

シミュレートされたデバイスを実行しているマシンに、**iotege** サービス レジストリを変更して環境変数を設定します。

1. **スタート** メニューから **regedit** を開きます。 
2. **Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\iotedge** に移動します。 
3. **[編集]** > **[新規]** > **[複数行文字列値]** の順に選択します。 
4. 「**Environment**」という名前を入力します。 
5. 新しい変数をダブルクリックし、[値のデータ] を **IOTEDGE_USE_TPM_DEVICE=ON** に設定します。 
6. **[OK]** をクリックして変更を保存します。 

## <a name="restart-the-iot-edge-runtime"></a>IoT Edge ランタイムを再起動する

デバイスで行ったすべての構成の変更が反映されるように、IoT Edge ランタイムを再起動します。 

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

## <a name="verify-successful-installation"></a>インストールの成功を確認する

ランタイムが正常に起動した場合は、IoT Hub に移動して、新しいデバイスが自動的にプロビジョニングされ、IoT Edge モジュールを実行する準備ができていることを確認できます。 

IoT Edge サービスの状態を確認します。

```powershell
Get-Service iotedge
```

過去 5 分のサービス ログを調べます。

```powershell
# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

実行中のモジュールを一覧表示します。

```powershell
iotedge list
```

## <a name="next-steps"></a>次の手順

Device Provisioning Service の登録プロセスでは、新しいデバイスをプロビジョニングするときに、デバイス ID とデバイス ツイン タグを同時に設定できます。 これらの値を使用して、個々のデバイスまたはデバイス グループをデバイスの自動管理で使用できます。 使用方法については、「[Azure Portal を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-monitor.md)」または「[Azure CLI を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-monitor-cli.md)」を参照してください。