---
title: Azure の Windows 向け SAP BusinessObjects BI プラットフォームのデプロイ | Microsoft Docs
description: Azure 上に Windows 向け SAP BusinessObjects BI プラットフォームをデプロイして構成します。
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,azure-sql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/08/2021
ms.author: depadia
ms.openlocfilehash: d230c000eedf4cb6a4d01b7dc20f48e5712862c4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130225088"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-windows-on-azure"></a>Azure の Windows 向け SAP BusinessObjects BI プラットフォーム デプロイ ガイド

この記事では、Azure 上に Windows 向け SAP BusinessObjects Business Intelligence (SAP BOBI) プラットフォームをデプロイする戦略について説明します。 この例では、Premium SSD マネージド ディスクをインストール ディレクトリとする 2 つの仮想マシン (VM) が構成されます。 サービスとしてのプラットフォーム (PaaS) サービスである Azure SQL Database は、中央管理サーバー (CMS) および監査データベースに使用されます。 SMB プロトコルである Azure Premium Files は、両方の VM 間で共有されるファイル ストアとして使用されます。 既定の Tomcat Java Web アプリケーションとビジネス インテリジェンス (BI) プラットフォーム アプリケーションは、両方の VM に一緒にインストールされます。 ユーザー要求の負荷を分散するために、ネイティブの TLS/SSL オフロード機能を備えた Azure Application Gateway が使用されます。

この種類のアーキテクチャは、小規模なデプロイまたは非運用環境で効果的です。 運用環境または大規模デプロイの場合は、Web アプリケーション用のホストを分離する必要があります。また、複数の SAP BOBI アプリケーション ホストを使用すると、サーバーでより多くの情報を処理できます。

![Azure の Windows 向け SAP BOBI のデプロイを示す図。](media/businessobjects-deployment-guide/businessobjects-deployment-windows.png)

この例では、次の製品バージョンとファイル システム レイアウトが使用されます。

- SAP BusinessObjects プラットフォーム 4.3 SP01 Patch 1
- Windows Server 2019
- SQL Database (バージョン 12.0.2000.8)
- Microsoft ODBC ドライバー - msodbcsql.msi (バージョン: 13.1)

| ファイル システム        | 説明                                                                                                               | サイズ (GB)             | 必要なアクセス  | Storage                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|---------------|----------------------------|
| F:          | SAP BOBI インスタンス、既定の Tomcat Web アプリケーション、データベース ドライバー (必要な場合) をインストールするためのファイル システム。 | SAP のサイズ設定のガイドライン | ローカル管理者特権 | Azure Premium SSD マネージド ディスク|
| \\\azusbobi.file.core.windows.net\frsinput  | このマウント ディレクトリは、インプット ファイルストア ディレクトリとして使用されるすべての SAP BOBI ホスト間の共有ファイル用です。 | ビジネス ニーズ         | ローカル管理者特権 | Azure NetApp Files         |
| \\\azusbobi.file.core.windows.net\frsoutput | このマウント ディレクトリは、アウトプット ファイルストア ディレクトリとして使用されるすべての SAP BOBI ホスト間の共有ファイル用です。 | ビジネス ニーズ         | ローカル管理者特権 | Azure NetApp Files         |

## <a name="deploy-a-windows-virtual-machine-via-the-azure-portal"></a>Azure portal を使用した Windows 仮想マシンのデプロイ

このセクションでは、Windows オペレーティング システム (OS) イメージを使用して SAP BOBI プラットフォーム用の VM を 2 つ作成します。 VM を作成する手順の概要は次のとおりです。

1. [リソース グループ](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)を作成します。

