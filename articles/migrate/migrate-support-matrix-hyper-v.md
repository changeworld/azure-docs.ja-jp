---
title: Hyper-V の評価と移行に関する Azure Migrate サポート マトリックス
description: Azure Migrate サービスを使用した Hyper-V の評価と移行に関する設定と制限について、要点を説明します。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/02/2019
ms.author: raynew
ms.openlocfilehash: f6edbe19429b38d68aea1f1ecfe426c9b2d194d0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810082"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>Hyper-V の評価と移行のサポート マトリックス

[Azure Migrate サービス](migrate-overview.md)を使用して、マシンを評価して Microsoft Azure クラウドに移行することができます。 この記事では、オンプレミスの Hyper-V VM を評価して移行する際にサポートされる設定と制限について要点を説明します。



## <a name="hyper-v-scenarios"></a>Hyper-V のシナリオ

この表は、Hyper-V VM に関してサポートされるシナリオをまとめたものです。

**Deployment** | **詳細*** 
--- | --- 
**オンプレミスの Hyper-V VM を評価する** | 最初の評価を[設定](tutorial-prepare-hyper-v.md)します。<br/><br/> 大規模な評価を[実行](scale-hyper-v-assessment.md)します。
**Hyper-V VM を Azure に移行する** | Azure への移行を[試します](tutorial-migrate-hyper-v.md)。

    

## <a name="azure-migrate-projects"></a>Azure Migrate プロジェクト

**サポート** | **詳細**
--- | ---
Azure のアクセス許可 | Azure Migrate プロジェクトを作成するには、サブスクリプションの共同作成者または所有者のアクセス許可が必要です。
Hyper-V VM | 単一のプロジェクトで最大 10,000 個の Hyper-V VM を評価します。

評価の上限に達するまでは、プロジェクトに VMware VM と Hyper-V VM の両方を含めることができます。


## <a name="assessment-hyper-v-host-requirements"></a>評価 - Hyper-V ホストの要件

| **サポート**                | **詳細**               
| :-------------------       | :------------------- |
| **ホストのデプロイ**       | Hyper-V ホストは、スタンドアロンにすることも、クラスターにデプロイすることもできます。 |
| **アクセス許可**           | Hyper-V ホストに対する管理者のアクセス許可が必要です。 |
| **ホスト オペレーティング システム** | Windows Server 2016 または Windows Server 2012 R2。<br/> Windows Server 2019 を実行している Hyper-V ホスト上にある VM を評価することはできません。 |
| **PowerShell リモート処理**   | 各ホストで有効にする必要があります。 |
| **Hyper-V レプリカ**       | Hyper-V レプリカを使用する (または、同じ VM 識別子を持つ複数の VM がある) 場合、Azure Migrate を使用して元の VM とレプリケートされた VM の両方を検出すると、Azure Migrate によって生成される評価が正確ではない可能性があります。 |


## <a name="assessment-hyper-v-vm-requirements"></a>評価 - Hyper-V VM の要件

| **サポート**                  | **詳細**               
| :----------------------------- | :------------------- |
| **オペレーティング システム** | Azure でサポートされているすべての [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) および [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) オペレーティング システム。 |
| **アクセス許可**           | 評価する各 Hyper-V VM に対する管理者のアクセス許可が必要です。 |
| **統合サービス**       | オペレーティング システム情報をキャプチャするには、評価する VM で [Hyper-V 統合サービス](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services)が実行されている必要があります。 |
| **Azure に必要な変更** | 一部の VM では、Azure で実行できるように変更が必要になる場合があります。 次のオペレーティング システムでは、これらの変更が Azure Migrate によって自動的に行われます。<br/> - Red Hat Enterprise Linux 6.5+、7.0+<br/> - CentOS 6.5+、7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS、16.04LTS、18.04LTS<br/> - Debian 7、8<br/><br/> その他のオペレーティング システムでは、手動で調整してから移行する必要があります。 関連する記事にその手順が含まれています。 |
| **Linux ブート**                 | /boot が専用パーティション上にある場合、OS ディスク上に存在する必要があり、複数のディスクにまたがることはできません。<br/> /boot がルート (/) パーティションの一部である場合、‘/’ パーティションは OS ディスク上にある必要があり、他のディスクにまたがることはできません。 |
| **UEFI ブート**                  | UEFI ブートを使用した VM の移行はサポートされません。 |
| **暗号化されたディスク/ボリューム**    | 暗号化されたディスク/ボリュームを含む VM の移行はサポートされません。 |
| **RDM/パススルー ディスク**      | RDM またはパススルー ディスクが VM にある場合、これらのディスクは Azure にレプリケートされません。 |
| **NFS**                        | VM 上のボリュームとしてマウントされた NFS ボリュームは、レプリケートされません。 |
| **ターゲット ディスク**                | Azure Migrate の評価では、マネージド ディスクのみを含む Azure VM への移行が推奨されます。 |


