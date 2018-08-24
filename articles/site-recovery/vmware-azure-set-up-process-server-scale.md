---
title: Azure Site Recovery を使用した VMware VM および物理サーバーのフェールバックのために Azure でプロセス サーバーを設定する | Microsoft Docs
description: この記事では、Azure VM を VMware にフェールバックするために Azure でプロセス サーバーを設定する方法について説明します。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 478b678f514959b8ae9f6492d4920faf894c321d
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40004183"
---
# <a name="set-up-additional-process-servers-for-scalability"></a>スケーラビリティのために追加のプロセス サーバーを設定する

既定では、[Site Recovery](site-recovery-overview.md) を使用して VMware VM または物理サーバーを Azure にレプリケートするときに、構成サーバー マシン上にプロセス サーバーがインストールされ、Site Recovery とオンプレミスのインフラストラクチャ間のデータ転送を調整するために使用されます。 容量を増加させ、レプリケーションのデプロイをスケールアウトするために、別のスタンドアロン プロセス サーバーを追加できます。 この記事では、その方法について説明します。

## <a name="before-you-start"></a>開始する前に

### <a name="capacity-planning"></a>容量計画

VMware レプリケーションのために[容量計画](site-recovery-plan-capacity-vmware.md)を実行したことを確認してください。 これは、追加のプロセス サーバーをどのように、いつデプロイする必要があるかを識別する助けとなります。

### <a name="sizing-requirements"></a>サイズ変更の要件 

表にまとめられているサイズ変更の要件を確認します。 一般に、ソース マシンが 200 台を超えるまでデプロイメントをスケールする必要がある場合や、合計日次変更率が 2 TB を超える場合は、トラフィック ボリュームの処理のために追加のプロセス サーバーが必要です。

| **追加のプロセス サーバー** | **キャッシュ ディスク サイズ** | **データの変更率** | **保護されたマシン** |
| --- | --- | --- | --- |
|4 vCPU (2 ソケット * 2 コア \@ 2.5 GHz)、8 GB メモリ |300 GB |250 GB 以下 |85 台以下のマシンをレプリケートします。 |
|8 vCPU (2 ソケット * 4 コア \@ 2.5 GHz)、12 GB メモリ |600 GB |250 GB ～ 1 TB |85 ～ 150 台のマシンをレプリケートします。 |
|12 vCPU (2 ソケット * 6 コア \@ 2.5 GHz)、24 GB メモリ |1 TB (テラバイト) |1 TB ～ 2 TB |150 ～ 225 台のマシンをレプリケートします。 |

保護された各ソース マシンには、それぞれ 100 GB の 3 つのディスクが構成されています。

### <a name="prerequisites"></a>前提条件

次の表に、追加のプロセス サーバーの前提条件をまとめます。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]


## <a name="download-installation-file"></a>インストール ファイルをダウンロードする

次のように、プロセス サーバーのインストール ファイルをダウンロードします。

1. Azure Portal にログオンし、Recovery Services コンテナーを参照します。
2. **[Site Recovery インフラストラクチャ]** > **[VMware and Physical Machines] (VMware と物理マシン)** > **[構成サーバー]** ([For VMware & Physical Machines] \(VMware および物理マシン) の下) に移動します。
3. 構成サーバーを選択して、サーバーの詳細に移動します。 次に、**[+ プロセス サーバー]** をクリックします。
4. **[プロセス サーバーの追加]** >  **[プロセス サーバーのデプロイ先を選択してください]** で、**[スケールアウト プロセス サーバーをオンプレミスにデプロイします]** を選択します。

  ![[サーバーの追加] ページ](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. **[Download the Microsoft Azure Site Recovery Unified Setup]** (Microsoft Azure Site Recovery 統合セットアップのダウンロード) をクリックします。 これで最新バージョンのインストール ファイルがダウンロードされます。

  > [!WARNING]
  プロセス サーバーのインストール バージョンは、実行している構成サーバーのバージョンと同じ、またはそれより前である必要があります。 バージョンの互換性を確保する簡単な方法は、構成サーバーをインストールまたは更新するために最近使用したものと同じインストーラーを使用することです。

## <a name="install-from-the-ui"></a>UI からインストールする

次のようにインストールします。 サーバーをセットアップしたら、サーバーを使用するソース マシンを移行します。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>コマンドラインからインストールする

次のコマンドを実行してインストールします。

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

ここで、コマンドライン パラメーターは次のとおりです。

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

例: 

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>プロキシ設定ファイルを作成する

プロキシを設定する必要がある場合、ProxySettingsFilePath パラメーターは、入力としてファイルを受け取ります。 ファイルは次のように作成し、それを ProxySettingsFilePath の入力パラメーターとして渡します。

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>次の手順
[プロセス サーバー設定の管理](vmware-azure-manage-process-server.md)について学習します
