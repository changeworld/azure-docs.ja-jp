---
title: リホスト-移行およびオンプレミス Linux アプリの Azure VM へのリホスト | Microsoft Docs
description: Contoso が、Azure VM に移行することによってオンプレミス Linux アプリをどのようにリホストするかを説明します。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: 6c96beee347a7a36a3dc04ecf8cd994484fd6bb7
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39007253"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms"></a>Contoso の移行: オンプレミス Linux アプリを Azure VM にリホストする

この記事では、Contoso が、オンプレミスの Linux ベースのサービスデスク アプリ (**osTicket**) を、どのように Azure IaaS VM にリホストするかを示します。

このドキュメントは、架空の会社 Contoso がオンプレミス リソースを Microsoft Azure クラウドに移行する方法を説明するシリーズ記事の 1 つです。 このシリーズには背景情報とシナリオのセットが含まれており、移行インフラストラクチャのセットアップ方法と、さまざまな種類の移行を実施する方法を具体的に説明しています。 シナリオが複雑になってきているため、徐々に記事が追加される予定です。

**記事** | **詳細** | **状態**
--- | --- | ---
[記事 1: 概要](contoso-migration-overview.md) | Contoso の移行戦略、記事シリーズ、および使用するサンプル アプリの概要を示します。 | 使用可能
[記事 2: Azure インフラストラクチャのデプロイ](contoso-migration-infrastructure.md) | Contoso が移行に備えて、オンプレミスと Azure のインフラストラクチャをどのように準備するかを説明します。 Contoso のすべての移行シナリオで、同じインフラストラクチャが使用されます。 | 使用可能
[記事 3: オンプレミス リソースの評価](contoso-migration-assessment.md)  | Contoso が、VMware 上で実行されるオンプレミスの 2 階層アプリ、SmartHotel の評価をどのように実行するかを示します。 アプリの VM については [Azure Migrate](migrate-overview.md) サービスで、アプリの SQL Server データベースについては [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) で評価を行います。 | 使用可能
[記事 4: Azure VM および SQL Managed Instance へのリホスト](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso が SmartHotel アプリを Azure に移行する方法を説明します。 SQL Managed Instance に移行するために、アプリのフロントエンド VM は [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) を使用して移行し、アプリのデータベースは [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) サービスを使用して移行します。 | 使用可能
[記事 5: Azure VM へのリホスト](contoso-migration-rehost-vm.md) | Contoso が SmartHotel アプリの VM を Azure VM に移行する方法を示します。 | 使用可能
[記事 6: Azure VM および SQL Server 可用性グループへのリホスト](contoso-migration-rehost-vm-sql-ag.md) | Contoso が SmartHotel アプリを移行する方法を示します。 Contoso は、Site Recovery を使用してアプリの VM を移行し、Database Migration サービスを使用してアプリのデータベースを SQL Server 可用性グループに移行します。 | 使用可能
記事 7: Linux アプリの Azure VM へのリホスト | Contoso が、Azure Site Recovery を使用して、osService Linux アプリを移行する方法を示します。 | この記事の内容は次のとおりです。
[記事 8: Linux アプリの Azure VM および Azure MySQL Server へのリホスト](contoso-migration-rehost-linux-vm-mysql.md) | Contoso が、VM 移行のために Site Recovery を使用して osService Linux アプリを移行し、MySQL Workbench を使用して Azure MySQL Server インスタンスに移行する方法を説明します。 | 使用可能
[記事 9: Azure Web Apps と Azure SQL Database でのアプリのリファクター](contoso-migration-refactor-web-app-sql.md) | Contoso が SmartHotel アプリを Azure Web アプリに移行して、アプリ データベースを Azure SQL Server インスタンスに移行する方法を示します | 使用可能
[記事 10: Azure Web Apps と Azure MySQL での Linux アプリのリファクター](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso が Linux osTicket アプリを複数のサイトの (継続的デリバリーのために GitHub と統合された) Azure Web Apps に移行する方法を示します。 Contoso は、アプリ データベースを Azure MySQL インスタンスに移行します。 | 使用可能
[記事 11: VSTS での TFS のリファクター](contoso-migration-tfs-vsts.md) | Contoso がオンプレミスの Team Foundation Server (TFS) のデプロイを Azure の Visual Studio Team Services (VSTS) に移行する方法を示します。 | 使用可能
[記事 12: Azure コンテナーと Azure SQL Database でのアプリの再構築](contoso-migration-rearchitect-container-sql.md) | Contoso が SmartHotel アプリを Azure に移行して再構築する方法を示します。 アプリの Web 階層を Windows コンテナーとして再構築し、Azure SQL Database でアプリ データベースを再構築します。 | 使用可能
[記事 13: Azure でのアプリのリビルド](contoso-migration-rebuild.md) | Contoso が Azure のさまざまな機能とサービス (App Services、Azure Kubernetes、Azure Functions、Cognitive Services、Cosmos DB など) を使用して SmartHotel アプリをリビルドする方法を示します。 | 使用可能

この記事で Contoso は、Linux Apache MySQL PHP (LAMP) で実行されている 2 階層の **osTicket** アプリを Azure に移行します。 アプリの VM は、Azure Site Recovery サービスを使用して移行されます。 このオープンソース アプリを使用したい場合は、[GitHub](https://github.com/osTicket/osTicket) からダウンロードできます。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応**: Contoso は成長を続けています。その結果、オンプレミスのシステムとインフラストラクチャに負荷がかかっています。
- **リスクの限定**: サービスデスク アプリは、Contoso のビジネスにとって非常に重要です。 Contoso はこれを、リスクを負うことなく Azure に移行したいと考えています。
- **拡張**: 今すぐアプリに変更を加えることは望んでいません。 ただその安定を確保することが望みです。


## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行の目標を明確にして、最適な移行方法を決定しました。

- 移行後の Azure 内のアプリは、現在のオンプレミス VMWare 環境と同じパフォーマンスを発揮できる必要があります。  このアプリは、オンプレミスの場合と同様に、クラウド内にあっても非常に重要であり続けます。 
- Contoso は、このアプリへの投資は望んでいません。  これはビジネスにとって重要なものですが、現状のまま確実にクラウドに移すことだけを望んでいます。
- Contoso は、このアプリの操作モデルを変更したくありません。 クラウドでは、今行っているのと同じ方法で操作したいのです。
- Contoso は、アプリの機能を変更したくありません。 変えるのはアプリの場所のみです。
- Windows アプリの移行を何件か完了したら、Contoso は Azure で Linux ベースのインフラストラクチャを使用する方法を学びたいと考えています。

## <a name="proposed-architecture"></a>提案されたアーキテクチャ

このシナリオでは:

- アプリは 2 つの VM (OSTICKETWEB および OSTICKETMYSQL) にわたって階層化されます。
- VM は、VMware ESXi ホスト **contosohost1.contoso.com** (バージョン 6.5) 上に配置されます。
- VMware 環境は、VM 上で実行中の vCenter Server 6.5 (**vcenter.contoso.com**) によって管理されます。
- Contoso には、オンプレミスのドメイン コントローラー (**contosodc1**) を含むオンプレミスのデータセンター (**contoso-datacenter**) があります。
- このアプリは運用のワークロードであるため、Azure 内の VM は、運用リソース グループ **ContosoRG** 内に存在することになります。
- VM はプライマリ リージョン (米国東部 2) に移行され、運用ネットワーク (VNET-PROD-EUS2) 内に配置されます。
    - Web VM は、フロントエンド サブネット (PROD-FE-EUS2) 内に存在することになります。
    - データベース VM は、データベース サブネット (PROD-DB-EUS2) 内に存在することになります。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。

![シナリオのアーキテクチャ](./media/contoso-migration-rehost-linux-vm/architecture.png) 

## <a name="migration-process"></a>移行プロセス

Contoso は次のように移行されます。

1. 最初の手順として、Contoso は、Site Recovery をデプロイするために必要な Azure とオンプレミスのインフラストラクチャを設定します。
2. Azure とオンプレミスのコンポーネントを準備したら、これらを設定し、VM のレプリケーションを有効にします。
3. レプリケーションが動作中になったら、VM を Azure にフェールオーバーすることによって VM を移行します。

![移行プロセス](./media/contoso-migration-rehost-linux-vm/migration-process.png)

### <a name="azure-services"></a>Azure サービス

**サービス** | **説明** | **コスト**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | このサービスは、Azure VM、オンプレミス VM、物理サーバーの移行とディザスター リカバリーを調整および管理します。  | Azure へのレプリケーションの間に、Azure Storage の料金が発生します。  フェールオーバーが発生すると、Azure VM が作成されて料金がかかります。 料金と価格について[詳しくはこちら](https://azure.microsoft.com/pricing/details/site-recovery/)をご覧ください。

 
## <a name="prerequisites"></a>前提条件

以下に、このシナリオのために作業担当者 (と Contoso) が必要とするものを示します。

**要件** | **詳細**
--- | ---
**Azure サブスクリプション** | このシリーズの最初の方の記事で、既にサブスクリプションを作成しているはずです。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)を作成してください。<br/><br/> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。<br/><br/> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。<br/><br/> さらに詳細なアクセス許可が必要な場合は、[こちらの記事](../site-recovery/site-recovery-role-based-linked-access-control.md)をご覧ください。 
**Azure インフラストラクチャ** | Contoso は、[移行のための Azure インフラストラクチャ](contoso-migration-infrastructure.md)についての記事で説明されているように、Azure インフラストラクチャを設定します。<br/><br/> Site Recovery 用の[ネットワーク](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network)と[ストレージ](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage)の具体的な要件の詳細を確認してください。
**オンプレミスのサーバー** | オンプレミスの vCenter Server は、バージョン 5.5、6.0、または 6.5 を実行している必要があります。<br/><br/> バージョン 5.5、6.0、または 6.5 を実行している ESXi ホスト<br/><br/> ESXi ホスト上で実行している 1 つ以上の VMware VM。
**オンプレミスの VM** | Site Recovery を使用した移行がサポートされる [Linux マシンを確認](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines)します。<br/><br/> サポートされる [Linux のファイル システムとストレージ システム](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage)を確認します。<br/><br/> VM は [Azure の要件](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)を満たす必要があります。


## <a name="scenario-steps"></a>シナリオのステップ

Azure で、この移行がどのように完了されるかを示します。

> [!div class="checklist"]
> * **ステップ 1: Site Recovery のために Azure を準備する**: レプリケートされたデータを保持するための Azure ストレージ アカウントと、Recovery Services のコンテナーを作成します。
> * **ステップ 2: Site Recovery のためにオンプレミスの VMware を準備する**: VM の検出とエージェント インストールのために使用されるアカウントを準備し、フェールオーバー後に Azure VM に接続する準備を行います。
> * **ステップ 3: VM をレプリケートする**: 移行元と移行先の環境を設定し、レプリケーション ポリシーを作成して、Azure のストレージへの VM のレプリケーションを開始します。
> * **ステップ 4: Site Recovery を使用して VM を移行する**: テスト フェールオーバーを実行してすべてが機能していることを確認してから、完全なフェールオーバーを実行して VM を Azure に移行します。


## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>ステップ 1: Site Recovery サービスのために Azure を準備する

Site Recovery のために、Contoso には数個の Azure コンポーネントが必要です。

- フェールオーバーされたリソースが置かれる VNet (Contoso では既にデプロイ済みの運用 VNet を使用します)
- レプリケートされたデータを保持する Azure の新しいストレージ アカウント。 
- Azure 内の Recovery Services コンテナー。

Contoso は、[Azure インフラストラクチャのデプロイメント](contoso-migration-infrastructure.md)の間に VNet を既に作成したため、必要なのはストレージ アカウントとコンテナーを作成することだけです。

1. Contoso は、米国東部 2 リージョンの中に Azure のストレージ アカウント (contosovmsacc20180528) を作成します。

    - ストレージ アカウントは、Recovery Services コンテナーと同じリージョンに存在する必要があります。
    - Contoso は、標準のストレージがあり、LRS レプリケーションが設定された汎用のアカウントを使用しています。

    ![Site Recovery ストレージ](./media/contoso-migration-rehost-linux-vm/asr-storage.png)

2. ネットワークとストレージ アカウントを準備したので、Contoso はコンテナー (ContosoMigrationVault) を作成し、それをプライマリの米国東部 2 リージョン内の **ContosoFailoverRG** リソース グループ内に配置します。

    ![Recovery Services コンテナー](./media/contoso-migration-rehost-linux-vm/asr-vault.png)


**さらにサポートが必要な場合**

Site Recovery のために Azure を設定することについての[説明を参照します](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure)。


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>ステップ 2: Site Recovery のためにオンプレミスの VMware を準備する

Contoso では、次のようにオンプレミスの VMware インフラストラクチャを準備します。

- VM の検出を自動化するために、vCenter サーバー上または vSphere ESXi ホスト上にアカウントを作成します。
- レプリケートする VMware VM 上にモビリティ サービスを自動インストールできるアカウントを作成します。
- オンプレミスの VM を準備して、それらの VM が、移行後に Azure VM が作成されたらそこへ接続できるようにします。


### <a name="prepare-an-account-for-automatic-discovery"></a>自動検出用のアカウントを準備する

Site Recovery では、次のことを実行するために、VMware サーバーへのアクセスが必要です。

- VM を自動的に検出します。 少なくとも読み取り専用のアカウントが必要です。
- レプリケーション、フェールオーバー、およびフェールバックを調整します。 ディスクを作成および削除する、VM の電源をオンにするなどの操作を実行できるアカウントが必要です。

Contoso は、アカウントを以下のように設定します。

1. Contoso は、vCenter レベルでロールを作成します。
2. Contoso は次に、必要なアクセス許可をそのロールに割り当てます。



### <a name="prepare-an-account-for-mobility-service-installation"></a>モビリティ サービスのインストール用のアカウントを準備する

Contoso が移行しようとしている Linux VM にモビリティ サービスをインストールする必要があります。

- VM のレプリケーションが有効になっているときには、Site Recovery はこのコンポーネントの自動プッシュ インストールを行うことができます。
- 自動プッシュ インストールの場合は、Site Recovery が VM へのアクセスに使用するアカウントを準備する必要があります。
- アカウントの詳細が、レプリケーションのセットアップ中の入力です。 
- このアカウントは、VM にインストールするアクセス許可を持つドメインまたはローカルのアカウントです。


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>フェールオーバー後に Azure VM に接続するための準備をする

Contoso は、Azure へのフェールオーバー後に、Azure 内のレプリケートされた VM に接続できることを望んでいます。 これを行うには、いくつかのことを実行する必要があります。

- インターネット経由でアクセスするには、移行前に、オンプレミス Linux VM 上で SSH を有効にします。  Ubuntu の場合、これは **Sudo apt-get ssh install -y** というコマンドを使用して済ませることができます。
- 移行を実行 (フェールオーバー) したら、**ブート診断**を調べて、VM のスクリーンショットを表示します。
- これが機能しない場合は、その VM が実行中であることを確認し、これらの[トラブルシューティングのヒント](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を見直してください。


**さらにサポートが必要な場合**

- 自動検出のためにロールを作成して割り当てることについての[説明を参照します](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery)。
- [モビリティ サービスのプッシュ インストール用のアカウントの作成](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation)についての説明を参照します。


## <a name="step-3-replicate-the-on-premises-vms"></a>ステップ 3: オンプレミスの VM をレプリケートする

Web VM を Azure に移行する前に、Contoso はレプリケーションを設定し、有効にします。

### <a name="set-a-protection-goal"></a>保護の目標を設定する

1. コンテナー内のコンテナー名 (ContosoVMVault) の下で、レプリケーションの目標を設定します (**[作業の開始]** > **[Site Recovery]** > **[Prepare infrastructure]**(インフラストラクチャの準備))。
2. マシンがオンプレミスに配置されていること、それらのマシンは VMware VM であること、およびレプリケート先は Azure にすることを指定します。
    ![レプリケーションの目標](./media/contoso-migration-rehost-linux-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>展開の計画を確認する

続行するには、**[Yes, I have done it]**(はい、完了しました) を選択して、デプロイ計画が完了したことを確認します。 このシナリオで Contoso が移行するのは 1 つの VM だけなので、デプロイ計画は不要です。

### <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

Contoso はソース環境を構成する必要があります。 これを行うには、OVF テンプレートをダウンロードし、それを使用して、Site Recovery 構成サーバーを高可用性オンプレミス VMware VM としてデプロイします。 構成サーバーを起動して実行したら、それをコンテナーに登録します。

構成サーバーでは、いくつかのコンポーネントが実行されます。

- オンプレミスと Azure の間の通信を調整し、データのレプリケーションを管理する構成サーバー コンポーネント。
- プロセス サーバー: レプリケーション ゲートウェイとして機能します。 レプリケーション データを受信し、そのデータをキャッシュ、圧縮、暗号化によって最適化して、Azure Storage に送信します。
- また、プロセス サーバーは、レプリケートする VM へのモビリティ サービスのインストールや、オンプレミスの VMware VM の自動検出も行います。

Contoso は、これらのステップを以下のように実行します。

1. OVF テンプレートを **[Prepare Infrastructure]**(インフラストラクチャの準備) > **[ソース]** > **[構成サーバー]** からダウンロードします。
    
    ![OVF をダウンロードする](./media/contoso-migration-rehost-linux-vm/add-cs.png)

2. VMware にテンプレートをインポートして VM を作成し、その VM をデプロイします。

    ![OVF テンプレート](./media/contoso-migration-rehost-linux-vm/vcenter-wizard.png)

3. 初めて VM をオンにすると、Windows Server 2016 インストール環境が起動します。 使用許諾契約書に同意し、管理者パスワードを入力します。
4. インストールの完了後に、管理者として VM にサインインします。 初めてサインインしたときは、Azure Site Recovery 構成ツールが既定で実行されます。
5. このツールでは、構成サーバーをコンテナーに登録するために使用する名前を指定します。
6. このツールは、VM が Azure に接続できることを確認します。 接続が確立されたら、Azure サブスクリプションにサインインします。 この資格情報は、構成サーバーを登録するコンテナーにアクセスできる必要があります。

    ![構成サーバーの登録](./media/contoso-migration-rehost-linux-vm/config-server-register2.png)

7. ツールがいくつかの構成タスクを実行した後、再起動されます。
8. マシンに再度サインインすると、構成サーバーの管理ウィザードが自動的に起動されます。
9. ウィザードで、レプリケーション トラフィックを受信する NIC を選択します。 この設定は、構成後に変更することはできません。
10. サブスクリプション、リソース グループ、および構成サーバーを登録するコンテナーを選択します。

    ![コンテナー](./media/contoso-migration-rehost-linux-vm/cswiz1.png) 

11. MySQL Server と VMWare PowerCLI をダウンロードしてインストールします。 
12. 検証が終わったら、vCenter サーバーまたは vSphere ホストの FQDN または IP アドレスを指定します。 ポートは既定のままにして、vCenter サーバーには、わかりやすい名前を指定します。
13. 自動検出のために作成したアカウントと、モビリティ サービスを自動的にインストールするために使用する必要がある資格情報を指定します。

    ![vCenter](./media/contoso-migration-rehost-linux-vm/cswiz2.png)

14. 登録が完了したら、Azure Portal で、コンテナーの **[ソース]** ページの一覧に、構成サーバーと VMware サーバーが表示されていることを確認します。 検出には 15 分以上かかる場合があります。 
15. Site Recovery はその後、VMware サーバーに接続し、VM を検出します。

### <a name="set-up-the-target"></a>ターゲットをセットアップする

ここで Contoso は、ターゲットのレプリケーション設定を構成します。

1. **[インフラストラクチャの準備]**(インフラストラクチャの準備) > **[ターゲット]** で、ターゲットの設定を選択します。
2. Site Recovery は、指定されたターゲットに Azure のストレージ アカウントとネットワークが存在することを確認します。

### <a name="create-a-replication-policy"></a>レプリケーション ポリシーを作成する

ソースとターゲットが設定されたら、Contoso はレプリケーション ポリシーを作成する準備が整います。

1. **[インフラストラクチャの準備]** > **[Replication Settings]\(レプリケーションの設定\)** > **[レプリケーション ポリシー]** >  **[Create and Associate]\(作成および関連付け\)** で、ポリシー **ContosoMigrationPolicy** を作成します。
2. 以下の既定の設定を使用します。
    - **[RPO しきい値]**: 既定値は 60 分です。 この値で、復旧ポイントの作成頻度を指定します。 継続的なレプリケーションがこの制限を超えると、アラートが生成されます。
    - **[復旧ポイントのリテンション期間]**。 既定値は 24 時間です。 この値は、各復旧ポイントのリテンション期間の長さを指定します。 レプリケートされた VM は、期間内の任意の時点に復旧できます。
    - **[アプリ整合性スナップショットの頻度]**。 既定値は 1 時間です。 この値は、アプリケーション整合性スナップショットが作成される頻度を指定します。
 
        ![レプリケーション ポリシーの作成](./media/contoso-migration-rehost-linux-vm/replication-policy.png)

5. このポリシーは自動的に構成サーバーに関連付けられます。 

    ![レプリケーション ポリシーを関連付ける](./media/contoso-migration-rehost-linux-vm/replication-policy2.png)

**さらにサポートが必要な場合**

- これらすべてのステップの詳細な手順については、[オンプレミス VMware VM のディザスター リカバリーの設定に関する記事](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial)を参照してください。
- [ソース環境の設定](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source)、[構成サーバーのデプロイ](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server)、および[レプリケーション設定の構成](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication)に役立つ詳細な手順が記載されています。
- Linux 用の Azure ゲスト エージェントの詳細については、[こちら](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)を参照してください。

**さらにサポートが必要な場合**

- これらすべてのステップの詳細な手順については、[オンプレミス VMware VM のディザスター リカバリーの設定に関する記事](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial)を参照してください。
- [ソース環境の設定](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source)、[構成サーバーのデプロイ](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server)、および[レプリケーション設定の構成](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication)に役立つ詳細な手順が記載されています。
- Linux 用の Azure ゲスト エージェントの詳細については、[こちら](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)を参照してください。



### <a name="enable-replication-for-osticketweb"></a>OSTICKETWEB のレプリケーションを有効にする

これで Contoso は、**OSTICKETWEB** VM のレプリケートを開始できるようになりました。

1. **[アプリケーションのレプリケート]** > **[ソース]** > **[+Replicate]\(+ レプリケート\)** で、ソースの設定を選択します。
2. vCenter サーバーと構成サーバーを含めて、仮想マシンを有効にすることを選択し、ソース設定を選択します。

    ![レプリケーションを有効にする](./media/contoso-migration-rehost-linux-vm/enable-replication-source.png)

3. ターゲットの設定を指定します。これには、フェールオーバー後に Azure VM が配置されるリソース グループと VNet、レプリケートされたデータが保存されるストレージ アカウントを指定することも含まれます。

     ![レプリケーションを有効にする](./media/contoso-migration-rehost-linux-vm/enable-replication2.png)

3. Contoso はレプリケーションのために **OSTICKETWEB** を選択します。 

    - この段階では、Contoso は **OSTICKETWEB** のみを選択します。これは、VNet とサブネットを選択する必要があり、VM は同じサブネットの中にないためです。
    - VM に対してレプリケーションが有効にされると、Site Recovery がモビリティ サービスを自動的にインストールします。

    ![レプリケーションを有効にする](./media/contoso-migration-rehost-linux-vm/enable-replication3.png)

4. VM のプロパティで、モビリティ サービスをマシンに自動的にインストールするためにプロセス サーバーが使用するアカウントを選択します。

     ![モビリティ サービス](./media/contoso-migration-rehost-linux-vm/linux-mobility.png)

5. **[レプリケーションの設定]** > **[レプリケーション設定の構成]** で、正しいレプリケーション ポリシーが適用されていることを確認し、**[Enable Replication]**(レプリケーションを有効にする) を選択します。
6.  レプリケーションの進行状況は、**[ジョブ]** で追跡します。 **保護の最終処理**ジョブが実行されると、マシンはフェールオーバーできる状態になります。



### <a name="enable-replication-for-osticketmysql"></a>OSTICKETMYSQL のレプリケーションを有効にする

Contoso は、**OSTICKETMYSQL** のレプリケートを開始できるようになりました。

1. **[アプリケーションのレプリケート]** > **[ソース]** > **[+Replicate]\(+ レプリケート\)** で、ソースのとターゲットの設定を選択します。
2. レプリケーションのために **OSTICKETMYSQL** を選択し、モビリティ サービスのインストールに使用するアカウントを選択します。

    ![レプリケーションを有効にする](./media/contoso-migration-rehost-linux-vm/mysql-enable.png)

3. Contoso は、OSTICKETWEB に使用されたのと同じレプリケーション ポリシーを適用し、レプリケーションを有効にします。  

**さらにサポートが必要な場合**

これらのすべてのステップの詳細な手順は、「[レプリケーションを有効にする](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication)」で確認できます。


## <a name="step-4-migrate-the-vms"></a>ステップ 4: VM を移行する 

Contoso は、簡単なテスト フェールオーバーを実行してから VM を移行します。

### <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

テスト フェールオーバーを実行すると、移行の前に、すべてが正常に機能していることを確認する助けになります。 

1. Contoso では、利用可能な最新の時点 (**[最後に処理があった時点]**) へのテスト フェールオーバーを実行します。
2. **[Shut down machine before beginning failover]\(フェールオーバー前にマシンをシャットダウンする\)** を選択します。これにより、Site Recovery は、フェールオーバーをトリガーする前にソース VM のシャットダウンを試みます。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。 
3. テスト フェールオーバーでは、以下が実行されます。 
    - 移行を実行するために必要なすべての条件が満たされていることを確認する前提条件チェックが実行されます。
    - Azure VM を作成できるように、フェールオーバーによってデータが処理されます。 最新の復旧ポイントが選択された場合は、データから復旧ポイントが作成されます。
    - 前の手順で処理されたデータを使用して、Azure VM が作成されます。
3. フェールオーバーが完了すると、Azure Portal にはレプリカの Azure VM が表示されます。 VM が適切なサイズであること、適切なネットワークに接続されていること、実行中であることを確認します。 
4. 確認したら、フェールオーバーをクリーンアップして、確認された事柄をすべて記録して保存します。

### <a name="create-and-customize-a-recovery-plan"></a>復旧計画を作成してカスタマイズする

 テスト フェールオーバーが正常に機能することを確認したら、移行のための復旧計画を作成します。 

- 復旧計画では、どのような順序でフェールオーバーを実行し、どのように Azure VM を Azure 内に加えるかを指定します。
- 2 階層のアプリを移行しようとしているため、データ VM (SQLVM) がフロントエンド (WEBVM) の前に起動されるように復旧計画をカスタマイズします。


1. **[復旧計画 (サイトの回復)]** > **[+ 復旧計画]** で計画を作成し、その計画に VM を追加します。

    ![復旧計画](./media/contoso-migration-rehost-linux-vm/recovery-plan.png)

2. 計画を作成したら、カスタマイズのためにその計画を選択します (**[復旧計画]** > **[OsTicketMigrationPlan]** > **[カスタマイズ]**)。
3.  **[グループ 1: 開始]** から **OSTICKETWEB** を削除します。  これにより、最初の開始操作が **OSTICKETMYSQL** のみに影響を与えるようにします。

    ![復旧グループ](./media/contoso-migration-rehost-linux-vm/recovery-group1.png)

4.  **[+ グループ]** > **[保護された項目の追加]** で、**OSTICKETWEB** を **[グループ 2: 開始]** に 追加します。  Contoso では、これらが 2 つの異なるグループ内にあることが必要です。

    ![復旧グループ](./media/contoso-migration-rehost-linux-vm/recovery-group2.png)


### <a name="migrate-the-vms"></a>VM を移行する


Contoso は、復旧計画に基づいてフェールオーバーを実行し、VM を移行する準備が整いました。

1. 計画を選択してから **[フェールオーバー]** を選択します。
2.  最新の復旧ポイントにフェールオーバーすることを選択し、Site Recovery では、フェールオーバーをトリガーする前にオンプレミス VM のシャットダウンを試みる必要があることを指定します。 フェールオーバーの進行状況は **[ジョブ]** ページで確認できます。

    ![フェールオーバー](./media/contoso-migration-rehost-vm/failover1.png)

3. フェールオーバー中に、vCenter Server は、ESXi ホストで実行されている 2 つの VM を停止するコマンドを発行します。

    ![フェールオーバー](./media/contoso-migration-rehost-linux-vm/vcenter-failover.png)

3. フェールオーバーの後、Azure VM が Azure Portal に正常に表示されることを確認します。

    ![フェールオーバー](./media/contoso-migration-rehost-linux-vm/failover2.png)  

3. Azure で VM を確認した後は、移行を完了し、VM ごとの移行プロセスを終了させます。 これで、VM のレプリケーションが停止し、その VM に対する Site Recovery の課金が停止します。

    ![フェールオーバー](./media/contoso-migration-rehost-linux-vm/failover3.png)


### <a name="connect-the-vm-to-the-database"></a>VM をデータベースに接続する

移行プロセスの最終ステップとして、**OSTICKETMYSQL** VM で実行されているアプリ データベースを指すようにアプリケーションの接続文字列を更新します。 

1. Putty または別の SSH クライアントを使用して、**OSTICKETWEB** VM への SSH 接続を作成します。 この VM はプライベートなので、プライベート IP アドレスを使用して接続します。

    ![データベースへの接続](./media/contoso-migration-rehost-linux-vm/db-connect.png)  

    ![データベースへの接続](./media/contoso-migration-rehost-linux-vm/db-connect2.png)  

2. **OSTICKETWEB** VM が **OSTICKETMYSQL** VM と通信できることを確認する必要があります。 構成は、この時点で、オンプレミスの IP アドレス 172.16.0.43 を使用してハードコードされています。

    **更新より前**
    
    ![IP の更新](./media/contoso-migration-rehost-linux-vm/update-ip1.png)  

    **更新より後**
    
    ![IP の更新](./media/contoso-migration-rehost-linux-vm/update-ip2.png) 
    
3. **systemctl restart apache2** と指定してサービスを再起動します。

    ![Restart](./media/contoso-migration-rehost-linux-vm/restart.png) 

4. 最後に、いずれかの Contoso ドメイン コント ローラー上で、**OSTICKETWEB** と **OSTICKETMYSQL** の DNS レコードを更新します。

    ![DNS の更新](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

    ![DNS の更新](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

**さらにサポートが必要な場合**

- テスト フェールオーバーの実行に関する[説明を参照します](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure)。 
- 復旧計画の作成方法に関する[説明を参照します](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans)。
- Azure へのフェールオーバーに関する[説明を参照します](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover)。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了した時点では、Azure VM で osTicket のアプリ層が実行されています。

ここで、いくつかのクリーンアップを行う必要があります。  

- vCenter のインベントリからオンプレミスの VM を削除します。
- ローカルのバックアップ ジョブからからオンプレミスの VM を削除します。
- 社内のドキュメントを更新して、OSTICKETWEB および OSTICKETMYSQL の新しい場所と IP アドレスを示します。
- こうした VM と対話しているリソースがないか確認し、新しい構成が反映されるように、関連する設定やドキュメントをすべて更新します。
- Contoso では、依存関係のマッピングを指定して Azure Migrate サービスを使用し、移行のための VM を評価しました。 この目的のためにインストールした Microsoft Monitoring Agent と Dependency Agent を、VM から削除する必要があります。

## <a name="review-the-deployment"></a>デプロイを再調査する

アプリが実行されるようになり、Contoso は新しいインフラストラクチャを完全に操作可能にして、セキュリティで保護する必要があります。

### <a name="security"></a>セキュリティ

Contoso のセキュリティ チームは、OSTICKETWEB と OSTICKETMYSQL の VM を再調査して、セキュリティの問題を特定します。

- VM がアクセスを制御するためのネットワーク セキュリティ グループ (NSG) を見直します。 NSG は、アプリケーションに対して許可されるトラフィックのみが通過できるようにするために使用されます。
- ディスクの暗号化と Azure KeyVault を使用した、VM ディスク上のデータのセキュリティ保護も検討します。

VM に関するセキュリティの実務の[詳細については、こちら](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control)を参照してください。

### <a name="backups"></a>バックアップ

Contoso は、Azure Backup サービスを使用して VM 上のデータをバックアップします。 [詳細情報](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- Contoso は、リソースのデプロイ後、[Azure インフラストラクチャのデプロイ](contoso-migration-infrastructure.md#set-up-tagging)中に定義されたとおりに Azure タグを割り当てます。
- Contoso に、Ubuntu サーバーとのライセンスの問題はありません。
- Contoso は、Microsoft の子会社である Cloudyn からライセンスが供与される Azure Cost Management を有効にします。 これは、Azure やその他のクラウド リソースの利用や管理に役立つ、マルチクラウド対応のコスト管理ソリューションです。  Azure Cost Management の詳細については、[こちら](https://docs.microsoft.com/azure/cost-management/overview)を参照してください。 


## <a name="next-steps"></a>次の手順

この記事では、Contoso が、2 つの Linux VM 上で階層化されているオンプレミス サービスデスク アプリを、Azure Site Recovery を使用して Azure IaaS VM に移行した方法を示しました。

このシリーズの次の記事では、Contoso が、同じサービスデスク アプリをどのように Azure に移行するかを示します。 Contoso は今回、Site Recovery を使用してアプリのフロントエンド VM を移行します。そして、バックアップと復元に加えて、MySQL ワークベンチ ツールを使用してアプリ データベースを Azure Database for MySQL に移行します。 では、[始めてください](contoso-migration-rehost-linux-vm-mysql.md)。
