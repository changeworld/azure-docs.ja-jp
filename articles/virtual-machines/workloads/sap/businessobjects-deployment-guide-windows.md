---
title: Azure の Windows 向け SAP BusinessObjects BI プラットフォームのデプロイ | Microsoft Docs
description: Azure の Windows 向け SAP BusinessObjects BI プラットフォームのデプロイと構成
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,azure-sql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/08/2021
ms.author: depadia
ms.openlocfilehash: a13b62da91e3ce6f18ba4dcda2b3b0f22c815026
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108144387"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-windows-on-azure"></a>Azure の Windows 向け SAP BusinessObjects BI プラットフォーム デプロイ ガイド

この記事では、Azure 上に Windows 向け SAP BusinessObjects BI プラットフォームをデプロイする戦略について説明します。 この例には、Premium SSD マネージド ディスクをインストール ディレクトリとする 2 つの仮想マシンが構成されています。 Azure SQL データベース (PaaS オファリング) は、中央管理サービス (CMS) および監査データベースに使用されます。Azure Premium Files (SMB プロトコル) は、両方の仮想マシン間で共有されるファイル ストアとして使用されます。 既定の Tomcat Java Web アプリケーションと BI プラットフォーム アプリケーションは、両方の仮想マシンに一緒にインストールされます。 ユーザー要求の負荷を分散するために、ネイティブの TLS/SSL オフロード機能を備えた Azure Application Gateway が使用されます。

この種類のアーキテクチャは、小規模なデプロイまたは非運用環境で効果的です。 運用環境または大規模なデプロイの場合は、Web アプリケーション用に個別のホストを使用する必要があります。また、複数の BOBI アプリケーション ホストを使用して、サーバーでより多くの情報を処理できます。

![Azure での Windows 向け SAP BOBI のデプロイ](media/businessobjects-deployment-guide/businessobjects-deployment-windows.png)

この例では、次の製品バージョンとファイル システム レイアウトが使用されています。

- SAP BusinessObjects プラットフォーム 4.3 SP01 Patch 1
- Windows Server 2019
- Azure SQL データベース (バージョン: 12.0.2000.8)
- Microsoft ODBC ドライバー - msodbcsql.msi (バージョン: 13.1)

| ファイル システム        | 説明                                                                                                               | サイズ (GB)             | 必要なアクセス  | Storage                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|---------------|----------------------------|
| F:          | SAP BOBI インスタンス、既定の Tomcat Web アプリケーション、およびデータベース ドライバー (必要な場合) をインストールするためのファイル システム | SAP のサイズ設定のガイドライン | ローカル管理者特権 | マネージド Premium ディスク - SSD |
| \\\azusbobi.file.core.windows.net\frsinput  | マウント ディレクトリは、入力 Filestore ディレクトリとして使用されるすべての BOBI ホスト間の共有ファイル用です | ビジネス ニーズ         | ローカル管理者特権 | Azure NetApp Files         |
| \\\azusbobi.file.core.windows.net\frsoutput | マウント ディレクトリは、出力 Filestore ディレクトリとして使用されるすべての BOBI ホスト間の共有ファイル用です | ビジネス ニーズ         | ローカル管理者特権 | Azure NetApp Files         |

## <a name="deploy-windows-virtual-machine-via-azure-portal"></a>Azure portal を使用した Windows 仮想マシンのデプロイ

このセクションでは、Windows オペレーティング システム (OS) イメージを使用して SAP BOBI プラットフォーム用の仮想マシン (VM) を 2 つ作成します。 仮想マシンを作成する手順の概要は次のとおりです。

1. [リソース グループ](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)を作成します。

