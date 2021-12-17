---
title: Azure IoT Edge for Linux on Windows とは | Microsoft Docs
description: Windows 10 デバイスで Linux IoT Edge モジュールを実行する方法の概要
author: kgremban
ms.reviewer: twarwick
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/18/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 881d0fe19339976342db866072dcf86009b1bbd5
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578863"
---
# <a name="what-is-azure-iot-edge-for-linux-on-windows"></a>Azure IoT Edge for Linux on Windows とは

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge for Linux on Windows を使用すると、Windows IoT デプロイで Windows アプリケーションと共に、コンテナー化された Linux ワークロードを実行できます。 エッジ デバイスで Windows IoT を活用している企業は、Linux で構築されるクラウドネイティブの分析ソリューションを利用できるようになりました。

IoT Edge for Linux on Windows は、Windows デバイスで Linux 仮想マシンを実行することで機能します。 Linux 仮想マシンは、IoT Edge ランタイムと共にプレインストールされています。 デバイスにデプロイされているすべての IoT Edge モジュールは、仮想マシン内で実行されます。 一方、Windows ホスト デバイスで実行されている Windows アプリケーションは、Linux 仮想マシンで実行されているモジュールと通信できます。

今すぐ[お試しください](how-to-provision-single-device-linux-on-windows-symmetric.md)。

## <a name="components"></a>コンポーネント

IoT Edge for Linux on Windows では次のコンポーネントを使用することで、Linux と Windows のワークロードを相互に同時実行でき、また、これらがシームレスに通信できるようになります。

* **Azure IoT Edge を実行している Linux 仮想マシン**: Microsoft のファースト パーティ [CBL-Mariner](https://github.com/microsoft/CBL-Mariner) オペレーティング システムに基づく Linux 仮想マシンは、IoT Edge ランタイムを使用して構築され、IoT Edge ワークロード用の第 1 層対応環境として検証されています。

* **Windows Admin Center**:Windows Admin Center の IoT Edge 拡張機能を使用すると、Linux 仮想マシンでの IoT Edge のインストール、構成、診断を簡単に行うことができます。 Windows Admin Center では、ローカル デバイスに IoT Edge for Linux on Windows をデプロイしたり、ターゲット デバイスに接続してリモートで管理したりすることができます。

* **Microsoft Update**: Microsoft Update との統合により、Windows ランタイム コンポーネント、CBL-Mariner Linux VM、および IoT Edge を最新の状態に保つことができます。

![Windows と Linux VM は並行して実行され、Windows Admin Center によって両方のコンポーネントが制御されます。](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)

Windows プロセスと Linux 仮想マシンの間の双方向通信では、Windows プロセスにより、Linux コンテナーで実行されるワークロードのユーザー インターフェイスまたはハードウェア プロキシを提供できます。


## <a name="prerequisites"></a>前提条件

以下の最小要件を備えた Windows デバイス:

* システム要件
   * Windows 10¹/11 (Pro、Enterprise、IoT Enterprise)
   * Windows Server 2019¹/2022  
   <sub>¹ 現在のすべての累積更新プログラムがインストールされた Windows 10 および Windows Server 2019 最小ビルド 17763。</sub>

* ハードウェア要件
  * 最小空きメモリ容量: 1 GB
  * 最小空きディスク領域:10 GB


## <a name="samples"></a>サンプル

IoT Edge for Linux on Windows では、Linux コンポーネントと Windows コンポーネントの間の相互運用性が重視されています。

Windows アプリケーションと IoT Edge モジュールの間の通信方法を示すサンプルについては、[EFLOW と Windows 10 IoT のサンプル](https://aka.ms/AzEFLOW-Samples)を参照してください。

## <a name="support"></a>サポート

IoT Edge サポートとフィードバック チャネルを使用して、IoT Edge for Linux on Windows についてサポートを受けることができます。

**バグの報告** – Azure IoT Edge for Linux on Windows に関するバグは、[iotedge-eflow の問題ページ](https://aka.ms/AzEFLOW-Issues)で報告できます。 IoT Edge 関連のバグは、IoT Edge オープンソース プロジェクトの[問題ページ](https://github.com/azure/iotedge/issues)で報告できます。

**Microsoft カスタマー サポート チーム** – [サポート プラン](https://azure.microsoft.com/support/plans/)に加入しているユーザーは、[Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac) から直接サポート チケットを作成することで、Microsoft カスタマー サポート チームとやり取りをすることができます。

**機能の要望** – Azure IoT Edge 製品はその製品の [ユーザーの声のページ](https://feedback.azure.com/d365community/forum/0e2fff5d-f524-ec11-b6e6-000d3a4f0da0)を介して機能の要望を追跡します。

## <a name="next-steps"></a>次のステップ

詳細と操作中のサンプルについては、[IoT Edge for Linux on Windows 10 IoT Enterprise](https://aka.ms/azeflow-show) に関する動画をご覧ください。

「[Azure IoT Edge for Linux on Windows デバイスを手動でプロビジョニングする](how-to-provision-single-device-linux-on-windows-symmetric.md)」の手順に従って、IoT Edge for Linux on Windows でデバイスを設定します。
