---
title: Azure for Linux での SAP BusinessObjects BI プラットフォームのデプロイ | Microsoft Docs
description: Azure for Linux での SAP BusinessObjects BI プラットフォームのデプロイと構成
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,mysql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: e3cc7420a976812d462b3f5c5e60878ba67641ff
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130249155"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>Linux on Azure 向け SAP BusinessObjects BI プラットフォーム デプロイ ガイド

このアーティクルでは、Azure for Linux で SAP BusinessObjects BI (BOBI) プラットフォームをデプロイする戦略について説明します。 この例では、プレミアム ソリッドステートドライブ (SSD) マネージド ディスクを持つ 2 つの仮想マシンをインストール ディレクトリとして設定します。 CMS データベースには Azure Database for MySQL を使用し、両方のサーバーでファイル リポジトリ サーバーの Azure NetApp Files を共有します。 両方の仮想マシンに、既定の Tomcat Java Web アプリケーションと BI プラットフォーム アプリケーションを一緒にインストールします。 ユーザー要求の負荷を分散するために、ネイティブな TLS/SSL オフロード機能を持つ Azure Application Gateway を使用します。

この種類のアーキテクチャは、小規模なデプロイまたは非運用環境で効果的です。 大規模なデプロイまたは運用環境では、Web アプリケーション用に個別のホストを使用できます。 また、複数の BOBI アプリケーションホストを使用して、サーバーがより多くの情報を処理できるようにすることもできます。

