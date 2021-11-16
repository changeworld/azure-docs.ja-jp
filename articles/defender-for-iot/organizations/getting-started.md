---
title: 'クイックスタート: 概要'
description: このクイックスタートでは、Defender for IoT のデプロイに関する基本的なワークフローを理解して作業を開始する方法について学習します。
ms.topic: quickstart
ms.date: 11/09/2021
ms.openlocfilehash: 85bd254236a6d08d8f232e14e5a41dab99f0af04
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283868"
---
# <a name="quickstart-get-started-with-defender-for-iot"></a>クイックスタート: Defender for IoT での作業を開始する

この記事では、Microsoft Defender for IoT を設定するために実行する手順の概要について説明します。 このプロセスでは、次のことを行う必要があります。

- Azure portal で Defender for IoT のサブスクリプションとセンサーを登録します。
- センサーとオンプレミス管理コンソール ソフトウェアをインストールします。
- センサーと管理コンソールの初回のアクティブ化を実行します。

## <a name="permission-requirements"></a>権限の要件

### <a name="for-sensors-and-on-premises-management-consoles"></a>センサーおよびオンプレミスの管理コンソールの場合

一部の設定手順では、特定のユーザーのアクセス許可が必要です。

センサーと管理コンソールをアクティブにし、SSL/TLS 証明書をアップロードして、新しいパスワードを生成するには、管理ユーザーのアクセス許可が必要です。

### <a name="for-defender-for-iot-in-the-azure-portal"></a>Azure portal の Defender for IoT の場合

Azure portal のツールに対するユーザー アクセス許可を次の表で説明します。

| 権限 | セキュリティ閲覧者 | セキュリティ管理者 | サブスクリプションの共同作成者 | サブスクリプションの所有者 |
|--|--|--|--|--|
| ソフトウェア、アクティブ化ファイル、および脅威インテリジェンス パッケージの詳細を表示してアクセスする  | ✓ | ✓ | ✓ | ✓ |
| センサーをオンボードする  |  |  ✓ | ✓ | ✓ |
| サブスクリプションをオンボードし、コミットされたデバイスを更新する  |  |  | ✓ | ✓ |
| パスワードを回復する  | ✓  |  ✓ | ✓ | ✓ |

## <a name="identify-the-solution-infrastructure"></a>ソリューション インフラストラクチャを特定する

**ネットワーク設定のニーズを明確にする**

以下を調査します。

- ネットワーク アーキテクチャ
- 監視対象の帯域幅
- 証明書を作成するための要件
- その他のネットワークの詳細

詳細については、[Microsoft Defender for IoT ネットワーク設定の概要](how-to-set-up-your-network.md)に関する記事を参照してください。

**ネットワーク負荷を処理するために必要なセンサーと管理コンソール アプライアンスを明確にする**

Microsoft Defender for IoT では、物理的な配置と仮想デプロイの両方をサポートしています。 物理的な配置では、さまざまな認定アプライアンスを購入できます。 詳細については、「[必要なアプライアンスを特定する](how-to-identify-required-appliances.md)」を参照してください。

監視するデバイスのおおよその数を計算することをお勧めします。 後で Azure サブスクリプションをポータルに登録するときに、この番号を入力するように求められます。 数値は 1,000 間隔 (たとえば 1000、2000、3000) で追加できます。 監視対象デバイスの数は、"*コミット済みデバイス*" と呼ばれます。

## <a name="register-with-microsoft-defender-for-iot"></a>Microsoft Defender for IoT を使用して登録する

登録には次のものが含まれます。

- Defender for IoT への Azure サブスクリプションのオンボード。
- コミット済みデバイスの定義。
- オンプレミス管理コンソールのアクティブ化ファイルのダウンロード。

