---
title: Azure Site Recovery を使用して VMware VM と物理サーバーを Azure にディザスター リカバリーする場合のレプリケーションの問題のトラブルシューティング | Microsoft Docs
description: この記事では、Azure Site Recovery を使用して VMware VM と物理サーバーを Azure にディザスター リカバリーする際の一般的なレプリケーションの問題のトラブルシューティングについて説明します。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: c53dc81da9469c0628adbd3751dc818997fa4d05
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063680"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>VMware VM および物理サーバーのレプリケーション問題のトラブルシューティング

VMware 仮想マシンまたは物理サーバーを Azure Site Recovery を使用して保護する際に、特定のエラー メッセージが表示される場合があります。 この記事では、[Site Recovery](site-recovery-overview.md) を使用してオンプレミスの VMware VM および物理サーバーを Azure にレプリケートする際に起きることがある一般的な問題について説明します。

## <a name="initial-replication-issues"></a>初期レプリケーションの問題

初期レプリケーションのエラーの多くは、ソース サーバーとプロセス サーバー間、またはプロセス サーバーと Azure 間の接続の問題が原因で発生します。 ほとんどの場合、これらの問題は、次のセクションの手順を実行することにより、トラブルシューティングできます。

### <a name="check-the-source-machine"></a>ソース マシンの確認

ソース マシンの確認方法を次に示します。

*  Telnet を使用して、HTTPS ポート経由でプロセス サーバーを ping します (既定の HTTPS ポートは 9443)。これを行うには、ソース サーバー上のコマンド ラインで、以下のコマンドを実行します。 このコマンドでは、ネットワーク接続の問題と、ファイアウォール ポートをブロックする問題がないかが確認されます。


   `telnet <process server IP address> <port>`


   > [!NOTE]
   > 接続をテストする際は、Telnet を使用します。 `ping` は、使用しないでください。 Telnet がインストールされていない場合は、「[Install Telnet Client (Telnet クライアントのインストール)](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)」に記載されている手順を実行してください。

   プロセス サーバーに接続できない場合は、プロセス サーバー上の受信ポート 9443 を許可します。 たとえば、お使いのネットワークに境界ネットワークやスクリーンド サブネットがある場合は、プロセス サーバー上で受信ポート 9443 を許可することが必要になる場合があります。 この後、問題が引き続き発生するかどうかを確認します。

*  **InMage Scout VX Agent – Sentinel/OutpostStart** サービスの状態を確認します。 サービスが稼働していない場合は、サービスを起動して、問題が引き続き発生するかどうかを確認します。   

### <a name="check-the-process-server"></a>プロセス サーバーの確認

プロセス サーバーの確認方法を次に示します。

*  **プロセス サーバーにより、データが Azure にアクティブにプッシュされているかどうかを確認します**。

   1. プロセス サーバー上で、タスク マネージャーを開きます (Ctrl + Shift + Esc キーを押します)。
   2. **[パフォーマンス]** タブを選択し、**[リソース モニターを開く]** リンクを選択します。 
   3. **[リソース モニター]** ページ上で **[ネットワーク]** タブを選択します。**[Processes with Network Activity]\(ネットワーク活動を伴うプロセス\)** の下で、**cbengine.exe** により大量のデータがアクティブに送信されているかどうかを確認します。

        ![[Processes with Network Activity]\(ネットワーク活動を伴うプロセス\) の下にボリュームが示されているスクリーンショット ](./media/vmware-azure-troubleshoot-replication/cbengine.png)

   Cbengine.exe により大量のデータが送信されていない場合は、次のセクションの手順を実行します。

