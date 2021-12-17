---
title: 'チュートリアル: Azure Data Studio を使用して SQL Server をオフラインで Azure Virtual Machine 上の SQL Server に移行する'
titleSuffix: Azure Database Migration Service
description: Azure Data Studio と Azure Database Migration Service を使用して SQL Server を Azure Virtual Machine 上の SQL Server にオフラインで移行する (プレビュー)
services: dms
author: kbarlett001
ms.author: kebarlet
manager: ''
ms.reviewer: cawrites
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 10/05/2021
ms.openlocfilehash: 01b16a8ac28f89f0bb59944bdb790a2fa6cc8e75
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028555"
---
# <a name="tutorial-migrate-sql-server-to-sql-server-on-azure-virtual-machine-offline-using-azure-data-studio-with-dms-preview"></a>チュートリアル: Azure Data Studio と DMS を使用してオフラインで SQL Server を Azure Virtual Machine 上の SQL Server に移行する (プレビュー)

Azure Data Studio の Azure SQL Migration 拡張機能を使用して、データベースを SQL Server インスタンスから [Azure Virtual Machine 上の SQL Server](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) に最小限のダウンタイムで移行します。 一定の手作業が必要になる可能性のある方法については、[Azure Virtual Machine 上の SQL Server への SQL Server インスタンスの移行](../azure-sql/migration-guides/virtual-machines/sql-server-to-sql-on-azure-vm-migration-overview.md)に関する記事を参照してください。

このチュートリアルでは、Azure Data Studio と Azure Database Migration Service を使用して、SQL Server のオンプレミスのインスタンスから Azure Virtual Machine 上の SQL Server に、オフライン移行方式で **AdventureWorks** データベースを移行します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
>
> * Azure Data Studio で Azure SQL への移行ウィザードを起動します。
> * ソース SQL Server データベースの評価を実行します
> * ソース SQL Server、バックアップ場所、Azure Virtual Machine 上のターゲット SQL Server の詳細を指定します
> * 新しい Azure Database Migration Service を作成し、セルフホステッド統合ランタイムをインストールしてソース サーバーとバックアップにアクセスします。
> * 移行を開始して完了までの進行状況を監視する

