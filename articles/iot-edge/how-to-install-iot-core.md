---
title: Azure IoT Edge を IoT Core にインストールする | Microsoft Docs
description: Azure IoT Edge ランタイムを Windows IoT Core デバイスにインストールします
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 03/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f57db00894dab80f96f45111331d47a173520ced
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576000"
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>IoT Edge ランタイムを Windows IoT Core にインストールする - プレビュー

Azure IoT Edge と [Windows IoT Core](https://docs.microsoft.com/windows/iot-core/) が連携することで、小さなデバイスでもエッジ コンピューティングが有効になります。 Azure IoT Edge ランタイムは、IoT 業界で広く普及している小型のシングル ボード コンピューター (SBC) デバイス上でも実行できます。 

この記事では、Windows IoT Core を実行している開発ボードにランタイムをプロビジョニングする手順について説明します。 

**現在、Windows IoT Core は、Intel x64 ベース プロセッサでのみ Azure IoT Edge をサポートしています。**

## <a name="install-the-container-runtime"></a>コンテナー ランタイムをインストールする

1. **Build 17134 (RS4)** IoT Core イメージを使用してボードを構成します。 
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

## <a name="finish-installing"></a>インストールを完了する

IoT Edge Security Daemon をインストールし、[この記事][lnk-install-windows-on-windows]の手順に従って構成します。

## <a name="next-steps"></a>次の手順

IoT Edge ランタイムを実行するデバイスが準備できたので、[IoT Edge モジュールを大規模にデプロイし、監視する][lnk-deploy]方法について学習してください。

<!--Links-->
[lnk-install-windows-on-windows]: how-to-install-iot-edge-windows-with-windows.md
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