1. [仮想ネットワークを作成します](../../../virtual-network/quick-create-portal.md#create-a-virtual-network)。

   - SAP BI プラットフォームのデプロイでは、すべての Azure サービスで 1 つのサブネットを使用しないでください。 SAP BI プラットフォームのアーキテクチャに基づいて、複数のサブネットを作成することが必要な場合があります。 このデプロイでは、BI アプリケーション サブネットと Application Gateway サブネットの 2 つのサブネットを作成します。
   - SAP ノート [2276646](https://launchpad.support.sap.com/#/notes/2276646) に従って、各種コンポーネント間の SAP BOBI プラットフォーム通信用のポートを特定します。
   - SQL Database の通信は、ポート 1433 上で行われます。 ポート 1433 で、SAP BOBI アプリケーション サーバーからの送信トラフィックを許可する必要があります。
   - Azure では、Application Gateway は別のサブネットに存在する必要があります。 詳細については、「[アプリケーション ゲートウェイ構成の概要](../../../application-gateway/configuration-overview.md)」を参照してください。
   - Azure Files ではなく Azure NetApp Files をファイル ストアに使用する場合は、Azure NetApp Files 用に別のサブネットを作成します。 詳細については、「[Azure NetApp Files のネットワーク計画のガイドライン](../../../azure-netapp-files/azure-netapp-files-network-topologies.md)」を参照してください。

1. 可用性セットを作成します。

   - 複数インスタンスのデプロイで各層の冗長性を実現するには、可用性セット内の各層に VM を配置します。 アーキテクチャに応じて、各レベルの可用性セットを分離してください。

1. 仮想マシン 1 (azuswinboap1) を作成します。

   - カスタム イメージを使用することも、Azure Marketplace からイメージを選択することもできます。 必要に応じて、「[SAP 用 Azure Marketplace から VM をデプロイする](deployment-guide.md)」または「[SAP のカスタム イメージを使用して VM をデプロイする](deployment-guide.md)」を参照してください。

1. 仮想マシン 2 (azuswinboap2) を作成します。
1. Premium SSD ディスクを 1 つ追加します。 これは SAP BOBI のインストール ディレクトリとして使用されます。

## <a name="provision-azure-premium-files"></a>Azure Premium Files のプロビジョニング

Azure Files のセットアップを続行する前に、[Azure Files](../../../storage/files/storage-files-introduction.md) のドキュメントの内容をよく理解しておいてください。

Azure Files には、HDD ベースのハードウェアでホストされる Standard ファイル共有と、SSD ベースのハードウェアでホストされる Premium ファイル共有が用意されています。 SAP BusinessObjects ファイル ストアの場合は、Azure Premium Files を使用します。

Azure Premium ファイル共有は、リージョンのサブセット内でローカルおよびゾーン冗長で利用できます。 ご自分のリージョンで現在 Premium ファイル共有を使用できるかどうかを確認するには、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=storage)」を参照してください。 ゾーン冗長ストレージ (ZRS) をサポートするリージョンの詳細については、「[Azure Storage の冗長性](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」を参照してください。

### <a name="deploy-an-azure-files-storage-account-and-nfs-shares"></a>Azure Files ストレージ アカウントと NFS 共有のデプロイ

Azure ファイル共有は、ストレージの共有プールを表す最上位オブジェクトである "ストレージ アカウント" にデプロイされます。 このストレージのプールは、複数のファイル共有をデプロイするのに使用できます。 Azure では、お客様のさまざまなストレージ シナリオに対して複数のストレージ アカウントの種類がサポートされています。 SAP BusinessObjects ファイル ストレージの場合は、FileStorage アカウントを作成する必要があります。 これを使用して、Premium SSD ベースのハードウェアに Azure ファイル共有をデプロイします。

> [!NOTE]
> FileStorage アカウントは、Azure ファイル共有を格納するためにのみ使用できます。 FileStorage アカウントでその他のストレージ リソース (BLOB、コンテナー、キュー、テーブルなど) をデプロイすることはできません。

ストレージ アカウントは、[プライベート エンドポイント](../../../storage/files/storage-files-networking-endpoints.md)経由でアクセスされ、SAP BOBI プラットフォームの同じ仮想ネットワークにデプロイされます。 このセットアップでは、SAP システムからのトラフィックが仮想ネットワークのセキュリティ境界の外に出ることはありません。 SAP システムには、機密性が高くビジネスにとって重要なデータが含まれていることが多いため、仮想ネットワークの境界内に留まることは、多くのお客様にとって重要なセキュリティ上の考慮事項です。

別の仮想ネットワークからストレージ アカウントにアクセスする必要がある場合は、[Azure 仮想ネットワーク ピアリング](../../../virtual-network/virtual-network-peering-overview.md)を使用できます。

#### <a name="azure-files-storage-account"></a>Azure Files ストレージ アカウント

1. Azure portal を使用してストレージ アカウントを作成するには、 **[リソースの作成]**  >  **[ストレージ]**  >  **[ストレージ アカウント]** を選択します。

1. **[基本]** タブで、ストレージ アカウントを作成するのに必要なすべてのフィールドに入力します。

   1. **[サブスクリプション]**  >  **[リソース グループ]**  >  **[リージョン]** を選択します。

   1. **ストレージ アカウント名** を入力します。 たとえば、「**azusbobi**」と入力します。 この名前はグローバルに一意である必要がありますが、任意の名前を指定できます。

   1. パフォーマンス レベルとして **[Premium]** を選択し、アカウントの種類として **[FileStorage]** を選択します。

   1. **[レプリケーション]** ラベルで、冗長性レベルを選択します。 **[ローカル冗長ストレージ (LRS)]** を選択します。

      Premium FileStorage の場合、使用可能なオプションは LRS と ZRS に限られます。 デプロイ戦略 (可用性セットまたは可用性ゾーン) に応じて、適切な冗長性レベルを選択します。 詳細については、「[Azure Storage の冗長性](../../../storage/common/storage-redundancy.md)」を参照してください。

   1. **[次へ]** を選択します。

1. **[ネットワーク]** タブで、接続方法として [[プライベート エンドポイント]](../../../storage/files/storage-files-networking-endpoints.md) を選択します。 詳細については、「[Azure Files のネットワークに関する考慮事項](../../../storage/files/storage-files-networking-overview.md)」を参照してください。

   1. プライベート エンドポイント セクションで **[追加]** を選択します。

   1. **[サブスクリプション]**  >  **[リソース グループ]**  >  **[場所]** を選択します。

   1. プライベート エンドポイントの **[名前]** を入力します。 たとえば、「**azusbobi-pe**」と入力します。

   1. **[ストレージ サブリソース]** で **[ファイル]** を選択します。

   1. **[ネットワーク]** セクションで、SAP BusinessObjects BI アプリケーションをデプロイする **[仮想ネットワーク]** と **[サブネット]** を選択します。

   1. **[プライベート DNS ゾーンと統合する]** は、**既定値 (はい)** をそのまま使用します。

   1. ドロップダウン リストからお使いの **[プライベート DNS ゾーン]** を選択します。

   1. **[OK]** を選択して、 **[ストレージ アカウントの作成]** の **[ネットワーク]** タブに戻ります。

1. **[データ保護]** タブで、お使いのストレージ アカウント内の Azure ファイル共有に対する論理的な削除ポリシーを構成します。 既定では、論理的な削除の機能は無効になっています。 論理的な削除の詳細については、「[Azure ファイル共有の誤削除を防ぐ](../../../storage/files/storage-files-prevent-file-share-deletion.md)」を参照してください。

1. **[詳細設定]** タブで、各種のセキュリティ オプションを選択します。

   **[安全な転送が必須]** フィールドは、ストレージ アカウントへの通信でストレージ アカウントが転送中の暗号化を必要とするかどうかを示します。 SMB 2.1 サポートが必要な場合は、このフィールドを無効にする必要があります。 SAP BOBI プラットフォームの場合は、**既定値 (有効)** のままにします。

1. 続行してストレージ アカウントを作成します。

ストレージ アカウントの作成方法の詳細については、[FileStorage ストレージ アカウントの作成](../../../storage/files/storage-how-to-create-file-share.md)に関するページをご覧ください。

#### <a name="create-azure-file-shares"></a>Azure ファイル共有を作成する

次の手順では、ストレージ アカウントに Azure Files を作成します。 Azure Files では、Premium ファイル共有にプロビジョニング モデルが使用されます。 プロビジョニング ビジネス モデルでは、使用量に基づいて請求されるのではなく、Azure Files にストレージ要件を事前に指定します。 このモデルの詳細については、「[プロビジョニング モデル](../../../storage/files/understanding-billing.md#provisioned-model)」を参照してください。 この例では、SAP BOBI ファイル ストア用に、frsinput (256 GB) と frsoutput (256 GB) の 2 つの Azure Files を作成します。

1. ストレージ アカウント **azusbobi** >  **[ファイル共有]** にアクセスします。
1. **[新しいファイル共有]** を選択します。
1. ファイル共有の **[名前]** を入力します。 たとえば、「**frsinput**」または「**frsouput**」と入力します。
1. **[プロビジョニング済みの容量]** で、必要なファイル共有のサイズを挿入します。 たとえば、「**256**」(GB) と入力します。
1. **[プロトコル]** は **[SMB]** を選択します。
1. **[作成]** を選択します。

## <a name="configure-a-data-disk-on-a-windows-virtual-machine"></a>Windows 仮想マシンでのデータ ディスクの構成

このセクションの手順では、次のプレフィックスを使用します。

**[A]** :この手順はすべてのホストに適用されます。

### <a name="initialize-a-new-data-disk"></a>新しいデータ ディスクの初期化

SAP BusinessObjects BI アプリケーションには、そのバイナリをインストールできるパーティションが必要です。 SAP BOBI アプリケーションは OS パーティション (C:) にインストールできますが、デプロイと OS のための十分な領域があることを確認する必要があります。 一時ファイルと Web アプリケーションのために、少なくとも 2 GB の空き領域を用意することをお勧めします。 また、SAP BOBI インストール バイナリを別のパーティションに分離することをお勧めします。

この例では、SAP BOBI アプリケーションは別のパーティション (F:) にインストールされます。 VM のプロビジョニング時に接続した Premium SSD ディスクを初期化します。

1. **[A]** VM (azuswinboap1 と azuswinboap2) にデータ ディスクが接続されていない場合は、「[データ ディスクの追加](../../windows/attach-managed-disk-portal.md#add-a-data-disk)」の手順に従って、新しいマネージド データ ディスクを接続します。
1. **[A]** マネージド ディスクが VM に接続されたら、「[新しいデータ ディスクの初期化](../../windows/attach-managed-disk-portal.md#initialize-a-new-data-disk)」の手順に従って、ディスクを初期化します。

### <a name="mount-azure-premium-files"></a>Azure Premium Files のマウント

Azure Files をファイル ストアとして使用するには、マウントする (つまり、ドライブ文字またはマウント ポイント パスを割り当てる) 必要があります。

**[A]** Azure ファイル共有をマウントするには、「[Azure ファイル共有をマウントする](../../../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share)」の手順に従います。

Windows サーバーに Azure ファイル共有をマウントするには、ポート 445 が開いていることを確認します。 SMB プロトコルでは、TCP ポート 445 が開いている必要があります。 ポート 445 がブロックされている場合は、接続が失敗します。 ファイアウォールでポート 445 がブロックされているかどうかは、[トラブルシューティング](../../../storage/files/storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked) ガイドで説明されている `Test-NetConnection` コマンドレットを使用して確認できます。

## <a name="configure-a-cms-database-azure-sql"></a>CMS データベースの構成: Azure SQL

このセクションでは、Azure portal を使用して Azure SQL をプロビジョニングする方法について詳しく説明します。 また、SAP BOBI プラットフォーム用の CMS および監査データベースと、データベースにアクセスするためのユーザー アカウントを作成する方法についても説明します。

このガイドラインは、SQL Database を使用している場合にのみ適用されます。 その他のデータベースでの手順については、SAP またはデータベース固有のドキュメントをご覧ください。

### <a name="create-a-sql-database-server"></a>SQL Database サーバーの作成

SQL Database には、単一データベース、エラスティック プール、データベース サーバーなど、さまざまなデプロイ オプションが用意されています。 SAP BOBI プラットフォームの場合、CMS と監査の 2 つのデータベースが必要です。 2 つの単一データベースを作成する代わりに、単一データベースとエラスティック プールのグループを管理できる SQL Database サーバーを作成できます。 SQL Database サーバーを作成するには、次の手順に従います。

1. [[SQL デプロイ オプションの選択]](https://portal.azure.com/#create/Microsoft.AzureSQL) ページを参照します。
1. **[SQL データベース]** で、 **[リソースの種類]** を **[データベース サーバー]** に変更します。 **[作成]** を選択します。
1. **[基本]** タブで、**SQL Database サーバーを作成する** のに必要なすべてのフィールドに入力します。

   1. **[プロジェクトの詳細]** で、 **[サブスクリプション]** と **[リソース グループ]** を選択します。

   1. **[サーバー名]** を入力します。 たとえば、「**azussqlbodb**」と入力します。 サーバー名はグローバルに一意である必要がありますが、任意の名前を指定できます。

   1. **[場所]** を選択します。

   1. **[サーバー管理者ログイン]** を入力します。 たとえば、「**boadmin**」と入力します。 その後、 **[パスワード]** を入力します。

1. **[ネットワーク]** タブの **[ファイアウォール規則]** で、 **[Azure のサービスとリソースにこのサーバーへのアクセスを許可する]** を **[いいえ]** に変更します。
1. **[追加設定]** では、既定の設定をそのままにします。
1. 続行して **[SQL データベース サーバー]** を作成します。

次の手順では、CMS および監査データベースを SQL Database サーバー (azussqlbodb.database.windows.net) 内に作成します。

### <a name="create-the-cms-and-the-audit-database"></a>CMS および監査データベースの作成

SQL Database サーバーをプロビジョニングした後、リソース azussqlbodb を参照します。 その後、以下の手順に従って CMS および監査データベースを作成します。

1. azussqlbodb の **[概要]** ページで、 **[データベースの作成]** を選択します。
1. **[基本]** タブで、必要なすべてのフィールドに入力します。

   1. **[データベース名]** を入力します。 たとえば、「**bocms**」または「**boaudit**」と入力します。

   1. **[コンピューティングとストレージ]** オプションで、 **[データベースの構成]** を選択します。 サイズ設定の結果に基づいて適切なモデルを選択します。 オプションの詳細については、「[Azure SQL Database のサイズ設定モデル](businessobjects-deployment-guide.md#sizing-models-for-azure-sql-database)」を参照してください。
1. **[ネットワーク]** タブで、接続方法として [[プライベート エンドポイント]](../../../private-link/tutorial-private-endpoint-sql-portal.md) を選択します。 プライベート エンドポイントは、構成された仮想ネットワーク内の SQL Database にアクセスするために使用されます。

   1. **[プライベート エンドポイントの追加]** を選択します。

   1. **[サブスクリプション]**  >  **[リソース グループ]**  >  **[場所]** を選択します。

   1. プライベート エンドポイントの **[名前]** を入力します。 たとえば、「**azusbodb-pe**」と入力します。

   1. **[ターゲット サブリソース]** で **SqlServer** を選択します。

   1. **[ネットワーク]** セクションで、SAP BusinessObjects BI アプリケーションをデプロイする **[仮想ネットワーク]** と **[サブネット]** を選択します。

   1. **[プライベート DNS ゾーンと統合する]** については、**既定値 (はい)** をそのまま使用します。

   1. ドロップダウン リストからお使いの **[プライベート DNS ゾーン]** を選択します。

   1. **[OK]** を選択して、 **[SQL データベースの作成]** の **[ネットワーク]** タブに戻ります。
1. **[追加設定]** タブで、 **[照合順序]** の設定を **SQL_Latin1_General_CP850_BIN2** に変更します。
1. 続行して CMS データベースを作成します。

同様に、監査データベースを作成できます。 たとえば、「**boaudit**」と入力します。

### <a name="download-and-install-an-odbc-driver"></a>ODBC ドライバーのダウンロードとインストール

SAP BOBI アプリケーション サーバーから CMS または監査データベースへのアクセスには、データベース クライアントまたはドライバーが必要です。 Microsoft ODBC ドライバーは、SQL Database で実行されている CMS および監査データベースにアクセスするために使用されます。 このセクションでは、Windows で ODBC ドライバーをダウンロードしてセットアップする手順について説明します。 

1. SQL Database と互換性のあるデータベース コネクタを見つけるには、[SAP BusinessObjects BI プラットフォームの製品出荷マトリックス (PAM)](https://support.sap.com/pam) の、**OS による CMS および監査リポジトリのサポート** に関するセクションをご覧ください。
1. [リンク](/sql/connect/odbc/windows/release-notes-odbc-sql-server-windows?preserve-view=true&view=sql-server-ver15)から該当するバージョンの ODBC ドライバーをダウンロードします。 この例では、ODBC ドライバー [13.1](/sql/connect/odbc/windows/release-notes-odbc-sql-server-windows?preserve-view=true&view=sql-server-ver15#131) をダウンロードします。
1. すべての BI サーバー (azuswinboap1 および azuswinboap2) に ODBC ドライバーをインストールします。
1. **azuswinboap1** にドライバーをインストールしたら、 **[スタート]**  >  **[Windows 管理ツール]**  >  **[ODBC データ ソース (64 ビット)]** にアクセスします。
1. **[システム DSN]** タブにアクセスします。 
1. **[追加]** を選択して、CMS データベースへの接続を作成します。
1. **[ODBC Driver 13 for SQL Server]** を選択して、 **[完了]** を選択します。
1. 次のように CMS データベースの情報を入力して、 **[次へ]** を選択します。
   - **[名前]** :「CMS および監査データベースの作成」で作成したデータベースの名前。 たとえば、「**bocms**」または「**boaudit**」と入力します。
   - **[説明]** : データ ソースの説明。 たとえば、「**CMS データベース**」または「**監査データベース**」と入力します。
   - **サーバー**:「SQL Database サーバーの作成」で作成したサーバーの名前。 たとえば、「azussqlbodb.database.windows.net」と入力します。
1. **[ユーザーが入力する SQL Server 用のログイン ID とパスワードを使う]** を選択して、Azure SQL Server に対する信頼性を確認します。 SQL Database サーバーの作成時に作成されたユーザー資格情報を入力します。 たとえば、「**boadmin**」と入力します。 **[次へ]** を選択します。
1. **既定のデータベース** を **bocms** に変更し、それ以外はすべて既定のままにします。 **[次へ]** を選択します。
1. **[データに強力な暗号を使用する]** チェック ボックスをオンにし、それ以外はすべて既定のままにします。 **[完了]** を選択します。
1. CMS データベースへのデータ ソースが作成されました。 ここで、 **[データ ソースのテスト]** を選択して、BI アプリケーションから CMS データベースへの接続を検証できます。 これは正常に完了する必要があります。 失敗した場合は、接続の問題の[トラブルシューティング](../../../azure-sql/database/troubleshoot-common-errors-issues.md)を行います。

>[!Note]
>SQL Database の通信は、ポート 1433 上で行われます。 ポート 1433 で、SAP BOBI アプリケーション サーバーからの送信トラフィックを許可する必要があります。

上記の手順を繰り返して、サーバー azuswinboap1 に監査データベースの接続を作成します。 同様に、すべての BI アプリケーション サーバー (azuswinboap2) に、両方の ODBC データ ソース (bocms と boaudit) をインストールして構成します。 

## <a name="server-preparation"></a>サーバーの準備

SAP の最新ガイドに従って、BI プラットフォームのインストールのためにサーバーを準備します。 最新情報については、「[SAP Business Intelligence プラットフォーム インストール ガイド (Windows)](https://help.sap.com/viewer/df8899896b364f6c880112f52e4d06c8/4.3.1/en-US/46b0d1a26e041014910aba7db0e91070.html)」の「事前準備」を参照してください。

## <a name="installation"></a>インストール

Windows ホストに BI プラットフォームをインストールするには、ローカル管理者特権を持つユーザーでサインインします。

SAP BusinsessObjects BI プラットフォームのメディアにアクセスして、`setup.exe` を実行します。

お使いのバージョンに固有の「[SAP Business Intelligence プラットフォーム インストール ガイド (Windows)](https://help.sap.com/viewer/df8899896b364f6c880112f52e4d06c8/4.3.1/en-US/46ae62456e041014910aba7db0e91070.html)」の手順に従います。 Windows に SAP BOBI プラットフォームをインストールするときの注意点を次に示します。

- **[インストール先フォルダーの設定]** 画面では、BI プラットフォームのインストール先フォルダーを指定します。 たとえば、「**F:\SAP BusinessObjects\*** 」と入力します。 
- **[製品登録の設定]** 画面で、SAP ノート [1288121](https://launchpad.support.sap.com/#/notes/1288121) にある SAP BusinessObjects ソリューションの一時ライセンス キーを使用するか、SAP サービス マーケットプレースでライセンス キーを生成することができます。
- **[インストールの種類を選択]** 画面で、最初のサーバー (azuswinboap1) については **[フル]** インストールを選択します。 もう一方のサーバー (azuswinboap2) については、既存の SAP BOBI セットアップを拡張する **[カスタム/拡張]** を選択します。
- **[デフォルト データベースまたは既存データベースの選択]** 画面で、 **[既存のデータベースの設定]** を選択します。これにより、CMS および監査データベースを選択するよう求められます。 **CMS データベース** タイプおよび **監査データベース** タイプとして、 **[Microsoft SQL Server (ODBC 使用)]** を選択します。

  インストール中に監査を構成しない場合は、 **[監査データベースなし]** を選択することもできます。

- **[Java Web アプリケーション サーバーの選択]** 画面で、使用している SAP BOBI アーキテクチャに基づいて適切なオプションを選択します。 この例では、同じ SAP BOBI プラットフォームに Tomcat サーバーをインストールするオプション 1 を選択しました。
- CMS データベースの情報を **[CMS リポジトリ データベースの設定 - SQL Server (ODBC)]** に入力します。 次の図は、Windows インストールの CMS データベース情報の入力例を示しています。
  
  ![Windows の CMS データベース情報を示すスクリーンショット。](media/businessobjects-deployment-guide/businessobjects-deployment-windows-sql-cms.png)
- (オプション) 監査データベースの情報を **[監査データベースの設定 - SQL Server (ODBC)]** に入力します。 次の図は、Windows インストールの監査データベース情報の入力例を示しています。
  
  ![Windows の監査データベース情報を示すスクリーンショット。](media/businessobjects-deployment-guide/businessobjects-deployment-windows-sql-audit.png)

- 手順に従って必要な情報を入力し、インストールを完了します。

複数インスタンス デプロイの場合は、2 番目のホスト (azuswinboap2) に対してインストール セットアップを実行します。 **[インストール タイプの選択]** 画面で、 **[カスタム/拡張]** を選択します。これにより、既存の SAP BOBI セットアップが拡張されます。 詳細については、SAP ブログの「[SAP BusinessObjects Business Intelligence platform setup with Azure SQL Database](https://blogs.sap.com/2020/06/19/sap-on-azure-sap-businessobjects-business-intelligence-platform-setup-with-azure-sql-db-managed-paas-database/)」(Azure SQL Database を使用した SAP BusinessObjects Business Intelligence プラットフォームのセットアップ) を参照してください。

> [!IMPORTANT]
> SQL Server と SQL Database のデータベース エンジンのバージョン番号は比較できません。 これらは、別の製品の内部ビルド番号です。 SQL Database のデータベース エンジンは SQL Server データベース エンジンと同じコードに基づいています。 最も重要なこととして、SQL Database のデータベース エンジンでは常に最新の SQL Database エンジン ビットが使用されます。 SQL Database のバージョン 12 は SQL Server のバージョン 15 より新しくなります。

現在の SQL Database のバージョンを確認するには、セントラル管理コンソール (CMC) の設定をチェックするか、[sqlcmd](/sql/tools/sqlcmd-utility?preserve-view=true&view=sql-server-ver15) または [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms?preserve-view=true&view=sql-server-ver15) を使用して次のクエリを実行します。 SQL のバージョンを既定の互換性に合わせる方法については、[データベースの互換性レベル](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?preserve-view=true&view=sql-server-ver15)に関する記事をご覧ください。

![CMC のデータベース情報を示すスクリーンショット。](media/businessobjects-deployment-guide/businessobjects-deployment-windows-sql-cmc.PNG)

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

### <a name="configure-a-cluster-name"></a>クラスター名の構成

SAP BOBI プラットフォームの複数インスタンスのデプロイでは、1 つのクラスターで複数の CMS サーバーを同時に実行する必要があります。 クラスターは、共通の CMS システム データベースに対して連携する 2 つ以上の CMS サーバーで構成されます。 CMS で実行されているノードで障害が発生した場合、別の CMS があるノードで BI プラットフォームの要求の処理が続けられます。 SAP BOBI プラットフォームの既定の設定では、クラスター名に、インストールした最初の CMS のホスト名が反映されます。 

Windows でクラスター名を構成するには、「[SAP Business Intelligence プラットフォーム管理者ガイド](https://help.sap.com/viewer/2e167338c1b24da9b2a94e68efd79c42/4.3.1/en-US)」の手順に従います。 クラスター名を構成した後、SAP ノート [1660440](https://launchpad.support.sap.com/#/notes/1660440) に従って、CMC または BI スタート パッドのサインイン ページで既定のシステム エントリを設定します。 

### <a name="configure-the-input-and-output-filestore-location-to-azure-premium-files"></a>Azure Premium Files に対するインプットおよびアウトプット ファイルストアの場所を構成する

ファイルストアとは、実際の SAP BusinessObjects BI ファイルが配置されるディスク ディレクトリのことです。 SAP BOBI プラットフォーム用のファイル リポジトリ サーバーの既定の場所は、ローカル インストール ディレクトリ内にあります。 複数インスタンスのデプロイでは、すべてのストレージ層サーバーからアクセスできるように、Azure Premium Files や Azure NetApp Files などの共有ストレージにファイルストアを設定することが重要です。

1. Azure Premium Files が作成されていない場合は、「Azure Premium Files のプロビジョニング」で説明されている手順に従って作成し、マウントします。

   > [!Tip]
   > VM のデプロイ (可用性セットまたは可用性ゾーン) に基づいて、Azure Premium Files (LRS または ZRS) のストレージ冗長性を選択します。

1. SAP ノート [2512660](https://launchpad.support.sap.com/#/notes/0002512660) に従って、ファイル リポジトリ (入力と出力) のパスを変更します。

### <a name="tomcat-clustering-session-replication"></a>Tomcat クラスタリング: セッション レプリケーション

Tomcat を使用すると、セッション レプリケーションとフェールオーバーのために、2 つ以上のアプリケーション サーバーのクラスタリングがサポートされます。 SAP BOBI プラットフォーム セッションがシリアル化されている場合は、アプリケーション サーバーで障害が発生しても、ユーザー セッションを Tomcat の別のインスタンスにシームレスにフェールオーバーできます。 たとえば、ユーザーが SAP BI アプリケーションでフォルダー階層内を移動しているときに、そのユーザーが接続している Web サーバーに障害が発生する可能性があります。 正しく構成されたクラスターでは、ユーザーはサインイン ページにリダイレクトされることなく、フォルダー階層内を移動することができます。

SAP ノート [2808640](https://launchpad.support.sap.com/#/notes/2808640) には、マルチキャストを使用して Tomcat クラスタリングを構成する手順が示されています。 しかし、マルチキャストは Azure でサポートされていません。 Azure で Tomcat クラスターを機能させるには、[StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) を使用する必要があります (SAP ノート [2764907](https://launchpad.support.sap.com/#/notes/2764907))。 Azure で Tomcat クラスターを設定するには、SAP ブログの「[Tomcat Clustering using Static Membership for SAP BusinessObjects BI Platform](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/)」(SAP BusinessObjects BI プラットフォームの静的なメンバーシップを使用した Tomcat クラスタリング) を参照してください。

### <a name="load-balance-a-web-tier-of-an-sap-bi-platform"></a>SAP BI プラットフォームの Web 層の負荷分散

SAP BOBI の複数インスタンス デプロイの場合、Java Web アプリケーション サーバー (Web 層) が 2 つ以上のホストで実行されています。 Web サーバー間でユーザー負荷を均等に分散するために、エンド ユーザーと Web サーバーの間にロード バランサーを使用できます。 Azure Load Balancer または Application Gateway を使用して、Web アプリケーション サーバーへのトラフィックを管理できます。 これらのサービスについては、以下のセクションで説明します。

* [Load Balancer](../../../load-balancer/load-balancer-overview.md) は、ハイ パフォーマンスで低遅延のレイヤー 4 (TCP、UDP) ロード バランサーであり、正常な VM 間でトラフィックを分散します。 ロード バランサーの正常性プローブでは、各 VM の特定のポートを監視し、稼働している VM にのみトラフィックを分散します。 SAP BI プラットフォームをインターネットからアクセスできるようにするかどうかに応じて、パブリック ロード バランサーまたは内部ロード バランサーのいずれかを選択できます。 ゾーン冗長であるため、可用性ゾーン全体で高可用性が確保されます。

   次の図の「内部ロード バランサー」セクションを参照してください。Web アプリケーション サーバーは、ポート 8080 (既定の Tomcat HTTP ポート) で実行され、正常性プローブによって監視されます。 ユーザーからの受信要求は、バックエンド プール内の Web アプリケーション サーバー (azuswinboap1 または azuswinboap2) にリダイレクトされます。 Load Balancer で TLS/SSL 終端 (TLS/SSL オフロードとも呼ばれます) はサポートされていません。 Load Balancer を使用して Web サーバー間でトラフィックを分散する場合は、Standard Load Balancer を使用することをお勧めします。

   > [!NOTE]
   > パブリック IP アドレスのない VM が、内部の (パブリック IP アドレスのない) Standard Load Balancer のバックエンド プール内に配置されている場合、パブリック エンドポイントへのルーティングを許可するように追加の構成が実行されない限り、送信インターネット接続はありません。 送信接続を実現する方法の詳細については、「[SAP の高可用性シナリオにおける Azure Standard Load Balancer を使用した Virtual Machines のパブリック エンドポイント接続](high-availability-guide-standard-load-balancer-outbound-connections.md)」を参照してください。

   ![Web サーバー間でトラフィックを分散するために使用される Load Balancer を示すスクリーンショット。](media/businessobjects-deployment-guide/businessobjects-deployment-windows-load-balancer.png)

* [Application Gateway](../../../application-gateway/overview.md) では、サービスとしてのアプリケーション配信コントローラーが提供されます。これは、アプリケーションでユーザー トラフィックを 1 つ以上の Web アプリケーション サーバーに転送するために使用されます。 TLS/SSL オフロード、Web Application Firewall、Cookie ベースのセッション アフィニティなど、レイヤー 7 のさまざまな負荷分散機能がアプリケーションに提供されます。

   Application Gateway によって、SAP BI プラットフォームのアプリケーション Web トラフィックがバックエンド プール内の指定されたリソースに転送されます。 この場合は、azuswinboap1 または azuswinboap2 のいずれかです。 リスナーをポートに割り当て、ルールを作成し、リソースをバックエンド プールに追加します。 次の図では、プライベート フロントエンド IP アドレス (10.31.3.25) を持つ Application Gateway がユーザーのエントリ ポイントとして機能し、それによって受信 TLS/SSL (HTTPS - TCP/443) 接続が処理され、TLS/SSL の暗号化が解除され、要求 (HTTP - TCP/8080) がバックエンド プール内のサーバーに渡されます。 組み込みの TLS/SSL 終端機能を使用することで、Application Gateway で 1 つの TLS/SSL 証明書を維持するだけで済み、操作が簡単になります。

   ![Web サーバー間でトラフィックを分散するために使用される Application Gateway を示すスクリーンショット。](media/businessobjects-deployment-guide/businessobjects-deployment-windows-application-gateway.png)

   SAP BOBI Web サーバー用に Application Gateway を構成するには、SAP ブログの「[Load Balancing SAP BOBI Web Servers using Application Gateway](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/)」(Application Gateway を使用した SAP BOBI Web サーバーの負荷分散) を参照してください。

   > [!NOTE]
   > Web サーバーへのトラフィックを負荷分散するには、Application Gateway を使用してください。なぜなら、それによって SSL オフロード、サーバーでの暗号化と解読のオーバーヘッドを削減する SSL 管理の一元化、トラフィック分散のためのラウンドロビン アルゴリズム、Web アプリケーション ファイアウォール機能、高可用性のような機能が提供されるためです。

## <a name="sap-businessobjects-bi-platform-reliability-on-azure"></a>Azure での SAP BusinessObjects BI プラットフォームの信頼性

SAP BusinessObjects BI プラットフォームには、特定のタスクと操作用に最適化されたさまざまな階層が含まれています。 いずれか 1 つの層のコンポーネントが使用できなくなると、SAP BOBI アプリケーションがアクセス不能になるか、アプリケーションの特定の機能が動作しなくなります。 ビジネスの中断なくアプリケーションが稼働し続けるには、各層が高い信頼性を持つように設計されている必要があります。

このガイドでは、Azure のネイティブ機能と SAP BOBI プラットフォームの構成の組み合わせにより、SAP デプロイの可用性を向上させる方法について説明します。 このセクションでは、Azure での SAP BOBI プラットフォームの信頼性に関する次のオプションについて重点的に説明します。

- **バックアップと復元**: これは、データとアプリケーションのコピーを別の場所に定期的に作成するプロセスです。 元のデータまたはアプリケーションが消失または破損した場合、コピーを使用して以前の状態に復元または復旧できます。
- **高可用性**: 高可用性プラットフォームには、Azure リージョン内のあらゆるものが少なくとも 2 つずつ存在し、いずれかのサーバーが使用できなくなった場合でもアプリケーションが稼働し続けます。
- **ディザスター リカバリー (DR)** : このプロセスでは、致命的な損失が発生した場合にアプリケーションの機能が復元されます。 たとえば、自然災害のために Azure リージョン全体が使用できなくなる可能性があります。

このソリューションの実装は、Azure のシステム設定の種類によって異なります。 バックアップと復元、高可用性、DR ソリューションは、ビジネス要件に基づいて調整する必要があります。

## <a name="backup-and-restore"></a>バックアップと復元

バックアップと復元は、データとアプリケーションのコピーを別の場所に定期的に作成するプロセスです。それにより、元のデータまたはアプリケーションが消失または破損した場合に、以前の状態に復元または復旧できます。 また、これはビジネス DR 戦略の不可欠な要素でもあります。 これらのバックアップにより、構成された保有期間内の特定の時点にアプリケーションとデータベースを復元できます。

SAP BOBI プラットフォームの包括的なバックアップと復元の戦略を策定するには、アプリケーションのシステム ダウンタイムや中断につながるコンポーネントを特定します。 SAP BOBI プラットフォームでは、アプリケーションを保護するために、次のコンポーネントのバックアップが不可欠です。

- SAP BOBI インストール ディレクトリ (マネージド Premium ディスク)
- ファイルストア (分散インストール用の Azure Premium Files または Azure NetApp Files)
- CMS および監査データベース (SQL Database、Azure Database for MySQL、または Azure Virtual Machines 上のデータベース)

次のセクションでは、SAP BOBI プラットフォームの各コンポーネントのバックアップと復元の戦略を実装する方法について説明します。

### <a name="backup-and-restore-for-an-sap-bobi-installation-directory"></a>SAP BOBI インストール ディレクトリのバックアップと復元

Azure において、VM および接続されているすべてのディスクをバックアップする最も簡単な方法は、[Azure Backup](../../../backup/backup-azure-vms-introduction.md) を使用することです。 それにより、VM 上のデータが誤って破壊されることを防ぐために、独立した分離されたバックアップが提供されます。 バックアップは、復旧ポイントの管理機能をビルトインで備えた Recovery Services コンテナーに格納されます。 構成とスケーリングはシンプルです。 バックアップは最適化され、必要に応じて簡単に復元できます。

バックアップ プロセスの一環として、スナップショットが取得されます。 運用環境のワークロードに影響を与えることなく、データが Recovery Services コンテナーに転送されます。 「[スナップショットの整合性](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency)」で説明されているように、スナップショットによってさまざまなレベルの一貫性が提供されます。 Backup では、[Azure VM バックアップ](../../../backup/backup-azure-vms-introduction.md) ソリューションに加えて、[Azure ディスク バックアップ](../../../backup/disk-backup-overview.md)を使用したマネージド ディスクのバックアップの並列サポートも提供されます。 これは、VM の整合性バックアップが 1 日 1 回必要であり、OS ディスクまたは特定のデータ ディスクのより頻繁なクラッシュ整合性バックアップも行う必要がある場合に便利です。 詳細については、[Azure VM バックアップの概要](../../../backup/backup-azure-vms-introduction.md)、[Azure ディスク バックアップ](../../../backup/disk-backup-overview.md)、[Azure VM バックアップの FAQ](../../../backup/backup-azure-vm-backup-faq.yml) に関するページをご覧ください。

### <a name="backup-and-restore-for-filestore"></a>ファイルストアのバックアップと復元

SAP BOBI プラットフォームのファイルストアは、デプロイに応じて Azure NetApp Files または Azure Files に配置できます。 ファイルストアに使用するストレージに基づいて、バックアップと復元に関する次のオプションを選択します。

* **Azure NetApp Files**: Azure NetApp Files では、オンデマンドのスナップショットを作成し、スナップショット ポリシーを使用して自動スナップショット作成のスケジュールを設定できます。 スナップショット コピーによって、Azure NetApp Files ボリュームの特定時点のコピーが提供されます。 詳細については、「[Azure NetApp Files を使用して、スナップショットを管理する](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md)」を参照してください。
* **Azure Files**: Azure Files バックアップは、[Azure Backup](../../../backup/backup-overview.md) のネイティブ インスタンスと統合されています。これにより、バックアップと復元の機能が VM のバックアップと共に一元化され、運用作業が簡単になります。 詳細については、[Azure ファイル共有のバックアップ](../../../backup/azure-file-share-backup-overview.md)と [Azure Files のバックアップの FAQ](../../../backup/backup-azure-files-faq.yml) に関するページをご覧ください。

NFS サーバーを別に作成した場合は、バックアップと復元の戦略が同じように実装されていることを確認します。

### <a name="backup-and-restore-for-the-cms-and-audit-database"></a>CMS および監査データベースのバックアップと復元

Windows VM 上で実行されている SAP BOBI プラットフォームの場合、Azure での SAP BOBI プラットフォームの計画および実装ガイドの[サポート マトリックス](businessobjects-deployment-guide.md#support-matrix)に記載されている、任意のサポートされているデータベースで、CMS および監査データベースを実行できます。 そのため、CMS および監査データ ストレージに使用されているデータベースに基づいて、バックアップと復元の戦略を採用することが重要です。

* **SQL Database** では、SQL Server 技術を利用して、[完全バックアップ](/sql/relational-databases/backup-restore/full-database-backups-sql-server?preserve-view=true&view=sql-server-ver15)を毎週、[差分バックアップ](/sql/relational-databases/backup-restore/differential-backups-sql-server?preserve-view=true&view=sql-server-ver15)を 12 から 24 時間ごと、[トランザクション ログ](/sql/relational-databases/backup-restore/transaction-log-backups-sql-server?preserve-view=true&view=sql-server-ver15) バックアップを 5 から 10 分ごとに作成します。 トランザクション ログ バックアップの頻度は、コンピューティング サイズとデータベース アクティビティの量に基づいて決まります。
 
   ユーザーは、LRS、ZRS、または GRS BLOB 間でバックアップ ストレージの冗長性を構成するオプションを選択できます。 ストレージの冗長性メカニズムでは、計画されたイベントや計画外のイベント (一時的なハードウェア障害、ネットワークの停止や停電、大規模な自然災害など) から保護するために、データのコピーが複数格納されます。 SQL Database では、既定で、[ペアになっているリージョン](../../../best-practices-availability-paired-regions.md)にレプリケートされる [GRS BLOB](../../../storage/common/storage-redundancy.md) にバックアップが格納されます。 これは、ビジネスの要件に応じて、LRS または ZRS BLOB に変更できます。 SQL Database のバックアップのスケジュール、保有期間、ストレージ使用量に関する最新情報は、「[自動バックアップ - Azure SQL Database および SQL Managed Instance](../../../azure-sql/database/automated-backups-overview.md)」を参照してください。

* **Azure Database for MySQL** では、サーバーのバックアップを自動的に作成し、ユーザーが構成した LRS または GRS に保存します。 Azure Database for MySQL では、データ ファイルとトランザクション ログのバックアップが作成されます。 サポートされている最大ストレージ サイズに応じて、完全および差分バックアップ (最大 4 TB のストレージ サーバー) またはスナップショット バックアップ (最大 16 TB のストレージ サーバー) が作成されます。 これらのバックアップを使用すると、サーバーを、構成済みのバックアップ保有期間内の任意の時点に復元できます。 既定のバックアップ保有期間は 7 日間です。これは、最大 35 日間まで[必要に応じて構成](../../../mysql/howto-restore-server-portal.md#set-backup-configuration)できます。 すべてのバックアップが、AES 256 ビット暗号化を使用して暗号化されます。 これらのバックアップ ファイルはユーザーに公開されておらず、エクスポートできません。 これらのバックアップは、Azure Database for MySQL の復元操作にのみ使用できます。 [mysqldump](../../../mysql/concepts-migrate-dump-restore.md) を使用して、データベースをコピーできます。 詳細については、「[Azure Database for MySQL でのバックアップと復元](../../../mysql/concepts-backup.md)」を参照してください。

* **Azure VM にインストールされているデータベースの場合は**、サポートされているデータベース用の標準のバックアップ ツールまたは [Backup](../../../backup/sap-hana-db-about.md) を使用できます。 また、Azure のサービスとツールが要件を満たさない場合は、すべての SAP BOBI プラットフォーム コンポーネントのバックアップと復旧のためのエージェントが用意されている、サードパーティ製のバックアップ ツールを使用できます。

## <a name="high-availability"></a>高可用性

高可用性は、IT の中断を最小限に抑えることができる一連のテクノロジを表し、同じデータ センター内の冗長性、フォールト トレランス、またはフェールオーバーで保護されたコンポーネントを通じてアプリケーションやサービスのビジネス継続性を提供することで実現されます。 今回の場合、データ センターは 1 つの Azure リージョン内にあります。 このセクションの手順を補完する [SAP のための高可用性アーキテクチャとシナリオ](sap-high-availability-architecture-scenarios.md)に関する記事から、Azure に用意されている SAP アプリケーション向けのさまざまな高可用性の手法と推奨事項に関する洞察が得られます。

SAP BOBI プラットフォームのサイズ設定の結果に基づいて、ランドスケープを設計し、Azure VM およびサブネット全体での BI コンポーネントの分散を決定する必要があります。 分散アーキテクチャの冗長性レベルは、ビジネスに必要な目標復旧時間 (RTO) と目標復旧時点 (RPO) によって異なります。 SAP BOBI プラットフォームには異なる複数の層が含まれており、各層のコンポーネントを、冗長性を実現するように設計する必要があります。 その後、どれか 1 つのコンポーネントで障害が発生した場合に SAP BOBI アプリケーションが中断されることはほとんど、またはまったくありません。 次に例を示します。

- BI アプリケーション サーバーや Web サーバーなどの冗長アプリケーション サーバー
- CMS データベース、ファイルストア、ロード バランサーなどの固有のコンポーネント

次のセクションでは、SAP BOBI プラットフォームの各コンポーネントで高可用性を実現する方法について説明します。

### <a name="high-availability-for-application-servers"></a>アプリケーション サーバーの高可用性

BI および Web アプリケーション サーバーでは、個別に (または一緒に) インストールされているかどうかにかかわらず、特定の高可用性ソリューションは必要ありません。 さまざまな Azure VM に BI および Web サーバーの複数のインスタンスを構成することで、冗長性による高可用性を実現できます。 これらの VM は、ビジネスに必要な RTO に応じて、[可用性セット](sap-high-availability-architecture-scenarios.md#multiple-instances-of-virtual-machines-in-the-same-availability-set)または[可用性ゾーン](sap-high-availability-architecture-scenarios.md#azure-availability-zones)のどちらかにデプロイできます。 可用性ゾーン間のデプロイに関しては、SAP BOBI プラットフォームの他のすべてのコンポーネントもゾーン冗長になるように設計されていることを確認してください。

現在、すべての Azure リージョンで可用性ゾーンが提供されているわけではないため、リージョンに応じたデプロイ戦略を採用する必要があります。 ゾーンを提供する Azure リージョンの一覧は、[Azure Availability Zones](../../../availability-zones/az-overview.md) に関するページにあります。

> [!Important]
> Azure Availability Zones と Azure 可用性セットの概念は、相互に排他的です。 つまり、2 つまたはそれ以上の VM を、特定の可用性ゾーンまたは Azure 可用性セットのどちらか一方にデプロイできますが、両方にすることはできません。

### <a name="high-availability-for-the-cms-database"></a>CMS データベースの高可用性

Azure のソリューションとしてのデータベースを CMS および監査データベースとして使用している場合は、ローカル冗長高可用性フレームワークが既定で提供されています。 高可用性、冗長性、回復性の各機能が最初から備わっているリージョンとサービスを選択するため、追加のコンポーネントを構成する必要はありません。 SAP BOBI プラットフォームのデプロイ戦略が可用性ゾーンをまたがっている場合は、CMS および監査データベースでゾーン冗長性を実現する必要があります。 Azure でサポートされているデータベース サービスの高可用性の詳細については、[Azure SQL Database の高可用性](../../../azure-sql/database/high-availability-sla.md)に関する記事と「[Azure Database for MySQL での高可用性](../../../mysql/concepts-high-availability.md)」を参照してください。 

CMS データベース用のその他のデータベース管理システム (DBMS) のデプロイについては、[SAP ワークロードのための DBMS デプロイ ガイド](dbms_guide_general.md)のページをご覧ください。異なる DBMS デプロイと、その高可用性を実現する方法に関する洞察が得られます。

### <a name="high-availability-for-filestore"></a>Filestore の高可用性

Filestore とは、レポート、ユニバース、接続などの内容が格納されているディスク ディレクトリのことです。 それは、そのシステムのすべてのアプリケーション サーバー間で共有されます。 そのため、他の SAP BOBI プラットフォーム コンポーネントと共に、高可用性が確保されるようにする必要があります。

Windows で実行されている SAP BOBI プラットフォームの場合、高可用性と高耐久性を実現するように設計された [Azure Premium Files](../../../storage/files/storage-files-introduction.md) または [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) をファイルストアのために選択できます。 Azure Premium Files では、ZRS をサポートしています。これは、SAP BOBI プラットフォームのクロスゾーン デプロイに役立つことがあります。 詳細については、Azure Files の「[冗長性](../../../storage/files/storage-files-planning.md#redundancy)」セクションを参照してください。

ファイル共有サービスはすべてのリージョンで使用できるわけではないため、最新情報については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」の一覧を確認してください。 お使いのリージョンでサービスを利用できない場合は、NFS サーバーを作成し、そのファイル システムを SAP BOBI アプリケーションと共有できます。 しかし、高可用性を考慮する必要もあります。

### <a name="high-availability-for-the-load-balancer"></a>ロード バランサーの高可用性

Web サーバー全体でトラフィックを分散するには、Load Balancer または Application Gateway を使用します。 デプロイ用に選択した SKU に基づいて、どちらのロード バランサーの冗長性も実現できます。

* **Load Balancer**: Standard Load Balancer フロントエンドをゾーン冗長として構成することで冗長性を実現できます。 詳細については、「[Standard Load Balancer と可用性ゾーン](../../../load-balancer/load-balancer-standard-availability-zones.md)」を参照してください。
* **Application Gateway**: デプロイ中に選択した層の種類に基づいて高可用性を実現できます。
   * v1 SKU を使用すると、2 つ以上のインスタンスをデプロイした場合に高可用性シナリオがサポートされます。 Azure は、これらのインスタンスを更新ドメインと障害ドメインに分散させ、全インスタンスで同時に障害が発生しないようにします。 この SKU によって、ゾーン内で冗長性を実現できます。
   * v2 SKU では、新しいインスタンスが障害ドメインと更新ドメインに自動的に分散されるようにします。 ゾーン冗長を選択した場合は、ゾーン障害回復性を提供するために、最新のインスタンスが可用性ゾーンにも分散されます。 詳細については、「[自動スケーリングとゾーン冗長 Application Gateway v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md)」を参照してください。

### <a name="reference-high-availability-architecture-for-the-sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI プラットフォームの高可用性の参照アーキテクチャ

次の参照アーキテクチャでは、Windows サーバーで実行されている可用性ゾーン間での SAP BOBI プラットフォームのセットアップについて説明します。 このアーキテクチャは、Application Gateway、Azure Premium Files (ファイルストア)、SQL Database (CMS および監査データベース) などの Azure サービスの使用方法を示しています。 SAP BOBI プラットフォームでは、組み込みのゾーン冗長性が提供されるため、異なる複数の高可用性ソリューションを管理する複雑さが軽減されます。

次の図では、複数の可用性ゾーンにまたがる Application Gateway v2 SKU を使用して、受信トラフィック (HTTPS - TCP/443) を負荷分散しています。 アプリケーション ゲートウェイでは、可用性ゾーン間で分散している Web サーバー間にユーザー要求を分散させます。 Web サーバーでは、可用性ゾーンをまたいで別々の VM にデプロイされている管理および処理サーバー インスタンスに要求を転送します。 ZRS を備える Azure Premium Files は、レポート、ユニバース、接続などのコンテンツにアクセスするために、プライベート リンクを介して管理およびストレージ層の VM に接続されます。 アプリケーションがアクセスする CMS および監査データベースは、Azure リージョン内の複数の物理的な場所にまたがってデータベースをレプリケートする SQL Database のゾーン冗長インスタンスで実行されています。

![Windows 上の SAP BOBI プラットフォームの高可用性アーキテクチャを示す図。](media/businessobjects-deployment-guide/businessobjects-deployment-windows-high-availability-availability-zone.png)

上記のアーキテクチャから、Azure での SAP BOBI デプロイの実行方法に関する洞察が得られます。 ただし、Azure 上の SAP BOBI プラットフォームでの可能なすべての構成オプションが網羅されているわけではありません。 Load Balancer、ファイル リポジトリ サーバー、DBMS など、異なる製品やサービスをコンポーネントとして選択することで、ビジネス要件に基づいてデプロイを調整できます。

選択したリージョンで可用性ゾーンが使用できない場合は、可用性セットに Azure VM をデプロイできます。 Azure では、可用性セット内に配置された VM は、複数の物理サーバー、コンピューティング ラック、ストレージ ユニット、ネットワーク スイッチ間で実行されます。 ハードウェアまたはソフトウェアの障害が発生しても、影響を受けるのは VM のサブセットだけであり、ソリューション全体は動作状態を維持します。

## <a name="disaster-recovery"></a>障害復旧

このセクションでは、SAP BOBI プラットフォームの DR 保護を提供する戦略について説明します。 これにより、SAP の DR アプローチ全体の主要なリソースである [SAP のディザスター リカバリー](../../../site-recovery/site-recovery-sap.md)に関するドキュメントを補完します。 SAP BOBI プラットフォームについては、SAP ノート [2056228](https://launchpad.support.sap.com/#/notes/2056228) を参照してください。そこでは、DR 環境を安全に実装するための次の方法について説明されています。

 * プライマリ システムからコンテンツを昇格または配布するための、ライフサイクル管理またはフェデレーションの完全または選択的な使用。
 * CMS と FRS の内容の定期的なコピー。

このガイドでは、DR 環境を実装する 2 番目のオプションについて説明します。 ここでは、DR で使用できるすべての構成オプションを完全には網羅しません。 ここでは、Azure のネイティブ サービスと SAP BOBI プラットフォームの構成を組み合わせて使用するソリューションについて説明します。

>[!Important]
>SAP BOBI プラットフォームの各コンポーネントの可用性をセカンダリ リージョンに織り込む必要があります。 DR 戦略全体を徹底的にテストする必要があります。

### <a name="reference-dr-architecture-for-an-sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI プラットフォームの DR の参照アーキテクチャ

この参照アーキテクチャでは、冗長なアプリケーション サーバーを使用した SAP BOBI プラットフォームの複数インスタンス デプロイが実行されています。 DR の場合、SAP BOBI プラットフォームのすべてのコンポーネントをセカンダリ リージョンにフェールオーバーする必要があります。 次の図では、Azure Premium Files がファイルストアとして使用され、SQL Database が CMS および監査リポジトリとして使用され、Application Gateway がトラフィックの負荷分散に使用されています。 各コンポーネントの DR 保護を実現するための戦略は異なります。これについては、次のセクションで説明します。

![Windows の SAP BusinessObjects BI プラットフォームの DR を示す図。](media\businessobjects-deployment-guide\businessobjects-deployment-windows-disaster-recovery.png)

### <a name="load-balancer"></a>Load Balancer

Load Balancer は、SAP BOBI プラットフォームの Web アプリケーション サーバー間でトラフィックを分散するために使用されます。 Azure では、Load Balancer または Application Gateway を使用して、Web サーバー間でトラフィックを負荷分散できます。 ロード バランサー サービスの DR を実現するには、セカンダリ リージョンに別の Load Balancer または Application Gateway を実装する必要があります。 DR フェールオーバー後に同じ URL を保持するには、DNS のエントリを変更して、セカンダリ リージョンで実行されている負荷分散サービスを参照します。

### <a name="virtual-machines-that-run-web-and-bi-application-servers"></a>Web および BI アプリケーション サーバーを実行する仮想マシン

[Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) を使用して、Web および BI アプリケーション サーバーを実行している VM をセカンダリ リージョン上でレプリケートできます。 災害や障害が発生したときに、レプリケートされた環境に簡単にフェールオーバーして作業を続けることができるように、サーバーとアタッチされているすべてのマネージド ディスクがセカンダリ リージョンにレプリケートされます。 Azure DR データ センターへのすべての SAP アプリケーション VM のレプリケートを開始するには、「[仮想マシンを Azure にレプリケートする](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md)」の手順に従います。

### <a name="filestore"></a>Filestore

ファイルストアは、レポートや BI ドキュメントなどの実際のファイルが格納されているディスク ディレクトリです。 ファイルストア内のすべてのファイルが DR リージョンと同期されていることが重要です。 Windows で実行されている SAP BOBI プラットフォームに使用しているファイル共有サービスの種類に基づいて、コンテンツを同期するために必要な DR 戦略を採用する必要があります。 次に例を示します。

- **Azure Premium Files** では、LRS と ZRS のみがサポートされています。 Azure Premium Files の DR 戦略を使用する場合、[AzCopy](../../../storage/common/storage-use-azcopy-v10.md) または [Azure PowerShell](/powershell/module/az.storage/?preserve-view=true&view=azps-5.8.0) を使用して、異なるリージョンの別のストレージ アカウントにファイルをコピーすることができます。 詳細については、「[ディザスター リカバリーとストレージ アカウントのフェールオーバー](../../../storage/common/storage-disaster-recovery-guidance.md)」を参照してください。
- **Azure NetApp Files** によって NFS および SMB ボリュームが提供されるため、ファイルベースの任意のコピー ツールを使用して、Azure リージョン間でデータをレプリケートできます。 別のリージョンにある Azure NetApp Files ボリュームをコピーする方法の詳細については、「[Azure NetApp Files についての FAQ](../../../azure-netapp-files/faq-data-migration-protection.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)」を参照してください。

  Azure NetApp Files リージョン間レプリケーションを使用できます。これは現在[プレビュー](https://azure.microsoft.com/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/)段階であり、NetApp SnapMirror テクノロジが活用されています。 このテクノロジでは、変更されたブロックだけが、圧縮された効率的な形式でネットワークを介して送信されます。 この独自のテクノロジによってリージョン間でのレプリケーションに必要なデータ量が最小化されることで、データ転送コストが削減されます。 また、レプリケーションにかかる時間が短縮されるため、より小さい RPO を実現できます。 詳細については、「[リージョン間レプリケーションを使用するための要件と考慮事項](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md)」を参照してください。

### <a name="cms-database"></a>CMS データベース

DR リージョンの CMS および監査データベースは、プライマリ リージョンで実行されているデータベースのコピーである必要があります。 データベースの種類に応じて、ビジネスに必要な RTO と RPO に基づいて、データベースを DR リージョンにコピーすることが重要です。 このセクションでは、Windows で動作する SAP BOBI アプリケーションでサポートされている Azure の各データベース ソリューションで使用できるさまざまなオプションについて説明します。

#### <a name="azure-sql-database"></a>Azure SQL データベース

[SQL Database](../../../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md) の DR 戦略では、データベースをセカンダリ リージョンにコピーするために 2 つのオプションを使用できます。 どちらの復旧オプションでも、さまざまなレベルの RTO と RPO が提供されます。 各復旧オプションの RTO と RPO の詳細については、「[既存のサーバーにデータベースを復旧する](../../../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md#recover-a-database-to-the-existing-server)」を参照してください。

オプション 1: [geo 冗長データベースのバックアップの復元](../../../azure-sql/database/recovery-using-backups.md#geo-restore)

   SQL Database では、既定で、[ペアになっているリージョン](../../../best-practices-availability-paired-regions.md)にレプリケートされる [GRS BLOB](../../../storage/common/storage-redundancy.md) にデータが格納されます。 SQL Database の場合、バックアップ ストレージの冗長性は、CMS および監査データベースの作成時に構成することも、既存のデータベースに対して更新することもできます。 既存のデータベースに対する変更は、それ以降のバックアップにのみ適用されます。 geo レプリケートされた最新のバックアップから任意の Azure リージョン内の任意の SQL Database でデータベースを復元することができます。 geo リストアでは、geo レプリケートされたバックアップをソースとして使用します。 バックアップが取得される時刻と、別のリージョンの Azure BLOB にその差分バックアップが geo レプリケートされる時刻には時間差があります。 その結果、復元されたデータベースは、元のデータベースより最大 1 時間遅れることがあります。

   >[!Important]
   >geo リストアは、geo 冗長[バックアップ ストレージ](../../../azure-sql/database/automated-backups-overview.md#backup-storage-redundancy)を使用して構成された SQL Database で利用できます。

オプション 2: [geo レプリケーション](../../../azure-sql/database/active-geo-replication-overview.md)または[自動フェールオーバー グループ](../../../azure-sql/database/auto-failover-group-overview.md)

   geo レプリケーションは SQL Database の機能です。これを使用すると、同じまたは異なるリージョン内のサーバー上に、個々のデータベースの読み取り可能なセカンダリ データベースを作成することができます。 CMS または監査データベースで geo レプリケーションが有効な場合、アプリケーションでは別の Azure リージョンにあるセカンダリ データベースへのフェールオーバーを開始できます。 geo レプリケーションは個々のデータベースに対しては有効ですが、SAP BOBI アプリケーション用の複数のデータベース (CMS および監査) の透過的で調整されたフェールオーバーを有効にするには、自動フェールオーバー グループを使用することをお勧めします。 それにより、アクティブ geo レプリケーションの上位にグループのセマンティックが提供されます。これは、個々のデータベースではなく SQL Server 全体 (すべてのデータベース) が他のリージョンにレプリケートされることを意味します。 「[geo レプリケーションとフェールオーバー グループを比較する](../../../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md#compare-geo-replication-with-failover-groups)」の機能の表を確認してください。

   自動フェールオーバー グループには、フェールオーバー時に変更されない読み取り/書き込みおよび読み取り専用リスナー エンドポイントが用意されています。 読み取り/書き込みエンドポイントは、CMS および監査データベースの ODBC 接続エントリでリスナーとして維持できます。 そのため、手動または自動フェールオーバーのどちらをアクティブ化するにしても、フェールオーバーはグループ内のすべてのデータベースをプライマリに切り替えます。 データベースのフェールオーバーが完了した後、DNS レコードが自動的に更新され、エンドポイントが新しいリージョンにリダイレクトされます。 読み取り/書き込みエンドポイントは ODBC 接続でリスナーとして維持されるため、アプリケーションは自動的に CMS データベースに接続されます。

   次の図では、米国東部 2 リージョンで実行されている SQL Server (azussqlbodb) の自動フェールオーバー グループを、米国東部セカンダリ リージョン (DR サイト) にレプリケートしています。 Windows で実行されている BI アプリケーション サーバーのために、読み取り/書き込みリスナー エンドポイントは ODBC 接続でリスナーとして維持されます。 フェールオーバー後も、エンドポイントは同じままです。 BI アプリケーションをセカンダリ リージョンの SQL Database に接続するための手動介入は必要ありません。

   ![SQL Database の自動フェールオーバー グループを示すスクリーンショット。](media\businessobjects-deployment-guide\businessobjects-deployment-windows-sql-failover-group.png)

   このオプションは、オプション 1 よりも低い RTO と RPO を提供します。 このオプションの詳細については、「[自動フェールオーバー グループを使用して、複数のデータベースの透過的な調整されたフェールオーバーを有効にする](../../../azure-sql/database/auto-failover-group-overview.md)」を参照してください。

#### <a name="azure-database-for-mysql"></a>Azure Database for MySQL

Azure Database for MySQL には、障害が発生した場合にデータベースを復旧するためのオプションが用意されています。 ご自身のビジネスに適したオプションを選択してください。

* 事業継続と DR の計画を強化するには、リージョンにまたがる読み取りレプリカを有効にします。 ソース サーバーから最大 5 つのレプリカにレプリケートできます。 読み取りレプリカは、Azure Database for MySQL のバイナリ ログ レプリケーション テクノロジを使用して非同期的に更新されます。 レプリカは、通常の Azure Database for MySQL サーバーと同様に管理する新しいサーバーです。 読み取りレプリカ、利用可能なリージョン、制限、フェールオーバーする方法については、「[Azure Database for MySQL の読み取りレプリカ](../../../mysql/concepts-read-replicas.md)」を参照してください。

* geo 冗長バックアップを使ってサーバーを復元する Azure Database for MySQL の geo リストア機能を使用します。 これらのバックアップは、サーバーがホストされているリージョンがオフラインのときでもアクセスできます。 これらのバックアップから他の任意のリージョンに復元し、サーバーをオンラインに戻すことができます。

  > [!Important]
  > geo リストアは、geo 冗長バックアップ ストレージでサーバーをプロビジョニングした場合にのみ可能です。 サーバー作成後のバックアップ冗長オプションの変更はサポートされていません。 詳細については、[バックアップの冗長性](../../../mysql/concepts-backup.md#backup-redundancy-options)に関する記事をご覧ください。

次の表に、この例で使用される各層の DR に関する推奨事項を示します。

| SAP BOBI プラットフォームの層                          | 推奨                                               |
| ------------------------------------------------ | ------------------------------------------------------------ |
| Azure Application Gateway または Azure Load Balancer | セカンダリ リージョンでの Application Gateway の並列セットアップ    |
| Web アプリケーション サーバー                          | Azure Site Recovery を使ったレプリケーション                             |
| BI アプリケーション サーバー                           | Site Recovery を使ったレプリケーション                             |
| Azure Premium Files                              | AzCopy *または* Azure PowerShell                               |
| Azure NetApp Files                               | セカンダリ リージョンにデータをレプリケートするためのファイルベースのコピー ツール "*または*" Azure NetApp Files のリージョン間レプリケーション (プレビュー) |
| Azure SQL データベース                               | geo レプリケーション/自動フェールオーバー グループ "*または*" geo リストア     |
| Azure Database for MySQL                         | リージョン間の読み取りレプリカ "*または*" geo 冗長バックアップからのバックアップの復元 |

## <a name="next-steps"></a>次の手順

- [多層 SAP アプリ デプロイのディザスター リカバリーを設定する](../../../site-recovery/site-recovery-sap.md)
- [SAP のための Azure Virtual Machines の計画と実装](planning-guide.md)
- [SAP のための Azure Virtual Machines のデプロイ](deployment-guide.md)
- [SAP のための Azure Virtual Machines DBMS のデプロイ](./dbms_guide_general.md)
