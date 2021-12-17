---
title: Azure IoT Edge for Linux on Windows 用の GPU アクセラレーション | Microsoft Docs
description: ホスト デバイスの GPU を使用するように Azure IoT Edge for Linux on Windows 仮想マシンを構成する方法について説明します。
author: v-tcassi
manager: kgremban
ms.author: v-tcassi
ms.date: 06/22/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: c85659a6c9e89145494d4683da37bd2f13cbd379
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234867"
---
# <a name="gpu-acceleration-for-azure-iot-edge-for-linux-on-windows-preview"></a>Azure IoT Edge for Linux on Windows 用の GPU アクセラレーション (プレビュー)

GPU では並列処理機能が提供され、多くの場合、CPU よりも高速なビジョンベースの推論を実行できるため、人工知能の計算によく使用されます。 人工知能と機械学習アプリケーションのサポートを強化するために、Azure IoT Edge for Linux on Windows では、仮想マシンの Linux モジュールに GPU を公開できます。

> [!NOTE]
> 以下で詳しく説明する GPU アクセラレーション機能は、プレビュー段階であり、変更される可能性があります。

Azure IoT Edge for Linux on Windows では、次のようないくつかの GPU パススルー テクノロジがサポートされています。

* **デバイスの直接割り当て (DDA)** - GPU コアは、Linux 仮想マシンまたはホストのいずれかに割り当てられます。

* **GPU 準仮想化 (GPU-PV)** - GPU は、Linux 仮想マシンとホストの間で共有されます。

Azure IoT Edge for Linux on Windows のデプロイでは、デバイスの GPU ハードウェアでサポートされている機能に合わせて適切なパススルー方法が自動的に選択されます。

> [!IMPORTANT]
> これらの機能には、NVIDIA Corporation またはそのライセンサーによって開発および所有されているコンポーネントが含まれる場合があります。 コンポーネントの使用は、[NVIDIA の Web サイト](https://www.nvidia.com/content/DriverDownload-March2009/licence.php?lang=us)にある NVIDIA のエンドユーザー使用許諾契約書によって管理されています。
>
> GPU アクセラレーションの機能を使用することにより、NVIDIA のエンドユーザー使用許諾契約書の条件を受け入れて同意したものとなります。

## <a name="prerequisites"></a>前提条件

Azure IoT Edge for Linux on Windows の GPU アクセラレーション機能では、現在、GPU ハードウェアの厳選されたセットがサポートされています。 また、この機能を使用するには、構成に応じて、最新の Windows Insider Dev チャネルのビルドが必要になる場合があります。

サポートされている GPU と必要な Windows のバージョンを次に示します。

* NVIDIA T4 (DDA をサポート)

  * Windows Server、ビルド 17763 以降
  * Windows Enterprise または Professional、ビルド 21318 以降 (Windows Insider ビルド)

* NVIDIA GeForce/Quadro (GPU-PV をサポート)

  * Windows Enterprise または Professional、ビルド 20145 以降 (Windows Insider ビルド)

### <a name="windows-insider-builds"></a>Windows Insider のビルド

Windows Enterprise または Professional ユーザーの場合は、[Windows Insider Program に登録](https://insider.windows.com/getting-started#register)する必要があります。

登録したら、 **[2. フライト]** タブの指示に従って、適切な Windows Insider ビルドにアクセスしてください。 使用するチャネルを選択する際は、[[Dev チャネル]](/windows-insider/flight-hub/#active-development-builds-of-windows-10) を選択します。 インストール後、コマンド プロンプトから `winver` を実行することで、ビルドのバージョン番号を確認できます。

### <a name="t4-gpus"></a>T4 GPU

**T4 GPU** の場合は、GPU のベンダーから入手したデバイスのリスク軽減ドライバーをインストールすることをお勧めします。 省略可能ですが、リスク軽減ドライバーをインストールすると、デプロイのセキュリティが向上する場合があります。 詳細については、[デバイスの直接割り当てを使用したグラフィックス デバイスのデプロイ](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda#optional---install-the-partitioning-driver)に関する記事を参照してください。

> [!WARNING]
> ハードウェア デバイスのパススルーを有効にすると、セキュリティ上のリスクが増える可能性があります。 該当する場合は、GPU ベンダーが提供する、デバイスのリスク軽減ドライバーをお勧めします。 詳細については、「[Discrete Device Assignment を使用したグラフィックス デバイスのデプロイ](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)」を参照してください。

### <a name="geforcequadro-gpus"></a>GeForce または Quadro GPU

**GeForce または Quadro GPU** の場合は、既存の CUDA ML ワークフローで使用するために、[Linux 用 Windows サブシステム (WSL) 用の NVIDIA CUDA 対応ドライバー](https://developer.nvidia.com/cuda/wsl)をダウンロードしてインストールします。 WSL 用の CUDA ドライバーは、もともと WSL 用に開発されたものですが、Azure IoT Edge for Linux on Windows にも使用されます。

## <a name="using-gpu-acceleration-for-your-linux-on-windows-deployment"></a>Linux on Windows デプロイでの GPU アクセラレーションの使用

これで、Azure IoT Edge for Linux on Windows を使用して、GPU アクセラレーションを使用する Linux モジュールを Windows 環境にデプロイして実行できるようになりました。 デプロイ プロセスについて詳しくは、[対称キーを使用した単一の IoT Edge for Linux on Windows デバイスのプロビジョニングに関するガイド](how-to-provision-single-device-linux-on-windows-symmetric.md)または [X.509 証明書の使用](how-to-provision-single-device-linux-on-windows-x509.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

* [Azure IoT Edge for Linux on Windows のデプロイを作成します](how-to-install-iot-edge-on-windows.md)

* 独自のビジョンベースの機械学習アプリケーションを構築する方法を示すソリューション テンプレートである、[Vision on Edge を使用する GPU 対応サンプル](https://github.com/Azure-Samples/azure-intelligent-edge-patterns/blob/master/factory-ai-vision/Tutorial/Eflow.md)をお試しください。

* GPU パススルー テクノロジの詳細については、[DDA のドキュメント](/windows-server/virtualization/hyper-v/plan/plan-for-gpu-acceleration-in-windows-server#discrete-device-assignment-dda)と [GPU-PV のブログ記事](https://devblogs.microsoft.com/directx/directx-heart-linux/#gpu-virtualization)をご覧ください。