## <a name="assessment-appliance-requirements"></a>評価 - アプライアンスの要件

Azure Migrate では評価のために軽量アプライアンスを実行して Hyper-V VM を検出し、VM メタデータとパフォーマンス データを Azure Migrate に送信します。 アプライアンスは Hyper-V VM 上で実行されます。設定は、Azure portal からダウンロードした圧縮された Hyper-V VHD を使用して行います。 次の表は、アプライアンスの要件をまとめたものです。

| **サポート**                | **詳細**               
| :-------------------       | :------------------- |
| **Azure Migrate プロジェクト**  |  単一のプロジェクトにアプライアンスを関連付けることができます。<br/> 単一のアプライアンスで最大 5,000 個の Hyper-V VM を検出できます。
| **Hyper-V の制限事項**    |  アプライアンスを Hyper-V VM としてデプロイします。<br/> 提供されるアプライアンス VM は、Hyper-V VM バージョン 5.0 です。<br/> VM ホストで Windows Server 2012 R2 以降が実行されている必要があります。<br/> アプライアンス VM に 16 GB RAM、4 つの仮想プロセッサ、および 1 つの外部スイッチを割り当てるのに十分な領域が必要です。<br/> アプライアンスには、静的または動的 IP アドレス、およびインターネット アクセスが必要です。
| **Hyper-V アプライアンス**      |  アプライアンスは Hyper-V VM として設定されます。<br/> ダウンロード用に提供される VHD は、Hyper-V VM バージョン 5.0 です。
| **Host**                   | アプライアンス VM を実行している VM ホストで、Windows Server 2012 R2 以降が実行されている必要があります。<br/> アプライアンス VM に 16 GB RAM、4 つの仮想プロセッサ、および 1 つの外部スイッチを割り当てるのに十分な領域が必要です。<br/> アプライアンスには、静的または動的 IP アドレス、およびインターネット アクセスが必要です。 |
| **移行サポート**      | マシンのレプリケートを開始するには、アプライアンス上の移行ゲートウェイ サービスが 1.18.7141.12919 以降である必要があります。 アプライアンス Web アプリにサインインして、バージョンを確認します。 |

## <a name="assessment-appliance-url-access"></a>評価 - アプライアンスの URL アクセス

VM を評価するには、Azure Migrate アプライアンスがインターネットに接続されている必要があります。

- アプライアンスをデプロイするときに、以下の表にまとめた URL への接続チェックが Azure Migrate によって実行されます。
- URL ベースの firewall.proxy を使用している場合は、表の URL へのアクセスを許可し、URL の検索中に受信されるすべての CNAME レコードがプロキシによって解決されることを確認してください。
- インターセプト プロキシがある場合は、プロキシ サーバーからアプライアンスへのサーバー証明書のインポートが必要になる場合があります。 

    
**URL** | **詳細**  
--- | --- 
*.portal.azure.com | Azure portal への移動
*.windows.net | Azure サブスクリプションにサインインします。
*.microsoftonline.com | アプライアンスからサービスへの通信のための Azure Active Directory アプリケーションの作成。
management.azure.com | アプライアンスからサービスへの通信のための Azure Active Directory アプリケーションの作成。
dc.services.visualstudio.com | ログ記録と監視 
*.vault.azure.net | アプライアンスとサービス間の通信時の Azure Key Vault のシークレットを管理します。


## <a name="assessment-port-requirements"></a>評価 - ポートの要件

次の表は、評価のためのポート要件をまとめたものです。

**デバイス** | **Connection**
--- | --- 
**アプライアンス** | TCP ポート 3389 で、アプライアンスへのリモート デスクトップ接続を許可するための受信接続。<br/> https://<appliance-ip-or-name>:44368 という URL を使用し、ポート 44368 で、アプライアンス管理アプリにリモート アクセスするための受信接続<br/> ポート 443 で、検出とパフォーマンスのメタデータを Azure Migrate に送信するための送信接続。
**Hyper-V ホスト/クラスター** | Common Information Model (CIM) セッションを使用し、WinRM ポート 5985 (HTTP) と 5986 (HTTPS) で、Hyper-V VM の構成とパフォーマンスのメタデータをプルするための受信接続。