![Azure for Linux での SAP BOBI のデプロイ](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

この例の製品バージョンとファイル システム レイアウトを次に示します。

- SAP BusinessObjects プラットフォーム 4.3
- SUSE Linux Enterprise Server 12 SP5
- Azure Database for MySQL (バージョン: 8.0.15)
- MySQL C API コネクタ - libmysqlclient (バージョン: 6.1.11)

| ファイル システム        | 説明                                                                                                               | サイズ (GB)             | 所有者  | グループ  | ストレージ                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/sap           | SAP BOBI インスタンス、既定の Tomcat Web アプリケーションと、必要に応じてデータベース ドライバをインストールするためのファイル システムです。 | SAP のサイズ設定のガイドライン | bl1adm | sapsys | マネージド プレミアム ディスク - SSD |
| /usr/sap/frsinput  | マウント ディレクトリは、すべての BOBI ホストで共有されるファイルのためのディレクトリで、入力ファイルのリポジトリ ディレクトリとして使用されます。  | ビジネス ニーズ         | bl1adm | sapsys | Azure NetApp Files         |
| /usr/sap/frsoutput | マウント ディレクトリは、すべての BOBI ホストで共有されるファイルのためのディレクトリで、出力ファイルのリポジトリ ディレクトリとして使用されます | ビジネス ニーズ         | bl1adm | sapsys | Azure NetApp Files         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Azure portal を使用した Linux 仮想マシンのデプロイ

このセクションでは、SAP BOBI プラットフォーム用の Linux オペレーティング システム イメージを持つ 2 つの仮想マシンを作成します。 仮想マシンを作成するための大まかな手順は以下の通りです。

1. [リソース グループ](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)を作成します。

2. [仮想ネットワーク](../../../virtual-network/quick-create-portal.md#create-a-virtual-network)を作成します。

   - SAP BI プラットフォームのデプロイでは、すべての Azure サービスで 1 つのサブネットを使用しないでください。 SAP BI プラットフォームのアーキテクチャに基づいて、複数のサブネットを作成する必要があります。 このデプロイでは、アプリケーション、ファイル リポジトリ ストア、Application Gateway の 3 つのサブネットを作成します。
   - Azure では、Application Gateway と Azure NetApp Files は常に別のサブネット上になければなりません。 詳細については、[Azure Application Gateway](../../../application-gateway/configuration-overview.md) と [Azure NetApp Files のネットワーク計画のガイドライン](../../../azure-netapp-files/azure-netapp-files-network-topologies.md)を参照してください。

3. 可用性セットを作成します。 複数インスタンスのデプロイで各層の冗長性を実現するには、可用性セット内の各層に仮想マシンを配置します。 アーキテクチャに応じて、各レベルの可用性セットを分離してください。

4. **(azusbosl1)** という名前の仮想マシン 1 を作成します。

   - カスタム イメージを使用することも、Azure Marketplace からイメージを選択することもできます。 詳細については、[Azure Marketplace から SAP 用の VM をデプロイする](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap)または [SAP 用のカスタム イメージで VM をデプロイする](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap)を参照してください。

5. **(azusbosl2)** という名前の仮想マシン 2 を作成します。
6. プレミアム SSD ディスクを 1 つ追加します。 これは、SAP BOBI のインストール ディレクトリとして使用します。

## <a name="provision-azure-netapp-files"></a>Azure NetApp Files のプロビジョニング

Azure NetApp Files の設定を続行する前に、[Azure NetApp Files のドキュメント](../../../azure-netapp-files/azure-netapp-files-introduction.md)の内容をよく理解しておいてください。

Azure NetApp Files はいくつかの [Azure リージョン](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)で利用できます。 選択した Azure リージョンで Azure NetApp Files が提供されているかどうかを確認してください。

[Azure NetApp Files を利用できる Azure リージョン](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)を参照して、リージョンごとの Azure NetApp Files の利用可能性を確認してください。

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files リソースのデプロイ

以下の手順では、お使いの [Azure 仮想ネットワーク](../../../virtual-network/virtual-networks-overview.md)が既にデプロイされていることを前提としています。 Azure NetApp Files のリソースと、そのリソースがマウントされる VM は、同じ Azure Virtual Network 内またはピアリングされた Azure Virtual Network 内にデプロイする必要があります。

1. 選択した Azure リージョンで、[Azure NetApp Files アカウントを作成](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)します。

2. [Azure NetApp Files 容量プールを設定](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)します。 このアーティクルで示されている SAP BI プラットフォーム アーキテクチャには、Premium サービス レベルで 1 つの Azure NetApp Files 容量プールが使用されています。 Azure の SAP BI ファイル リポジトリ サーバーには、Azure NetApp Files Premium または Ultra [サービス レベル](../../../azure-netapp-files/azure-netapp-files-service-levels.md)を使用することをお勧めします。

3. [サブネットを Azure NetApp Files に委任します](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)。

5. 「[Azure NetApp Files の NFS ボリュームを作成する](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)」の手順に従って、Azure NetApp Files のボリュームをデプロイします。

   ボリュームは、NFSv3 または NFSv4.1 としてデプロイできます。これは、両方のプロトコルが SAP BOBI プラットフォームでサポートされているためです。 それぞれの Azure NetApp Files サブネット内にボリュームをデプロイします。 Azure NetApp Files の IP アドレスは、自動的に割り当てられます。

Azure NetApp Files のリソースと Azure VM は、同じ Azure 仮想ネットワーク内またはピアリングされた Azure 仮想ネットワーク内に配置する必要があることに注意してください。 たとえば、*azusbobi-frsinput*、*azusbobi-frsoutput* がボリューム名で、*nfs://10.31.2.4/azusbobi-frsinput*、*nfs://10.31.2.4/azusbobi-frsoutput* が Azure NetApp Files ボリュームのファイル パスです。

- ボリューム azusbobi-frsinput (nfs://10.31.2.4/azusbobi-frsinput)
- ボリューム azusbobi-frsoutput (nfs://10.31.2.4/azusbobi-frsoutput)

### <a name="important-considerations"></a>重要な考慮事項

SAP BOBI プラットフォーム ファイル リポジトリ サーバー用の Azure NetApp Files を作成するときは、次の考慮事項にご注意ください。

- 最小容量のプールは 4 テビバイト (TiB) です。
- 最小ボリューム サイズは 100 ギビバイト (GiB) です。
- Azure NetApp Files と、Azure NetApp Files のボリュームがマウントされるすべての仮想マシンは、同じ Azure 仮想ネットワーク内、または同じリージョン内の[ピアリングされた仮想ネットワーク](../../../virtual-network/virtual-network-peering-overview.md)内に存在する必要があります。 同一リージョン内の仮想ネットワーク ピアリングを介した Azure NetApp Files のアクセスがサポートされています。 グローバル ピアリングによる Azure NetApp Files へのアクセスは現在サポートされていません。
- 選択した仮想ネットワークには、Azure NetApp Files に委任されているサブネットがある必要があります。
- Azure NetApp Files の[エクスポート ポリシー](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)では、許可するクライアント、アクセス タイプ (例えば、読み書きや読み取り専用など) を制御することができます。
- Azure NetApp Files 機能は、ゾーンにはまだ対応していません。 現在、その機能は、Azure リージョン内のすべての可用性ゾーンにはデプロイされていません。 Azure リージョンによっては、待ち時間が発生する可能性があることに注意してください。
- Azure NetApp Files ボリュームは、NFSv3 または NFSv4.1 ボリュームとしてデプロイできます。 両方のプロトコルが SAP BI プラットフォーム アプリケーション用にサポートされています。

## <a name="configure-file-systems-on-linux-servers"></a>Linux サーバーでのファイル システムの構成

このセクションの手順では、次のプレフィックスを使用します。

**[A]** :この手順はすべてのホストに適用されます。

### <a name="format-and-mount-the-sap-file-system"></a>SAP ファイル システムのフォーマットとマウント

1. **[A]** すべてのアタッチされたディスクを一覧表示します。

    ```bash
    sudo lsblk
    NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sda      8:0    0   30G  0 disk
    ├─sda1   8:1    0    2M  0 part
    ├─sda2   8:2    0  512M  0 part /boot/efi
    ├─sda3   8:3    0    1G  0 part /boot
    └─sda4   8:4    0 28.5G  0 part /
    sdb      8:16   0   32G  0 disk
    └─sdb1   8:17   0   32G  0 part /mnt
    sdc      8:32   0  128G  0 disk
    sr0     11:0    1  628K  0 rom  
    # Premium SSD of 128 GB is attached to virtual machine, whose device name is sdc
    ```

2. **[A]** /usr/sap のブロック デバイスをフォーマットします。

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A]** マウント ディレクトリを作成します。

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A]** ブロック デバイスの UUID を取得します。

    ```bash
    sudo blkid

    # It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A]** /etc/fstab にファイル システムのマウント エントリを維持します。

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A]** ファイル システムをマウントします。

    ```bash
    sudo mount -a

    sudo df -h

    Filesystem                     Size  Used Avail Use% Mounted on
    devtmpfs                       7.9G  8.0K  7.9G   1% /dev
    tmpfs                          7.9G   82M  7.8G   2% /run
    tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
    /dev/sda4                       29G  1.8G   27G   6% /
    tmpfs                          1.6G     0  1.6G   0% /run/user/1000
    /dev/sda3                     1014M   87M  928M   9% /boot
    /dev/sda2                      512M  1.1M  511M   1% /boot/efi
    /dev/sdb1                       32G   49M   30G   1% /mnt
    /dev/sdc                       128G   29G  100G  23% /usr/sap
    ```

### <a name="mount-the-azure-netapp-files-volume"></a>Azure NetApp Files ボリュームのマウント

1. **[A]** マウント ディレクトリを作成します。

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A]** NFSv4.1 マウントをサポートするようにクライアントの OS を構成します (NFSv4.1 を使用する場合のみ適用)。

   NFSv4.1 プロトコルの Azure NetApp Files ボリュームを使用している場合は、Azure NetApp Files NFSv4.1 ボリュームをマウントする必要があるすべての VM で次の構成を実行します。

   この手順では、NFS ドメイン設定を確認する必要があります。 ドメインが既定の Azure NetApp Files ドメイン (`defaultv4iddomain.com`) として設定されており、マッピングが `nobody` に設定されていることを確認します。

   ```bash
   sudo cat /etc/idmapd.conf
   # Example
   [General]
   Domain = defaultv4iddomain.com
   [Mapping]
   Nobody-User = nobody
   Nobody-Group = nobody
   ```

   > [!Important]
   > VM 上の /etc/idmapd.conf で NFS ドメインを設定し、Azure NetApp Files のデフォルトのドメイン設定と一致させてください (`defaultv4iddomain.com`)。 不一致がある場合、VM にマウントされている Azure NetApp Files ボリューム上のファイルのパーミッションが `nobody` と表示されます。

   `nfs4_disable_idmapping` を確認します。 これは `Y` に設定する必要があります。 `nfs4_disable_idmapping` が配置されるディレクトリ構造を作成するには、mount コマンドを実行します。 アクセスがカーネル/ドライバー用に予約されるため、/sys/modules の下に手動でディレクトリを作成することはできなくなります。

   ```bash
   # Check nfs4_disable_idmapping
   cat /sys/module/nfs/parameters/nfs4_disable_idmapping

   # If you need to set nfs4_disable_idmapping to Y
   mkdir /mnt/tmp
   mount -t nfs -o sec=sys,vers=4.1 10.31.2.4:/azusbobi-frsinput /mnt/tmp
   umount /mnt/tmp

   echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping

   # Make the configuration permanent
   echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
   ```

3. **[A]** マウント エントリを追加します。

   NFSv3 を使用している場合:

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   NFSv4.1 を使用している場合:

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   ```

4. **[A]** NFS ボリュームをマウントします。

   ```bash
   sudo mount -a

   sudo df -h

   Filesystem                     Size  Used Avail Use% Mounted on
   devtmpfs                       7.9G  8.0K  7.9G   1% /dev
   tmpfs                          7.9G   82M  7.8G   2% /run
   tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
   /dev/sda4                       29G  1.8G   27G   6% /
   tmpfs                          1.6G     0  1.6G   0% /run/user/1000
   /dev/sda3                     1014M   87M  928M   9% /boot
   /dev/sda2                      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1                       32G   49M   30G   1% /mnt
   /dev/sdc                       128G   29G  100G  23% /usr/sap
   10.31.2.4:/azusbobi-frsinput   101T   18G  100T   1% /usr/sap/frsinput
   10.31.2.4:/azusbobi-frsoutput  100T  512K  100T   1% /usr/sap/frsoutput
   ```

## <a name="configure-azure-database-for-mysql"></a>Azure Database for MySQL の構成

このセクションでは、Azure portal を使用して Azure Database for MySQL をプロビジョニングする方法について詳しく説明します。 また、SAP BOBI プラットフォーム用の CMS データベースと監査データベースの作成方法や、データベースにアクセスするためのユーザー アカウントについても説明しています。

このガイドラインは、Azure DB for MySQL を使用している場合にのみ適用されます。 その他のデータベースの場合、手順については、SAP またはデータベース固有のドキュメントを参照してください。

### <a name="create-a-database"></a>データベースを作成する

Azure portal にサインインし、[クイックスタート: Azure ポータルを使用して Azure Database for MySQL サーバーを作成する](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md)の手順に従います。 ここでは、Azure Database for MySQL をプロビジョニングする際に注意すべき点をいくつか紹介します。

- Azure Database for MySQL には、SAP BI プラットフォーム アプリケーション サーバーが実行されているのと同じリージョンを選択します。

- [SAP BI の製品出荷マトリックス (PAM)](https://support.sap.com/pam) に基づいて、お使いの SAP BOBI バージョンに固有のサポートされているデータベース バージョンを選択します。

- **[compute+storage (コンピューティングとストレージ)]** で、 **[Configure server (サーバーの構成)]** を選択し、出力のサイズに応じて適切な価格レベルを選択します。

- **[ストレージの自動拡張]** が既定で有効になります。 [ストレージ](../../../mysql/concepts-pricing-tiers.md#storage)はスケールアップのみ可能で、スケールダウンはできないことにご注意ください。

- 既定では、**バックアップのリテンション期間** は 7 日です。 [必要に応じて](../../../mysql/howto-restore-server-portal.md#set-backup-configuration)最大 35 日に設定できます。

- 既定では、Azure Database for MySQL のバックアップはローカルに冗長化されています。 サーバーのバックアップを地理的に冗長なストレージに保存したい場合は、 **[Backup Redundancy Options (バックアップ冗長オプション)]** から **[Geographically Redundant (地理冗長)]** を選択します。

>[!Important]
>サーバー作成後の[[Backup Redundancy Options (バックアップ冗長オプション)]](../../../mysql/concepts-backup.md#backup-redundancy-options)の変更はサポートされていません。

>[!Note]
>プライベート リンク機能は、General Purpose または Memory Optimized のいずれかの価格レベルの Azure Database for MySQL サーバーにのみ使用可能です。 データベース サーバーがこれらの価格レベルのいずれかであることを確認します。

### <a name="configure-azure-private-link"></a>Azure Private Link を構成する

このセクションでは、SAP BOBI 仮想マシンがプライベート エンドポイントを介して Azure Database for MySQL に接続するためのプライベート リンクを作成します。 Azure Private Link により、プライベート仮想ネットワーク内に Azure サービスが導入されます。

1. 前のセクションで作成したデータベースを選択します。
2. **[Security (セキュリティ)]**  >  **[プライベート エンドポイント接続]** に移動します。
3. **[プライベート エンドポイント接続]** セクションで、 **[プライベート エンドポイント]** を選択します。
4. **[サブスクリプション]**  >  **[リソース グループ]**  >  **[場所]** を選択します。
5. プライベート エンドポイントの **[名前]** を入力します。
6. **[リソース]** セクションで、次を指定します。
   - リソースの種類 - Microsoft.DBforMySQL/servers
   - リソース: 前のセクションで作成した MySQL データベース
   - 対象サブリソース - mysqlServer
7. **[ネットワーク]** セクションで、SAP BOBI アプリケーションをデプロイする **[仮想ネットワーク]** と **[サブネット]** を選択します。
   >[!NOTE]
   >サブネットで有効なネットワーク セキュリティ グループ (NSG) がある場合、それはこのサブネットのプライベート エンドポイントに対してのみ無効になります。 このサブネット上の他のリソースには引き続き NSG が強制されます。
8. **[Integrate with private DNS zone (プライベート DNS ゾーンと統合する)]** では、**既定値 (はい)** を使用します。
9.  ドロップダウン リストからお使いの **[プライベート DNS ゾーン]** を選択します。
10. **[Review+Create (確認と作成)]** を選択して、プライベート エンドポイントを作成します。

詳細については、[Azure Database for MySQL 用のプライベート リンク](../../../mysql/concepts-data-access-security-private-link.md)を参照してください。

### <a name="create-the-cms-and-audit-databases"></a>CMS および監査データベースの作成

1. [MySQL の Web サイト](https://dev.mysql.com/downloads/workbench/)から MySQL Workbench をダウンロードしてインストールします。 MySQL Workbench は、必ず Azure Database for MySQL にアクセスできるサーバーにインストールします。

2. MySQL Workbench を使用したサーバーへの接続 「[接続情報を取得する](../../../mysql/connect-workbench.md#get-connection-information)」の手順に従います。 接続テストが成功すると、次のメッセージが表示されます。

   ![MySQL Workbench のメッセージのスクリーンショット。](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. [SQL クエリ] タブで、次のクエリを実行して、CMS と監査データベースのスキーマを作成します。

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;
   ```

4. スキーマに接続するためのユーザー アカウントを作成します。

   ```sql
   # Create a user that can connect from any host, use the '%' wildcard as a host part
   CREATE USER 'cmsadmin'@'%' IDENTIFIED BY 'password';
   CREATE USER 'auditadmin'@'%' IDENTIFIED BY 'password';

   # Grant all privileges to a user account over a specific database:
   GRANT ALL PRIVILEGES ON cmsbl1.* TO 'cmsadmin'@'%' WITH GRANT OPTION;
   GRANT ALL PRIVILEGES ON auditbl1.* TO 'auditadmin'@'%' WITH GRANT OPTION;

   # Following any updates to the user privileges, be sure to save the changes by issuing the FLUSH PRIVILEGES
   FLUSH PRIVILEGES;
   ```

5. MySQL ユーザー アカウントの権限とロールを確認するには

   ```sql
   USE sys;
   SHOW GRANTS for 'cmsadmin'@'%';
   +------------------------------------------------------------------------+
   | Grants for cmsadmin@%                                                  |
   +------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `cmsadmin`@`%`                                   |
   | GRANT ALL PRIVILEGES ON `cmsbl1`.* TO `cmsadmin`@`%` WITH GRANT OPTION |
   +------------------------------------------------------------------------+

   USE sys;
   SHOW GRANTS FOR 'auditadmin'@'%';
   +----------------------------------------------------------------------------+
   | Grants for auditadmin@%                                                    |
   +----------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `auditadmin`@`%`                                     |
   | GRANT ALL PRIVILEGES ON `auditbl1`.* TO `auditadmin`@`%` WITH GRANT OPTION |
   +----------------------------------------------------------------------------+
   ```

### <a name="install-mysql-c-api-connector-on-a-linux-server"></a>Linux サーバーへの MySQL C API コネクタのインストール

SAP BOBI アプリケーション サーバーからデータベースへのアクセスには、データベース クライアント ドライバーが必要です。 CMS と監査データベースにアクセスするには、Linux 用の MySQL C API コネクタを使用する必要があります。 CMS データベースへの ODBC 接続はサポートされていません。 このセクションでは、Linux で MySQL C API コネクタを設定する方法について説明します。

1. [Azure Database for MySQL に対する MySQL ドライバーと管理ツールの互換性](../../../mysql/concepts-compatibility.md)を参照してください。 そのアーティクルで **MySQL コネクタ/C (libmysqlclient)** ドライバーについて確認します。

2. ドライバーをダウンロードするには [MySQL 製品アーカイブ](https://downloads.mysql.com/archives/c-c/)を参照してください。

3. オペレーティング システムを選択し、MySQL コネクタの共有コンポーネント rpm パッケージをダウンロードします。 この例では、mysql-connector-c-shared-6.1.11 コネクタ バージョンが使用されています。

4. すべての SAP BOBI アプリケーション インスタンスにコネクタをインストールします。

   ```bash
   # Install rpm package
   SLES: sudo zypper install <package>.rpm
   RHEL: sudo yum install <package>.rpm
   ```

5. libmysqlclient.so のパスを確認します。

   ```bash
   # Find the location of libmysqlclient.so file
   whereis libmysqlclient

   # sample output
   libmysqlclient: /usr/lib64/libmysqlclient.so
   ```

6. `LD_LIBRARY_PATH` には、インストールに使用するユーザー アカウントの `/usr/lib64` ディレクトリを指定します。

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>サーバーの準備

このセクションの手順では、次のプレフィックスを使用します。

**[A]** :この手順はすべてのホストに適用されます。

1. **[A]** Linux のフレーバー (SLES または RHEL) に基づいて、カーネル パラメーターを設定し、必要なライブラリをインストールする必要があります。 [Business Intelligence プラットフォーム インストール ガイド (UNIX)](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3)のシステム要件のセクションを参照してください。

2. **[A]** コンピューターのタイム ゾーンを適切に設定してください。 インストール ガイドの[ UNIX、Linux の追加要件](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/46b143336e041014910aba7db0e91070.html)のセクションを参照してください。

3. **[A]** ソフトウェアのバックグラウンド プロセスを実行できるユーザー アカウント (**bl1** adm) とグループ (sap sys) を作成します。 このアカウントを使用してインストールを実行し、ソフトウェアを実行します。 このアカウントにルート特権は必要ありません。

4. **[A]** ユーザー アカウント (**bl1** adm) の環境で、サポートされている UTF-8 のロケールを使用するように設定し、コンソール ソフトウェアが UTF-8 の文字セットをサポートしていることを確認してください。 オペレーティング システムで正しいロケールが使用されるようにするには、(**bl1** adm) ユーザー環境で、`LC_ALL` と `LANG` 環境変数を優先ロケールに設定します。

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LANG=en_US.utf8
   export LC_ALL=en_US.utf8
   ```

5. **[A]** ユーザー アカウント (**bl1** adm) を構成します。

   ```bash
   # Set ulimit for bl1adm to unlimited
   root@azusbosl1:~> ulimit -f unlimited bl1adm
   root@azusbosl1:~> ulimit -u unlimited bl1adm

   root@azusbosl1:~> su - bl1adm
   bl1adm@azusbosl1:~> ulimit -a

   core file size          (blocks, -c) unlimited
   data seg size           (kbytes, -d) unlimited
   scheduling priority             (-e) 0
   file size               (blocks, -f) unlimited
   pending signals                 (-i) 63936
   max locked memory       (kbytes, -l) 64
   max memory size         (kbytes, -m) unlimited
   open files                      (-n) 1024
   pipe size            (512 bytes, -p) 8
   POSIX message queues     (bytes, -q) 819200
   real-time priority              (-r) 0
   stack size              (kbytes, -s) 8192
   cpu time               (seconds, -t) unlimited
   max user processes              (-u) unlimited
   virtual memory          (kbytes, -v) unlimited
   file locks                      (-x) unlimited
   ```

6. SAP サービス マーケットプレースから SAP BusinessObjects BI プラットフォームのメディアをダウンロードして抽出します。

## <a name="installation"></a>インストール

サーバー上のユーザー アカウント **bl1** adm のロケールを確認します。

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

SAP BOBI プラットフォームのメディアにアクセスし、**bl1** adm ユーザーで以下のコマンドを実行してください。

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

[SAP BOBI プラットフォーム](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM) インストール ガイド (UNIX) に従います。 SAP BOBI プラットフォームをインストールするときの注意点を次に示します。

- **[Configure Product Registration (製品登録の設定)]** で、SAP ノート [1288121](https://launchpad.support.sap.com/#/notes/1288121) にある SAP BusinessObjects ソリューションの一時ライセンス キーを使用するか、SAP サービス マーケットプレースでライセンス キーを生成することができます。

- **[Select Install Type (インストールの種類を選択)]** で、 **[Full (フル)]** を選択し、最初のサーバーにインストールします (`azusbosl1`)。 もう一方のサーバー (`azusbosl2`) では、 **[Custom / Expand (カスタム/拡張)]** を選択すると、既存の BOBI の設定が拡張されます。

- **[Select Default or Existing Database (デフォルト データベースまたは既存データベースの選択)]** で、 **[configure an existing database (既存のデータベースの設定)]** を選択します。これにより、CMS および監査データベースを選択するよう求められます。 これらのデータベースの種類に対して **[MySQL]** を選択します。

  インストール中に監査を構成しない場合は、 **[No auditing database (監査データベースなし)]** を選択することもできます。

- **[Select Java Web Application Server (Java Web アプリケーション サーバーの選択)] 画面** で、使用している SAP BOBI アーキテクチャに基づいて適切なオプションを選択します。 この例では、オプション 1 を選択しました。これにより、同じ SAP BOBI プラットフォームに Tomcat サーバーがインストールされます。

- **[CMS リポジトリ データベースの設定 - MySQL]** に CMS データベースの情報を入力します。 次は、Linux をインストールした場合の CMS データベース情報の入力例です。 Azure Database for MySQL には既定のポート 3306 が使用されます。
  
  ![LINUX 上の SAP BOBI デプロイ - CMS データベースを示すスクリーンショット。](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

- (オプション) 監査データベースの情報を **[Configure Audit Repository Database - MySQL (監査リポジトリ データベースの設定 - MySQL)]** に入力します。 次の例では、Linux インストール向けの監査データベース情報の入力を示しています。

  ![LINUX 上の SAP BOBI デプロイ - 監査データベースを示すスクリーンショット。](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- 手順に従って必要な情報を入力し、インストールを完了させます。

複数インスタンス デプロイの場合は、2 台目のホスト (`azusbosl2`) でインストール セットアップを実行してください。 **[Select Install Type (インストール タイプの選択)]** で、 **[Custom / Expand (カスタム/拡張)]** を選択します。これにより、既存の BOBI セットアップを拡張します。

Azure Database for MySQL では、ゲートウェイを使用してサーバー インスタンスに接続をリダイレクトします。 接続が確立すると、MySQL には、MySQL サーバー インスタンスで実行されている実際のバージョンではなく、ゲートウェイに設定されている MySQL のバージョンが表示されます。 MySQL サーバー インスタンスのバージョンを判断するには、MySQL プロンプトで `SELECT VERSION();` コマンドを使用します。 詳細については、[サポートされている Azure Database for MySQL サーバー バージョン](../../../mysql/concepts-supported-versions.md)を参照してください。

![LINUX 上の SAP BOBI デプロイ - CMC 設定を示すスクリーンショット。](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

```sql
# Run direct query to the database using MySQL Workbench

select version();

+-----------+
| version() |
+-----------+
| 8.0.15    |
+-----------+
```

## <a name="post-installation"></a>インストール後

SAP BOBI プラットフォームの複数インスタンスのインストール後、アプリケーションの高可用性をサポートするために、設定後の追加手順を実行する必要があります。

### <a name="configure-the-cluster-name"></a>クラスター名を構成します

SAP BOBI プラットフォームの複数インスタンスのデプロイでは、1 つのクラスターで複数の CMS サーバーを同時に実行する必要があります。 クラスターは、共通の CMS システム データベースに対して連携する 2 つ以上の CMS サーバーで構成されます。 CMS で実行されているノードで障害が発生した場合、別の CMS があるノードで BI プラットフォームの要求の処理が続けられます。 SAP BOBI プラットフォームの既定の設定では、クラスター名に、インストールした最初の CMS のホスト名が反映されます。

Linux でクラスター名を構成するには、[SAP Business Intelligence プラットフォーム管理者ガイド](https://help.sap.com/viewer/2e167338c1b24da9b2a94e68efd79c42/4.3)の手順に従います。 クラスター名を構成した後、SAP Note [1660440](https://launchpad.support.sap.com/#/notes/1660440) に従って、CMC または BI ラウンチパッドのサインイン ページで既定のシステム エントリを設定します。

### <a name="configure-input-and-output-filestore-location-to-azure-netapp-files"></a>Azure NetApp Files に対する入力と出力の Filestore の場所を構成する

Filestore とは、実際の SAP BusinessObjects ファイルが存在するディスク ディレクトリのことです。 SAP BOBI プラットフォーム用のファイル リポジトリ サーバーの既定の場所は、ローカル インストール ディレクトリ内にあります。 複数インスタンス デプロイでは、Azure NetApp Files などの共有ストレージにファイルストアを設定することが重要です。 これにより、すべてのストレージ層サーバーからファイルストアにアクセスできます。

1. NFS ボリュームをまだ作成していない場合は、次の手順で Azure NetApp Files に作成します。 (前述の「Azure NetApp Files のプロビジョニング」の手順に従ってください)

2. NFS ボリュームをマウントします。 (前述の「Azure NetApp Files ボリュームのマウント」の手順に従ってください)

3. SAP ノート [2512660](https://launchpad.support.sap.com/#/notes/0002512660) に従って、ファイル リポジトリ (入力と出力の両方) のパスを変更します。

### <a name="session-replication-in-tomcat-clustering"></a>Tomcat クラスタリングでのセッション レプリケーション

Tomcat を使用すると、セッション レプリケーションとフェールオーバーのために、2 つ以上のアプリケーション サーバーのクラスタリングがサポートされます。 SAP BOBI プラットフォーム セッションはシリアル化されています。アプリケーション サーバーで障害が発生した場合でも、Tomcat の別のインスタンスへのユーザー セッションのシームレスなフェールオーバーが可能です。

たとえば、ユーザーが SAP BI アプリケーションでフォルダ階層をナビゲートしているときに、Web サーバーに障害が発生したとします。 正しく構成されたクラスターでは、ユーザーはサインイン ページにリダイレクトされることなく、フォルダー階層内を移動できます。

SAP ノート [2808640](https://launchpad.support.sap.com/#/notes/2808640) には、マルチキャストを使用して Tomcat クラスタリングを構成する手順が示されています。 ただし、Azure ではマルチキャストはサポートされていません。 そのため、Tomcat クラスターを Azure で機能させるには、[StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) を使用する必要があります (SAP ノート [2764907](https://launchpad.support.sap.com/#/notes/2764907))。 詳細については、ブログ記事 [SAP BusinessObjects BI Platform の静的メンバーシップを使用した Tomcat クラスタリング](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/)を参照してください。

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>SAP BI プラットフォームの Web 層の負荷分散

SAP BOBI の複数インスタンス デプロイの場合、Java Web アプリケーション サーバー (Web 層) が 2 つ以上のホストで実行されています。 Web サーバー間でユーザー負荷を均等に分散するために、エンド ユーザーと Web サーバーの間にロード バランサーを使用できます。 Azure で Azure Load Balancer または Azure Application Gateway を使用して、Web アプリケーション サーバーへのトラフィックを管理できます。 各オファリングの詳細について次のセクションで説明します。

#### <a name="azure-load-balancer"></a>Azure Load Balancer

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) は、ハイ パフォーマンスかつ低レイテンシーのレイヤー4 (TCP、UDP) ロードバランサーです。 正常な仮想マシン (VM) 間でトラフィックが分散されます。 ロード バランサーの正常性プローブは、各 VM の特定のポートを監視し、稼働している VM にのみトラフィックを分散します。 SAP BI プラットフォームをインターネットからアクセスできるようにするかどうかに応じて、パブリック ロード バランサーまたは内部ロード バランサーのいずれかを選択できます。 ゾーン冗長であるため、可用性ゾーン全体で高可用性が確保されます。

以下の図では、内部 Azure Load Balancer のセクションを参照しています。 Web アプリケーション サーバーは、ポート 8080 (既定の Tomcat HTTP ポート) で実行され、正常性プローブによって監視されます。 エンド ユーザーから送信された受信要求は、Web アプリケーション サーバー (`azusbosl1` または `azusbosl2`) にリダイレクトされます。 Azure Load Balancer で TLS/SSL 終端 (TLS/SSL オフロードとも呼ばれます) はサポートされていません。 ロード バランサーを使用して Web サーバー間でトラフィックを分散する場合は、Standard Load Balancer を使用することを推奨します。

> [!NOTE]
> パブリック IP アドレスのない VM が、内部の (パブリック IP アドレスのない) Standard Load Balancer のプール内に配置されている場合、パブリック エンドポイントへのルーティングを許可するように追加の構成が実行されない限り、送信インターネット接続はありません。 詳細は、[SAP の高可用性シナリオにおける Azure Standard Load Balancer を使用した仮想マシンのパブリック エンドポイント接続](high-availability-guide-standard-load-balancer-outbound-connections.md)を参照してください。

![Azure Load Balancer による Web サーバー間でのトラフィック分散を示した図。](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway"></a>Azure Application Gateway

[Azure Application Gateway](../../../application-gateway/overview.md) は、アプリケーション デリバリー コントローラー (ADC) をサービスとして提供します。 このサービスは、アプリケーションがユーザーのトラフィックを 1 つまたは複数の Web アプリケーション サーバーに誘導するために使用されます。 TLS/SSL オフロード、Web Application Firewall (WAF)、Cookie ベースのセッション アフィニティなど、様々なレイヤー 7 の負荷分散機能を提供します。

Application Gateway によって、SAP BI プラットフォームはアプリケーション Web トラフィックを `azusbosl1` または `azusbos2` の指定されたリソースに誘導します。 ユーザーはリスナーをポートに割り当て、ルールを作成し、リソースをプールに追加します。 次の図では、Application Gateway には、ユーザーのエントリ ポイントとして機能するプライベート IP アドレス (10.31.3.20) があります。 また、受信 TLS/SSL (HTTPS - TCP/443) 接続を処理し、TLS/SSL を復号化し、暗号化されていない要求 (HTTP - TCP/8080) をサーバーに渡します。 Application Gateway 上の TLS/SSL 証明書を 1 つだけ維持することで、運用が簡素化されます。

![Application Gateway による Web サーバー間のトラフィック分散を示した図。](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

SAP BOBI Web サーバー用に Application Gateway を構成するには、ブログ記事 [Azure Application Gateway を使用した SAP BOBI Web サーバーの負荷分散](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/)を参照してください。

> [!NOTE]
> Web サーバーへのトラフィックをロードバランスするには、Azure Application Gateway が適しています。 SSL オフロード、サーバー上の暗号化と復号化のオーバーヘッドを削減するための一元的な SSL 管理、トラフィックを分散するためのラウンド ロビン アルゴリズム、WAF 機能、高可用性などの便利な機能が提供されます。

## <a name="sap-bobi-platform-reliability-on-azure"></a>Azure での SAP BOBI プラットフォームの信頼性

SAP BOBI プラットフォームには、特定のタスクと操作用に最適化されたさまざまな階層が含まれています。 いずれかのレベルのコンポーネントが使用できなくなった場合、SAP BOBI アプリケーションは、その機能にアクセスできなくなるか、制限されます。 ビジネスの中断なくアプリケーションが稼働し続けるには、各層が高い信頼性を持つように設計されている必要があります。

このガイドでは、Azure のネイティブ機能と SAP BOBI プラットフォームの構成の組み合わせにより、SAP デプロイの可用性を向上させる方法について説明します。 ここでは、以下のオプションを中心に説明しています。

- **バックアップと復元:** データとアプリケーションのコピーを別の場所に定期的に作成するプロセスです。 元のデータやアプリケーションが失われたり破損したりした場合に、以前の状態に復元、復旧できます。

- **高可用性**: 高可用性プラットフォームには、Azure リージョン内のあらゆるものが少なくとも 2 つずつ存在し、いずれかのサーバーが使用できなくなった場合でもアプリケーションが稼働し続けます。
- **ディザスター リカバリー:** 自然災害により Azure のリージョン全体が利用できなくなるなど、壊滅的な損失が発生した場合に、アプリケーションの機能を回復させるプロセスです。

このソリューションの実装は、Azure のシステム設定の種類によって異なります。 ビジネス要件に応じて、バックアップ/復元、高可用性、ディザスター リカバリー ソリューションを調整します。

## <a name="backup-and-restore"></a>バックアップと復元

バックアップと復元はビジネス ディザスター リカバリー戦略の不可欠な要素です。 SAP BOBI プラットフォームの包括的な戦略を策定するには、アプリケーションのシステム ダウンタイムや中断につながるコンポーネントを特定します。 SAP BOBI プラットフォームでは、アプリケーションを保護するために、次のコンポーネントのバックアップが不可欠です。

- SAP BOBI インストール ディレクトリ (マネージド Premium ディスク)
- ファイル リポジトリ サーバー (Azure NetApp Files または Azure Premium Files)
- CMS データベース (Azure Database for MySQL、または Azure Virtual Machines 上のデータベース)

次のセクションでは、これらのコンポーネントごとにバックアップと復元の戦略を実装する方法を説明します。

### <a name="backup-and-restore-for-sap-bobi-installation-directory"></a>SAP BOBI インストール ディレクトリのバックアップと復元

Azure において、アプリケーション サーバーおよび接続されているすべてのディスクをバックアップする最も簡単な方法は、[Azure Backup](../../../backup/backup-overview.md) を使用することです。 Azure Backup では、VM 上のデータが誤って破壊されることを防ぐために、独立して分離されたバックアップを提供しています。 バックアップは、復旧ポイントの管理機能が組み込まれた Recovery Services コンテナーに格納されます。 構成とスケーリングは単純で、バックアップは最適化され、必要に応じて簡単に復元することができます。

バックアップ プロセスの一環として、スナップショットが取得され、運用環境のワークロードに影響を与えることなく、データがコンテナーに転送されます。 詳細については、[スナップショットの一貫性](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency)に関するページを参照してください。 また、選択的なディスク バックアップと復元の機能を使用して、VM 内のデータ ディスクのサブセットをバックアップすることもできます。 詳細については、[Azure VM バックアップ](../../../backup/backup-azure-vms-introduction.md)のドキュメントと、[よくあるご質問 - Azure VM のバックアップ](../../../backup/backup-azure-vm-backup-faq.yml)を参照してください。

### <a name="backup-and-restore-for-file-repository-server"></a>ファイル リポジトリ サーバーのバックアップと復元

SAP BOBI を Linux でデプロイしている場合、SAP BOBI プラットフォームのファイルストアとして Azure NetApp Files を使用できます。 Filestore に使用するストレージに基づいて、バックアップと復元に関する次のオプションを選択します。

- **Azure NetApp Files** の場合、スナップショットポリシーを使用して、オンデマンドのスナップショットを作成したり、自動スナップショットをスケジュールできます。 スナップショット コピーによって、ボリュームの特定時点のコピーが提供されます。 詳細については、「[Azure NetApp Files を使用して、スナップショットを管理する](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md)」を参照してください。

- NFS サーバーを別に作成した場合は、同じサーバーに対してバックアップと復元の戦略を実施してください。

### <a name="backup-and-restore-for-cms-and-audit-databases"></a>CMS、監査データベースのバックアップと復元

Linux VM では、サポートされている任意のデータベースで CMS と監査データベースを実行できます。 詳細については、[サポート マトリックス](businessobjects-deployment-guide.md#support-matrix)に関するページをご覧ください。 そのため、CMS と監査データ ストアに使用されているデータベースに基づいて、バックアップと復元の戦略を採用することが重要です。

- Azure Database for MySQL は、サーバーのバックアップを自動的に作成し、ユーザーが構成したローカル冗長または geo 冗長ストレージに保存します。 Azure Database for MySQL では、データ ファイルとトランザクション ログのバックアップが作成されます。 サポートされている最大ストレージ サイズに応じて、完全および差分バックアップ (最大 4 TB のストレージ サーバー) またはスナップショット バックアップ (最大 16 TB のストレージ サーバー) が作成されます。 これらのバックアップを使用すると、サーバーを、構成済みのバックアップ保有期間内の任意の時点に復元できます。 既定のバックアップ保持期間は 7 日間です。これは最大 3 日間まで[必要に応じて構成](../../../mysql/howto-restore-server-portal.md#set-backup-configuration)できます。 すべてのバックアップが、AES 256 ビット暗号化を使用して暗号化されます。 これらのバックアップ ファイルはユーザーに公開されておらず、エクスポートできません。 これらのバックアップは、Azure Database for MySQL の復元操作にのみ使用できます。 [mysqldump](../../../mysql/concepts-migrate-dump-restore.md) を使用して、データベースをコピーできます。 詳細については、「[Azure Database for MySQL でのバックアップと復元](../../../mysql/concepts-backup.md)」を参照してください。

- Azure 仮想マシンにインストールされているデータベースの場合、サポートされているデータベース用の、標準のバックアップ ツールまたは [Azure Backup](../../../backup/sap-hana-db-about.md) を使用できます。 また、すべての SAP BOBI プラットフォーム コンポーネントのバックアップと復旧のためのエージェントが用意されている、サードパーティ製のバックアップ ツールを使用できます。

## <a name="high-availability"></a>高可用性

*高可用性* とは、アプリケーションとサービスのビジネス継続性を提供することで、IT の中断を最小限に抑える一連のテクノロジを指します。 これは、同じデータセンター内の冗長、フォールト トレラント、またはフェールオーバーで保護されたコンポーネントを介して行われます。 今回の場合、データ センターは 1 つの Azure リージョン内にあります。 詳細については、[SAP のための高可用性のアーキテクチャとシナリオ](sap-high-availability-architecture-scenarios.md)を参照してください。

SAP BOBI プラットフォームのサイズ設定の結果に基づいて、ランドスケープを設計し、Azure Virtual Machines およびサブネット全体での BI コンポーネントの分散を決定する必要があります。 分散アーキテクチャの冗長性レベルは、ビジネスに必要な目標復旧時間 (RTO) と目標復旧時点 (RPO) によって異なります。 SAP BOBI プラットフォームには異なる複数の層が含まれており、各層のコンポーネントを、冗長性を実現するように設計する必要があります。 次に例を示します。

- BI アプリケーション サーバーや Web サーバーなどの冗長アプリケーション サーバー
- CMS データベース、ファイル リポジトリ サーバー、ロード バランサーなどの固有のコンポーネント。

次のセクションでは、SAP BOBI プラットフォームの各コンポーネントで高可用性を実現する方法について説明します。

### <a name="high-availability-for-application-servers"></a>アプリケーション サーバーの高可用性

冗長性を採用することで、アプリケーション サーバーの高可用性を実現できます。 これを行うには、さまざまな Azure VM で BI サーバーと Web サーバーの複数のインスタンスを構成します。

1 つ以上のイベントによるダウンタイムの影響を軽減するには、次の処理を行うのが良い方法です。

- 可用性ゾーンを使ってデータセンターの障害から保護する。
- 冗長性実現のために複数の仮想マシンを可用性セット内に構成する。
- 可用性セット内の VM にマネージド ディスクを使用する。
- 各アプリケーション層に対して別々の可用性セットを構成します。

詳細については、[Linux 仮想マシンの可用性の管理](../../availability.md)を参照してください。

>[!Important]
>Azure Availability Zones と Azure 可用性セットの概念は、相互に排他的です。 つまり、2つまたはそれ以上の VM を、特定の可用性ゾーンまたは Azure 可用性セットのいずれかにデプロイすることができますが、両方にはデプロイできません。

### <a name="high-availability-for-a-cms-database"></a>CMS データベースの高可用性

CMS や監査データベースに、Azure Database for MySQL を使用している場合、既定ではローカル冗長の高可用性フレームワークがあります。 ユーザーは、高可用性、冗長性、回復性の各機能が最初から備わっているリージョンとサービスを選択するだけで済み、追加のコンポーネントを構成する必要がありません。 SAP BOBI プラットフォームのデプロイ戦略が可用性ゾーンをまたがっている場合は、CMS および監査データベースに対してゾーン冗長を実現する必要があります。 詳細については、[Azure Database for MySQL での高可用性](../../../mysql/concepts-high-availability.md)と、[Azure SQL Database での高可用性](../../../azure-sql/database/high-availability-sla.md)を参照してください。

CMS データベースのその他のデプロイについては、[SAP Workload 用の DBMS 導入ガイド](dbms_guide_general.md) の高可用性に関する情報を参照してください。

### <a name="high-availability-for-filestore"></a>Filestore の高可用性

Filestore とは、レポート、ユニバース、接続などの内容が格納されているディスク ディレクトリのことです。 それは、そのシステムのすべてのアプリケーション サーバー間で共有されます。 そのため、他の SAP BOBI プラットフォーム コンポーネントと共に、高可用性が確保されるようにする必要があります。

Linux 上で実行されている SAP BOBI プラットフォームの場合、高可用性と高い耐久性を実現するように設計された [Azure Premium Files](../../../storage/files/storage-files-introduction.md) または [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) をファイル共有のために選択できます。 詳細については、Azure Files の[冗長性](../../../storage/files/storage-files-planning.md#redundancy)を参照してください。

> [!Important]
> Azure Files の SMB プロトコルは一般公開されていますが、Azure Files の NFS プロトコル サポートは現在プレビューの段階です。 詳細については、[Azure Files での NFS 4.1 サポートのプレビューが開始されました](https://azure.microsoft.com/blog/nfs-41-support-for-azure-files-is-now-in-preview/)を参照してください。

なお、このファイル共有サービスは一部のリージョンでは利用できません。 詳細については、[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)を参照してください。 お使いのリージョンでサービスを利用できない場合は、NFS サーバーを作成し、そのファイル システムを SAP BOBI アプリケーションと共有できます。 しかし、高可用性を考慮する必要もあります。

### <a name="high-availability-for-load-balancer"></a>ロード バランサーの高可用性

Web サーバー全体でトラフィックを分散するには、Azure Load Balancer または Azure Application Gateway のいずれかを使用できます。 デプロイ用に選択した SKU に基づいて、どちらの冗長性も実現できます。

- Azure Load Balancer の場合、Standard Load Balancer をゾーン冗長として構成することで冗長性を実現できます。 詳細については、「[Standard Load Balancer と可用性ゾーン](../../../load-balancer/load-balancer-standard-availability-zones.md)」をご覧ください。

- Application Gateway の場合、デプロイ中に選択した層の種類に基づいて高可用性を実現できます。
   -  v1 SKU を使用すると、2 つ以上のインスタンスをデプロイした場合に高可用性シナリオがサポートされます。 Azure は、これらのインスタンスを更新ドメインと障害ドメインに分散させ、全インスタンスで同時に障害が発生しないようにします。 ゾーン内で冗長性を実現します。
   -  v2 SKU を使用すると、新しいインスタンスが障害ドメインと更新ドメインに自動的に分散されます。 ゾーン冗長を選択した場合は、ゾーン障害回復性を提供するために、最新のインスタンスが可用性ゾーンにも分散されます。 詳細については、[自動スケーリングとゾーン冗長 Application Gateway v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md)を参照してください。

### <a name="reference-high-availability-architecture-for-sap-bobi-platform"></a>SAP BOBI プラットフォームのリファレンス高可用性アーキテクチャ

次の図は、Linux サーバーで実行されている可用性セットを使用している場合の SAP BOBI プラットフォームの設定を示しています。 このアーキテクチャでは、Azure Application Gateway、Azure NetApp Files、Azure Database for MySQL など、さまざまなサービスの利用を紹介しています。 これらのサービスでは、組み込みのゾーン冗長性が提供されるため、異なる複数の高可用性ソリューションを管理する複雑さが軽減されます。

受信トラフィック (HTTPS - TCP/443) は、Azure Application Gateway v1 SKU を使用して負荷分散されていることに注目してください。 冗長性を確保するために、Web サーバー、管理サーバー、処理サーバーの複数のインスタンスが別々の VM にデプロイされ、各層が別々の可用性セットにデプロイされます。 Azure NetApp Files では、データセンター内に冗長性が組み込まれているため、ファイル リポジトリ サーバー用の Azure NetApp Files ボリュームは高可用性が確保されます。 CMS データベースは、高可用性が最初から備わっている Azure Database for MySQL 上にプロビジョニングされます。 詳細については、[Azure Database for MySQL での高可用性](../../../mysql/concepts-high-availability.md)を参照してください。

![可用性セットを使用した SAP BusinessObjects BI プラットフォームの冗長性を示す図。](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

上記のアーキテクチャから、Azure での SAP BOBI デプロイの実行方法に関する洞察が得られます。 ただし、すべての構成オプションを網羅しているわけではありません。 ビジネス要件に基づいて、デプロイをカスタマイズできます。

複数の Azure リージョンでは、可用性ゾーンを使用できます。 これは、独立した電源、冷却装置、およびネットワークを利用できることを意味します。 これにより、お客様は 2 つまたは 3 つの可用性ゾーンにまたがってアプリケーションをデプロイできます。 可用性ゾーンをまたいで高可用性を実現したい場合は、アプリケーションの各コンポーネントがゾーン冗長化されていることを確認した上で、SAP BOBI プラットフォームをこれらのゾーンにデプロイできます。

## <a name="disaster-recovery"></a>障害復旧

ここでは、Linux 上で動作する SAP BOBI プラットフォームのディザスター リカバリー対策を説明します。 それにより、SAP のディザスター リカバリー アプローチ全体の主要なリソースを表す [SAP のディザスター リカバリー](../../../site-recovery/site-recovery-sap.md) ドキュメントを補完します。 SAP BOBI プラットフォームについては、SAP ノート [2056228](https://launchpad.support.sap.com/#/notes/2056228) を参照し、ディザスター リカバリー環境を安全に導入するための以下の方法を説明しています。

- プライマリ システムからコンテンツを昇格または配布するための、ライフサイクル管理またはフェデレーションの完全または選択的な使用。
- CMS、ファイル リポジトリ サーバーのコンテンツを定期的にコピーする。

このガイドでは、2 番目のオプションについて説明します。 ここでは、ディザスター リカバリーのためのすべての可能な構成オプションを網羅しませんが、ネイティブな Azure サービスと SAP BOBI プラットフォームの構成を組み合わせたソリューションを取り上げています。

>[!Important]
>SAP BOBI プラットフォームの各コンポーネントの可用性をセカンダリ リージョンに織り込んで、ディザスター リカバリー戦略全体を徹底的にテストする必要があります。

### <a name="reference-disaster-recovery-architecture-for-sap-bobi-platform"></a>SAP BOBI プラットフォームのディザスター リカバリーの参照アーキテクチャ

この参照アーキテクチャでは、冗長なアプリケーション サーバーを使用した SAP BOBI プラットフォームの複数インスタンス デプロイが実行されています。 ディザスター リカバリーの場合、SAP BOBI プラットフォームのすべてのコンポーネントをセカンダリ リージョンにフェールオーバーする必要があります。 次の図では、Azure NetApp Files を、CMS と監査リポジトリとしての filestore、Azure Database for MySQL として使用し、ロードバランサーとして Azure Application Gateway を使用しています。 各コンポーネントのディザスター リカバリー保護を実現する方法は異なります。詳細については、次のセクションで説明します。

![SAP BusinessObjects BI プラットフォームのディザスター リカバリーを示した図。](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

### <a name="load-balancer"></a>Load Balancer

ロード バランサーは、SAP BOBI プラットフォームの Web アプリケーション サーバー間でトラフィックを分散するために使用されます。 Azure では、この目的のために、Azure Load Balancer か Azure Application Gateway を使用できます。 ロード バランサー サービスのディザスター リカバリーを実現するには、セカンダリ リージョンに別の Azure Load Balancer または Azure Application Gateway を実装する必要があります。 ディザスター リカバリー フェールオーバー後に同じ URL を保持するには、セカンダリ リージョンで実行されている負荷分散サービスを指すように、DNS のエントリを変更する必要があります。

### <a name="vms-running-web-and-bi-application-servers"></a>Web および BI アプリケーション サーバーを実行している VM

[Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) を使用して、Web および BI アプリケーション サーバーを実行している VM をセカンダリ リージョン上でレプリケートできます。 災害や障害が発生したときに、レプリケートされた環境に簡単にフェールオーバーして作業を続けることができるように、サーバーとそれにアタッチされているすべてのマネージド ディスクがセカンダリ リージョンにレプリケートされます。 Azure ディザスター リカバリー データ センターへのすべての SAP アプリケーション VM のレプリケートを開始するには、「[仮想マシンを Azure にレプリケートする](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md)」の手順に従います。

### <a name="file-repository-servers"></a>ファイル リポジトリ サーバー

ファイルストアは、レポートや BI ドキュメントなどの実際のファイルが格納されているディスク ディレクトリです。 ファイルストア内のすべてのファイルがディザスター リカバリー リージョンと同期されていることが重要です。 Linux で実行されている SAP BOBI プラットフォームに使用しているファイル共有サービスの種類に基づいて、コンテンツを同期するために必要なディザスター リカバリー戦略を採用する必要があります。

- **Azure NetApp Files** によって NFS および SMB ボリュームが提供されるため、ファイルベースの任意のコピー ツールを使用して、Azure リージョン間でデータをレプリケートできます。 別のリージョンにあるボリュームをコピーする方法の詳細については、[Azure NetApp Files に関するよくあるご質問](../../../azure-netapp-files/faq-data-migration-protection.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)を参照してください。

  Azure NetApp Files リージョン間レプリケーションを使用できます。これは現在[プレビュー](https://azure.microsoft.com/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/)段階です。 変更されたブロックだけが、圧縮された効率的な形式でネットワーク経由で送信されます。 これにより、リージョン間でのレプリケーションに必要なデータ量が最小限に抑えられるため、データ転送コストが削減されます。 また、レプリケーションにかかる時間が短縮されるため、より小さい RPO を実現できます。 詳細については、「[リージョン間レプリケーションを使用するための要件と考慮事項](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md)」を参照してください。

- **Azure Premium Files** を使用する場合、ローカル冗長 (LRS) およびゾーン冗長ストレージ (ZRS) のみがサポートされます。 ディザスター リカバリー戦略を使用する場合、[AzCopy](../../../storage/common/storage-use-azcopy-v10.md) または [Azure PowerShell](/powershell/module/az.storage/) を使用して、異なるリージョンの別のストレージ アカウントにファイルをコピーすることができます。 詳細については、「[ディザスター リカバリーとストレージ アカウントのフェールオーバー](../../../storage/common/storage-disaster-recovery-guidance.md)」を参照してください。

   > [!Important]
   > Azure Files の SMB プロトコルは一般公開されていますが、Azure Files の NFS プロトコル サポートは現在プレビューの段階です。 詳細については、[Azure Files での NFS 4.1 サポートのプレビューが開始されました](https://azure.microsoft.com/blog/nfs-41-support-for-azure-files-is-now-in-preview/)を参照してください。

### <a name="cms-database"></a>CMS データベース

ディザスター リカバリー リージョンの CMS や監査データベースは、プライマリ リージョンで実行されているデータベースのコピーである必要があります。 データベースの種類によっては、ビジネスで必要とされる RTO と RPO に基づいて、データベースをディザスター リカバリー リージョンにコピーすることが重要です。

#### <a name="azure-database-for-mysql"></a>Azure Database for MySQL

Azure Database for MySQL には、障害が発生した場合にデータベースを復旧するための複数のオプションが用意されています。 ご自身のビジネスに適したオプションを選択してください。

- 事業継続とディザスター リカバリーの計画を強化するには、リージョンにまたがる読み取りレプリカを有効にします。 ソース サーバーから最大 5 つのレプリカにレプリケートできます。 読み取りレプリカは、MySQL のバイナリ ログ レプリケーション テクノロジを使用して非同期的に更新されます。 レプリカは、通常の Azure Database for MySQL 内のサーバーと同様に管理する新しいサーバーです。 詳細については、[Azure Database for MySQL での読み取りレプリカ](../../../mysql/concepts-read-replicas.md)を参照してください。

- geo-restore 機能では、geo 冗長バックアップを使用してサーバーを復元します。 これらのバックアップは、サーバーがホストされているリージョンがオフラインのときでもアクセスできます。 これらのバックアップから他の任意のリージョンに復元し、サーバーをオンラインに戻すことができます。

  > [!NOTE]
  > geo リストアは、geo 冗長バックアップ ストレージでサーバーをプロビジョニングした場合にのみ可能です。 サーバー作成後の **[Backup Redundancy Options (バックアップ冗長オプション)]** の変更はサポートされていません。 詳細については、[バックアップの冗長性](../../../mysql/concepts-backup.md#backup-redundancy-options)に関する記事をご覧ください。

次の表は、この例で使用した各階層のディザスター リカバリーに関する推奨事項です。

| SAP BOBI プラットフォームの層   | 推奨                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Azure Application Gateway | セカンダリ リージョンでの Application Gateway の並列セットアップ。                                             |
| Web アプリケーション サーバー   | Azure Site Recovery を使ったレプリケーション。                                                                  |
| BI アプリケーション サーバー    | Site Recovery を使ったレプリケーション。                                                                        |
| Azure NetApp Files        | ファイルベースのコピー ツールで、セカンダリ リージョンにデータを複製したり、クロスリージョン レプリケーションを使用します。      |
| Azure Database for MySQL  | クロスリージョンでの読み取りレプリカや、geo-redundant バックアップからの復元が可能です。                                |

## <a name="next-steps"></a>次の手順

- [多層 SAP アプリ デプロイのディザスター リカバリーを設定する](../../../site-recovery/site-recovery-sap.md)
- [SAP のための Azure Virtual Machines の計画と実装](planning-guide.md)
- [SAP のための Azure Virtual Machines のデプロイ](deployment-guide.md)
- [SAP のための Azure Virtual Machines DBMS のデプロイ](./dbms_guide_general.md)