試用版サブスクリプションを使用して、30 日間無料で 1,000 デバイスを監視することもできます。 詳細については、[試用版サブスクリプションのオンボード](how-to-manage-subscriptions.md#onboard-a-trial-subscription)に関するページを参照してください。

**登録するには**:

1. Azure portal で [Defender for IoT の概要](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)ページに移動します。

1. **[Onboard subscription]\(サブスクリプションのオンボード\)** を選択します。

1. **[価格]** ページでサブスクリプションを選択するか、新しいサブスクリプションを作成してコミット済みデバイスの数を追加します。

1. **[Download the on-premises management console]\(オンプレミス管理コンソールのダウンロード\)** タブを選択し、ダウンロードしたアクティブ化ファイルを保存します。 このファイルには、定義したコミット済みデバイスがまとめられています。 このファイルは、最初のサインイン後に管理コンソールにアップロードされます。

サブスクリプションをオフボードする方法について詳しくは、「[サブスクリプションをオフボードする](how-to-manage-subscriptions.md#offboard-a-subscription)」を参照してください。

## <a name="install-and-set-up-the-on-premises-management-console"></a>オンプレミス管理コンソールをインストールして設定する

オンプレミス管理コンソール アプライアンスを取得した後:

- Azure portal から ISO パッケージをダウンロードします。
- このソフトウェアをインストールします。
- 管理コンソールの初期設定をアクティブにして実行します。

**インストールして設定するには**:

1. Azure portal で [Defender for IoT の概要](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)ページに移動します。

1. **[On-premises management console]\(オンプレミス管理コンソール\)** タブを選択します。

1. バージョンを選択し、 **[ダウンロード]** を選択します。

1. オンプレミス管理コンソール ソフトウェアをインストールします。 詳細については、[Defender for IoT のインストール](how-to-install-software.md)に関する記事を参照してください。

1. 管理コンソールをアクティブにして設定します。 詳細については、「[オンプレミス管理コンソールをアクティブにしてセットアップする](how-to-activate-and-set-up-your-on-premises-management-console.md)」を参照してください。

## <a name="onboard-a-sensor"></a>センサーをオンボードする ##

センサーをオンボードするために、Microsoft Defender for IoT に登録して、センサー アクティブ化ファイルをダウンロードします。

1. センサー名を定義し、サブスクリプションに関連付けます。

1. センサーの接続モードを選択します。

   - **クラウド接続センサー**:センサーによって検出された情報は、センサー コンソールに表示されます。 さらに、アラート情報は IoT ハブ経由で配信され、Microsoft Sentinel などの他の Azure サービスと共有できます。 Defender for IoT からセンサーに脅威インテリジェンス パッケージを自動的にプッシュすることを選択することもできます。 詳細については、「[脅威インテリジェンスの調査とパッケージ](how-to-work-with-threat-intelligence-packages.md)」を参照してください。

   - **ローカル管理センサー**:センサーによって検出された情報は、センサー コンソールに表示されます。 エアギャップ ネットワークで作業していて、複数のローカル管理センサーによって検出されるすべての情報を一元的に表示したい場合は、オンプレミスの管理コンソールを使用します。

1. センサーを関連付けるサイトを IoT Hub 内で選択します。 IoT Hub は、このセンサーと Microsoft Defender for IoT の間のゲートウェイとして機能します。 表示名とゾーンを定義します。 説明タグを追加することもできます。 表示名、ゾーン、タグは、[[サイトとセンサー] ページ](how-to-manage-sensors-on-the-cloud.md#view-onboarded-sensors)にある説明用のエントリです。

1. **[登録]** を選択します。

1. **[アクティブ化ファイルのダウンロード]** を選択します。

オンボードの詳細については、[Defender for IoT を使用したセンサーのオンボードと管理](how-to-manage-sensors-on-the-cloud.md)に関する記事を参照してください。

## <a name="install-and-set-up-the-sensor"></a>センサーをインストールして設定する

Azure portal から ISO パッケージをダウンロードしてソフトウェアをインストールし、センサーを設定します。

1. Azure portal で [Defender for IoT の概要](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)ページに移動します。

1. **[Set up sensor]\(センサーの設定\)** を選択します。

1. バージョンを選択し、 **[ダウンロード]** を選択します。

1. センサー ソフトウェアをインストールします。 詳細については、[Defender for IoT のインストール](how-to-install-software.md)に関する記事を参照してください。

1. センサーをアクティブにして設定します。 詳細については、[センサーのサインインとアクティブ化](how-to-activate-and-set-up-your-sensor.md)に関する記事を参照してください。

## <a name="connect-sensors-to-an-on-premises-management-console"></a>センサーをオンプレミス管理コンソールに接続します。

センサーを管理コンソールに接続して、次のことを確認します。

- センサーからオンプレミス管理コンソールにアラートとデバイス インベントリ情報が送信されること。

- オンプレミス管理コンソールを使用して、センサーのバックアップを実行し、センサーによって検出されたアラートを管理し、センサーの切断を調査し、接続されたセンサーに対してその他のアクティビティを実行できること。

同じネットワークを監視する複数のセンサーを 1 つのゾーンにグループ化することをお勧めします。 こうすることで、複数のセンサーによって収集された情報がまとめられます。

詳細については、「[センサーをオンプレミス管理コンソールに接続する](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console)」を参照してください。

## <a name="populate-microsoft-sentinel-with-alert-information-optional"></a>Microsoft Sentinel にアラート情報を取り込む

Microsoft Sentinel を構成して、アラート情報を Microsoft Sentinel に送信します。 「[Defender for IoT からのデータを Microsoft Sentinel に接続する](how-to-configure-with-sentinel.md)」を参照してください。  

## <a name="next-steps"></a>次のステップ ##

[Microsoft Defender for IoT へようこそ](overview.md)

[Microsoft Defender for IoT のアーキテクチャ](architecture.md)
