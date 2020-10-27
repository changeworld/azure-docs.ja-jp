---
title: デプロイ オプション
description: Defender for IoT の機能とサービスの基本的なワークフローへの理解を深めます。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: e3de253ee6f45f9296d6b09189fe4bc488be36ad
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090065"
---
# <a name="getting-started-with-azure-defender-for-iot"></a>Azure Defender for IoT を使用した作業の開始

この記事では、Azure Defender for IoT を稼働させるために必要なデプロイとオンボードのプロセスについて説明します。 追加のステップも必要です。 これらのステップを理解し、付随するドキュメント内の情報を理解することをお勧めします。

すべてのステップを完了すると、Azure Defender for IoT センサーによってネットワークが監視されます。 ソリューションの設定方法によっては、オンプレミスの管理コンソールまたは IoT Hub に検出を送信することもできます。

次のステップを実行して、Azure Defender for IoT を準備して実行します。

## <a name="1-set-up-azure"></a>1.Azure をセットアップする

- Azure アカウントを設定します。 詳細については、「[Azure アカウントの作成](https://docs.microsoft.com/learn/modules/create-an-azure-account/)」をご覧ください。

- ファイアウォールまたはプロキシ:特定の接続を許可するように構成されているファイアウォールまたは同様の介在するネットワーク デバイスがある場合は、*.azure-devices.net:443 がファイアウォールまたはプロキシに開かれていることを確認します。 ワイルドカードがサポートされていない場合、または細かく制御する必要がある場合は、特定の IoT Hub FQDN を FW またはプロキシで開く必要があります。 詳しくは、「[リファレンス - IoT Hub エンドポイント](/azure/iot-hub/iot-hub-devguide-endpoints)」をご覧ください。

## <a name="2-deploy-hardware-software-and-onboard-to-sensor"></a>2.センサーにハードウェア、ソフトウェア、オンボードをデプロイする

- センサー ハードウェアを購入し、ソフトウェアをインストールします。 ここに記載されているステップに従ってください。詳細については、この記事、[Defender for IoT ハードウェアに関するガイド](https://aka.ms/AzureDefenderforIoTBareMetalAppliance)、および[インストールに関するガイド](https://aka.ms/AzureDefenderforIoTInstallSensorISO)をご覧ください。

  - センサーをインストールした後、センサーのサインイン資格情報を安全に記録します。 センサーにアクティブ化ファイルをアップロードするには、資格情報が必要です。

  - ローカルで管理されているセンサーを使用する場合は、センサーの IP アドレスまたはインストール時に定義したセンサー名を安全に記録します。 これは、Defender for IoT ポータルでのセンサー登録でセンサー名を作成するときに使用できます。 後からこれらを使用することで追跡が容易になり、また、Azure Defender for IoT ポータルにおける登録名と、センサーのコンソールに表示されるデプロイ済みセンサーの IP アドレスとの間で名前の一貫性が確保されます。

- Defender for IoT ポータルにセンサーを登録し、センサー アクティブ化ファイルをダウンロードします。

- アクティブ化ファイルをセンサーにアップロードします。

## <a name="3-perform-network-setup-for-sensor-monitoring-and-management"></a>3.センサーの監視と管理のためにネットワークのセットアップを実行する

- センサーをネットワークに接続します。 [ネットワーク セットアップ ガイド](https://aka.ms/AzureDefenderForIoTNetworkSetup)で説明されています。

## <a name="4-start-discovering-your-network"></a>4.ネットワークの検出を開始する

- センサー コンソールのシステム設定を調整します。

- センサーをオンプレミスの管理コンソールに接続します。

詳細については、[Azure Defender for IoT センサーのユーザー ガイド](https://aka.ms/AzureDefenderforIoTUserGuide)および[Defender for IoT オンプレミス管理コンソールのユーザー ガイド](https://aka.ms/DefenderForIoTManagementConsole)を参照してください。

## <a name="5-populate-azure-sentinel-with-alert-information"></a>5.Azure Sentinel にアラート情報を設定する

- Azure Sentinel にアラート情報を送信するには、Azure Sentinel を構成します。[Defender for IoT からのデータを Azure Sentinel に接続します](how-to-configure-with-sentinel.md)。
 

## <a name="next-steps"></a>次のステップ

- [Defender for IoT](quickstart-onboard-iot-hub.md) を有効にする
- [ソリューションを構成する](quickstart-configure-your-solution.md)
- [セキュリティ モジュールを作成する](quickstart-create-security-twin.md)
- [カスタム アラートを構成する](quickstart-create-custom-alerts.md)
- [セキュリティ エージェントをデプロイする](how-to-deploy-agent.md)