この記事では、SQL Server から Azure Virtual Machine 上の SQL Server へのオフライン移行について説明します。 オンライン移行については、「[Azure Data Studio と DMS を使用してオンラインで SQL Server を Azure Virtual Machine 上の SQL Server に移行する](tutorial-sql-server-to-virtual-machine-online-ads.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

* [Azure Data Studio をダウンロードし、インストールする](/sql/azure-data-studio/download-azure-data-studio)
* Azure Data Studio マーケットプレースから [Azure SQL Migration 拡張機能をインストール](/sql/azure-data-studio/extensions/azure-sql-migration-extension)します
* 次に示す組み込みロールのいずれかに割り当てられている Azure アカウントを用意します。
    - ターゲット Azure SQL Managed Instance の共同作成者 (および SMB ネットワーク共有からデータベース バックアップ ファイルをアップロードするためのストレージ アカウント)。
    - ターゲット Azure SQL Managed Instance または Azure ストレージ アカウントを含む Azure リソース グループの所有者または共同作成者ロール。
    - Azure サブスクリプションの所有者または共同作成者ロール。
* [Azure Virtual Machine 上にターゲット SQL Server](../azure-sql/virtual-machines/windows/create-sql-vm-portal.md) を作成します。
* ソース SQL Server の接続に使用するログイン情報が、*sysadmin* サーバー ロールのメンバーであるか、`CONTROL SERVER` アクセス許可が付与されていることを確認します。 
* データベースの完全バックアップ ファイルとトランザクション ログ バックアップ ファイルに、次のいずれかのストレージ オプションを使用します。 
    - SMB ネットワーク共有 
    - Azure ストレージ アカウントのファイル共有または BLOB コンテナー 

    > [!IMPORTANT]
    > - データベース バックアップ ファイルが SMB ネットワーク共有で提供されている場合は、DMS サービスがデータベース バックアップ ファイルをアップロードできる [Azure ストレージ アカウントを作成](../storage/common/storage-account-create.md)します。  Azure Storage アカウントは、Azure Database Migration Service インスタンスの作成先と同じリージョンに作成してください。
    > - Azure Database Migration Service によってバックアップが開始されることはなく、移行には既存のバックアップが使用されます。これは、ディザスター リカバリー計画の一部として既に作成されている場合があります。
    > - 各バックアップは、独立したバックアップ ファイルまたは複数のバックアップ ファイルに書き込まれます。 ただし、1 つのバックアップ メディアに複数のバックアップ (完全バックアップとトランザクション ログなど) を追加することはサポートされていません。 
    > - 大きなバックアップの移行に関連する潜在的な問題が発生する可能性を低減するには、圧縮されたバックアップを使用します。
* ソース SQL Server インスタンスを実行しているサービス アカウントに、データベース バックアップ ファイルが格納されている SMB ネットワーク共有に対する読み取りおよび書き込みアクセス許可があることを確認します。
* Transparent Data Encryption (TDE) で保護されているデータベースのソース SQL Server インスタンス証明書は、データを移行する前に、Azure Virtual Machine 上の SQL Server に移行する必要があります。 詳細については、「[別の SQL Server への TDE で保護されたデータベースの移動](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server)」を参照してください。
    > [!TIP]
    > データベースに [Always Encrypted](/sql/relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio) で保護されている機密データが含まれている場合、Azure Data Studio と DMS を使用した移行プロセスでは、Azure Virtual Machine 上のターゲット SQL Server に Always Encrypted キーが自動的に移行されます。

* データベース バックアップがネットワーク ファイル共有にある場合は、データベース バックアップにアクセスして移行するための[セルフホステッド統合ランタイム](../data-factory/create-self-hosted-integration-runtime.md)をインストールするマシンを用意します。 移行ウィザードでは、セルフホステッド統合ランタイムをダウンロードしてインストールするためのダウンロード リンクと認証キーが提供されます。 移行の準備として、セルフホステッド統合ランタイムをインストールする予定のマシンで、次のアウトバウンド ファイアウォール規則とドメイン名が有効になっていることを確認します。

    | ドメイン名                                          | 送信ポート | 説明                |
    | ----------------------------------------------------- | -------------- | ---------------------------|
    | パブリック クラウド: `{datafactory}.{region}.datafactory.azure.net`<br> または `*.frontend.clouddatahub.net` <br> Azure Government: `{datafactory}.{region}.datafactory.azure.us` <br> 中国: `{datafactory}.{region}.datafactory.azure.cn` | 443            | セルフホステッド統合ランタイムがデータ移行サービスに接続するために必要です。 <br>パブリック クラウドに新しく作成されたデータ ファクトリの場合、セルフホステッド統合ランタイム キーから `{datafactory}.{region}.datafactory.azure.net` 形式の FQDN を見つけます。 古いデータ ファクトリで、セルフホステッド統合キーに FQDN が見つからない場合は、代わりに *.frontend.clouddatahub.net を使用してください。 |
    | `download.microsoft.com`    | 443            | セルフホステッド統合ランタイムが更新プログラムをダウンロードするために必要です。 自動更新を無効にしている場合は、このドメインの構成をスキップできます。 |
    | `*.core.windows.net`          | 443            | ネットワーク共有からデータベース バックアップをアップロードするために Azure ストレージ アカウントに接続するセルフホステッド統合ランタイムによって使用されます |

    > [!TIP]
    > データベース バックアップ ファイルが Azure ストレージ アカウントで既に提供されている場合、移行プロセス中にセルフホステッド統合ランタイムは不要です。

* セルフホステッド統合ランタイムを使用する場合は、ランタイムがインストールされているマシンが、ソース SQL Server インスタンスと、バックアップ ファイルがあるネットワーク ファイル共有に接続できることを確認します。 アウトバウンド ポート 445 を有効にして、ネットワーク ファイル共有へのアクセスを許可する必要があります。 [セルフホステッド統合ランタイムの使用に関する推奨事項](migration-using-azure-data-studio.md#recommendations-for-using-self-hosted-integration-runtime-for-database-migrations)も参照してください
* Azure Database Migration Service を初めて使用する場合は、Microsoft.DataMigration リソース プロバイダーがサブスクリプションに登録されていることを確認します。 [リソース プロバイダーを登録する](quickstart-create-data-migration-service-portal.md#register-the-resource-provider)手順に従ってください

## <a name="launch-the-migrate-to-azure-sql-wizard-in-azure-data-studio"></a>Azure Data Studio で [Azure SQL への移行] ウィザードを起動します

1. Azure Data Studio を開き、サーバー アイコンを選択して、オンプレミスの SQL Server (または Azure Virtual Machine の SQL Server) に接続します。
1. サーバーの接続で、右クリックして **[管理]** を選択します。
1. サーバーのホーム ページで、 **[Azure SQL の移行]** 拡張機能を選択します。
1. [Azure SQL の移行] ダッシュボードで、 **[Azure SQL への移行]** を選択して移行ウィザードを起動します。
    :::image type="content" source="media/tutorial-sql-server-to-virtual-machine-online-ads/launch-migrate-to-azure-sql-wizard.png" alt-text="[Azure SQL への移行] ウィザードを起動する":::
1. 移行ウィザードの最初の手順で、既存または新しい Azure アカウントを Azure Data Studio にリンクします。

## <a name="run-database-assessment-and-select-target"></a>データベースの評価を実行してターゲットを選択する

1. 評価を実行するデータベースを選択し、 **[次へ]** を選択します。
1. Azure Virtual Machine 上の SQL Server をターゲットとして選択します。
    :::image type="content" source="media/tutorial-sql-server-to-virtual-machine-offline-ads/assessment-complete-target-selection.png" alt-text="評価の確認":::
1. **[表示/選択]** ボタンを選択してデータベースの評価結果の詳細を表示し、移行するデータベースを選択し、 **[OK]** を選択します。
1. 対応するドロップダウン リストからサブスクリプション、場所、リソース グループを選択して **Azure Virtual Machine 上のターゲット SQL Server** を指定し、 **[次へ]** を選択します。


## <a name="configure-migration-settings"></a>移行の設定の構成

1. 移行モードとして **[オフライン移行]** を選択します。
    > [!NOTE]
    > オフライン移行モードでは、データベース バックアップ ファイルがターゲットの Azure SQL データベースに復元されている間、ソース SQL Server データベースは書き込みアクティビティに使用できません。 アプリケーションのダウンタイムは、移行プロセスの開始から完了まで通して継続されます。
1. データベース バックアップの場所を選択します。 データベース バックアップは、オンプレミスのネットワーク共有または Azure Storage Blob コンテナーに置くことができます。
    > [!NOTE]
    > データベース バックアップがオンプレミスのネットワーク共有で提供される場合、DMS により、ウィザードの次の手順でセルフホステッド統合ランタイムを設定することが求められます。 セルフホステッド統合ランタイムは、ソース データベースのバックアップにアクセスし、バックアップ セットの有効性を確認して Azure ストレージ アカウントにアップロードするために必要です。<br/> データベース バックアップが既に Azure Storage Blob コンテナー上にある場合は、セルフホステッド統合ランタイムをセットアップする必要はありません。
1. バックアップの場所を選択したら、ソース SQL Server とソース バックアップ場所の詳細を指定します。

    |フィールド    |説明  |
    |---------|-------------|
    |**ソースの資格情報 - ユーザー名**    |ソース SQL Server インスタンスに接続し、バックアップ ファイルを検証するための資格情報 (Windows/SQL 認証)。         |
    |**ソースの資格情報 - パスワード**    |ソース SQL Server インスタンスに接続し、バックアップ ファイルを検証するための資格情報 (Windows/SQL 認証)。         |
    |**バックアップを含むネットワーク共有の場所**     |完全およびトランザクション ログのバックアップ ファイルを含むネットワーク共有の場所。 有効なバックアップ セットに属していないネットワーク共有内の無効なファイルまたはバックアップ ファイルは、移行プロセス中に自動的に無視されます。        |
    |**ネットワーク共有の場所への読み取り権限のある Windows ユーザー アカウント**     |バックアップ ファイルを取得するためのネットワーク共有への読み取りアクセス権を持つ Windows 資格情報 (ユーザー名)。       |
    |**パスワード**     |バックアップ ファイルを取得するためのネットワーク共有への読み取りアクセス権を持つ Windows 資格情報 (パスワード)。         |
    |**ターゲット データベース名** |ターゲット データベース名は、移行プロセス中にターゲットのデータベース名を変更する場合は変更できます。            |

1. 対応するドロップダウン リストから **[サブスクリプション]** 、 **[場所]** 、 **[リソース グループ]** を選択して、**Azure ストレージ アカウント** を指定します。 この Azure ストレージ アカウントは、ネットワーク共有からデータベース バックアップをアップロードするために DMS によって使用されます。 コンテナーを作成する必要はありません。アップロード プロセス中に DMS によって自動的に、指定されたストレージ アカウントに BLOB コンテナーが作成されるためです。
    > [!IMPORTANT]
    > ループバック チェック機能が有効になっていて、ソース SQL Server とファイル共有が同じコンピューター上にある場合、ソースは FQDN を使用してファイル共有にアクセスできません。 この問題を解決するには、[こちら](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd)の手順を使用して、ループバック チェック機能を無効にしてください

## <a name="create-azure-database-migration-service"></a>Azure Database Migration Service を作成する

1. 新しい Azure Database Migration Service を作成するか、以前に作成した既存のサービスを再使用します。
    > [!NOTE]
    > 以前に Azure portal を使用して DMS を作成した場合、Azure Data Studio の移行ウィザードでそれを再使用することはできません。 以前に Azure Data Studio を使用して作成された DMS だけが再使用できます。
1. 既存の DMS がある **リソース グループ** を選択します。そうでない場合は、新しいものを作成する必要があります。 **[Azure Database Migration Service]** ドロップダウンに、選択したリソース グループ内の既存の DMS が一覧表示されます。
1. 既存の DMS を再使用するには、ドロップダウン リストから選択します。セルフホステッド統合ランタイムの状態がページの下部に表示されます。
1. 新しい DMS を作成するには、 **[新規作成]** を選択します。
1. **[Azure Database Migration Service の作成]** 画面で、DMS の名前を入力し、 **[作成]** を選択します。
1. DMS の作成が正常に完了すると、**統合ランタイムをセットアップ** するための詳細が表示されます。
1. **[統合ランタイムのダウンロードとインストール]** を選択して、Web ブラウザーでダウンロード リンクを開きます。 ダウンロードを完了します。 ソース SQL Server への、およびソース バックアップを含む場所への接続の前提条件を満たすマシンに統合ランタイムをインストールします。
1. インストールが完了すると、**Microsoft Integration Runtime Configuration Manager** が自動的に起動して登録プロセスが開始されます。
1. Azure Data Studio のウィザード画面に表示された認証キーの 1 つをコピーして貼り付けます。 認証キーが有効であれば、Integration Runtime Configuration Manager に緑色のチェック マーク アイコンが表示され、引き続き **登録** できることが示されます。
1. セルフホステッド統合ランタイムの登録が正常に完了したら、**Microsoft Integration Runtime Configuration Manager** を閉じ、Azure Data Studio の移行ウィザードに戻ります。
1. Azure Data Studio の **[Azure Database Migration Service の作成]** 画面で **[接続のテスト]** を選択して、新しく作成された DMS が新しく登録されたセルフホステッド統合ランタイムに接続されていることを確認し、 **[完了]** を選択します。
    :::image type="content" source="media/tutorial-sql-server-to-virtual-machine-offline-ads/test-connection-integration-runtime-complete.png" alt-text="統合ランタイムの [接続のテスト]":::
1. 概要を確認し、 **[完了]** を選択してデータベースの移行を開始します。

## <a name="monitor-your-migration"></a>移行を監視する

1. **[データベースの移行状態]** で、進行中の移行、完了した移行、失敗した移行 (ある場合) を追跡できます。

    :::image type="content" source="media/tutorial-sql-server-to-virtual-machine-offline-ads/monitor-migration-dashboard.png" alt-text="移行の監視ダッシュボード":::
1. **[データベースの移行が進行中]** を選択して、進行中の移行を表示し、データベース名を選択して詳細情報を取得します。
1. 移行の詳細ページには、バックアップ ファイルとそれらに対応する状態が表示されます。

    | 状態 | 説明 |
    |--------|-------------|
    | [受信] | バックアップ ファイルがソース バックアップ場所に到着し、検証されました |
    | アップロード | 統合ランタイムで現在、バックアップ ファイルが Azure Storage にアップロードされています|
    | アップロード完了 | バックアップ ファイルが Azure Storage にアップロードされました |
    | Restoring | Azure Database Migration Service で現在バックアップ ファイルが Azure SQL Managed Instance に復元されています|
    | 復元 | バックアップ ファイルが Azure SQL Managed Instance に正常に復元されました |
    | Canceled | 移行プロセスが取り消されました |
    | 無視 | バックアップ ファイルは有効なデータベース バックアップ チェーンに属していないため、無視されました |

すべてのデータベース バックアップが Azure Virtual Machine 上の SQL Server に復元されると、移行されたデータベースが確実に使用できる状態になるように Azure DMS によって自動移行カットオーバーが開始され、移行の状態が *[処理中です]* から *[成功しました]* に変わります。

## <a name="next-steps"></a>次のステップ

* T-SQL RESTORE コマンドを使用して Azure Virtual Machines 上の SQL Server にデータベースを移行する方法を示すチュートリアルについては、「[SQL Server データベースを仮想マシン上の SQL Server に移行する](../azure-sql/virtual-machines/windows/migrate-to-vm-from-sql-server.md)」を参照してください。
* Azure Virtual Machines 上の SQL Server については、[Azure Windows Virtual Machines 上の SQL Serverの概要](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)に関する記事を参照してください。
* Azure Virtual Machines 上の SQL Server へのアプリの接続については、[アプリケーションの接続](../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md)に関する記事を参照してください。
