---
title: IoT Edge デバイスを作成する - Azure IoT Edge | Microsoft Docs
description: IoT Edge デバイスを作成するためのプラットフォームとプロビジョニング オプションについて学習します
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/11/2021
ms.author: kgremban
ms.openlocfilehash: 6fdfbd937e767cde118ed80476e73d6207c657e6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495255"
---
# <a name="create-an-iot-edge-device"></a>IoT Edge デバイスを作成する

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

この記事では、デバイスに IoT Edge をインストールしてプロビジョニングするために使用できるオプションの概要を示します。

この記事では、IoT Edge ソリューションのすべてのオプションについて説明します。これは、次の場合に役立ちます。

* [プラットフォームの選択](#choose-a-platform)
* [デバイスをプロビジョニングする方法を選択する](#choose-how-to-provision-your-devices)
* [認証方法を選択する](#choose-an-authentication-method)

この記事の最後には、IoT Edge ソリューションに使用するプラットフォーム、プロビジョニング、および認証のオプションが明確になります。

## <a name="get-started"></a>はじめに

IoT Edge デバイスの作成に使用するプラットフォーム、プロビジョニング、および認証のオプションの種類がわかっている場合は、次の表のリンクを使って作業を開始してください。

適切なオプションを選択する方法の詳細については、この記事を読み進めて確認してください。

<!--1.1-->
:::moniker range="iotedge-2018-06"

|    | Linux ホスト上の Linux コンテナー | Windows ホスト上の Linux コンテナー | Windows ホスト上の Windows コンテナー |
|--| ----- | ---------------- | ------- |
| **手動プロビジョニング (単一デバイス)** | [X.509 証明書](how-to-provision-single-device-linux-x509.md)<br><br>[対称キー](how-to-provision-single-device-linux-symmetric.md) | [X.509 証明書](how-to-provision-single-device-linux-on-windows-x509.md)<br><br>[対称キー](how-to-provision-single-device-linux-on-windows-symmetric.md) | [X.509 証明書](how-to-provision-single-device-windows-x509.md)<br><br>[対称キー](how-to-provision-single-device-windows-symmetric.md) |
| **自動プロビジョニング (大規模なデバイス)** | [X.509 証明書](how-to-provision-devices-at-scale-linux-x509.md)<br><br>[TPM](how-to-provision-devices-at-scale-linux-tpm.md)<br><br>[対称キー](how-to-provision-devices-at-scale-linux-symmetric.md) | [X.509 証明書](how-to-provision-devices-at-scale-linux-on-windows-x509.md)<br><br>[TPM](how-to-provision-devices-at-scale-linux-on-windows-tpm.md)<br><br>[対称キー](how-to-provision-devices-at-scale-linux-on-windows-symmetric.md) | [X.509 証明書](how-to-provision-devices-at-scale-windows-x509.md)<br><br>[TPM](how-to-provision-devices-at-scale-windows-tpm.md)<br><br>[対称キー](how-to-provision-devices-at-scale-windows-symmetric.md) |

:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>次の表には、IoT Edge バージョン 1.2 でサポートされているシナリオが示されています。 Windows デバイスに関する内容を表示する場合は、この記事の [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true) バージョンに切り替えてください。

|    | Linux ホスト上の Linux コンテナー |
|--| ----- |
| **手動プロビジョニング (単一デバイス)** | [X.509 証明書](how-to-provision-single-device-linux-x509.md)<br><br>[対称キー](how-to-provision-single-device-linux-symmetric.md) |
| **自動プロビジョニング (大規模なデバイス)** | [X.509 証明書](how-to-provision-devices-at-scale-linux-x509.md)<br><br>[TPM](how-to-provision-devices-at-scale-linux-tpm.md)<br><br>[対称キー](how-to-provision-devices-at-scale-linux-symmetric.md) |

:::moniker-end

## <a name="terms-and-concepts"></a>用語と概念

IoT Edge の用語にまだ慣れていない場合は、次のいくつかの主要な概念を確認してください。

**IoT Edge ランタイム**: [IoT Edge ランタイム](iot-edge-runtime.md)は、デバイスを IoT Edge デバイスに変えるプログラムのコレクションです。 一括して、IoT Edge ランタイム コンポーネントを使用すると、IoT Edge デバイスで IoT Edge モジュールを実行できます。

**プロビジョニング**: 各 IoT Edge デバイスをプロビジョニングする必要があります。 プロビジョニングは 2 段階のプロセスです。 最初の手順では、IoT ハブにデバイスを登録し、IoT ハブへの接続を確立するためにデバイスで使用されるクラウド ID を作成します。 2 番目の手順では、クラウド ID を使用してデバイスを構成します。 プロビジョニングは、デバイスごとに手動で行うことができます。あるいは、[IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md) を使用して大規模に行うことができます。

**認証**: IoT Edge デバイスでは、IoT Hub に接続するときに ID を検証する必要があります。 対称キー パスワード、証明書の拇印、トラステッド プラットフォーム モジュール (TPM) など、使用する認証方法を選択できます。

## <a name="choose-a-platform"></a>プラットフォームの選択

プラットフォーム オプションは、コンテナー オペレーティング システムとホスト オペレーティング システムによって参照されます。 コンテナー オペレーティング システムは、IoT Edge ランタイムおよびモジュール コンテナー内で使用されるオペレーティング システムです。 ホスト オペレーティング システムは、IoT Edge ランタイム コンテナーおよびモジュールが実行されるデバイスのオペレーティング システムです。

IoT Edge デバイスには 3 つのプラットフォーム オプションがあります。

* **Linux ホスト上の Linux コンテナー**: Linux ホスト上で直接 Linux ベースの IoT Edge コンテナーを実行します。 IoT Edge ドキュメント全体で、わかりやすくするために、このオプションは **Linux** および **Linux コンテナー** と呼ばれます。

* **Windows ホスト上の Linux コンテナー**: Windows ホスト上の Linux 仮想マシンで Linux ベースの IoT Edge コンテナーを実行します。 IoT Edge ドキュメント全体で、このオプションは **Linux on Windows**、**IoT Edge for Linux on Windows**、および **EFLOW** とも呼ばれます。

* **Windows ホスト上の Windows コンテナー**: Windows ホスト上で直接 Windows ベースの IoT Edge コンテナーを実行します。 IoT Edge ドキュメント全体で、わかりやすくするために、このオプションは **Windows** および **Windows コンテナー** とも呼ばれます。

運用シナリオ向けに現在サポートされているオペレーティング システムに関する最新の情報については、「[Azure IoT Edge のサポートされるシステム](support.md#operating-systems)」を参照してください。

### <a name="linux-containers-on-linux"></a>Linux 上の Linux コンテナー

Linux デバイスの場合、IoT Edge ランタイムはホスト デバイスに直接インストールされます。

IoT Edge では、X64、ARM32、および ARM64 Linux デバイスがサポートされています。 Microsoft からは、Ubuntu Server 18.04 および Raspberry Pi OS Stretch の各オペレーティング システム用のインストール パッケージが提供されています。

ARM64 デバイスのサポートは、[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)にあります。

### <a name="linux-containers-on-windows"></a>Windows 上の Linux コンテナー

<!--1.1-->
:::moniker range="iotedge-2018-06"
IoT Edge for Linux on Windows により、Windows デバイス上の Linux 仮想マシンがホストされます。 仮想マシンには、IoT Edge ランタイムがあらかじめ組み込まれており、更新は Microsoft Update を介して管理されます。

IoT Edge for Linux on Windows は、Windows デバイスで IoT Edge を実行する場合に推奨される方法です。 詳細については、「[Azure IoT Edge for Linux on Windows とは](iot-edge-for-linux-on-windows.md)」を参照してください。

現在、IoT Edge for Linux on Windows ではバージョン 1.2 の Azure IoT Edge はサポートされていません。
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
現在、IoT Edge バージョン 1.2 では IoT Edge for Linux on Windows はサポートされていません。 IoT Edge for Linux on Windows の詳細については、この記事の [IoT Edge 1.1 ](?view=iotedge-2018-06&preserve-view=true)バージョンを参照してください。
:::moniker-end

### <a name="windows-containers-on-windows"></a>Windows 上の Windows コンテナー

<!--1.1-->
:::moniker range="iotedge-2018-06"
Windows デバイスの場合、IoT Edge ランタイムはホスト デバイス上に直接インストールされます。 このプラットフォームを使用すると、IoT Edge モジュールを Windows コンテナーとしてビルド、デプロイ、および実行できます。

   > [!NOTE]
   > Windows コンテナーは、バージョン 1.1 以降の Azure IoT Edge ではサポートされないため、Windows デバイスで IoT Edge を実行する方法としては推奨されません。
   >
   > 今後のバージョンでサポートされる、IoT Edge for Linux on Windows の使用を検討してください。
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
IoT Edge バージョン 1.2 では Windows コンテナーはサポートされていません。 バージョン 1.1 以降では、Windows コンテナーはサポートされません。 Windows コンテナーを使用する IoT Edge の詳細については、この記事の [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true) バージョンを参照してください。
:::moniker-end

## <a name="choose-how-to-provision-your-devices"></a>デバイスをプロビジョニングする方法を選択する

IoT Edge ソリューションのニーズに応じて、単一デバイス、または複数のデバイスを大規模にプロビジョニングすることができます。

IoT Edge デバイスと IoT ハブ間の通信を認証するために使用できるオプションは、選択するプロビジョニング方法によって異なります。 これらのオプションの詳細については、「[認証方法を選択する」セクション](#choose-an-authentication-method)を参照してください。

### <a name="single-device"></a>単一デバイス

単一デバイスのプロビジョニングとは、[IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md) (DPS) を利用せずに IoT Edge デバイスをプロビジョニングすることを指します。 単一デバイスのプロビジョニングは、**手動プロビジョニング** とも呼ばれます。

単一デバイスのプロビジョニングを使用する場合は、デバイス上で、接続文字列などのプロビジョニング情報を手動で入力する必要があります。 手動プロビジョニングの場合、少数のデバイスに対して迅速かつ簡単に設定できますが、デバイスの数に応じてワークロードが増加します。 ソリューションのスケーラビリティを検討する際には、この点に注意してください。

**対称キー** および **X.509 自己署名** 認証方法は、手動プロビジョニングで使用できます。 これらのオプションの詳細については、「[認証方法を選択する」セクション](#choose-an-authentication-method)を参照してください。

### <a name="devices-at-scale"></a>大規模なデバイス

大規模なデバイスのプロビジョニングとは、[IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md) を利用して 1 台または複数台の IoT Edge デバイスをプロビジョニングすることを指します。 大規模なプロビジョニングは、**自動プロビジョニング** とも呼ばれます。

IoT Edge ソリューションで複数のデバイスが必要な場合、DPS を使用した自動プロビジョニングにより、使用する各デバイスの構成ファイルにプロビジョニング情報を手動で入力する手間が省けます。 この自動モデルは、数百万台の IoT Edge デバイスにスケーリングできます。 自動プロビジョニング フローは、[IoT Hub DPS の概要に関するページの「バックグラウンド処理」セクション](../iot-dps/about-iot-dps.md#behind-the-scenes)で確認できます。

任意の認証方法を使用して、IoT Edge ソリューションをセキュリティで保護できます。 **対称キー**、**X.509 証明書**、および **トラステッド プラットフォーム モジュール (TPM) 構成証明** 認証方法は、大規模なデバイスのプロビジョニングに使用できます。 これらのオプションの詳細については、「[認証方法を選択する」セクション](#choose-an-authentication-method)を参照してください。

DPS の機能の詳細については、[概要ページの機能に関するセクション](../iot-dps/about-iot-dps.md#features-of-the-device-provisioning-service)を参照してください。

## <a name="choose-an-authentication-method"></a>認証方法を選択する

### <a name="symmetric-keys-attestation"></a>対称キーの構成証明

対称キーの構成証明は、デバイスを認証するための簡単な方法です。 この構成証明の方法では、初めてデバイスのプロビジョニングを行う開発者または厳密なセキュリティ要件がない開発者に対して、"Hello world" エクスペリエンスを提示します。

IoT Hub で新しいデバイス ID を作成すると、サービスによって 2 つのキーが作成されます。 いずれかのキーをデバイスに配置すると、認証時にそのキーが IoT Hub に提供されます。

この認証方法は比較的すばやく開始できますが、それほど安全ではありません。 TPM または X.509 証明書を使用するデバイス プロビジョニングの方が安全であり、より厳しいセキュリティ要件を持つソリューションにはそれを使用する必要があります。

### <a name="x509-certificate-attestation"></a>X.509 証明書の構成証明

X.509 証明書を構成証明メカニズムとして使用することは、実稼働環境を拡張し、デバイスのプロビジョニングを簡素化するための優れた方法です。 通常、X.509 証明書は信頼する証明書チェーンに配置されます。 自己署名証明書または信頼されたルート証明書から開始して、チェーン内の各証明書によって、それよりも 1 つ下位にある証明書が署名されます。 このパターンにより、ルート証明書から各中間証明書を経由して、デバイスにインストールされた最終的な "リーフ" 証明書に至る、信頼する委任チェーンが作成されます。

2 つの X.509 ID 証明書を作成し、デバイスに配置します。 IoT Hub で新しいデバイス ID を作成するときは、両方の証明書の拇印を指定します。 デバイスでは IoT Hub に対して認証を行うときに、1 つの証明書が提示され、その証明書がその拇印と一致することが IoT によって検証されます。

この認証方法は対称キーよりも安全であり、運用シナリオに推奨されます。

### <a name="trusted-platform-module-tpm-attestation"></a>トラステッド プラットフォーム モジュール (TPM) 構成証明

TPM 構成証明の使用は、ソフトウェアとハードウェアの両方で認証機能が提供されるため、デバイス プロビジョニングの最も安全な方法です。 各 TPM チップでは一意の認証キーを使用して、その信頼性を検証します。

TPM 構成証明は、DPS を使用した大規模なプロビジョニングでのみ使用できます。また、グループ登録ではなく個々の登録のみがサポートされます。 TPM のデバイス固有の性質上、グループ登録は使用できません。

Device Provisioning Service で TPM 構成証明を使用する場合は、TPM 2.0 が必要です。

この認証方法は対称キーよりも安全であり、運用シナリオに推奨されます。

## <a name="next-steps"></a>次の手順

目次を使用して、IoT Edge ソリューションのプラットフォーム、プロビジョニング、および認証の要件に合った IoT Edge デバイスを作成するための適切なエンドツーエンド ガイドに移動できます。

以下のリンクを使用して、関連記事に移動することもできます。

### <a name="linux-containers-on-linux-hosts"></a>Linux ホスト上の Linux コンテナー

**単一デバイスを手動でプロビジョニングする**:

* [X.509 証明書を使用して単一の Linux デバイスをプロビジョニングする](how-to-provision-single-device-linux-x509.md)
* [対称キーを使用して単一の Linux デバイスをプロビジョニングする](how-to-provision-single-device-linux-symmetric.md)

**複数のデバイスを大規模にプロビジョニングする**:

* [X.509 証明書を使用して Linux デバイスを大規模にプロビジョニングする](how-to-provision-devices-at-scale-linux-x509.md)
* [TPM 構成証明を使用して Linux デバイスを大規模にプロビジョニングする](how-to-provision-devices-at-scale-linux-tpm.md)
* [対称キーを使用して Linux デバイスを大規模にプロビジョニングする](how-to-provision-devices-at-scale-linux-symmetric.md)

<!--1.1-->
:::moniker range="iotedge-2018-06"
### <a name="linux-containers-on-windows-hosts"></a>Windows ホスト上の Linux コンテナー

**単一デバイスを手動でプロビジョニングする**:

* [X.509 証明書を使用して単一の Linux on Windows デバイスをプロビジョニングする](how-to-provision-single-device-linux-on-windows-x509.md)
* [対称キーを使用して単一の Linux on Windows デバイスをプロビジョニングする](how-to-provision-single-device-linux-on-windows-symmetric.md)

**複数のデバイスを大規模にプロビジョニングする**:

* [X.509 証明書を使用して Linux on Windows デバイスを大規模にプロビジョニングする](how-to-provision-devices-at-scale-linux-on-windows-x509.md)
* [TPM 構成証明を使用して Linux on Windows デバイスを大規模にプロビジョニングする](how-to-provision-devices-at-scale-linux-on-windows-tpm.md)
* [対称キーを使用して Linux on Windows デバイスを大規模にプロビジョニングする](how-to-provision-devices-at-scale-linux-on-windows-symmetric.md)

### <a name="windows-containers-on-windows-hosts"></a>Windows ホスト上の Windows コンテナー

**単一デバイスを手動でプロビジョニングする**:

* [X.509 証明書を使用して単一の Windows デバイスをプロビジョニングする](how-to-provision-single-device-windows-x509.md)
* [対称キーを使用して単一の Windows デバイスをプロビジョニングする](how-to-provision-single-device-windows-symmetric.md)

**複数のデバイスを大規模にプロビジョニングする**:

* [X.509 証明書を使用して Windows デバイスを大規模にプロビジョニングする](how-to-provision-devices-at-scale-windows-x509.md)
* [TPM 構成証明を使用して Windows デバイスを大規模にプロビジョニングする](how-to-provision-devices-at-scale-windows-tpm.md)
* [対称キーを使用して Windows デバイスを大規模にプロビジョニングする](how-to-provision-devices-at-scale-windows-symmetric.md)
:::moniker-end