2. [仮想ネットワーク](../../../virtual-network/quick-create-portal.md#create-a-virtual-network)を作成します。

   - SAP BI プラットフォームのデプロイの場合、すべての Azure サービス用に単一のサブネットを使用しないでください。 SAP BI プラットフォームのアーキテクチャに基づいて、複数のサブネットを作成することが必要な場合があります。 このデプロイの場合、BI アプリケーション サブネット、および Application Gateway サブネットという 2 つのサブネットを作成します。
   - SAP ノート [2276646](https://launchpad.support.sap.com/#/notes/2276646) に従って、各種コンポーネント間の SAP BusinessObjects BI プラットフォーム通信のためのポートを特定します。
   - Azure SQL Database の通信は、ポート 1433 上で行われます。 したがって、ポート 1433 で、SAP BOBI アプリケーション サーバーからの送信トラフィックを許可する必要があります。
   - Azure では、Application Gateway は別のサブネットに存在する必要があります。 詳細については、[Azure Application Gateway](../../../application-gateway/configuration-overview.md) の記事を参照してください。
   - Azure Files ではなく Azure NetApp Files をファイル ストアに使用する場合は、Azure NetApp Files 用に別のサブネットを作成する必要があります。 詳細については、「[Azure NetApp Files のネットワーク計画のガイドライン](../../../azure-netapp-files/azure-netapp-files-network-topologies.md)」の記事を参照してください。

3. 可用性セットを作成します。

   - 複数インスタンスのデプロイで各層の冗長性を実現するには、可用性セット内の各層に仮想マシンを配置します。 アーキテクチャに応じて、各レベルの可用性セットを分離してください。

4. 仮想マシン 1 **(azuswinboap1)** を作成します。

   - カスタム イメージを使用することも、Azure Marketplace からイメージを選択することもできます。 必要に応じて「[Azure Marketplace から SAP 用 VM をデプロイする](deployment-guide.md)」または「[SAP のカスタム イメージを使用して VM をデプロイする](deployment-guide.md)」を参照してください。

5. 仮想マシン 2 **(azuswinboap2)** を作成します。
6. Premium SSD ディスクを 1 つ追加します。 それは SAP BOBI のインストール ディレクトリとして使用されます。

## <a name="provision-azure-premium-files"></a>Azure Premium Files のプロビジョニング

Azure Files のセットアップを続行する前に、[Azure Files](../../../storage/files/storage-files-introduction.md) のドキュメントの内容をよく理解しておいてください。

Azure Files には、ハード ディスク ベース (HDD ベース) のハードウェアでホストされる Standard ファイル共有と、ソリッドステート ディスク ベース (SSD ベース) ハードウェアでホストされる Premium ファイル共有が用意されています。 SAP BusinessObjects ファイル ストアの場合は、Azure Premium Files を使用することをお勧めします。

Azure Premium ファイル共有は、リージョンのサブセット内でローカルおよびゾーン冗長で利用できます。 ご自分のリージョンで現在 Premium ファイル共有を使用できるかどうかを見つけるには、Azure の [[リージョン別の利用可能な製品]](https://azure.microsoft.com/global-infrastructure/services/?products=storage) ページを参照してください。 ZRS をサポートするリージョンの詳細については、「[Azure Storage の冗長性](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」を参照してください。

### <a name="deploy-azure-files-storage-account-and-nfs-shares"></a>Azure Files ストレージ アカウントと NFS 共有のデプロイ

Azure ファイル共有は、ストレージの共有プールを表す最上位オブジェクトである "ストレージ アカウント" にデプロイされます。 このストレージのプールは、複数のファイル共有をデプロイするのに使用できます。 Azure では、お客様にとって可能性があるさまざまなストレージ シナリオのために複数の種類のストレージ アカウントをサポートしていますが、SAP BusinessObjects ファイル ストアの場合は **FileStorage** ストレージ アカウントを作成する必要があります。 これにより、Premium のソリッドステート ディスク ベース (SSD ベース) のハードウェアに Azure ファイル共有をデプロイできます。

> [!NOTE]
> FileStorage アカウントは、Azure ファイル共有を格納するためにのみ使用できます。FileStorage アカウントでその他のストレージ リソース (BLOB、コンテナー、キュー、テーブルなど) をデプロイすることはできません。

ストレージアカウントには、SAP BOBI プラットフォームの同じ仮想ネットワークにデプロイされた[プライベート エンドポイント](../../../storage/files/storage-files-networking-endpoints.md)経由でアクセスします。 このセットアップでは、SAP システムからのトラフィックは、仮想ネットワークのセキュリティ境界の外に出ることはありません。 SAP システムには、機密性が高くビジネスにとって重要なデータが含まれていることが多く、仮想ネットワークの境界内にとどまることは、多くのお客様にとって重要なセキュリティ上の考慮事項です。

別の仮想ネットワークからストレージ アカウントにアクセスする必要がある場合は、[Azure VNET ピアリング](../../../virtual-network/virtual-network-peering-overview.md)を使用できます。

#### <a name="azure-files-storage-account"></a>Azure Files ストレージ アカウント

1. Azure portal からストレージ アカウントを作成するには、 **[リソースの作成]**  >  **[ストレージ]**  >  **[ストレージ アカウント]** を選択します。

2. **[基本]** タブで、すべての必要なフィールドに入力してストレージ アカウントを作成します。

   a. **[サブスクリプション]** 、 **[リソース グループ]** 、 **[リージョン]** を選択します。

   b. **[ストレージ アカウント名]** (例: **azusbobi**) を入力します。 この名前はグローバルに一意である必要がありますが、任意の名前を指定できます。

   c. パフォーマンス レベルは **[Premium]** を、アカウントの種類は **[FileStorage]** を選択します。

   d. [レプリケーション ラベル] で、冗長性レベルを選択します。 **[ローカル冗長ストレージ (LRS)]** を選択します。

   Premium FileStorage の場合、使用可能なオプションはローカル冗長ストレージ (LRS) とゾーン冗長ストレージ (ZRS) に限られます。 そのため、デプロイ戦略 (可用性セットまたは可用性ゾーン) に応じて、適切な冗長性レベルを選択します。 詳細については、「[Azure Storage の冗長性](../../../storage/common/storage-redundancy.md)」を参照してください。

   e. [次へ] を選択します。

3. **[ネットワーク]** タブで、接続方法として [[プライベート エンドポイント]](../../../storage/files/storage-files-networking-endpoints.md) を選択します。 詳細については、「[Azure Files のネットワークに関する考慮事項](../../../storage/files/storage-files-networking-overview.md)」を参照してください。

   a. プライベート エンドポイント セクションで **[追加]** を選択します。

   b. **[サブスクリプション]** 、 **[リソース グループ]** 、 **[場所]** を選択します。

   c. プライベート エンドポイントの **[名前]** (例: azusbobi-pe) を入力します。

   d. **[ストレージ サブリソース]** で **[ファイル]** を選択します。

   e. **[ネットワーク]** セクションで、SAP BusinessObjects BI アプリケーションをデプロイする **[仮想ネットワーク]** と **[サブネット]** を選択します。

   f. **[プライベート DNS ゾーンと統合する]** は、**既定値 (はい)** をそのまま使用します。

   g. ドロップダウンからお使いの **プライベート DNS ゾーン** を選択します。

   h. **[OK]** を選択して、ストレージ アカウントの作成の [ネットワーク] タブに戻ります。

4. **[データ保護]** タブで、お使いのストレージ アカウント内の Azure ファイル共有に対する論理的な削除ポリシーを構成します。 既定では、論理的な削除の機能は無効になっています。 論理的な削除の詳細については、[Azure ファイル共有の誤削除を回避する方法](../../../storage/files/storage-files-prevent-file-share-deletion.md)に関するページをご覧ください。

5. **[詳細設定]** タブで、各種のセキュリティ オプションを確認します。

   **[安全な転送が必須]** フィールドは、ストレージ アカウントへの通信でストレージ アカウントが転送中の暗号化を必要とするかどうかを示します。 SMB 2.1 サポートが必要な場合は、このフィールドを無効にする必要があります。 SAP BusinessObjects BI プラットフォームの場合は、**既定値 (有効)** のままにします。

6. 続行してストレージ アカウントを作成します。

ストレージ アカウントの作成方法の詳細については、[FileStorage ストレージ アカウントの作成](../../../storage/files/storage-how-to-create-file-share.md)に関するページを参照してください。

#### <a name="create-azure-file-shares"></a>Azure ファイル共有の作成

次の手順では、ストレージ アカウントに Azure Files を作成します。 Azure Files では、Premium ファイル共有にプロビジョニング モデルが使用されます。 プロビジョニング ビジネス モデルでは、使用量に基づいて請求されるのではなく、Azure Files サービスにストレージ要件を事前に指定します。 このモデルの詳細については、「[プロビジョニング モデル](../../../storage/files/understanding-billing.md#provisioned-model)」を参照してください。 この例では、SAP BOBI ファイル ストア用に、frsinput (256 GB) と frsoutput (256 GB) の 2 つの Azure Files を作成します。

ストレージ アカウント **azusbobi** >  **[ファイル共有]** にアクセスします。

1. **[新しいファイル共有]** を選択します。
2. ファイル共有の **[名前]** (例: frsinput、frsouput) を入力します。
3. **[プロビジョニング済み容量]** で、必要なファイル共有のサイズを挿入します (例: 256 GB)。
4. **[プロトコル]** は **[SMB]** を選択します。
5. **[作成]** を選択します。

## <a name="configure-data-disk-on-windows-virtual-machine"></a>Windows 仮想マシンでのデータ ディスクの構成

このセクションの手順では、次のプレフィックスを使用します。

**[A]** :この手順はすべてのホストに適用されます

### <a name="initialize-a-new-data-disk"></a>新しいデータ ディスクの初期化

SAP BusinessObjects BI アプリケーションには、そのバイナリをインストールできるパーティションが必要です。 SAP BOBI アプリケーションは、オペレーティング システム パーティション (C:) にインストールできますが、デプロイとオペレーティング システムのための十分な領域があることを確認してください。 一時ファイルと Web アプリケーションのために、少なくとも 2 GB の空き領域を用意することをお勧めします。 以上のすべてを考慮して、SAP BOBI インストール バイナリを個別のパーティションに分割することをお勧めします。

この例では、SAP BOBI アプリケーションを別のパーティション (F:) にインストールします。 仮想マシンのプロビジョニング中に接続した Premium SSD ディスクを初期化します。

1. **[A]** 仮想マシン (azuswinboap1 と azuswinboap2) にデータ ディスクが接続されていない場合は、「[データ ディスクの追加](../../windows/attach-managed-disk-portal.md#add-a-data-disk)」で説明されている手順に従って新しいマネージド データ ディスクを接続します。
2. **[A]** マネージド ディスクが仮想マシンに接続されたら、「[新しいデータ ディスクの初期化](../../windows/attach-managed-disk-portal.md#initialize-a-new-data-disk)」で説明されている手順に従ってディスクを初期化します。

### <a name="mount-azure-premium-files"></a>Azure Premium Files のマウント

Azure Files をファイル ストアとして使用するには、マウントする (つまり、ドライブ文字またはマウント ポイント パスを割り当てる) 必要があります。

1. **[A]** Azure ファイル共有をマウントするには、「[Azure ファイル共有をマウントする](../../../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share)」で説明されている手順に従います。

Windows サーバーに Azure ファイル共有をマウントするには、ポート 445 が開いていることを確認します SMB プロトコルでは、TCP ポート 445 が開いている必要があります。ポート 445 がブロックされている場合は、接続が失敗します。 ファイアウォールでポート 445 がブロックされているかどうかは、[トラブルシューティング](../../../storage/files/storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked) ガイドで説明されている `Test-NetConnection` コマンドレットを使用して確認できます。

## <a name="configure-cms-database---azure-sql"></a>CMS データベースの構成 - Azure SQL

このセクションでは、Azure portal を使用して Azure SQL をプロビジョニングする方法について詳しく説明します。 また、SAP BOBI プラットフォーム用の CMS および監査データベースを作成する方法と、データベースにアクセスするためのユーザー アカウントについても説明します。

このガイドラインは、Azure SQL (Azure での DBaaS オファリング) を使用している場合にのみ適用されます。 その他のデータベースの場合、手順については、SAP またはデータベース固有のドキュメントを参照してください。

### <a name="create-sql-database-server"></a>SQL データベース サーバーの作成

Azure SQL データベースには、単一データベース、エラスティック プール、データベース サーバーなど、さまざまなデプロイ オプションが用意されています。 SAP BOBI の場合、2 つのデータベース (CMS と監査) が必要です。 そのため、2 つの単一データベースを作成する代わりに、単一データベースとエラスティック プールのグループを管理できる SQL データベース サーバーを作成できます。  SQL データベース サーバーを作成する手順を次に示します。

1. [[Select SQL Deployment option]\(SQL デプロイ オプションの選択\)](https://portal.azure.com/#create/Microsoft.AzureSQL) ページを参照します。
2. **[SQL データベース]** で、 **[リソースの種類]** を **[データベース サーバー]** に変更して **[作成]** を選択します。
3. **[基本]** タブで、すべての必要なフィールドに入力して **[SQL データベース サーバーを作成]** します。

   a. **[プロジェクトの詳細]** で、 **[サブスクリプション]** と **[リソース グループ]** を選択します。

   b. **[サーバー名]** (例: azussqlbodb) を入力します。 サーバー名はグローバルに一意である必要がありますが、任意の名前を指定できます。

   c. **[場所]** を選択します。

   d. **[サーバー管理者ログイン]** (例: boadmin) と **[パスワード]** を入力します。

4. **[ネットワーク]** タブの **[ファイアウォール規則]** で、 **[Azure のサービスとリソースにこのサーバーへのアクセスを許可する]** を **[いいえ]** に変更します。
5. **[追加設定]** では、既定の設定をそのままにします。
6. 続行して **[SQL データベース サーバー]** を作成します。

次の手順では、CMS および監査データベースを SQL データベース サーバー (azussqlbodb.database.windows.net) 内に作成します。

### <a name="create-the-cms-and-the-audit-database"></a>CMS および監査データベースの作成

SQL データベース サーバーをプロビジョニングした後、リソース **azussqlbodb** を参照し、以下の手順に従って CMS および監査データベースを作成します。

1. azussqlbodb の **[概要]** ページで、 **[データベースの作成]** を選択します。
2. **[基本]** タブで、すべての必須フィールドに入力します。

   a. **[データベース名]** (例: bocms または boaudit) を入力します。

   b. **[コンピューティングとストレージ]** オプションで、 **[データベースの構成]** を選択し、サイズ変更の結果に基づいて適切なモデルを選択します。 オプションの詳細については、「[Azure SQL Database のサイズ設定モデル](businessobjects-deployment-guide.md#sizing-models-for-azure-sql-database)」を参照してください。
3. **[ネットワーク]** タブで、接続方法として [[プライベート エンドポイント]](../../../private-link/tutorial-private-endpoint-sql-portal.md) を選択します。 プライベート エンドポイントは、構成された仮想ネットワーク内の Azure SQL データベースにアクセスするために使用されます。

   a. **[プライベート エンドポイントの追加]** を選択します。

   b. **[サブスクリプション]** 、 **[リソース グループ]** 、 **[場所]** を選択します。

   c. プライベート エンドポイントの **[名前]** (例: azusbodb-pe) を入力します。

   e. **[ターゲット サブリソース]** で **SqlServer** を選択します。

   f. **[ネットワーク]** セクションで、SAP BusinessObjects BI アプリケーションをデプロイする **[仮想ネットワーク]** と **[サブネット]** を選択します。

   g. **[プライベート DNS ゾーンと統合する]** は、**既定値 (はい)** をそのまま使用します。

   h. ドロップダウンからお使いの **プライベート DNS ゾーン** を選択します。

   i. **[OK]** を選択して、SQL データベースの作成の [ネットワーク] タブに戻ります。
4. **[追加設定]** タブで、 **[照合順序]** を **SQL_Latin1_General_CP850_BIN2** に変更します。
5. 続行して CMS データベースを作成します。

同様に、 **[監査]** データベース (例: boaudit) を作成できます。

### <a name="download-and-install-odbc-driver"></a>ODBC ドライバーのダウンロードとインストール

SAP BOBI アプリケーション サーバーから CMS または監査データベースへのアクセスには、データベース クライアントまたはドライバーが必要です。 Microsoft ODBC ドライバーは、Azure SQL データベースで実行されている CMS および監査データベースにアクセスするために使用されます。 このセクションでは、Windows で ODBC ドライバーをダウンロードしてセットアップする方法について説明します。 

1. Azure SQL データベースと互換性のあるデータベース コネクタを探すには、[SAP BusinessObjects BI プラットフォームの製品出荷マトリクス (PAM)](https://support.sap.com/pam) の、**OS による CMS および監査リポジトリのサポート** に関するセクションを参照してください。
2. [リンク](/sql/connect/odbc/windows/release-notes-odbc-sql-server-windows?preserve-view=true&view=sql-server-ver15)から ODBC ドライバーのバージョンをダウンロードします。 この例では、ODBC ドライバー [13.1](/sql/connect/odbc/windows/release-notes-odbc-sql-server-windows?preserve-view=true&view=sql-server-ver15#131) をダウンロードします。
3. すべての BI サーバー (azuswinboap1 および azuswinboap2) に ODBC ドライバーをインストールします。
4. **azuswinboap1** にドライバーをインストールしたら、 **[スタート]**  >  **[Windows 管理ツール]**  >  **[ODBC データ ソース (64 ビット)]** にアクセスします。  
5. **[システム DSN]** タブにアクセスします。 
6. **[追加]** を選択して、CMS データベースへの接続を作成します。
7. **[ODBC Driver 13 for SQL Server]** を選択して、 **[完了]** を選択します。
8. 次のように CMS データベースの情報を入力して **[次へ]** を選択します。
   - **[名前]:** *「CMS および監査データベースの作成」で作成したデータベースの名前* (例: bocms または boaudit)
   - **[説明]:** *データ ソースの説明* (例: "CMS データベース" または "監査データベース")
   - **[サーバー]:** *「SQL データベース サーバーの作成」で作成した SQL サーバーの名前* (例: azussqlbodb.database.windows.net)
9. **[ユーザーが入力する SQL Server 用のログイン ID とパスワードを使う]** を選択して、SQL サーバーに対する信頼性を確認します。 Azure SQL データベース サーバーの作成時に作成したユーザー資格情報 (例: boadmin) を入力して **[次へ]** を選択します。
10. **bocms** に **既定のデータベースを変更** し、それ以外はすべて既定のままにします。 **[次へ]** を選択します。
11. **[データに強力な暗号を使用する]** チェック ボックスをオンにし、それ以外はすべて既定のままにします。 **[完了]** を選択します。
12. CMS データベースのデータ ソースが作成されました。ここで、 **[データ ソースのテスト]** を選択して、BI アプリケーションから CMS データベースへの接続を検証できます。 正常に完了したことを確認します。 失敗した場合は、接続の問題の[トラブルシューティング](../../../azure-sql/database/troubleshoot-common-errors-issues.md)を行います。

>[!Note]
>Azure SQL Database の通信は、ポート 1433 上で行われます。 したがって、ポート 1433 で、SAP BOBI アプリケーション サーバーからの送信トラフィックを許可する必要があります。

上記と同じ手順を繰り返して、サーバー azuswinboap1 に監査データベースの接続を作成します。 同様に、すべての BI アプリケーション サーバー (azuswinboap2) に、両方の ODBC データソース (bocms および boaudit) をインストールして構成します。 

## <a name="server-preparation"></a>サーバーの準備

SAP の最新ガイドに従って、BI プラットフォームのインストールのためにサーバーを準備します。 最新情報については、「[Business Intelligence プラットフォーム インストール ガイド (Windows)](https://help.sap.com/viewer/df8899896b364f6c880112f52e4d06c8/4.3.1/en-US/46b0d1a26e041014910aba7db0e91070.html)」の「事前準備」のセクションを参照してください。

## <a name="installation"></a>インストール

Windows ホストに BI プラットフォームをインストールするには、ローカル管理者特権を持つユーザーでサインインします。

SAP BusinsessObjects BI プラットフォームのメディアにアクセスして `setup.exe` を実行します。

ご使用のバージョンに応じて、「[SAP Business Intelligence プラットフォーム インストール ガイド (Windows)](https://help.sap.com/viewer/df8899896b364f6c880112f52e4d06c8/4.3.1/en-US/46ae62456e041014910aba7db0e91070.html)」に従います。 Windows での SAP BOBI プラットフォームのインストール時に注意する点がいくつかあります。 

- **[インストール先フォルダーの設定]** 画面では、BI プラットフォームのインストール先フォルダーを指定します (F:\SAP BusinessObjects など\)。 

- **[製品登録の設定]** 画面で、SAP ノート [1288121](https://launchpad.support.sap.com/#/notes/1288121) にある SAP BusinessObjects ソリューションの一時ライセンス キーを使用するか、SAP サービス マーケットプレースでライセンス キーを生成することができます。

- **[インストールの種類を選択]** 画面で、最初のサーバー (azuswinboap1) には **[フル]** インストールを選択します。他のサーバー (azuswinboap2) には、既存の BOBI セットアップを拡張する **[カスタム/拡張]** を選択します。

- **[デフォルト データベースまたは既存データベースの選択]** 画面で、 **[既存のデータベースの設定]** を選択します。これにより、CMS および監査データベースを選択するよう求められます。 [CMS データベースの種類] および [監査データベースの種類] には **[Microsoft SQL Server (ODBC 使用)]** を選択します。

  インストール中に監査を構成しない場合は、[監査データベースなし] を選択することもできます。

- **[Java Web アプリケーション サーバーの選択]** 画面で、使用している SAP BOBI アーキテクチャに基づいて適切なオプションを選択します。 この例では、オプション 1 を選択しました。これにより、同じ SAP BOBI プラットフォームに Tomcat サーバーがインストールされます。

- CMS データベースの情報を **[CMS リポジトリ データベースの設定 - SQL Server (ODBC)]** に入力します。 Windows インストールの CMS データベース情報の入力例。
  
  ![Windows の CMS データベース情報](media/businessobjects-deployment-guide/businessobjects-deployment-windows-sql-cms.png)

- (オプション) 監査データベースの情報を **[監査リポジトリ データベースの設定 - SQL Server (ODBC)]** に入力します。 Windows インストールの監査データベース情報の入力例。
  
  ![Windows の監査データベース情報](media/businessobjects-deployment-guide/businessobjects-deployment-windows-sql-audit.png)

- 手順に従って必要な情報を入力し、インストールを完了させます。

複数インスタンス デプロイの場合は、2 番目のホスト (azuswinboap2) に対してインストール セットアップを実行します。 **[インストール タイプの選択]** 画面で、 **[カスタム/拡張]** を選択します。これにより、既存の BOBI セットアップを拡張します。 [Azure SQL DB を使用した SAP BusinessObjects Business Intelligence プラットフォームのセットアップ](https://blogs.sap.com/2020/06/19/sap-on-azure-sap-businessobjects-business-intelligence-platform-setup-with-azure-sql-db-managed-paas-database/)に関する SAP のブログを参照してください。

> [!IMPORTANT]
> SQL Server と Azure SQL Database のデータベース エンジンのバージョン番号は類似のものではありません。別個の製品に与えられる内部製造番号になっています。 Azure SQL Database のデータベース エンジンは SQL Server データベース エンジンと同じコードに基づいています。 最も重要なことですが、Azure SQL Database のデータベース エンジンには常に最新の SQL データベース エンジン ビットが与えられます。 Azure SQL Database のバージョン 12 は SQL Server のバージョン 15 より新しくなります。

現在の Azure SQL データベースのバージョンを確認するには、中央管理コンソール (CMC) の設定をチェックインするか、[sqlcmd](/sql/tools/sqlcmd-utility?preserve-view=true&view=sql-server-ver15) または [SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms?preserve-view=true&view=sql-server-ver15) を使用して次のクエリを実行します。 SQL のバージョンを既定の互換性に合わせる方法については、[データベース互換性レベル](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?preserve-view=true&view=sql-server-ver15)に関する記事を参照してください。

![CMC でのデータベース情報](media/businessobjects-deployment-guide/businessobjects-deployment-windows-sql-cmc.PNG)

```sql
1> select @@version as version;
2> go
version
------------------------------------------------------------------------------------------
Microsoft SQL Azure (RTM) - 12.0.2000.8
        Feb 20 2021 17:51:58
        Copyright (C) 2019 Microsoft Corporation

(1 rows affected)

1> select name, compatibility_level from sys.databases;
2> go
name                                                                  compatibility_level
--------------------------------------------------------------------- -------------------
master                                                                                150
bocms                                                                                 150
boaudit                                                                               150

(3 rows affected)
```

## <a name="post-installation"></a>インストール後

SAP BOBI プラットフォームの複数インスタンスのインストール後に、アプリケーションの高可用性をサポートするには、追加の構成後手順を実行する必要があります。

### <a name="configuring-cluster-name"></a>クラスター名の構成

SAP BOBI プラットフォームの複数インスタンスのデプロイでは、1 つのクラスターで複数の CMS サーバーを一緒に実行します。 クラスターは、共通の CMS システム データベースに対して連携する 2 つ以上の CMS サーバーで構成されます。 CMS で実行されているノードで障害が発生した場合、別の CMS があるノードで BI プラットフォームの要求の処理が続けられます。 SAP BOBI プラットフォームの既定の設定では、クラスター名には、インストールした最初の CMS のホスト名が反映されます。 

Windows でクラスター名を構成するには、「[SAP Business Intelligence プラットフォーム管理者ガイド](https://help.sap.com/viewer/2e167338c1b24da9b2a94e68efd79c42/4.3.1/en-US)」に記載されている手順に従います。 クラスター名を構成した後、SAP ノート [1660440](https://launchpad.support.sap.com/#/notes/1660440) に従って、CMC または BI Launchpad のサインイン ページで既定のシステム エントリを設定します。 

### <a name="configure-input-and-output-filestore-location-to-azure-premium-files"></a>Azure Premium Files に対する入力と出力の Filestore の場所を構成する

Filestore とは、実際の SAP BusinessObjects ファイルが存在するディスク ディレクトリのことです。 SAP BOBI プラットフォーム用のファイル リポジトリ サーバーの既定の場所は、ローカル インストール ディレクトリ内にあります。 複数インスタンスのデプロイでは、すべてのストレージ層サーバーからアクセスできるように、Azure Premium Files や Azure NetApp Files などの共有ストレージに Filestore を設定することが重要です。

1. Azure Premium ファイルが作成されていない場合は、「**Azure Premium ファイルのプロビジョニング**」で説明されている手順に従って作成し、マウントします。

   > [!Tip]
   > 仮想マシンのデプロイ (可用性セットまたは可用性ゾーン) に基づいて、Azure Premium Files (LRS または ZRS) のストレージ冗長性を選択します。

2. SAP ノート [2512660](https://launchpad.support.sap.com/#/notes/0002512660) に従って、ファイル リポジトリ (入力と出力) のパスを変更します。

### <a name="tomcat-clustering---session-replication"></a>Tomcat クラスタリング - セッション レプリケーション

Tomcat を使用すると、セッション レプリケーションとフェールオーバーのために、2 つ以上のアプリケーション サーバーのクラスタリングがサポートされます。 SAP BOBI プラットフォーム セッションはシリアル化されています。アプリケーション サーバーで障害が発生した場合でも、Tomcat の別のインスタンスへのユーザー セッションのシームレスなフェールオーバーが可能です。 たとえば、ユーザーが SAP BI アプリケーションでフォルダー階層内を移動しているときに、接続している Web サーバーに障害が発生した場合などです。 正しく構成されたクラスターでは、ユーザーはサインイン ページにリダイレクトされることなく、フォルダー階層内を移動することができます。

SAP ノート [2808640](https://launchpad.support.sap.com/#/notes/2808640) には、マルチキャストを使用して Tomcat クラスタリングを構成する手順が示されています。 しかし、マルチキャストは Azure でサポートされていません。 そのため、Tomcat クラスターを Azure で機能させるには、[StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) を使用する必要があります (SAP ノート [2764907](https://launchpad.support.sap.com/#/notes/2764907))。 Azure で Tomcat クラスターを設定するには、SAP ブログの「[Tomcat Clustering using Static Membership for SAP BusinessObjects BI Platform](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/)」(SAP BusinessObjects BI プラットフォームの静的なメンバーシップを使用した Tomcat クラスタリング) をご確認ください。

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>SAP BI プラットフォームの Web 層の負荷分散

SAP BOBI の複数インスタンス デプロイの場合、Java Web アプリケーション サーバー (Web 層) が 2 つ以上のホストで実行されています。 Web サーバー間でユーザー負荷を均等に分散するために、エンド ユーザーと Web サーバーの間にロード バランサーを使用できます。 Azure で Azure Load Balancer または Azure Application Gateway を使用して、Web アプリケーション サーバーへのトラフィックを管理できます。 各オファリングの詳細について次のセクションで説明します。

1. [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) は、ハイ パフォーマンスで低遅延のレイヤー 4 (TCP、UDP) ロード バランサーであり、正常な仮想マシン間でトラフィックを分散します。 Load Balancer の正常性プローブによって、各 VM の特定のポートが監視され、稼働している仮想マシンにのみトラフィックが分散されます。 SAP BI プラットフォームをインターネットからアクセスできるようにするかどうかに応じて、パブリック ロード バランサーまたは内部ロード バランサーのいずれかを選択できます。 ゾーン冗長であるため、可用性ゾーン全体で高可用性が確保されます。

   次の図の内部ロード バランサーのセクションを参照してください。Web アプリケーション サーバーは、ポート 8080 (既定の Tomcat HTTP ポート) で実行され、正常性プローブによって監視されます。 そのため、エンド ユーザーからの受信要求は、バックエンド プール内の Web アプリケーション サーバー (azuswinboap1 または azuswinboap2) にリダイレクトされます。 Load Balancer で TLS/SSL 終端 (TLS/SSL オフロードとも呼ばれます) はサポートされていません。 Azure Load Balancer を使用して Web サーバー間でトラフィックを分散する場合は、Standard Load Balancer を使用することをお勧めします。

   > [!NOTE]
   > パブリック IP アドレスのない VM が、内部 (パブリック IP アドレスがない) Standard の Azure Load Balancer のバックエンド プール内に配置されている場合、パブリック エンドポイントへのルーティングを許可するように追加の構成が実行されない限り、送信インターネット接続はありません。 送信接続を実現する方法の詳細については、「[SAP の高可用性シナリオにおける Azure Standard Load Balancer を使用した Virtual Machines のパブリック エンドポイント接続](high-availability-guide-standard-load-balancer-outbound-connections.md)」を参照してください。

   ![Web サーバー間でトラフィックを分散するための Azure Load Balancer](media/businessobjects-deployment-guide/businessobjects-deployment-windows-load-balancer.png)

2. [Azure Application Gateway](../../../application-gateway/overview.md) によって、サービスとしてのアプリケーション デリバリー コントローラー (ADC) が提供されます。これは、アプリケーションでユーザー トラフィックを 1 つ以上の Web アプリケーション サーバーに転送するために使用されます。 TLS/SSL オフロード、Web Application Firewall (WAF)、Cookie ベースのセッション アフィニティなど、レイヤー 7 のさまざまな負荷分散機能がアプリケーションに提供されます。

   Application Gateway によって、SAP BI プラットフォームのアプリケーション Web トラフィックがバックエンド プール (azuswinboap1 または azuswinboap2) 内の指定されたリソースに転送されます。 ユーザーはリスナーをポートに割り当て、ルールを作成し、リソースをバックエンド プールに追加します。 次の図では、プライベート フロントエンド IP アドレス (10.31.3.25) を持つ Application Gateway がユーザーのエントリ ポイントとして機能し、それによって受信 TLS/SSL (HTTPS - TCP/443) 接続が処理され、TLS/SSL の暗号化が解除され、要求 (HTTP - TCP/8080) がバックエンド プール内のサーバーに渡されます。 組み込みの TLS/SSL 終端機能を使用することで、Application Gateway で 1 つの TLS/SSL 証明書を維持するだけで済み、操作が簡単になります。

   ![Web サーバー間でトラフィックを分散するための Application Gateway](media/businessobjects-deployment-guide/businessobjects-deployment-windows-application-gateway.png)

   SAP BOBI Web サーバー用に Application Gateway を構成するには、SAP ブログの「[Load Balancing SAP BOBI Web Servers using Azure Application Gateway](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/)」(Azure Application Gateway を使用した SAP BOBI Web サーバーの負荷分散) を参照してください。

   > [!NOTE]
   > Azure Application Gateway を使用して Web サーバーへのトラフィックの負荷を分散することをお勧めします。それにより、SSL オフロードや、サーバーでの暗号化と解読のオーバーヘッドを削減する SSL 管理の一元化、トラフィック分散のためのラウンドロビン アルゴリズム、Web アプリケーション ファイアウォール (WAF) 機能、高可用性などのような機能が提供されます。

## <a name="sap-businessobjects-bi-platform-reliability-on-azure"></a>Azure での SAP BusinessObjects BI プラットフォームの信頼性

SAP BusinessObjects BI プラットフォームには、特定のタスクと操作用に最適化されたさまざまな階層が含まれています。 いずれか 1 つの層のコンポーネントが使用できなくなると、SAP BOBI アプリケーションがアクセス不能になるか、アプリケーションの特定の機能が動作しなくなります。 そのため、ビジネスの中断なくアプリケーションが稼働し続けるには、各層が高い信頼性を持つように設計されている必要があります。

このガイドでは、Azure のネイティブ機能と SAP BOBI プラットフォームの構成の組み合わせにより、SAP デプロイの可用性を向上させる方法について説明します。 このセクションでは、Azure での SAP BOBI プラットフォームの信頼性に関する次のオプションについて重点的に説明します。

- **バックアップと復元:** データとアプリケーションのコピーを別の場所に定期的に作成するプロセスです。 それにより、元のデータまたはアプリケーションが失われたり破損したりした場合に、以前の状態に復元または復旧できます。

- **高可用性:** 高可用性プラットフォームには、Azure リージョン内のあらゆるものが少なくとも 2 つずつ存在し、いずれかのサーバーが使用できなくなった場合でもアプリケーションが稼働し続けます。
- **ディザスター リカバリー:** 何らかの自然災害によって Azure リージョン全体が使用できなくなるなどの致命的な損失が発生した場合に、アプリケーションの機能を復元するプロセスです。

このソリューションの実装は、Azure のシステム設定の種類によって異なります。 そのため、バックアップと復元、高可用性、ディザスター リカバリー ソリューションは、お客様がビジネス要件に基づいて調整する必要があります。

## <a name="back-up-and-restore"></a>バックアップと復元

バックアップと復元は、データとアプリケーションのコピーを別の場所に定期的に作成するプロセスです。 それにより、元のデータまたはアプリケーションが失われたり破損したりした場合に、以前の状態に復元または復旧できます。 また、それはビジネス ディザスター リカバリー戦略の不可欠な要素でもあります。 これらのバックアップにより、構成された保有期間内の特定の時点にアプリケーションとデータベースを復元できます。

SAP BOBI プラットフォームの包括的なバックアップと復元の戦略を策定するには、アプリケーションのシステム ダウンタイムや中断につながるコンポーネントを特定します。 SAP BOBI プラットフォームでは、アプリケーションを保護するために、次のコンポーネントのバックアップが不可欠です。

- SAP BOBI インストール ディレクトリ (マネージド Premium ディスク)
- Filestore (分散インストール用の Azure Premium Files または Azure NetApp Files)
- CMS および監査データベース (Azure SQL データベース、Azure Database for MySQL、または Azure 仮想マシン上のデータベース)

次のセクションでは、SAP BOBI プラットフォームの各コンポーネントのバックアップと復元の戦略を実装する方法について説明します。

### <a name="backup--restore-for-sap-bobi-installation-directory"></a>SAP BOBI インストール ディレクトリのバックアップと復元

Azure において、すべての接続されているディスクを含めた仮想マシンをバックアップする最も簡単な方法は、[Azure VM バックアップ](../../../backup/backup-azure-vms-introduction.md) サービスを使用することです。 それにより、VM 上のデータが誤って破壊されることを防ぐために、独立した、分離されたバックアップが提供されます。 バックアップは、復旧ポイントの管理機能をビルトインで備えた Recovery Services コンテナーに格納されます。 構成とスケーリングはシンプルで、バックアップは最適化され、必要に応じて簡単に復元することができます。

バックアップ プロセスの一環として、スナップショットが取得され、運用環境のワークロードに影響を与えることなく、データが Recovery Services コンテナーに転送されます。 スナップショットによって、「[スナップショットの整合性](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency)」の記事で説明されているように、さまざまなレベルの一貫性が提供されます。 Azure Backup では、[Azure VM バックアップ](../../../backup/backup-azure-vms-introduction.md) ソリューションに加えて、[Azure ディスク バックアップ](../../../backup/disk-backup-overview.md)を使用したマネージド ディスクのバックアップの並列サポートも提供されます。 これは、仮想マシンの整合性バックアップが 1 日 1 回必要であり、OS ディスクまたは特定のデータ ディスクのより頻繁なクラッシュ整合性バックアップも行う必要がある場合に便利です。 詳細については、[Azure VM バックアップ](../../../backup/backup-azure-vms-introduction.md)、[Azure ディスク バックアップ](../../../backup/disk-backup-overview.md)、および [Azure VM のバックアップの FAQ](../../../backup/backup-azure-vm-backup-faq.yml) に関するページを参照してください。

### <a name="backup--restore-for-filestore"></a>Filestore のバックアップと復元

SAP BOBI プラットフォームの Filestore は、デプロイに応じて Azure NetApp Files または Azure Files にすることができます。 Filestore に使用するストレージに基づいて、バックアップと復元に関する次のオプションを選択します。

1. **Azure NetApp Files** に対しては、オンデマンドのスナップショットを作成し、スナップショット ポリシーを使用して自動スナップショット作成のスケジュールを設定できます。 スナップショット コピーによって、ANF ボリュームの特定時点のコピーが提供されます。 詳細については、「[Azure NetApp Files を使用して、スナップショットを管理する](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md)」を参照してください。
2. **Azure Files** バックアップは、ネイティブの [Azure Backup](../../../backup/backup-overview.md) サービスと統合されています。これにより、バックアップと復元の機能が VM のバックアップと共に一元化され、運用作業が簡単になります。 詳細については、「[Azure ファイル共有のバックアップについて](../../../backup/azure-file-share-backup-overview.md)」と[よくあるご質問 (Azure Files のバックアップ)](../../../backup/backup-azure-files-faq.yml) に関するページを参照してください。

NFS サーバーを別に作成した場合は、バックアップと復元の戦略が同じように実装されていることを確認します。

### <a name="backup--restore-for-cms-and-audit-database"></a>CMS および監査データベースのバックアップと復元

Windows 仮想マシン上で実行されている SAP BOBI プラットフォームの場合、Azure での SAP BusinessObjects BI プラットフォームの計画および実装ガイドの[サポート マトリックス](businessobjects-deployment-guide.md#support-matrix)に記載されている、任意のサポートされているデータベースで、CMS と監査データベースを実行できます。 そのため、CMS および監査データ ストアに使用されているデータベースに基づいて、バックアップと復元の戦略を採用することが重要です。

1. **Azure SQL データベース** では、SQL Server 技術を利用して、[完全バックアップ](/sql/relational-databases/backup-restore/full-database-backups-sql-server?preserve-view=true&view=sql-server-ver15)を毎週、[差分バックアップ](/sql/relational-databases/backup-restore/differential-backups-sql-server?preserve-view=true&view=sql-server-ver15)を 12 ～ 24 時間ごと、そして[トランザクション ログ](/sql/relational-databases/backup-restore/transaction-log-backups-sql-server?preserve-view=true&view=sql-server-ver15) バックアップを 5 ～ 10 分ごとに作成します。 トランザクション ログ バックアップの頻度は、コンピューティング サイズとデータベース アクティビティの量に基づいて決まります。 ユーザーは、ローカル冗長、ゾーン冗長、または geo 冗長の各ストレージ BLOB 間でバックアップ ストレージの冗長性を構成するオプションを選択できます。 ストレージの冗長性メカニズムでは、計画されたイベントや計画外のイベント (一時的なハードウェア障害、ネットワークの停止や停電、大規模な自然災害など) から保護するために、データのコピーが複数格納されます。 Azure SQL データベースでは既定で、[ペアになっているリージョン](../../../best-practices-availability-paired-regions.md)にレプリケートされる geo 冗長[ストレージ BLOB](../../../storage/common/storage-redundancy.md) にバックアップが格納されます。 これは、ビジネスの要件に応じて、ローカル冗長またはゾーン冗長のストレージ BLOB に変更できます。 Azure SQL データベースのバックアップのスケジュール、保有期間、ストレージ使用量に関する最新情報は、「[自動バックアップ - Azure SQL Database および SQL Managed Instance](../../../azure-sql/database/automated-backups-overview.md)」を参照してください。

2. **Azure Database of MySQL** では、サーバーのバックアップを自動的に作成し、ユーザーが構成したローカル冗長または geo 冗長ストレージに保存します。 Azure Database for MySQL によって、データ ファイルとトランザクション ログのバックアップが作成されます。 サポートされている最大ストレージ サイズに応じて、完全バックアップと差分バックアップ (最大 4 TB のストレージ サーバー) またはスナップショット バックアップ (最大 16 TB のストレージ サーバー) が作成されます。 これらのバックアップを使用すると、サーバーを、構成済みのバックアップ保持期間内の任意の時点に復元できます。 既定のバックアップ保有期間は 7 日間です。これは最大 35 日間まで[必要に応じて構成](../../../mysql/howto-restore-server-portal.md#set-backup-configuration)できます。 すべてのバックアップが、AES 256 ビット暗号化を使用して暗号化されます。 これらのバックアップ ファイルはユーザーに公開されておらず、エクスポートできません。 これらのバックアップは、Azure Database for MySQL の復元操作にのみ使用できます。 [mysqldump](../../../mysql/concepts-migrate-dump-restore.md) を使用して、データベースをコピーできます。 詳細については、「[Azure Database for MySQL でのバックアップと復元](../../../mysql/concepts-backup.md)」を参照してください。

3. Azure 仮想マシンにインストールされているデータベースの場合、サポートされているデータベース用の、標準のバックアップ ツールまたは [Azure Backup](../../../backup/sap-hana-db-about.md) サービスを使用できます。 また、Azure のサービスとツールが要件を満たさない場合は、すべての SAP BOBI プラットフォーム コンポーネントのバックアップと復旧のためのエージェントが用意されている、サードパーティ製のバックアップ ツールを使用できます。

## <a name="high-availability"></a>高可用性

高可用性は、IT の中断を最小限に抑えることができる一連のテクノロジを表し、同じデータ センター内の冗長性、フォールト トレランス、またはフェールオーバーで保護されたコンポーネントを通じてアプリケーションやサービスのビジネス継続性を提供することで実現されます。 ここの例では、データ センターは 1 つの Azure リージョン内にあります。 このセクションの手順を補完する [SAP のための高可用性アーキテクチャとシナリオ](sap-high-availability-architecture-scenarios.md)に関する記事から、Azure に用意されている SAP アプリケーション向けのさまざまな高可用性の手法と推奨事項についての情報が提供されます。

SAP BOBI プラットフォームのサイズ設定の結果に基づいて、ランドスケープを設計し、Azure Virtual Machines およびサブネット全体での BI コンポーネントの分散を決定する必要があります。 分散アーキテクチャの冗長性レベルは、ビジネスに必要な目標復旧時間 (RTO) と目標復旧時点 (RPO) によって異なります。 SAP BOBI プラットフォームには異なる複数の層が含まれており、各層のコンポーネントを、冗長性を実現するように設計する必要があります。 それにより、どれか 1 つのコンポーネントで障害が発生した場合に SAP BOBI アプリケーションが中断されることはほとんど、またはまったくありません。 たとえば、次のように入力します。

- BI アプリケーション サーバーや Web サーバーなどの冗長アプリケーション サーバー
- CMS データベース、Filestore、ロード バランサーなどの固有のコンポーネント

次のセクションでは、SAP BOBI プラットフォームの各コンポーネントで高可用性を実現する方法について説明します。

### <a name="high-availability-for-application-servers"></a>アプリケーション サーバーの高可用性

BI および Web アプリケーション サーバーの場合、個別に (または一緒に) インストールされているかどうかにかかわらず、特定の高可用性ソリューションは必要ありません。 さまざまな Azure 仮想マシンに BI および Web サーバーの複数のインスタンスを構成することで、冗長性による高可用性を実現できます。 この仮想マシンは、ビジネスに必要な RTO に応じて、[可用性セット](sap-high-availability-architecture-scenarios.md#multiple-instances-of-virtual-machines-in-the-same-availability-set)または[可用性ゾーン](sap-high-availability-architecture-scenarios.md#azure-availability-zones)のどちらかにデプロイできます。 可用性ゾーン間のデプロイに関しては、SAP BOBI プラットフォームの他のすべてのコンポーネントもゾーン冗長になるように設計されていることを確認してください。

現在、すべての Azure リージョンで可用性ゾーンが提供されているわけではないため、リージョンに応じたデプロイ戦略を採用する必要があります。 ゾーンを提供する Azure リージョンの一覧は、[Azure Availability Zones](../../../availability-zones/az-overview.md) に関するページにあります。

> [!Important]
> Azure Availability Zones と Azure 可用性セットの概念は、相互に排他的です。 つまり、2つまたはそれ以上の VM を、特定の可用性ゾーンまたは Azure 可用性セットのどちらか一方にデプロイできますが、 両方にすることはできません。

### <a name="high-availability-for-cms-database"></a>CMS データベースの高可用性

Azure Database as a Service (DBaaS) サービスを CMS および監査データベースとして使用している場合は、ローカル冗長高可用性フレームワークが既定で提供されています。 ユーザーは、高可用性、冗長性、回復性の各機能が最初から備わっているリージョンとサービスを選択するだけで済み、追加のコンポーネントを構成する必要がありません。 SAP BOBI プラットフォームのデプロイ戦略が可用性ゾーンをまたがっている場合は、CMS および監査データベースに対してゾーン冗長を実現する必要があります。 Azure でサポートされている DBaaS オファリング用の高可用性オファリングの詳細については、[Azure SQL Database の高可用性](../../../azure-sql/database/high-availability-sla.md)に関する記事、および「[Azure Database for MySQL での高可用性](../../../mysql/concepts-high-availability.md)」を参照してください。 

CMS データベースのその他の DBMS デプロイについては、[SAP ワークロードのための DBMS デプロイ ガイド](dbms_guide_general.md)のページを参照してください。さまざまな DBMS デプロイと、それぞれの高可用性を実現する方法に関する情報が提供されています。

### <a name="high-availability-for-filestore"></a>Filestore の高可用性

Filestore とは、レポート、ユニバース、接続などの内容が格納されているディスク ディレクトリのことです。 それは、そのシステムのすべてのアプリケーション サーバー間で共有されます。 そのため、他の SAP BOBI プラットフォーム コンポーネントと共に、高可用性が確保されるようにする必要があります。

Windows で実行されている SAP BOBI プラットフォームの場合、高可用性と高耐久性を実現するように設計された [Azure Premium Files](../../../storage/files/storage-files-introduction.md) または [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) を Filestore のために選択できます。 Azure Premium Files では、ゾーン冗長ストレージをサポートしています。これは、SAP BOBI プラットフォームのクロスゾーン デプロイに役立つことがあります。 詳細については、Azure Files の「[冗長性](../../../storage/files/storage-files-planning.md#redundancy)」セクションを参照してください。

ファイル共有サービスはすべてのリージョンで使用できるわけではないため、最新情報については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/en-us/global-infrastructure/services/)」のサイトを参照してください。 お客様のリージョンでサービスを利用できない場合は、NFS サーバーを作成し、そのファイル システムを SAP BOBI アプリケーションと共有できます。 しかし、高可用性を考慮する必要もあります。

### <a name="high-availability-for-load-balancer"></a>ロード バランサーの高可用性

Web サーバー全体でトラフィックを分散するには、Azure Load Balancer または Azure Application Gateway のいずれかを使用できます。 デプロイ用に選択した SKU に基づいて、どちらのロード バランサーの冗長性も実現できます。

1. Azure Load Balancer の場合、Standard Load Balancer フロントエンドをゾーン冗長として構成することで冗長性を実現できます。 詳細については、「[Standard Load Balancer と可用性ゾーン](../../../load-balancer/load-balancer-standard-availability-zones.md)」を参照してください。
2. Application Gateway の場合、デプロイ中に選択した層の種類に基づいて高可用性を実現できます。
   1. v1 SKU を使用すると、2 つ以上のインスタンスをデプロイした場合に高可用性シナリオがサポートされます。 Azure は、これらのインスタンスを更新ドメインと障害ドメインに分散させ、全インスタンスで同時に障害が発生しないようにします。 そのため、この SKU によって、ゾーン内で冗長性を実現できます。
   2. v2 SKU を使用すると、新しいインスタンスが障害ドメインと更新ドメインに自動的に分散されます。 ゾーン冗長を選択した場合は、ゾーン障害回復性を提供するために、最新のインスタンスが可用性ゾーンにも分散されます。 詳細については、「[自動スケーリングとゾーン冗長 Application Gateway v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md)」を参照してください。

### <a name="reference-high-availability-architecture-for-sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI プラットフォームの高可用性の参照アーキテクチャ

次の参照アーキテクチャでは、Windows サーバーで実行されている可用性ゾーン間での SAP BOBI プラットフォームのセットアップについて説明します。 このアーキテクチャには、Azure Application Gateway、Azure Premium Files (Filestore)、Azure SQL データベース (CMS および監査データベース) など、SAP BOBI プラットフォーム向けのさまざまな Azure サービスが使用されています。組み込みのゾーン冗長性が提供されるため、異なる複数の高可用性ソリューションを管理する複雑さが軽減されます。

次の図では、複数の可用性ゾーンにまたがる Azure Application Gateway v2 SKU を使用して、受信トラフィック (HTTPS - TCP/443) を負荷分散しています。 アプリケーション ゲートウェイでは、可用性ゾーン間で分散している Web サーバー間にユーザー要求を分散させます。 Web サーバーでは、可用性ゾーンをまたいで別々の仮想マシンにデプロイされている管理および処理サーバー インスタンスに要求を転送します。 ゾーン冗長ストレージを備える Azure Premium Files は、レポート、ユニバース、接続などのコンテンツにアクセスするために、プライベート リンクを介して、管理およびストレージ層の仮想マシンに接続されます。 アプリケーションがアクセスする CMS および監査データベースは、Azure リージョン内の複数の物理的な場所にまたがってデータベースをレプリケートする、ゾーン冗長 Azure SQL データベース (DBaaS) で実行されています。

![Windows 上の SAP BOBI プラットフォームの高可用性アーキテクチャ](media/businessobjects-deployment-guide/businessobjects-deployment-windows-high-availability-availability-zone.png)

上記のアーキテクチャは、Azure での SAP BOBI のデプロイを実行する分析情報を明解に示しています。 ただし、Azure 上の SAP BOBI プラットフォームでの可能なすべての構成オプションを網羅しているわけではありません。 お客様は、Load Balancer、ファイル リポジトリ サーバー、DBMS などのコンポーネントとして異なる製品やサービスを選択することにより、ビジネス要件に基づいてデプロイを調整できます。

選択したリージョンで可用性ゾーンが使用できない場合は、可用性セットに Azure 仮想マシンをデプロイできます。 Azure では、可用性セット内に配置された VM は、複数の物理サーバー、コンピューティング ラック、ストレージ ユニット、およびネットワーク スイッチ間で実行されます。 そのため、ハードウェアまたはソフトウェアの障害が発生しても、影響を受けるのは VM のサブセットだけであり、ソリューション全体は動作状態を維持します。

## <a name="disaster-recovery"></a>障害復旧

このセクションの手順では、SAP BOBI プラットフォームのディザスター リカバリー保護を用意する方法について説明します。 それにより、SAP のディザスター リカバリー アプローチ全体の主要なリソースを表す [SAP のディザスター リカバリー](../../../site-recovery/site-recovery-sap.md) ドキュメントを補完します。 SAP BusinessObjects BI プラットフォームの場合は、SAP ノート [2056228](https://launchpad.support.sap.com/#/notes/2056228) を参照してください。ここでは、DR 環境を安全に実装するための方法について説明されています。

 1. プライマリ システムからコンテンツを昇格および配布するための、ライフサイクル管理 (LCM) またはフェデレーションの完全または選択的な使用。
 2. CMS と FRS の内容の定期的なコピー。

このガイドでは、DR 環境を実装する 2 番目のオプションについて説明します。 ディザスター リカバリーに使用できるすべての構成オプションを網羅した一覧については説明しませんが、Azure のネイティブ サービスと SAP BOBI プラットフォームの構成の組み合わせを使用するソリューションについて説明します。

>[!Important]
>SAP BusinessObjects BI プラットフォームの各コンポーネントの可用性をセカンダリ リージョンに織り込む必要があり、ディザスター リカバリー戦略全体を徹底的にテストする必要があります。

### <a name="reference-disaster-recovery-architecture-for-sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI プラットフォームのディザスター リカバリーの参照アーキテクチャ

この参照アーキテクチャにおいては、冗長なアプリケーション サーバーを使用した SAP BOBI プラットフォームの複数インスタンス デプロイが実行されています。 ディザスター リカバリーの場合、SAP BOBI プラットフォームのすべてのコンポーネントをセカンダリ リージョンにフェールオーバーする必要があります。 次の図では、Azure Premium Files が Filestore として使用され、Azure SQL データベースが CMS および監査リポジトリとして使用され、Azure Application Gateway がトラフィックの負荷分散に使用されています。 各コンポーネントのディザスター リカバリー保護を実現する方法は異なります。詳細については、次のセクションで説明します。

![SAP BusinessObjects BI プラットフォームのディザスター リカバリー (Windows)](media\businessobjects-deployment-guide\businessobjects-deployment-windows-disaster-recovery.png)

### <a name="load-balancer"></a>Load Balancer

Load Balancer は、SAP BOBI プラットフォームの Web アプリケーション サーバー間でトラフィックを分散するために使用されます。 Azure では、Azure Load Balancer または Azure Application Gateway を使用して、Web サーバー間にトラフィックを負荷分散できます。 ロード バランサー サービスの DR を実現するには、セカンダリ リージョンに別の Azure Load Balancer または Azure Application Gateway を実装する必要があります。 DR フェールオーバー後に同じ URL を保持するには、セカンダリ リージョンで実行されている負荷分散サービスを指すように、DNS のエントリを変更する必要があります。

### <a name="virtual-machines-running-web-and-bi-application-servers"></a>Web および BI アプリケーション サーバーを実行している仮想マシン

[Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) サービスを使用して、Web および BI アプリケーション サーバーを実行している仮想マシンをセカンダリ リージョン上でレプリケートできます。 災害や障害が発生したときに、レプリケートされた環境に簡単にフェールオーバーして作業を続けることができるように、サーバーとそれにアタッチされているすべてのマネージド ディスクがセカンダリ リージョンにレプリケートされます。 Azure ディザスター リカバリー データ センターへのすべての SAP アプリケーション仮想マシンのレプリケートを開始するには、「[仮想マシンを Azure にレプリケートする](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md)」セクションの手順に従います。

### <a name="filestore"></a>Filestore

Filestore は、レポートや BI ドキュメントなどの実際のファイルが格納されているディスク ディレクトリです。 Filestore 内のすべてのファイルが DR リージョンと同期されていることが重要です。 Windows で実行されている SAP BOBI プラットフォームに使用しているファイル共有サービスの種類に基づいて、コンテンツを同期するために必要な DR 戦略を採用する必要があります。

- **Azure Premium Files** を使用する場合、ローカル冗長 (LRS) およびゾーン冗長ストレージ (ZRS) のみがサポートされます。 Azure Premium Files の DR 戦略を使用する場合、[AzCopy](../../../storage/common/storage-use-azcopy-v10.md) または [Azure PowerShell](/powershell/module/az.storage/?preserve-view=true&view=azps-5.8.0) を使用して、異なるリージョンの別のストレージ アカウントにファイルをコピーすることができます。 詳細については、「[ディザスター リカバリーとストレージ アカウントのフェールオーバー](../../../storage/common/storage-disaster-recovery-guidance.md)」を参照してください。

- **Azure NetApp Files** によって NFS および SMB ボリュームが提供されるため、ファイルベースの任意のコピー ツールを使用して、Azure リージョン間でデータをレプリケートできます。 別のリージョンにある ANF ボリュームをコピーする方法の詳細については、[Azure NetApp Files に関するよくあるご質問](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)のページを参照してください。

  Azure NetApp Files リージョン間レプリケーションを使用できます。これは現在[プレビュー](https://azure.microsoft.com/en-us/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/)段階であり、NetApp SnapMirror® テクノロジが活用されています。 そのため、変更されたブロックだけが、圧縮された効率的な形式でネットワーク経由で送信されます。 この独自のテクノロジによってリージョン間でのレプリケーションに必要なデータ量が最小化されることで、データ転送コストが削減されます。 また、レプリケーションにかかる時間が短縮されるため、より小さい回復ポイントの目標 (RPO) を実現できます。 詳細については、「[リージョン間レプリケーションを使用するための要件と考慮事項](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md)」を参照してください。

### <a name="cms-database"></a>CMS データベース

DR リージョンの CMS および監査データベースは、プライマリ リージョンで実行されているデータベースのコピーである必要があります。 データベースの種類に応じて、ビジネスに必要な RTO と RPO に基づいて、データベースを DR リージョンにコピーすることが重要です。 Windows で動作する SAP BOBI アプリケーションでは、Azure の "サービスとしてのデータベース" (DBaaS) サービスがサポートされています。このセクションでは、それらの各サービスで使用できるさまざまなオプションについて説明します。

#### <a name="azure-sql-database"></a>Azure SQL データベース

[Azure SQL Database](../../../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md) の DR 戦略には、データベースをセカンダリ リージョンにコピーするために使用できる 2 つのオプションがあります。 どちらの復旧オプションも、異なったレベルの RTO と RPO を提供します。 各復旧オプションの RTO と RPO の詳細については、「[既存のサーバーにデータベースを復旧する](../../../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md#recover-a-database-to-the-existing-server)」を参照してください。

1. [geo 冗長データベースのバックアップの復元](../../../azure-sql/database/recovery-using-backups.md#geo-restore)

   Azure SQL データベースでは既定で、[ペアになっているリージョン](../../../best-practices-availability-paired-regions.md)にレプリケートされる geo 冗長[ストレージ BLOB](../../../storage/common/storage-redundancy.md) にデータが格納されます。 SQL Database の場合、バックアップ ストレージの冗長性は、CMS および監査データベースの作成時に構成することも、既存のデータベースに対して更新することもできます。既存のデータベースに対する変更は、それ以降のバックアップにのみ適用されます。 geo レプリケートされた最新のバックアップから任意の Azure リージョン内の任意の Azure SQL データベースでデータベースを復元することができます。 geo リストアでは、geo レプリケートされたバックアップをソースとして使用します。 ただし、バックアップが取得される時刻と、別のリージョンの Azure BLOB にその差分バックアップが geo レプリケートされる時刻には時間差があります。 その結果、復元されたデータベースは、元のデータベースより最大 1 時間遅れることがあります。

   >[!Important]
   >geo リストアは、geo 冗長[バックアップ ストレージ](../../../azure-sql/database/automated-backups-overview.md#backup-storage-redundancy)を使用して構成された Azure SQL データベースでのみ利用できます。

2. [geo レプリケーション](../../../azure-sql/database/active-geo-replication-overview.md)または[自動フェールオーバー グループ](../../../azure-sql/database/auto-failover-group-overview.md)

   geo レプリケーションは Azure SQL Database の機能です。これを使用すると、同じまたは異なるリージョン内のサーバー上に、個々のデータベースの読み取り可能なセカンダリ データベースを作成することができます。 CMS または監査データベースで geo レプリケーションが有効な場合、アプリケーションは別の Azure リージョンにあるセカンダリ データベースへのフェールオーバーを開始できます。 geo レプリケーションは個々のデータベースに対しては有効ですが、SAP BOBI アプリケーション用の複数のデータベース (CMS および監査) の透過的で調整されたフェールオーバーを有効にするには、自動フェールオーバー グループを使用することをお勧めします。 それにより、アクティブ geo レプリケーションの上位にグループのセマンティックが提供されます。これは、個々のデータベースではなく Azure SQL サーバー全体 (すべてのデータベース) が他のリージョンにレプリケートされることを意味します。 「[geo レプリケーションとフェールオーバー グループを比較する](../../../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md#compare-geo-replication-with-failover-groups)」の機能の表を確認してください。

   自動フェールオーバー グループには、フェールオーバー中にそのまま残る読み取り/書き込みおよび読み取り専用リスナー エンドポイントが用意されています。 読み取り/書き込みエンドポイントは、CMS および監査データベースの ODBC 接続エントリでリスナーとして維持できます。 そのため、手動または自動フェールオーバーのどちらをアクティブ化するにしても、フェールオーバーはグループ内のすべてのデータベースをプライマリに切り替えます。 データベースのフェールオーバーが完了した後、DNS レコードが自動的に更新され、エンドポイントが新しいリージョンにリダイレクトされます。 これにより、読み取り/書き込みエンドポイントは ODBC 接続でリスナーとして維持されるため、アプリケーションは自動的に CMS データベースに接続されます。

   次の図では、"米国東部 2" リージョンで実行されている Azure SQL サーバー (azussqlbodb) の自動フェールオーバー グループを、"米国東部" セカンダリ リージョン (DR サイト) にレプリケートしています。 Windows で実行されている BI アプリケーション サーバーのために、読み取り/書き込みリスナー エンドポイントは ODBC 接続でリスナーとして維持されます。 フェールオーバー後もエンドポイントは同じままであり、BI アプリケーションをセカンダリ リージョンの Azure SQL データベースに接続するための手動介入は必要ありません。

   ![Azure SQL データベースの自動フェールオーバー グループ](media\businessobjects-deployment-guide\businessobjects-deployment-windows-sql-failover-group.png)

   このオプションは、オプション 1 よりも低い RTO と RPO を提供します。 このオプションの詳細については、「[自動フェールオーバー グループを使用して、複数のデータベースの透過的な調整されたフェールオーバーを有効にする](../../../azure-sql/database/auto-failover-group-overview.md)」を参照してください。

#### <a name="azure-database-for-mysql"></a>Azure Database for MySQL

Azure Database for MySQL には、障害が発生した場合にデータベースを復旧するための複数のオプションが用意されています。 ご自身のビジネスに適したオプションを選択してください。

1. 事業継続とディザスター リカバリーの計画を強化するには、リージョンにまたがる読み取りレプリカを有効にします。 ソース サーバーから最大 5 つのレプリカにレプリケートできます。 読み取りレプリカは、MySQL のバイナリ ログ レプリケーション テクノロジを使用して非同期的に更新されます。 レプリカは、通常の Azure Database for MySQL サーバーと同様に管理する新しいサーバーです。 読み取りレプリカ、利用可能なリージョン、制限、フェールオーバーする方法については、[読み取りレプリカの概念に関する記事](../../../mysql/concepts-read-replicas.md)を参照してください。

2. geo 冗長バックアップを使ってサーバーを復元する Azure Database for MySQL の geo リストア機能を使用します。 これらのバックアップは、サーバーがホストされているリージョンがオフラインのときでもアクセスできます。 これらのバックアップから他の任意のリージョンに復元し、サーバーをオンラインに戻すことができます。

  > [!Important]
  > geo リストアは、geo 冗長バックアップ ストレージでサーバーをプロビジョニングした場合にのみ可能です。 サーバー作成後に **[バックアップ冗長オプション]** を変更することはサポートされていません。 詳細については、[バックアップの冗長性](../../../mysql/concepts-backup.md#backup-redundancy-options)に関する記事を参照してください。

この例で使用される各層のディザスター リカバリーの推奨事項は次のとおりです。

| SAP BOBI プラットフォームの層                          | 推奨                                               |
| ------------------------------------------------ | ------------------------------------------------------------ |
| Azure Application Gateway または Azure Load Balancer | セカンダリ リージョンでの Application Gateway の並列セットアップ    |
| Web アプリケーション サーバー                          | Site Recovery を使ったレプリケーション                             |
| BI アプリケーション サーバー                           | Site Recovery を使ったレプリケーション                             |
| Azure Premium Files                              | AzCopy **または** Azure PowerShell                               |
| Azure NetApp Files                               | セカンダリ リージョンにデータをレプリケートするためのファイルベースのコピー ツール **または** ANF リージョン間レプリケーション (プレビュー) |
| Azure SQL データベース                               | geo レプリケーション/自動フェールオーバー グループ **または** geo リストア。     |
| Azure Database for MySQL                         | リージョン間の読み取りレプリカ **または** geo 冗長バックアップからのバックアップの復元。 |
## <a name="next-steps"></a>次の手順

- [多層 SAP アプリ デプロイのディザスター リカバリーを設定する](../../../site-recovery/site-recovery-sap.md)
- [SAP のための Azure Virtual Machines の計画と実装](planning-guide.md)
- [SAP のための Azure Virtual Machines のデプロイ](deployment-guide.md)
- [SAP のための Azure Virtual Machines DBMS のデプロイ](./dbms_guide_general.md)