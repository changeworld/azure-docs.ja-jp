---
title: "Azure IoT Edge を IoT Core にインストールする | Microsoft Docs"
description: "Azure IoT Edge ランタイムを Windows IoT Core デバイスにインストールします"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 03/05/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 75d2b370ed6118a30153a001a4b654d7212b56cd
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>IoT Edge ランタイムを Windows IoT Core にインストールする - プレビュー

Azure IoT Edge と [Windows IoT Core](https://docs.microsoft.com/windows/iot-core/) が連携することで、小さなデバイスでもエッジ コンピューティングが有効になります。 Azure IoT Edge ランタイムは、IoT 業界で広く普及している小型のシングル ボード コンピューター (SBC) デバイス上でも実行できます。 

この記事では、Windows IoT Core を実行している [MinnowBoard Turbot][lnk-minnow] 開発ボードにランタイムをプロビジョニングする手順について説明します。 Windows IoT Core は、Intel x64 ベース プロセッサでのみ Azure IoT Edge をサポートしています。 

## <a name="install-the-runtime"></a>ランタイムをインストールする

1. [Windows 10 IoT Core Dashboard][lnk-core] をホスト システムにインストールします。
1. 「[Set up your device (デバイスの設定)][lnk-board]」の手順に従って、ボードを MinnowBoard Turbot/MAX Build 16299 のイメージで構成します。 
1. デバイスをオンにし、[PowerShell を使用してリモートでログイン][lnk-powershell]します。
1. PowerShell コンソールで、コンテナー ランタイムをインストールします。 

   ```powershell
   Invoke-WebRequest https://master.dockerproject.org/windows/x86_64/docker-0.0.0-dev.zip -o temp.zip
   Expand-Archive .\temp.zip $env:ProgramFiles -f
   Remove-Item .\temp.zip
   $env:Path += ";$env:programfiles\docker"
   SETX /M PATH "$env:Path"
   dockerd --register-service
   start-service docker
   ```

   >[!NOTE]
   >このコンテナー ランタイムは、Moby プロジェクト ビルド サーバーからのものであり、評価だけを目的としています。 Docker によるテスト、動作保証、サポートは行われていません。

1. IoT Edge ランタイムをインストールし、お使いの構成を確認します。

   ```powershell
   Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
   ```

   このスクリプトにより、次のものが提供されます。 
   * Python 3.6
   * IoT Edge コントロール スクリプト (iotedgectl.exe)

リモートの PowerShell ウィンドウに、iotedgectl.exe ツールからの情報出力が緑色で表示される場合があります。 これは必ずしもエラーを示すものではありません。 

## <a name="next-steps"></a>次の手順

IoT Edge ランタイムを実行するデバイスが準備できたので、[IoT Edge モジュールを大規模にデプロイし、監視する][lnk-deploy]方法について学習してください。

<!--Links-->
[lnk-minnow]: https://minnowboard.org/ 
[lnk-core]: https://docs.microsoft.com/windows/iot-core/connect-your-device/iotdashboard
[lnk-board]: https://developer.microsoft.com/windows/iot/Docs/GetStarted/mbm/sdcard/stable/getstartedstep2
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers