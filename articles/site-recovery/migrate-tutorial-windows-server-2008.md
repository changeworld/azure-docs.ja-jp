---
title: Azure Site Recovery を使用してオンプレミスの Windows Server 2008 サーバーを Azure に移行する | Microsoft Docs
description: この記事では、Azure Site Recovery を使用して、オンプレミスの Windows Server 2008 マシンを Azure に移行する方法について説明します。
services: site-recovery
documentationcenter: ''
author: bsiva
manager: abhemraj
editor: raynew
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/23/2018
ms.author: bsiva
ms.openlocfilehash: 6e5946f3f9dcf1c7d941054c844adcf683b485ab
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308645"
---
# <a name="migrate-servers-running-windows-server-2008-to-azure"></a>Windows Server 2008 を実行しているサーバーを Azure に移行する

このチュートリアルでは、Azure Site Recovery を使用して Windows Server 2008 または 2008 R2 を実行しているオンプレミスのサーバーを Azure に移行する方法を示します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 移行対象のオンプレミス環境を準備する
> * ターゲット環境をセットアップする
> * レプリケーション ポリシーを設定する
> * レプリケーションを有効にする
> * テスト移行を実行して、すべて想定どおりに動作していることを確認する
> * Azure にフェールオーバーして、移行を完了する

「制限事項と既知の問題」セクションでは、いくつかの制限事項と、Windows Server 2008 マシンを Azure に移行する際に発生する可能性がある既知の問題の回避策を一覧に示しています。 


## <a name="supported-operating-systems-and-environments"></a>サポートされているオペレーティング システムと環境


|オペレーティング システム  | オンプレミス環境  |
|---------|---------|
|Windows Server 2008 SP2 - 32 ビットおよび 64 ビット(IA-32 および x86-64)</br>- Standard</br>- Enterprise</br>- Datacenter   |     VMware VM、Hyper-V VM、および物理サーバー    |
|Windows Server 2008 R2 SP1 - 64 bit</br>- Standard</br>- Enterprise</br>- Datacenter     |     VMware VM、Hyper-V VM、および物理サーバー|

> [!WARNING]
> - Server Core を実行しているサーバーの移行は、サポートされていません。
> - 移行前に、最新の Service Pack と Windows 更新プログラムがインストールされていることを確認してください。


## <a name="prerequisites"></a>前提条件

開始する前に、[VMware と物理サーバーの移行](vmware-azure-architecture.md)や [Hyper-V 仮想マシンの移行](hyper-v-azure-architecture.md)のための Azure Site Recovery アーキテクチャを確認することをお勧めします。 

Windows Server 2008 または Windows Server 2008 R2 を実行している Hyper-V を移行するには、[オンプレミス マシンを Azure に移行する](migrate-tutorial-on-premises-azure.md)チュートリアルの手順に従います。

このチュートリアルの手順以外の部分では、オンプレミス VMware 仮想マシンと Windows Server 2008 または 2008 R2 を実行している物理サーバーを移行する方法を説明しています。


## <a name="limitations-and-known-issues"></a>制限事項と既知の問題

