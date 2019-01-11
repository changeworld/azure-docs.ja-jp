---
title: Azure Site Recovery を使用して VMware VM と物理サーバーを Azure にディザスター リカバリーする場合のレプリケーションの問題のトラブルシューティング | Microsoft Docs
description: この記事では、Azure Site Recovery を使用して VMware VM と物理サーバーを Azure にディザスター リカバリーする際の一般的なレプリケーションの問題のトラブルシューティング情報を提供します。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: 1c37b764b47856d3a369228d3f224f2a464029bb
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790658"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>VMware VM および物理サーバーのレプリケーション問題のトラブルシューティング

VMware 仮想マシンまたは物理サーバーを Azure Site Recovery を使用して保護する際に、特定のエラー メッセージが表示される場合があります。 この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用してオンプレミスの VMware VM や物理サーバーをレプリケートする際に起こる、一般的な問題について説明します。


## <a name="initial-replication-issues"></a>初期レプリケーションの問題

多くの場合、サポートで検出される初期レプリケーション エラーは、ソース サーバーとプロセス サーバー間またはプロセス サーバーと Azure 間の接続問題によるものです。 ほとんどの場合、以下に示す手順に従うことで、これらの問題をトラブルシューティングできます。

### <a name="verify-the-source-machine"></a>ソース マシンを確認する
* ソース サーバー マシンのコマンド ラインから、Telnet を使用して、次のように https ポート (既定値は 9443) を指定してプロセス サーバーを ping し、ネットワーク接続の問題であるか、ファイアウォールのポート ブロックの問題であるかを確認します。

    `telnet <PS IP address> <port>`
> [!NOTE]
    > 接続をテストする場合は、PING ではなく、Telnet を使用してください。  Telnet がインストールされていない場合は、[こちら](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)に示されている手順に従ってください。

接続できない場合は、プロセス サーバーの受信ポート 9443 を許可し、問題がまだ発生するかどうかを確認します。 場合によっては、プロセス サーバーが DMZ の背後にあることが原因で、この問題が発生します。

* サービス (`InMage Scout VX Agent – Sentinel/OutpostStart`) の状態を調べ、実行されていないかどうかを確認し、問題がまだ発生するかどうかを確認します。   

### <a name="verify-the-process-server"></a>プロセス サーバーを確認する

* **プロセス サーバーが Azure にデータをアクティブにプッシュしているかどうかを確認する**

プロセス サーバー マシンから、タスク マネージャーを開きます (Ctrl + Shift + Esc キーを押します)。 [パフォーマンス] タブに移動し、[リソース モニターを開く] リンクをクリックします。 リソース マネージャーから、[ネットワーク] タブに移動します。[ネットワーク活動のプロセス] で cbengine.exe がアクティブに大量 (MB 単位) のデータを送信しているかどうかを確認します。

![レプリケーションを有効にする](./media/vmware-azure-troubleshoot-replication/cbengine.png)

送信していない場合は、次の手順に従います。

* **プロセス サーバーが Azure BLOB に接続できることを確認する**:cbengine.exe を選択し、[TCP 接続] を表示して、プロセス サーバーから Azure Storage の BLOB URL に接続されているかどうかを確認します。