*  **プロセス サーバーを Azure Blob Storage に接続できるかどうか確認します**。

   **[cbengine.exe]** を選択します。 **[TCP Connections]\(TCP 接続数\)** の下で、プロセス サーバーから Azure Blob Storage の URL への接続があるかどうかを確認します。

   ![cbengine.exe と Azure Blob Storage の URL 間の接続が示されているスクリーン ショット](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

   プロセス サーバーから Azure Blo Storage の URL への接続が存在しない場合は、コントロール パネルで **[サービス]** を選択します。 次のサービスが実行されているかどうかを確認します。

   *  cxprocessserver
   *  InMage Scout VX Agent – Sentinel/Outpost
   *  Microsoft Azure Recovery Services エージェント
   *  Microsoft Azure Site Recovery サービス
   *  tmansvc

   実行されていないサービスがあれば、そのサービスを起動または再起動します。 問題が引き続き発生するかどうかを確認します。

*  **プロセス サーバーがポート 443 を使用して Azure のパブリック IP アドレスに接続できるかどうかを確認します**。

   %programfiles%\Microsoft Azure Recovery Services Agent\Temp 内で最新の CBEngineCurr.errlog ファイルを開きます。 このファイル内で、**443** または **connection attempt failed** という文字列を検索します。

   ![Temp フォルダー内のエラー ログが示されているスクリーン ショット](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

   問題が表示された場合は、プロセス サーバー内のコマンド ラインで、Telnet を使用して Azure のパブリック IP アドレスを ping します (上記の画像では、IP アドレスはマスクされています)。 Azure のパブリック IP アドレスは、ポート 443 を使用して CBEngineCurr.currLog ファイル内で見つけることができます。

   `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

   接続できない場合は、ファイアウォール設定とプロキシ設定のいずれが原因でアクセスの問題が発生しているかを次の手順の説明に従って確認します。

*  **プロセス サーバー上の IP アドレス ベースのファイアウォールによってアクセスがブロックされているかどうかを確認します**。

   サーバー上で IP アドレス ベースのファイアウォールのルールを使用している場合は、[Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)の詳細なリストをダウンロードします。 IP アドレス範囲をファイアウォール構成に追加し、Azure (および既定の HTTPS ポートである 443) への通信がファイアウォールで許可されるようにします。 サブスクリプションの Azure リージョンと米国西部の Azure リージョンの IP アドレス範囲を許可します (アクセス制御と ID 管理に使用されます)。

*  **プロセス サーバー上の URL ベースのファイアウォールによってアクセスがブロックされているかどうかを確認します**。

   サーバー上で URL ベースのファイアウォールのルールを使用する場合は、次の表に示されている URL をファイアウォール構成に追加します。

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

*  **プロセス サーバー上のプロキシ設定によってアクセスがブロックされているかどうかを確認します**。

   プロキシ サーバーを使用している場合は、DNS サーバーでプロキシ サーバー名が解決されることを確認します。 構成サーバーの設定時に指定した値を確認するには、レジストリ キー **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings** に移動します。

   次に、データを送信するために Azure Site Recovery エージェントで同じ設定が使用されていることを確認します。 
      
   1. **Microsoft Azure Backup** を検索します。 
   2. **Microsoft Azure Backup** を開き、**[アクション]** > **[プロパティの変更]** を選択します。 
   3. **[プロキシの構成]** タブ上にプロキシのアドレスが表示されます。 プロキシのアドレスは、レジストリ設定に表示されるプロキシのアドレスと同じでなければなりません。 同じでない場合は、同じアドレスに変更してください。

*  **プロセス サーバー上でスロットルの帯域幅が制限されていないかどうかを確認します**。

   帯域幅を増やし、問題がまだ発生するかどうかを確認します。

## <a name="source-machine-isnt-listed-in-the-azure-portal"></a>ソース マシンが Azure portal 内に表示されない

Site Recovery を使用してレプリケーションを有効にするソース マシンを選択しようとした場合、次のいずれかの理由から、そのマシンが使用できないことがあります。

*  vCenter で管理されている仮想マシンの 2 つが同じインスタンス UUID を持つ場合は、構成サーバーで最初に検出された方の仮想マシンが Azure portal に表示されます。 この問題を解決するには、インスタンス UUID が同じ仮想マシンが 2 つ存在しないようにします。
*  OVF テンプレートまたは統合設定を使用して構成サーバーを設定するときに追加した vCenter 資格情報が正しいことを確認します。 設定時に追加した資格情報を確認するには、[自動検出のための資格情報の変更に関する記事](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)を参照してください。
*  vCenter にアクセスするために提供されたアクセス許可に、必要なアクセス許可がない場合は、仮想マシンを検出できない場合があります。 「[Prepare an account for automatic discovery (アカウントを自動検出のために準備する)](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)」に記載されているアクセス許可が vCenter ユーザー アカウントに追加されていることを確認します。
*  仮想マシンが Site Recovery で既に保護されている場合、その仮想マシンは、ポータル内で保護対象として選択することはできません。 ポータル内で探している仮想マシンが他のユーザーや別のサブスクリプションでまだ保護されていないことを確認します。

## <a name="protected-virtual-machines-arent-available-in-the-portal"></a>保護された仮想マシンがポータル内で使用できない

Site Recovery でレプリケートされる仮想マシンは、システム内に重複したエントリが存在する場合、Azure portal 内で使用できません。 古いエントリを削除してこの問題を解決する方法については、「[Azure Site Recovery VMware-to-Azure:How to clean up duplicate or stale entries (Azure Site Recovery (VMware から Azure へ): 重複エントリまたは古いエントリのクリーンアップ方法)](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)」を参照してください。

## <a name="next-steps"></a>次の手順

さらにサポートが必要な場合は、[Azure Site Recovery フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)に質問を投稿してください。 弊社のアクティブなコミュニティを通じて、エンジニアがサポートいたします。