## <a name="migration-hyper-v-host-requirements"></a>移行 - Hyper-V ホストの要件

| **サポート**                | **詳細**               
| :-------------------       | :------------------- |
| **ホストのデプロイ**       | Hyper-V ホストは、スタンドアロンにすることも、クラスターにデプロイすることもできます。 |
| **アクセス許可**           | Hyper-V ホストに対する管理者のアクセス許可が必要です。 |
| **ホスト オペレーティング システム** | Windows Server 2019、Windows Server 2016、または Windows Server 2012 R2。 |

## <a name="migration-hyper-v-vm-requirements"></a>移行 - Hyper-V VM の要件

| **サポート**                  | **詳細**               
| :----------------------------- | :------------------- |
| **オペレーティング システム** | Azure でサポートされているすべての [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) および [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) オペレーティング システム。 |
| **アクセス許可**           | 評価する各 Hyper-V VM に対する管理者のアクセス許可が必要です。 |
| **統合サービス**       | オペレーティング システム情報をキャプチャするには、評価する VM で [Hyper-V 統合サービス](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services)が実行されている必要があります。 |
| **Azure に必要な変更** | 一部の VM では、Azure で実行できるように変更が必要になる場合があります。 次のオペレーティング システムでは、これらの変更が Azure Migrate によって自動的に行われます。<br/> - Red Hat Enterprise Linux 6.5+、7.0+<br/> - CentOS 6.5+、7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS、16.04LTS、18.04LTS<br/> - Debian 7、8<br/><br/> その他のオペレーティング システムでは、手動で調整してから移行する必要があります。 関連する記事にその手順が含まれています。 |
| **Linux ブート**                 | /boot が専用パーティション上にある場合、OS ディスク上に存在する必要があり、複数のディスクにまたがることはできません。<br/> /boot がルート (/) パーティションの一部である場合、‘/’ パーティションは OS ディスク上にある必要があり、他のディスクにまたがることはできません。 |
| **UEFI ブート**                  | UEFI ブートを使用した VM の移行はサポートされません。 |
| **暗号化されたディスク/ボリューム**    | 暗号化されたディスク/ボリュームを含む VM の移行はサポートされません。 |
| **RDM/パススルー ディスク**      | RDM またはパススルー ディスクが VM にある場合、これらのディスクは Azure にレプリケートされません。 |
| **NFS**                        | VM 上のボリュームとしてマウントされた NFS ボリュームは、レプリケートされません。 |
| **ターゲット ディスク**                | マネージド ディスクのみを含む Azure VM に移行することができます。 |




## <a name="migration-hyper-v-host-url-access"></a>移行 - Hyper-V ホストの URL アクセス

次の表は、Hyper-V ホストの URL アクセス要件をまとめたものです。

**URL** | **詳細**  
--- | ---
login.microsoftonline.com | Active Directory を使用したアクセス制御と ID 管理。
*.backup.windowsazure.com | レプリケーション データの転送と調整。
*.hypervrecoverymanager.windowsazure.com | Azure Migrate サービスの URL に接続します。
*.blob.core.windows.net | ストレージ アカウントにデータをアップロードします。
dc.services.visualstudio.com | 内部監視に使用するアプリのログをアップロードします。
time.windows.com | システム時刻とグローバル時刻間の時刻同期を確認します。

## <a name="migration-port-access"></a>移行 - ポート アクセス

次の表は、VM 移行用の Hyper-V ホストと VM のポート要件をまとめたものです。

**デバイス** | **Connection**
--- | --- 
Hyper-V ホスト/VM | HTTPS ポート 443 で、VM レプリケーション データを Azure Migrate に送信するための送信接続。

  
## <a name="migration-vm-disk-support"></a>移行 - VM ディスクのサポート 

**サポート** | **詳細**
--- | ---
移行されたディスク | VM は、Azure 内のマネージド ディスク (Standard HHD、Premium SSD) にのみ移行できます。
   

## <a name="next-steps"></a>次の手順

移行のために [Hyper-V VM の評価を準備](tutorial-prepare-hyper-v.md)します。




 
