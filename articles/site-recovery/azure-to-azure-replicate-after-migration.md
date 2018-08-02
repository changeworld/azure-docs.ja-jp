---
title: Azure への移行後に Azure Site Recovery を使用して Azure VM のディザスター リカバリーをセットアップする |Microsoft Docs
description: この記事では、Azure Site Recovery を使用して Azure に移行した後、Azure リージョン間でのディザスター リカバリーをセットアップできるようにマシンを準備する方法について説明します。
services: site-recovery
author: ponatara
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: ponatara
ms.openlocfilehash: 3e26e40c26a27fdab1ec565dd4112b40acdd17d2
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213369"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Azure への移行後に Azure VM のディザスター リカバリーをセットアップする 


この記事は、[Site Recovery](site-recovery-overview.md) サービスを使用して[オンプレミス マシンを Azure VM に移行](tutorial-migrate-on-premises-to-azure.md)した後で読んでください。 この記事は、Site Recovery を使用して、セカンダリ Azure リージョンへのディザスター リカバリーのセットアップに備えて Azure VM を準備する際に役立ちます。



## <a name="before-you-start"></a>開始する前に

ディザスター リカバリーをセットアップする前に、移行が予期したとおりに完了したことを確認します。 移行を正常に完了するには、フェールオーバーの後で、移行するマシンごとに **[移行の完了]** オプションを選択する必要があります。 



## <a name="install-the-azure-vm-agent"></a>Azure VM エージェントのインストール

Site Recovery がレプリケートできるように Azure [VM エージェント](../virtual-machines/extensions/agent-windows.md)を VM にインストールする必要があります。


1. Windows を実行する VM に VM エージェントをインストールするには、[エージェント インストーラー](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)をダウンロードして実行します。 インストールを完了するには、VM の管理者特権が必要です。
2. Linux を実行する VM に VM エージェントをインストールするには、最新の [Linux エージェント](../virtual-machines/extensions/agent-linux.md)をインストールします。 インストールを実行するには、管理者特権が必要です。 ディストリビューション リポジトリからインストールすることをお勧めします。 GitHub から直接 Linux VM エージェントをインストールすることはお勧めしません。 


## <a name="validate-the-installation-on-windows-vms"></a>Windows VM でのインストールの検証

1. Azure VM の C:\WindowsAzure\Packages フォルダーで WaAppAgent.exe ファイルを確認します。
2. このファイルを右クリックし、**[プロパティ]** で **[詳細]** タブを選択します。
3. **[製品バージョン]** フィールドに 2.6.1198.718 以上が表示されていることを確認します。



## <a name="migration-from-vmware-vms-or-physical-servers"></a>VMware VM または物理サーバーからの移行

オンプレミスの VMware VM (または物理サーバー) を Azure に移行する場合は、次の点に注意してください。

- Azure VM エージェントをインストールする必要があるのは、移行済みのマシンにインストールされているモビリティ サービスが v9.6 以前の場合のみです。
- バージョン 9.7.0.0 以降のモビリティ サービスを実行している Windows VM では、サービス インストーラーによって、公開されている最新の Azure VM エージェントがインストールされます。 移行時、これらの VM は、Site Recovery 拡張機能を含め、VM 拡張機能を使用するためのエージェント インストール前提条件を既に満たしています。
- 次のいずれかの方法を使用して、モビリティ サービスを Azure VM から手動でアンインストールする必要があります。 VM を再起動してからレプリケーションを構成します。
    - Windows の場合は、[コントロール パネル] > **[プログラムの追加と削除]** で、**Microsoft Azure Site Recovery Mobility Service/Master Target server** をアンインストールします。 管理者特権でのコマンド プロンプトで、次を実行します。
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Linux の場合は、root ユーザーとしてサインインします。 端末で、**/user/local/ASR** に移動して、次のコマンドを実行します。
        ```
        uninstall.sh -Y
        ```


## <a name="next-steps"></a>次の手順

Azure VM をセカンダリ リージョンに[迅速にレプリケート](azure-to-azure-quickstart.md)します。
