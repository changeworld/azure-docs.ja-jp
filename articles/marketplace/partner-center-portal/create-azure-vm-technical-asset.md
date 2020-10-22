---
title: Azure Marketplace 仮想マシン オファーの技術資産を作成する
description: Azure Marketplace 向けの仮想マシン (VM) オファーの技術資産を作成および構成する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: ec043c97da8056fee4af8f6ead84f4df05c0f304
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2020
ms.locfileid: "92079022"
---
# <a name="create-technical-assets-for-an-azure-marketplace-virtual-machine-offer"></a>Azure Marketplace 仮想マシン オファーの技術資産を作成する

Azure Marketplace に仮想マシン (VM) イメージを発行しているとき、Azure チームはその VM イメージを検証して、その起動可能性、セキュリティ、Azure との互換性を確認します。 高品質テストのいずれかが失敗した場合は、その発行が失敗し、エラーおよび考えられる[修正手順](https://docs.microsoft.com/azure/marketplace/partner-center-portal/vm-certification-issues-solutions)を含むメッセージが表示されます。

この記事では、Azure Marketplace 向けの仮想マシン (VM) オファーの技術資産を作成および構成する方法について説明します。 1 つの VM には､オペレーティング システムの仮想ハード ディスク (VHD) と、オプションの関連するデータ ディスク VHD の 2 つのコンポーネントが含まれます｡

- **オペレーティング システム VHD**:オファーでデプロイされるオペレーティング システムとソリューションが含まれています。 VHD を準備するプロセスは、それが Linux ベースの VM、Windows ベースの VM、カスタム ベースの VM のいずれであるかによって異なります。

- **データ ディスク VHD**:VM 専用の永続的ストレージ。 永続的な情報の格納には、オペレーティング システム VHD (C: ドライブなど) を使用しないでください。

VM イメージには、1 個のオペレーティング システム ディスクと、最大 16 個のデータ ディスクが含まれます。 ディスクが空の場合でも、データ ディスクごとに 1 つの VHD を使用してください。

> [!NOTE]
> 使用するオペレーティング システムに関係なく、ソリューションに必要な最小数のデータ ディスクのみを追加します。 お客様はデプロイ時にイメージの一部であるディスクを削除することはできませんが、デプロイ中またはデプロイ後にいつでもディスクを追加できます。

> [!IMPORTANT]
> プラン内のどの VM イメージにも、同じ数のデータ ディスクが存在する必要があります。

## <a name="fundamental-technical-knowledge"></a>技術的な知識の基礎

こうした資産の設計と構築､テストには時間がかかり､Azure プラットフォームとその構築に使用する技術に関する知識が必要です。 エンジニアリング チームには､ソリューションのドメインばかりでなく､Microsoft の次の技術に関する知識も必要です。

- [Azure Services](https://azure.microsoft.com/services/) に関する基本知識
- [Azure アプリケーションそのものとそのアーキテクチャを](https://azure.microsoft.com/solutions/architecture/)設計する方法
- [Azure 仮想マシン](https://azure.microsoft.com/services/virtual-machines/)、[Azure ストレージ](https://azure.microsoft.com/services/?filter=storage)､[Azure ネットワーク](https://azure.microsoft.com/services/?filter=networking)に関する実用的な知識
- [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) に関する実用的な知識
- [JSON](https://www.json.org/) に関する実用的な知識

### <a name="optional-suggested-tools"></a>オプションの推奨されるツール

VM と VHD の管理に役立つ次のスクリプト環境のいずれかを利用することを検討してください。

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://code.visualstudio.com/)

また、開発環境に次のツールを追加することを検討してください。

- [Azure 記憶域エクスプローラー](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-vm-image-using-an-approved-base"></a>承認済みのベースを使用して VM イメージを作成する

自社で構築したイメージを使用して仮想マシンの技術資産を作成するには、後の「[承認済みのベースを使用して VM イメージを作成する](#create-a-vm-image-using-an-approved-base)」を参照してください。

このセクションでは、リモート デスクトップ プロトコル (RDP) の使用、VM サイズの選択、最新の Windows 更新プログラムのインストール、VHD イメージの一般化など、承認済みのベースを使用するさまざまな側面について説明します。

Azure を使用して、事前に構成された動作保証済みのオペレーティング システムを含む VM を作成するには、この記事のガイダンスに従います。 これがお使いのソリューションと互換性がない場合は、承認済みのオペレーティング システムを使用して、オンプレミスの VM を作成して構成することができます。 その場合は、「[Azure にアップロードする Windows VHD または VHDX を準備する](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image)」で説明されている方法で、アップロードのための構成と準備を行うことができます。

### <a name="select-an-approved-base-image"></a>承認済みのベース イメージを選択する

ベースとして Windows オペレーティング システムまたは Linux のいずれかを選択します。

VM イメージのオペレーティング システム VHD は、Azure 承認の基本イメージに基づいている必要があります (Windows Server、SQL Server など)。

更新を含むイメージを再発行する要求を送信すると、パート番号の検証テスト ケースが失敗する可能性があります。 そのインスタンスでは、イメージは承認されません。

このエラーは、別の発行元に属する基本イメージを使用しており、そのイメージを更新した場合に発生します。 この場合、イメージを発行することは許可されません。

この問題を解決するには、Azure Marketplace から最新のイメージを取得し、そのイメージに対して変更を加えます。 自分のイメージを検索できる承認された基本イメージを表示するには、次を参照してください。

#### <a name="windows"></a>Windows

- Windows Server ([2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)、[2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)、[2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)、[2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)、[2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
- SQL Server 2019 ([Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview)、[Standard](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview)、[Web](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview))
- SQL Server 2014 ([Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)、[Standard](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)、[Web](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance))
- SQL Server 2012 SP2 ([Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)、[Standard](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)、[Web](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance))

#### <a name="linux"></a>Linux

さまざまな承認済みの Linux ディストリビューションが Azure から提供されています。 現在のリストについては、「[Azure で動作保証済みの Linux ディストリビューション](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)」を参照してください。

### <a name="create-vm-on-the-azure-portal"></a>Azure portal で VM を作成する

[Azure portal](https://ms.portal.azure.com/) でベース VM イメージを作成するには、次の手順に従います。

1. VM オファーの公開に使用する Azure サブスクリプションに関連付けられている Microsoft アカウントで [Azure portal](https://ms.portal.azure.com/) にサインインします。
2. 新しいリソース グループを作成して、 **リソース グループ名**、**サブスクリプション**、**リソース グループの場所**を指定します。 詳細については、[リソースの管理](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)に関する記事を参照してください。

    :::image type="content" source="media/vm/create-resource-group.png" alt-text="リソース グループの作成の開始を示します。":::

3. 左側のナビゲーションで **[仮想マシン]** を選択して、仮想マシンの詳細ページを表示します。
4. **[+ 追加]** を選択して、 **[Create a virtual machine experience]\(仮想マシン エクスペリエンスの作成\)** を開きます。
5. ドロップダウン リストからイメージを選択するか、または **[すべてのパブリックおよびプライベート イメージを参照する]** を選択して、使用可能なすべての仮想マシン イメージを検索または参照します。 例:

    :::image type="content" source="media/vm/create-resource-group-example.png" alt-text="リソース グループの作成の開始を示します。":::

6. 次の推奨事項に従ってデプロイする VM のサイズを選択します。
    1. オンプレミスで VHD を開発する場合、サイズは重要ではありません。 小さいサイズの VM を使用することを検討してください。
    2. Azure でイメージを開発する場合は、選択したイメージに推奨される VM サイズを使用することを検討してください。

    :::image type="content" source="media/vm/create-virtual-machine.png" alt-text="リソース グループの作成の開始を示します。":::

7. **ディスク** セクションで、**詳細設定］** セクションを展開し、**マネージド ディスクの使用** オプションを **いいえ** に設定します。

    :::image type="content" source="media/vm/use-managed-disks.png" alt-text="リソース グループの作成の開始を示します。" というメッセージが表示されたら、 **[作成]** を選択します。

Azure で、指定した仮想マシンのプロビジョニングが開始されます。 左側の **[Virtual Machines]** タブを選択して、進捗状況を追跡できます。 作成後に、状態は **[実行中]** に変わります。

### <a name="create-a-generation-2-vm-on-the-azure-portal"></a>Azure portal で第 2 世代 VM を作成する

Azure portal で第 2 世代 (Gen2) VM を作成します。

1. Azure Portal [https://portal.azure.com](https://portal.azure.com/) にサインインします。
2. **[リソースの作成]** を選択します。
3. 左側の Azure Marketplace から **[すべて表示]** を選択します。
4. Gen2 をサポートするイメージを選択します。
5. **［作成］** を選択します
6. **[詳細設定]** タブで、 **[VM generation]\(VM の世代\)** セクションで、 **[Gen 2]** オプションを選択します。
7. **[基本]** タブの **[インスタンスの詳細]** で、 **[サイズ]** に移動し、 **[VM サイズの選択]** ブレードを開きます。
8. [サポートされている Gen 2 VM](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2#generation-2-vm-sizes) とサイズの推奨されるサイズを選択します。
9. [Azure portal での作成フロー](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)を参照して、VM の作成を完了します。

    :::image type="content" source="media/vm/vm-generation.png" alt-text="リソース グループの作成の開始を示します。":::

## <a name="connect-to-your-azure-vm"></a>Azure VM に接続する

このセクションでは、Azure で作成した VM に接続してサインインする方法について説明します。 正常に接続した後、ホスト サーバーにローカルでログインしているかのように VM を操作できます。

### <a name="connect-to-a-windows-based-vm"></a>Windows ベースの VM に接続する

Azure でホストされている Windows ベースの VM への接続には、リモート デスクトップ クライアントを使用します。 ほとんどのバージョンの Windows は、リモート デスクトップ プロトコル (RDP) をネイティブでサポートしています。 その他のオペレーティング システムについては、「[リモート デスクトップ クライアント](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)」でクライアントの詳細を確認できます。

次の記事では、組み込みの Windows RDP サポートを使用して VM に接続する方法について詳しく説明しています。「[Windows が実行されている Azure 仮想マシンに接続してサインオンする方法](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)」。

> [!TIP]
> このプロセス中にセキュリティ警告が表示されることがあります。 たとえば、"The .rdp file is from an unknown publisher" (.rdp ファイルの発行元が不明です) や "Your user credentials cannot be verified." (ユーザーの資格情報を確認できません) などの警告です。 これらの警告は無視しても問題ありません。

### <a name="connect-to-a-linux-based-vm"></a>Linux ベースの VM に接続する

Linux ベースの VM に接続するには、Secure Shell プロトコル (SSH) クライアントが必要です。 次の手順では、無料の [PuTTY](https://www.ssh.com/ssh/putty/) SSH ターミナルを使用します。

1. [Azure ポータル](https://ms.portal.azure.com/)にアクセスします。
2. [仮想マシン] を検索して選択します。
3. 接続先の VM を選択します。
4. その VM がまだ実行されていない場合は、それを起動します。
5. VM の名前を選択して、その [概要] ページを開きます。
6. VM のパブリック IP アドレスと DNS 名をメモします (これらの値が設定されていない場合は、[ネットワーク インターフェイスを作成する](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)必要があります)。
7. PuTTY アプリケーションを開きます。
8. [PuTTY Configuration]\(PuTTY 構成\) ダイアログで、VM の IP アドレスまたは DNS 名を入力します。

    :::image type="content" source="media/vm/putty-configuration.png" alt-text="リソース グループの作成の開始を示します。":::

9. **[Open]\(開く\)** を選択して PuTTY ターミナルを開きます。
10. プロンプトが表示されたら、Linux VM のアカウント名とパスワードを入力します。

## <a name="configure-the-virtual-machine"></a>仮想マシンを構成する

このセクションでは、Azure VM のサイズ変更、更新、一般化を行う方法について説明します。 これらの手順は、Azure Marketplace にデプロイする VM を準備するために必要です。

### <a name="sizing-the-vhds"></a>VHD のサイズ変更

OS ディスク サイズの制限には、次のルールが適用されます。 要求を送信する場合は、OS ディスク サイズが Linux または Windows の制限内にあることを確認してください。

| OS | 推奨される VHD サイズ |
| --- | --- |
| Linux | 30 ～ 1023 GB |
| Windows | 30 ～ 250 GB |

VM では基になるオペレーティング システムへのアクセスが許可されるため、VHD のサイズが VHD に対して十分に大きいことを確認してください。 ディスクはダウンタイムなしでは拡張できないため、30 ～ 50&nbsp;GB のディスク サイズを使用してください。

| VHD サイズ | 実際の占有サイズ | 解決策 |
| --- | --- | --- |
| >500 TB | 該当なし | 例外の承認については、サポート チームにお問い合わせください。 |
| 250-500 TB | BLOB サイズとは >200 GB の差 | 例外の承認については、サポート チームにお問い合わせください。 |

### <a name="install-the-most-current-updates"></a>最新の更新プログラムをインストールする

オペレーティング システム VM のベース イメージには、発行日までの最新の更新プログラムが含まれている必要があります。 作成したオペレーティング システム VHD を公開する前に、最新のすべてのセキュリティとメンテナンスの修正プログラムを使用して、OS およびすべてのインストール済みサービスを更新するようにしてください。

- Windows Server では、更新プログラムの確認コマンドを実行します。
- Linux ディストリビューションの場合、更新プログラムは通常、コマンド ライン ツールまたはグラフィカル ユーティリティによってダウンロードおよびインストールされます。 たとえば Ubuntu Linux では、OS を更新するための [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) コマンドおよび [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) ツールが提供されています。

#### <a name="perform-additional-security-checks"></a>追加のセキュリティ チェックを実行する

Azure Marketplace のソリューション イメージのセキュリティは、高いレベルに維持してください。 セキュリティ構成と手順のチェックリストについては、「[Azure Marketplace イメージのセキュリティに関する推奨事項](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)」を参照してください。 これらの推奨事項の一部は Linux ベースのイメージに固有ですが、ほとんどの事項はすべての VM イメージに当てはまります。

#### <a name="perform-custom-configuration-and-scheduled-tasks"></a>カスタム構成とスケジュールされたタスクを実行する

追加の構成が必要な場合は、起動時に実行されるスケジュールされたタスクを使用して、デプロイされた後の VM に最終的な変更を加えます。 また、以下の点についても検討してください。

- 一度だけ実行されるタスクの場合、タスクは正常に完了した後に自身を削除する必要があります。
- 構成は、C と D 以外のドライブに依存しないようにする必要があります。これは、存在が必ず保証されるドライブがこれら 2 つのみのためです (ドライブ C はオペレーティング システム ディスク、ドライブ D は一時ローカル ディスクです)。

拡張機能の詳細については、「[Linux 用の仮想マシンの拡張機能とその機能](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)」を参照してください。

## <a name="generalize-the-image"></a>イメージを汎用化する

Azure Marketplace のすべてのイメージは汎用的な方法で再利用できる必要があります。 これを実現するには、オペレーティング システム VHD を一般化する必要があります。これは、インスタンス固有の識別子とソフトウェア ドライバーを VM からすべて削除する操作です。

### <a name="for-windows"></a>Windows の場合

Windows OS ディスクは、[sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) ツールを使用して一般化されます。 後で OS を更新または再構成する場合は、sysprep を再び実行する必要があります。

> [!WARNING]
> 更新プログラムが自動的に実行される可能性があるため、sysprep を実行した後、デプロイされるまでは VM をオフにしてください。 このシャットダウンで、以降の更新プログラムによるオペレーティング システムまたはインストール済みサービスへのインスタンス固有の変更が行われなくなります。 sysprep の実行に関する詳細については、[VHD を一般化する手順](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)に関する記事を参照してください。

### <a name="for-linux"></a>Linux の場合

次のプロセスでは、Linux VM を一般化して別の VM として再デプロイします。 詳細については、「[仮想マシンまたは VHD のイメージを作成する方法](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)」を参照してください。 「キャプチャしたイメージから VM を作成する」というセクションに達したら止めることができます。

1. Azure Linux エージェントを削除する

    1. SSH クライアントを使用して Linux VM に接続します。
    2. SSH ウィンドウで、コマンド `sudo waagent –deprovision+user` を入力します。
    3. 「Y」と入力して続行します (前のコマンドに -force パラメーターを追加すると、この確認手順を省略できます)。
    4. コマンドが完了したら、「Exit」と入力して、SSH クライアントを閉じます。

2. 仮想マシンの停止

    1. Azure portal でリソース グループ (RG) を選択し、VM の割り当てを解除します。
    2. これで VHD が一般化されたため、この VHD を使用して新しい VM を作成できます。

## <a name="next-steps"></a>次のステップ

- Azure ベースの新しい VHD の作成で問題が発生した場合は、「[VHD 作成における一般的な問題](common-issues-during-vhd-creation.md)」を参照してください。
- そうでない場合は、[VHD からデプロイされた仮想マシン (VM) のテスト](azure-vm-image-certification.md)に関するページに、Certification Test Tool for Azure Certified ツールを取得する場所や、それを使用して VM イメージを認定する方法など、Azure Marketplace 認定のために VM イメージをテストして送信する方法が説明されています。
