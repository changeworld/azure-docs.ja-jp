---
title: Azure Site Recovery を使用して VMware VM と物理サーバーを Azure にディザスター リカバリーする場合のレプリケーションの問題のトラブルシューティング | Microsoft Docs
description: この記事では、Azure Site Recovery を使用して VMware VM と物理サーバーを Azure にディザスター リカバリーする際の一般的なレプリケーションの問題のトラブルシューティングについて説明します。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/7/2019
ms.author: mayg
ms.openlocfilehash: 71c07d93d75ee372a50ec4ff5fc81e92926d329b
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964775"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>VMware VM および物理サーバーのレプリケーション問題のトラブルシューティング

VMware 仮想マシンまたは物理サーバーを Azure Site Recovery を使用して保護する際に、特定のエラー メッセージが表示される場合があります。 この記事では、[Site Recovery](site-recovery-overview.md) を使用してオンプレミスの VMware VM および物理サーバーを Azure にレプリケートする際に起きることがある一般的な問題について説明します。

## <a name="monitor-process-server-health-to-avoid-replication-issues"></a>レプリケーションの問題を回避するためにプロセス サーバーの正常性を監視する

関連付けられたソース マシンに対してレプリケーションが進行していることを確認するために、ポータル上でプロセス サーバー (PS) の正常性を監視することをお勧めします。 コンテナーで、[管理] > [Site Recovery インフラストラクチャ] > [構成サーバー] に移動します。 [構成サーバー] ブレードで、[関連付けられたサーバー] の [プロセス サーバー] をクリックします。 [プロセス サーバー] ブレードが開き、正常性の統計情報が表示されます。 追跡できるのは、CPU 使用率、メモリ使用量、レプリケーションに必要な PS サービスの状態、証明書の有効期限日、および使用可能な空き領域です。 統計の状態はすべて緑色になっている必要があります。 