![レプリケーションを有効にする](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

接続されていない場合は、[コントロール パネル]、[サービス] の順に移動し、以下のサービスが稼働しているかどうかを確認します。

     * cxprocessserver
     * InMage Scout VX Agent – Sentinel/Outpost
     * Microsoft Azure Recovery Services Agent
     * Microsoft Azure Site Recovery Service
     * tmansvc
     *
実行されていないサービスを開始 (再始動) し、問題がまだ発生するかどうかを確認します。

* **プロセス サーバーがポート 443 を使用して Azure のパブリック IP アドレスに接続できることを確認する**

`%programfiles%\Microsoft Azure Recovery Services Agent\Temp` から最新の CBEngineCurr.errlog を開き、443 と失敗した接続試行を探します。

![レプリケーションを有効にする](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

問題がある場合は、プロセス サーバーのコマンドラインから telnet を使用し、ポート 443 を使用して CBEngineCurr.currLog で見つかった Azure のパブリック IP アドレス (上の画像ではマスクされている) を ping します。

      telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443
接続できない場合は、次の手順に従って、アクセスの問題がファイアウォールまたはプロキシによるものであるかを確認します。


* **プロセス サーバーの IP アドレスベースのファイアウォールがアクセスをブロックしていないかどうかを確認する**:サーバーで IP アドレスベースのファイアウォール ルールを使用している場合は、[こちら](https://www.microsoft.com/download/details.aspx?id=41653)から Microsoft Azure データセンターの IP 範囲の完全なリストをダウンロードしてファイアウォール構成に追加し、Azure (および HTTPS (443) ポート) との通信が許可されていることを確認します。  ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します (Access Control と ID 管理に使用されます)。

* **プロセス サーバーの URL ベースのファイアウォールがアクセスをブロックしていないかどうかを確認する**:サーバーで URL ベースのファイアウォール ルールを使用する場合は、以下の URL がファイアウォール構成に追加されていることを確認します。

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

* **プロセス サーバーのプロキシ設定がアクセスをブロックしていないかどうかを確認**:   プロキシ サーバーを使用している場合は、DNS サーバーでプロキシ サーバー名が解決されていることを確認します。
構成サーバーのセットアップ時に指定した内容を確認するには、 レジストリ キーに移動します。

    `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings`

ここで、データを送信するために Azure Site Recovery エージェントで同じ設定が使用されていることを確認します。
Microsoft Azure Backup を検索します。

これを開き、[アクション]、[プロパティの変更] の順にクリックします。 [プロキシの構成] タブに、プロキシ アドレスが表示されます。これは、レジストリ設定で表示されるものと同じです。 同じでない場合は、同じアドレスに変更します。


* **プロセス サーバーでスロットルの帯域幅が制限されていないかどうかを確認する**:帯域幅を増やし、問題がまだ発生するかどうかを確認します。

## <a name="source-machine-to-be-protected-through-site-recovery-is-not-listed-on-azure-portal"></a>Site Recovery で保護するソース コンピューターが Azure Portal に表示されない

Azure Site Recovery でレプリケーションを有効にするソース コンピューターを選択しようとすると、次の理由から、そのコンピューターの使用を続行できないことがあります。

* 同じインスタンス UUID を持つ vCenter の下に 2 つの仮想マシンが存在する場合は、構成サーバーによって検出された最初の仮想マシンがポータルに表示されます。 解決するには、2 つの仮想マシンが同じインスタンス UUID を持たないようにしてください。
* OVF テンプレートまたは統合セットアップを使用した構成のセットアップ中に、正しい vCenter 資格情報を追加したことを確認してください。 追加された資格情報を確認するには、[ここ](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)で共有されているガイドラインを参照してください。
* vCenter にアクセスするために指定されたアクセス許可に十分な特権がない場合は、仮想マシンの検出に失敗する可能性があります。 [ここ](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)で指定されたアクセス許可が vCenter ユーザー アカウントに追加されていることを確認してください。
* 仮想マシンが既に Site Recovery で保護されている場合は、その仮想マシンを保護のために使用することはできません。 ポータル上で探している仮想マシンが他のどのユーザーによっても、あるいは他のどのサブスクリプションのもとでもまだ保護されていないことを確認してください。

## <a name="protected-virtual-machines-are-greyed-out-in-the-portal"></a>保護された仮想マシンがポータルで灰色表示される

Site Recovery でレプリケートされる仮想マシンは、システム内に重複したエントリが存在すると灰色表示されます。 古いエントリを削除して問題を解決するには、[ここ](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)に掲載されているガイドラインを参照してください。

## <a name="next-steps"></a>次の手順
さらに支援が必要な場合は、[Azure Site Recovery フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)にクエリを投稿してください。 弊社のアクティブなコミュニティを通じて、エンジニアがサポートいたします。