- Windows Server 2008 SP2 サーバーの移行に使用される構成サーバー、追加のプロセス サーバー、およびモビリティ サービスは、Azure Site Recovery ソフトウェアのバージョン 9.18.0.1 を実行している必要があります。 バージョン 9.18.0.1 の構成サーバーとプロセス サーバーの統合セットアップは、[https://aka.ms/asr-w2k8-migration-setup](https://aka.ms/asr-w2k8-migration-setup) からダウンロードできます。

- 既存の構成サーバーまたはプロセスサーバーは、Windows Server 2008 SP2 を実行しているサーバーの移行には使用できません。 新しい構成サーバーは、Azure Site Recovery ソフトウェアのバージョン 9.18.0.1 を使ってプロビジョニングする必要があります。 この構成サーバーは、Windows サーバーの Azure への移行だけに使用する必要があります。

- Windows Server 2008 SP2 を実行しているサーバーのレプリケーションでは、アプリケーション整合性復旧ポイントおよびマルチ VM 整合性機能はサポートされません。 Windows Server 2008 SP2 サーバーは、クラッシュ整合性復旧ポイントに移行する必要があります。 クラッシュ整合性復旧ポイントは既定で、5 分ごとに生成されます。 構成済みのアプリケーション整合性スナップショットの頻度を備えたレプリケーション ポリシーを使用すると、アプリケーション整合性復旧ポイントの不足が原因で、レプリケーションの正常性が致命的な状態に変わります。 誤検知を避けるために、レプリケーション ポリシーのアプリケーション整合性スナップショットの頻度を "オフ" に設定します。

- 移行されるサーバーは、モビリティ サービスが動作するように .NET Framework 3.5 Service Pack 1 を備えている必要があります。


- サーバーに動的ディスクがある場合、特定の構成では、フェイルオーバーされたサーバー上のこれらのディスクはオフラインとしてマークされ、形式の異なるディスクとして表示されることが確認できます。 また、複数の動的ディスクにわたるミラー ボリュームのミラー化設定ステータスは、"Failed redundancy"\(冗長に失敗しました\) とマークされます。 これらのディスクを手動でインポートして再アクティブ化することで、diskmgmt.msc からこの問題を修正できます。

- 移行されるサーバーは、vmstorfl.sys ドライバーを備えている必要があります。 移行されるサーバーにドライバーが存在しない場合、フェールオーバーに失敗することがあります。 
  > [!TIP]
  >ドライバーが "C:\Windows\system32\drivers\vmstorfl.sys" にあるかどうかを確認します。 ドライバーが見つからない場合は、適切な場所にダミー ファイルを作成することで、問題を回避できます。 
  >
  > コマンド プロンプトを開き ([実行] > [cmd])、"copy nul c:\Windows\system32\drivers\vmstorfl.sys" を実行します。

- フェールオーバーされた後、または Azure へのフェール オーバーをテストした後に、32 ビットのオペレーティング システムを実行している Windows Server 2008 SP2 サーバーに対して、すぐに RDP できない場合があります。 Azure Portal からフェールオーバーした仮想マシンを再起動し、接続を再試行します。 まだ接続できない場合は、リモート デスクトップ接続を許可するようにサーバーが構成されているかどうかをチェックし、接続をブロックするファイアウォール ルールまたはネットワーク セキュリティ グループがないことを確認します。 
  > [!TIP]
  > サーバーを移行する前に、テスト フェールオーバーを行うことを強くお勧めします。 移行中の各サーバー上で少なくとも 1 回、正常にテスト フェールオーバーを実行したことを確認します。 テスト フェールオーバーの一環として、テスト フェールオーバーのマシンに接続して、期待通りに動作することを確認します。
  >
  >テスト フェールオーバー操作は中断がなく、選択した分離ネットワークに仮想マシンを作成することで、移行のテストを支援します。 フェールオーバー操作とは異なり、テスト フェールオーバー操作中は、データ レプリケーションは進行を続けます。 移行の準備が整う前に、好きな回数だけ、テスト フェールオーバーを実行できます。 
  >
  >


## <a name="getting-started"></a>使用の開始

次のタスクを実行して、Azure サブスクリプションおよびオンプレミス VMware/物理環境を準備します。

1. [Azure を準備する](tutorial-prepare-azure.md)
2. オンプレミスの [VMware](vmware-azure-tutorial-prepare-on-premises.md) を準備する


## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

1. [Azure Portal](https://portal.azure.com) > **Recovery Services** にサインインします。
2. **[リソースの作成]** > **[監視 + 管理]** > **[Backup and Site Recovery]** の順にクリックします。
3. **[名前]** に、フレンドリ名 **W2K8-migration** を指定します。 複数のサブスクリプションがある場合は、適切なものを選択します。
4. リソース グループ **w2k8migrate** を作成します。
5. Azure リージョンを指定します。 サポートされているリージョンを確認するには、[Azure Site Recovery の価格の詳細](https://azure.microsoft.com/pricing/details/site-recovery/)に関するページでご利用可能な地域をご覧ください。
6. ダッシュボードからコンテナーにすばやくアクセスするには、**[ダッシュボードにピン留めする]**、**[作成]** の順にクリックします。

   ![新しいコンテナー](media/migrate-tutorial-windows-server-2008/migrate-windows-server-2008-vault.png)

新しいコンテナーは、**[ダッシュボード]** の **[すべてのリソース]** と、メインの **[Recovery Services コンテナー]** ページに追加されます。


## <a name="prepare-your-on-premises-environment-for-migration"></a>移行対象のオンプレミス環境を準備する

- [https://aka.ms/asr-w2k8-migration-setup](https://aka.ms/asr-w2k8-migration-setup) から構成サーバーのインストーラー (統合セットアップ) をダウンロードする
- 次の手順に従って、前の手順でダウンロードされたインストーラー ファイルを使用して、ソース環境を設定します。

> [!IMPORTANT]
> - 構成サーバーをインストールして登録するために、上記の最初の手順でダウンロードした、セットアップ ファイルを使用していることを確認してください。 Azure Portal からセットアップ ファイルをダウンロードしないでください。 [https://aka.ms/asr-w2k8-migration-setup](https://aka.ms/asr-w2k8-migration-setup) で使用可能なセットアップ ファイルは、Windows Server 2008 の移行をサポートする唯一のバージョンです。
>
> - 既存の構成サーバーを使用して、Windows Server 2008 を実行しているマシンを移行することはできません。 上記のリンクを使用して、新しい構成サーバーをセットアップする必要があります。
>
> - 次の手順に従って、構成サーバーをインストールします。 統合セットアップを直接実行して、GUI ベースのインストール手順を使用しないでください。 これを行うと、インターネット接続がないことを示す誤ったエラーでインストールの試行が失敗します。

 
1) ポータルからコンテナーの資格情報ファイルをダウンロード: Azure portal で、前の手順で作成された Recovery Services コンテナーを選択します。 コンテナー ページのメニューから、**[Site Recovery インフラストラクチャ]** > **[構成サーバー]** を選択します。 **[+サーバー]** をクリックします。 表示されたページのドロップダウン フォームから、*[Configuration Server for Physical]\(物理用構成サーバー\)* を選択します。 手順 4 でダウンロード ボタンをクリックして、コンテナーの資格情報ファイルをダウンロードします。

 ![コンテナー登録キーをダウンロードする](media/migrate-tutorial-windows-server-2008/download-vault-credentials.png) 

2) 前の手順でダウンロードしたコンテナーの資格情報ファイルと、以前にダウンロードした統合セットアップ ファイルを、構成サーバー マシン (構成サーバー ソフトウェアをインストールする Windows Server 2012 R2 または Windows Server 2016 コンピューター) のデスクトップにコピーします。

3) 構成サーバーにインターネット接続があることと、コンピューターのシステム クロックとタイム ゾーンの設定が正しく構成されていることを確認します。 [MySQL 5.7](https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi) インストーラーをダウンロードして、*C:\Temp\ASRSetup* (ディレクトリが存在しない場合は作成する) に配置します。 

4) 次の行を使用して MySQL の資格情報ファイルを作成し、それをデスクトップ上の **C:\Users\Administrator\MySQLCreds.txt** に配置します。 次の "Password~1" を適切で強力なパスワードに置き換えます。

```
[MySQLCredentials]
MySQLRootPassword = "Password~1"
MySQLUserPassword = "Password~1"
```

5) 次のコマンドを実行して、デスクトップにダウンロードした統合セットアップ ファイルのコンテンツを抽出します。

```
cd C:\Users\Administrator\Desktop

MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Users\Administrator\Desktop\9.18
```
  
6) 次のコマンドを実行して、抽出したコンテンツを使用して、構成サーバー ソフトウェアをインストールします。

```
cd C:\Users\Administrator\Desktop\9.18.1

UnifiedSetup.exe /AcceptThirdpartyEULA /ServerMode CS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /MySQLCredsFilePath "C:\Users\Administrator\Desktop\MySQLCreds.txt" /VaultCredsFilePath <vault credentials file path> /EnvType VMWare /SkipSpaceCheck
```

## <a name="set-up-the-target-environment"></a>ターゲット環境をセットアップする

ターゲット リソースを選択して確認します。

1. **[インフラストラクチャの準備]** > **[ターゲット]** の順にクリックし、使用する Azure サブスクリプションを選択します。
2. Resource Manager デプロイ モデルを指定します。
3. Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。


## <a name="set-up-a-replication-policy"></a>レプリケーション ポリシーを設定する

1. 新しいレプリケーション ポリシーを作成するには、**[Site Recovery インフラストラクチャ]** > **[レプリケーション ポリシー]** > **[+ レプリケーション ポリシー]** の順にクリックします。
2. **[レプリケーション ポリシーの作成]** で、ポリシー名を指定します。
3. **[RPO しきい値]** で、回復ポイントの目標 (RPO) の上限を指定します。 レプリケーション RPO がこの制限を超えると、アラートが生成されます。
4. **[復旧ポイントの保持期間]** で、各復旧ポイントのリテンション期間の長さ (時間単位) を指定します。 レプリケートされた VM は、期間内の任意の時点に復旧できます。 Premium Storage にレプリケートされたマシンでは最大 24 時間のリテンション期間がサポートされ、Standard Storage の場合は 72 時間です。
5. **[アプリ整合性スナップショットの頻度]** で、**[オフ]** を指定します。 **[OK]** をクリックしてポリシーを作成します。

このポリシーは自動的に構成サーバーに関連付けられます。

> [!WARNING]
> レプリケーション ポリシーのアプリ整合性スナップショットの頻度の設定で、**[オフ]** を指定していることを確認してください。 Windows Server 2008 を実行しているサーバーのレプリケート時は、クラッシュ整合性復旧ポイントだけがサポートされます。 アプリ整合性スナップショットの頻度にその他の値を指定すると、アプリケーション整合性復旧ポイントの不足が原因で、サーバーのレプリケーションの正常性が致命的な状態に変わり、エラーのアラートが発生します。

   ![レプリケーション ポリシーの作成](media/migrate-tutorial-windows-server-2008/create-policy.png)

## <a name="enable-replication"></a>レプリケーションを有効にする

Windows Server 2008 SP2 / Windows Server 2008 R2 SP1 サーバーが移行されるように、[レプリケーションを有効にします](physical-azure-disaster-recovery.md#enable-replication)。
   
   ![物理サーバーを追加する](media/migrate-tutorial-windows-server-2008/Add-physical-server.png)

   ![レプリケーションを有効にする](media/migrate-tutorial-windows-server-2008/Enable-replication.png)

## <a name="run-a-test-migration"></a>テスト移行を実行する

初期レプリケーションが完了し、サーバーのステータスが **[保護済み]** に変わった後、サーバーのレプリケーションのテスト フェールオーバーを実行できます。

Azure への[テスト フェールオーバー](tutorial-dr-drill-azure.md)を実行して、すべて想定どおりに動作していることを確認します。

   ![[テスト フェールオーバー]](media/migrate-tutorial-windows-server-2008/testfailover.png)


## <a name="migrate-to-azure"></a>Azure への移行

移行するマシンのフェールオーバーを実行します。

1. **[設定]** > **[レプリケートされたアイテム]** で、[マシン] > **[フェールオーバー]** をクリックします。
2. **[フェールオーバー]** で、フェールオーバーする**復旧ポイント**を選択します。 最新の復旧ポイントを選択します。
3. **[フェールオーバーを開始する前にマシンをシャットダウンします]** を選択します。 Site Recovery は、フェールオーバーを開始する前にサーバーをシャットダウンしようとします。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。 フェールオーバーの進行状況は **[ジョブ]** ページで確認できます。
4. 想定どおりに Azure VM が Azure に表示されることを確認します。
5. **[レプリケートされたアイテム]** で [VM] > **[移行の完了]** を右クリックします。 これによって、移行プロセスが終了し、VM のレプリケーションが停止して、その VM での Site Recovery の課金が停止します。

   ![移行の完了](media/migrate-tutorial-windows-server-2008/complete-migration.png)


> [!WARNING]
> **進行中のフェールオーバーを取り消さないでください**。フェールオーバーが開始される前に VM のレプリケーションが停止します。 進行中のフェールオーバーをキャンセルすると、フェールオーバーは停止しますが、VM が再びレプリケートされることはありません。