**メモリ使用量と CPU 使用率が 70% 未満で、25% 以上の空き領域がある状態に維持することを推奨します**。 空き領域とは、プロセス サーバー内のキャッシュ ディスク領域のことを指します。これは、ソース マシンからのレプリケーション データを Azure にアップロードする前に、それらのデータを格納する目的で使用されます。 空き領域が 20% 以下になった場合は、関連付けられたすべてのソース マシンに対して、レプリケーションが調整されます。 [容量のガイダンス](./site-recovery-plan-capacity-vmware.md#capacity-considerations)に従って、ソース マシンのレプリケートに必要な構成を把握してください。

PS コンピューターで、次のサービスが実行されていることを確認してください。 実行されていないサービスがあれば、そのサービスを起動または再起動します。

**組み込みのプロセス サーバー**

* cxprocessserver
* InMage PushInstall
* ログ アップロード サービス (LogUpload)
* InMage Scout アプリケーション サービス
* Microsoft Azure Recovery Services エージェント (obengine)
* InMage Scout VX Agent - Sentinel/Outpost (svagents)
* tmansvc
* World Wide Web 発行サービス (W3SVC)
* MySQL
* Microsoft Azure Site Recovery サービス (dra)

**スケールアウト プロセス サーバー**

* cxprocessserver
* InMage PushInstall
* ログ アップロード サービス (LogUpload)
* InMage Scout アプリケーション サービス
* Microsoft Azure Recovery Services エージェント (obengine)
* InMage Scout VX Agent - Sentinel/Outpost (svagents)
* tmansvc

**フェールバック用の Azure 内プロセス サーバー**

* cxprocessserver
* InMage PushInstall
* ログ アップロード サービス (LogUpload)

すべてのサービスの StartType が、**[自動] または [自動 (遅延開始)]** に設定されていることを確認します。 Microsoft Azure Recovery Services エージェント (obengine) サービスについては、StartType を上記の設定にする必要はありません。

## <a name="initial-replication-issues"></a>初期レプリケーションの問題

初期レプリケーションのエラーの多くは、ソース サーバーとプロセス サーバー間、またはプロセス サーバーと Azure 間の接続の問題が原因で発生します。 ほとんどの場合、これらの問題は、次のセクションの手順を実行することにより、トラブルシューティングできます。

### <a name="check-the-source-machine"></a>ソース マシンの確認

ソース マシンの確認方法を次に示します。

*  Telnet を使用して、HTTPS ポート経由でプロセス サーバーを ping します。これを行うには、ソース サーバー上のコマンド ラインで、以下のコマンドを実行します。 HTTPS ポート 9443 は、レプリケーション トラフィックを送受信するためにプロセス サーバーによって使用される既定のポートです。 このポートは、登録時に変更することもできます。 次のコマンドでは、ネットワーク接続の問題と、ファイアウォール ポートをブロックする問題がないかが確認されます。


   `telnet <process server IP address> <port>`


   > [!NOTE]
   > 接続をテストする際は、Telnet を使用します。 `ping` は、使用しないでください。 Telnet がインストールされていない場合は、「[Install Telnet Client (Telnet クライアントのインストール)](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)」に記載されている手順を実行してください。

   Telnet が PS ポートに正常に接続できる場合は、空白の画面が表示されます。

   プロセス サーバーに接続できない場合は、プロセス サーバー上の受信ポート 9443 を許可します。 たとえば、お使いのネットワークに境界ネットワークやスクリーンド サブネットがある場合は、プロセス サーバー上で受信ポート 9443 を許可することが必要になる場合があります。 この後、問題が引き続き発生するかどうかを確認します。

*  Telnet が正常であるにもかかわらず PS にアクセスできないことがソース マシンによって報告された場合は、ソース マシン上で Web ブラウザーを開き、アドレス https://<PS_IP>:<PS_Data_Port>/ にアクセスできるかどうか確認してください。

    このアドレスにアクセスすると、通常は HTTPS 証明書エラーが返されます。 証明書のエラーを無視して続行すると、"400 – Bad Request" が発生し、サーバーがブラウザーの要求を処理できなくなって、サーバーへの標準 HTTPS 接続が正常に機能しなくなります。

    これについて問題が発生した場合は、ブラウザーのエラー メッセージで詳しいガイダンスを確認してください。 たとえば、プロキシ認証が正しくない場合は、プロキシ サーバーから "407 – プロキシの認証が必要です" が返され、エラー メッセージ内に必要な操作が示されます。 

*  ソース VM の次のログを見て、ネットワーク アップロードに関連するエラーを確認してください。

       C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

### <a name="check-the-process-server"></a>プロセス サーバーの確認

プロセス サーバーの確認方法を次に示します。

> [!NOTE]
> プロセス サーバーには静的な IPv4 アドレスが必要です。NAT IP を構成しないようにしてください。

* **ソース マシンとプロセス サーバーの間のネットワーク接続を確認する**
1. ソース マシンからの Telnet 接続が可能であるにもかかわらず、ソースから PS にアクセスすることができない場合は、ソース VM で cxpsclient ツールを実行して、ソース VM から cxprocessserver へのエンド ツー エンド接続をチェックしてください。

       <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>

    PS で生成されたログを次のディレクトリから参照し、対応するエラーの詳細を確認してください。

       C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err
       and
       C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer
2. PS からのハートビートがない場合は、PS で次のログを確認してください。

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

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

* **2 つの仮想マシンのインスタンス UUID が同じ**:vCenter で管理されている仮想マシンの 2 つが同じインスタンス UUID を持つ場合は、構成サーバーで最初に検出された方の仮想マシンが Azure portal に表示されます。 この問題を解決するには、インスタンス UUID が同じ仮想マシンが 2 つ存在しないようにします。 このシナリオは、バックアップ VM がアクティブになったときに検出レコード内に記録されるインスタンスでよく発生します。 解決するには、「[Azure Site Recovery VMware-to-Azure:How to clean up duplicate or stale entries (Azure Site Recovery (VMware から Azure へ): 重複エントリまたは古いエントリのクリーンアップ方法)](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)」を参照してください。
* **vCenter ユーザーの資格情報が正しくない**:OVF テンプレートまたは統合設定を使用して構成サーバーを設定するときに追加した vCenter 資格情報が正しいことを確認します。 設定時に追加した資格情報を確認するには、[自動検出のための資格情報の変更に関する記事](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)を参照してください。
* **vCenter の特権が不足している**:vCenter にアクセスするために提供されたアクセス許可に、必要なアクセス許可がない場合は、仮想マシンを検出できない場合があります。 「[Prepare an account for automatic discovery (アカウントを自動検出のために準備する)](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)」に記載されているアクセス許可が vCenter ユーザー アカウントに追加されていることを確認します。
* **Azure Site Recovery の管理サーバー**:仮想マシンが、構成サーバー/スケールアウト プロセス サーバー/マスター ターゲット サーバー のいずれかまたは複数のロールを持つ管理サーバーとして使用されている場合、ポータルからその仮想マシンを選択することはできません。 管理サーバーはレプリケートできません。
* **Azure Site Recovery サービスによって既に保護されている、またはフェールオーバーされている**:仮想マシンが Site Recovery によって既に保護されているかフェールオーバーされている場合、その仮想マシンは、ポータル内で保護対象として選択することはできません。 ポータル内で探している仮想マシンが他のユーザーや別のサブスクリプションでまだ保護されていないことを確認します。
* **vCenter に接続されていない**:vCenter が接続状態であることを確認します。 確認するには、[Recovery Services コンテナー]、[Site Recovery インフラストラクチャ]、[構成サーバー] の順に移動し、該当する構成サーバーをクリックします。右側にブレードが開き、関連付けられているサーバーの詳細が表示されます。 vCenter が接続されているがどうかをチェックします。 [未接続] 状態の場合は、その問題を解決した後、ポータルで[構成サーバーを更新](vmware-azure-manage-configuration-server.md#refresh-configuration-server)します。 その後、仮想マシンがポータルに表示されます。
* **ESXi の電源がオフになっている**:仮想マシンが存在する ESXi ホストの電源がオフ状態の場合、 Azure portal にその仮想マシンは表示されないか、Azure portal で選択することはできません。 ESXi ホストの電源をオンにし、ポータルで[構成サーバーを更新](vmware-azure-manage-configuration-server.md#refresh-configuration-server)します。 その後、仮想マシンがポータルに表示されます。
* **保留中の再起動**:仮想マシンの保留中の再起動がある場合、Azure portal でそのマシンを選択することはできません。 保留中の再起動アクティビティが完了していることを確認し、[構成サーバーを更新](vmware-azure-manage-configuration-server.md#refresh-configuration-server)します。 その後、仮想マシンがポータルに表示されます。
* **IP が見つからない**:仮想マシンに関連付けられている有効な IP アドレスがない場合、Azure portal でそのマシンを選択することはできません。 有効な IP アドレスが仮想マシンに割り当てられていることを確認し、[構成サーバーを更新](vmware-azure-manage-configuration-server.md#refresh-configuration-server)します。 その後、仮想マシンがポータルに表示されます。

## <a name="protected-virtual-machines-are-greyed-out-in-the-portal"></a>保護された仮想マシンがポータルで灰色表示される

Site Recovery でレプリケートされる仮想マシンは、システム内に重複したエントリが存在する場合、Azure portal 内で使用できません。 古いエントリを削除してこの問題を解決する方法については、「[Azure Site Recovery VMware-to-Azure:How to clean up duplicate or stale entries (Azure Site Recovery (VMware から Azure へ): 重複エントリまたは古いエントリのクリーンアップ方法)](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)」を参照してください。

## <a name="next-steps"></a>次の手順

さらにサポートが必要な場合は、[Azure Site Recovery フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)に質問を投稿してください。 弊社のアクティブなコミュニティを通じて、エンジニアがサポートいたします。
