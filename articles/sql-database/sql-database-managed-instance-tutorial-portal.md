---
title: 'Azure Portal: SQL Database マネージ インスタンスを作成する | Microsoft Docs'
description: VNet で Azure SQL Database マネージ インスタンスを作成し、SSMS を使用して Wide World Importers のデータベース バックアップを復元します。
keywords: SQL Database チュートリアル, SQL Database マネージ インスタンスの作成
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 03/14/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: 774a761465cfd886b85378a35dd43ac656a7ee48
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="create-an-azure-sql-database-managed-instance-in-the-azure-portal"></a>Azure Portal で Azure SQL Database マネージ インスタンスを作成する

このチュートリアルでは、Azure Portal を使用して、仮想ネットワーク (VNet) の専用サブネット内に Azure SQL Database マネージ インスタンス (プレビュー) を作成する方法について説明します。 また、同じ VNet 内の仮想マシン上の SQL Server Management Studio を使用してマネージ インスタンスに接続してから、Azure BLOB ストレージに格納されたデータベースのバックアップをマネージ インスタンスに復元する方法を示します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

> [!IMPORTANT]
> 現在マネージ インスタンスが提供されているリージョンの一覧については、「[Migrate your databases to a fully managed service with Azure SQL Database Managed Instance (Azure SQL Database マネージ インスタンスを使用してデータベースを完全管理型サービスに移行する)](https://azure.microsoft.com/blog/migrate-your-databases-to-a-fully-managed-service-with-azure-sql-database-managed-instance/)」を参照してください。
 
## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする

[Azure Portal](https://portal.azure.com/#create/Microsoft.SQLManagedInstance) にログインします。

## <a name="whitelist-your-subscription"></a>サブスクリプションをホワイトリストに登録する

マネージ インスタンスは、初期段階で、サブスクリプションをホワイトリストに登録する必要がある公開プレビューとしてリリースされています。 サブスクリプションがまだホワイトリストに登録されていない場合は、以下の手順でプレビューの使用条件に同意し、ホワイトリストに登録するための要求を送信します。

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。
2. **[マネージ インスタンス]** を探し、**[Azure SQL Database Managed Instance (preview)]\(Azure SQL Database マネージ インスタンス (プレビュー)\)** を選択します。
3. **Create** をクリックしてください。

   ![マネージ インスタンスの作成](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

3. サブスクリプションを選択し、**[プレビューの使用条件]** をクリックし、必要な情報を入力します。

   ![マネージ インスタンスのプレビューの使用条件](./media/sql-database-managed-instance-tutorial/preview-terms.png)

5. 完了したら、**[OK]** をクリックします。

   ![マネージ インスタンスのプレビューの使用条件](./media/sql-database-managed-instance-tutorial/preview-approval-pending.png)

   > [!NOTE]
   > プレビューの承認を待つ間、このチュートリアルの次のいくつかのセクションを続けて完了することができます。

## <a name="configure-a-virtual-network-vnet"></a>仮想ネットワーク (VNet) を構成する

ここでは、マネージ インスタンスで使用する新しい[Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md) 仮想ネットワーク (VNet) を作成する手順について説明します。 VNet の構成の詳細については、[マネージ インスタンス VNet の構成](sql-database-managed-instance-vnet-configuration.md)に関するページを参照してください。

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。
2. **[仮想ネットワーク]** を見つけてクリックし、**[リソース マネージャー]** がデプロイ モードとして選択されていることを確認してから、**[作成]** をクリックします。

   ![仮想ネットワークの作成](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. 次の表の情報を参考にして、仮想ネットワークのフォームに必要な情報を入力します。

   | Setting| 推奨値 | Description |
   | ------ | --------------- | ----------- |
   |**名前**|有効な名前|有効なサーバー名については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   |**アドレス空間**|任意の有効なアドレス範囲 (10.14.0.0/24 など)|仮想ネットワークのアドレス名 (CIDR 表記)。|
   |**サブスクリプション**|該当するサブスクリプション|サブスクリプションの詳細については、[サブスクリプション](https://account.windowsazure.com/Subscriptions)に関するページを参照してください。|
   |**リソース グループ**|任意の有効なリソース グループ (新規または既存)|有効なサーバー名については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   |**場所**|有効な場所| リージョンについては、「[Azure リージョン](https://azure.microsoft.com/regions/)」を参照してください。|
   |**サブネット名**|任意の有効なサブネット名 (mi_subnet など)|有効なサーバー名については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   |**サブネットのアドレス範囲**|任意の有効なサブネット アドレス (10.14.0.0/28 など)。 アドレス空間よりも小さいサブネット アドレス空間を使用して、テスト/クライアント アプリケーションをホスティングするサブネットや、オンプレミスや他の VNets から接続するゲートウェイ サブネットなど、同じ VNet 内に他のサブネットを作成できるようにします。|サブネットのアドレス範囲 (CIDR 表記)。 仮想ネットワークのアドレス空間に含まれている必要があります|
   |**サービス エンドポイント**|無効|このサブネットに対して 1 つ以上のサービス エンドポイントを有効にします|
   ||||

   ![仮想ネットワークの作成フォーム](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. **Create** をクリックしてください。

## <a name="create-new-route-table-and-a-route"></a>新しいルート テーブルとルートを作成する

ここでは、0.0.0.0/0 ネクスト ホップ インターネット ルートを作成する手順について説明します。

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。
2. **[ルート テーブル]** を見つけてクリックし、ルート テーブル ページで **[作成]** をクリックします。 

   ![ルート テーブルの作成](./media/sql-database-managed-instance-tutorial/route-table-create.png)

3. 次の表の情報を参考にして、ルート テーブルのフォームに必要な情報を入力します。

   | Setting| 推奨値 | Description |
   | ------ | --------------- | ----------- |
   |**名前**|有効な名前|有効なサーバー名については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   |**サブスクリプション**|該当するサブスクリプション|サブスクリプションの詳細については、[サブスクリプション](https://account.windowsazure.com/Subscriptions)に関するページを参照してください。|
   |**リソース グループ**|前の手順で作成したリソース グループを選択します|有効なサーバー名については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   |**場所**|前の手順で指定した場所を選択します| リージョンについては、「[Azure リージョン](https://azure.microsoft.com/regions/)」を参照してください。|
   |**BCP ルートの伝達を無効にする**|無効||
   ||||

   ![ルート テーブルの作成フォーム](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

4. **Create** をクリックしてください。
5. ルート テーブルが作成されたら、新しく作成されたルート テーブルを開きます。

   ![ルート テーブル](./media/sql-database-managed-instance-tutorial/route-table.png)

6. **[ルート]** をクリックし、**[追加]** をクリックします。

   ![ルート テーブルの追加](./media/sql-database-managed-instance-tutorial/route-table-add.png)

7.  次の表の情報を参考にして、**[0.0.0.0/0 Next Hop Internet route]\(0.0.0.0/0 次ホップ インターネット ルート\)** を**唯一**のルートとして追加します。

    | Setting| 推奨値 | [説明] |
    | ------ | --------------- | ----------- |
    |**ルート名**|有効な名前|有効なサーバー名については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
    |**アドレス プレフィックス**|0.0.0.0/0|このルートが適用される CIDR 表記の宛先 IP アドレス。|
    |**次ホップの種類**|インターネット|次ホップは、このルートに一致するパケットを処理します|
    |||

    ![ルート](./media/sql-database-managed-instance-tutorial/route.png)

8. Click **OK**.

## <a name="to-apply-the-route-table-to-the-managed-instance-subnet"></a>マネージ インスタンスのサブネットにルート テーブルを適用するには

ここでは、マネージ インスタンスのサブネット上に新しいルート テーブルを設定する手順について説明します。

1. マネージ インスタンスのサブネット上にルート テーブルを設定するには、以前に作成した仮想ネットワークを開きます。
2. **[サブネット]** をクリックし、マネージ インスタンスのサブネット (次のスクリーンショットの **mi_subnet**) をクリックします。

    ![サブネット](./media/sql-database-managed-instance-tutorial/subnet.png)

11. **[ルート テーブル]**をクリックし、**myMI_route_table** を選択します。

    ![ルート テーブルの設定](./media/sql-database-managed-instance-tutorial/set-route-table.png)

12. **[保存]**

    ![ルート テーブルの設定の保存](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)

## <a name="create-a-managed-instance"></a>マネージ インスタンスを作成する

ここでは、プレビューが承認された後、マネージ インスタンスを作成する手順について説明します。

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。
2. **[マネージ インスタンス]** を探し、**[Azure SQL Database Managed Instance (preview)]\(Azure SQL Database マネージ インスタンス (プレビュー)\)** を選択します。
3. **Create** をクリックしてください。

   ![マネージ インスタンスの作成](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

3. サブスクリプションを選択し、プレビューの使用条件が **[同意済み]** と表示されていることを確認します。

   ![マネージ インスタンスのプレビューの同意済み](./media/sql-database-managed-instance-tutorial/preview-accepted.png)

4. 次の表の情報を参考にして、マネージ インスタンスのフォームに必要な情報を入力します。

   | Setting| 推奨値 | [説明] |
   | ------ | --------------- | ----------- |
   |**マネージ インスタンス名**|有効な名前|有効なサーバー名については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   |**マネージ インスタンス管理者ログイン**|任意の有効なユーザー名|有効なサーバー名については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。| 
   |**パスワード**|有効なパスワード|パスワードは 12 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。|
   |**リソース グループ**|以前の手順で作成したリソース グループ||
   |**場所**|以前の手順で選択した場所|リージョンについては、「[Azure リージョン](https://azure.microsoft.com/regions/)」を参照してください。|
   |**Virtual Network**|以前の手順で作成した仮想ネットワーク|

   ![マネージ インスタンスの作成フォーム](./media/sql-database-managed-instance-tutorial/managed-instance-create-form.png)

5. **[価格レベル]** をクリックしてコンピューティング リソースとストレージ リソースのサイズを指定し、価格レベルのオプションを確認します。 インスタンスには既定で 32 GB の記憶域スペースが無料で提供されますが、アプリケーションによっては足りない可能性があります。
6. スライダーまたはテキスト ボックスを使用して、記憶域のサイズと仮想コアの数を指定します。 
   ![マネージ インスタンスの作成フォーム](./media/sql-database-managed-instance-tutorial/managed-instance-pricing-tier.png)

7. 完了したら **[適用]** をクリックして選択内容を保存します。  
8. **[作成]** をクリックしてマネージ インスタンスをデプロイします。
9. **[通知]** アイコンをクリックしてデプロイの状態を表示します。
 
   ![デプロイの進行状況](./media/sql-database-managed-instance-tutorial/deployment-progress.png)

9. **[デプロイを実行しています]** をクリックして [マネージ インスタンス] ウィンドウを開き、デプロイの進行状況を詳しく監視します。
 
   ![デプロイの進行状況 2](./media/sql-database-managed-instance-tutorial/managed-instance.png)

デプロイが行われている間に、次の手順に進みます。

> [!IMPORTANT]
> サブネット内の最初のインスタンスの場合、通常、デプロイ時間は後続のインスタンスよりもはるかに長くなり、完了するまでに 24 時間以上かかることがあります。 デプロイ操作が予想以上に長くなった場合でも、デプロイ操作を取り消さないでください。 最初のインスタンスをデプロイするときにかかるこの時間の長さは、一時的な問題です。 パブリック プレビューの開始から間もなく、デプロイ時間が大幅に短縮される予定です。

## <a name="create-a-new-subnet-in-the-vnet-for-a-virtual-machine"></a>仮想マシンの VNet に新しいサブネットを作成する

ここでは、仮想マシンの VNet に 2 つ目のサブネットを作成する手順について説明します。この手順の中で、SQL Server Management Studio をインストールし、マネージ インスタンスに接続します。

1. 仮想ネットワーク リソースを開きます。
 
   ![VNet](./media/sql-database-managed-instance-tutorial/vnet.png)

2. **[サブネット]**、**[+ サブネット]** の順にクリックします。
 
   ![サブネットの追加](./media/sql-database-managed-instance-tutorial/add-subnet.png)

3. 次の表の情報を参考にして、サブネットのフォームに必要な情報を入力します。

   | Setting| 推奨値 | Description |
   | ------ | --------------- | ----------- |
   |**名前**|有効な名前|有効なサーバー名については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   |**アドレス範囲 (CIDR ブロック)**|VNet 内の任意の有効なアドレス範囲 (既定を使用)||
   |**ネットワーク セキュリティ グループ**|なし||
   |**ルート テーブル**|なし||
   |**サービス エンド ポイント**|なし||

   ![VM サブネットの詳細](./media/sql-database-managed-instance-tutorial/vm-subnet-details.png)

4. Click **OK**.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>VNet の新しいサブネットに仮想マシンを作成する

ここでは、同じ VNet 内にマネージ インスタンスを作成する仮想マシンを作成する手順について説明します。 

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。
2. **[コンピューティング]** を選択し、**[Windows Server 2016 Datacenter]** または **[Windows 10]** を選択します。 このチュートリアルのセクションでは、Windows Server を使用しています。 基本的に Windows 10 を構成する方法も同様です。 

   ![コンピューティング](./media/sql-database-managed-instance-tutorial/compute.png)

3. 次の表の情報を参考にして、仮想マシンのフォームに必要な情報を入力します。

   | Setting| 推奨値 | Description |
   | ------ | --------------- | ----------- |
   |**名前**|有効な名前|有効なサーバー名については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   | **VM ディスクの種類**|SSD|SSD を選択すると、価格とパフォーマンスのバランスが最適になります。|   
   |**ユーザー名**|任意の有効なユーザー名|有効なサーバー名については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。| 
   |**パスワード**|有効なパスワード|パスワードは 12 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。| 
   |**サブスクリプション**|該当するサブスクリプション|サブスクリプションの詳細については、[サブスクリプション](https://account.windowsazure.com/Subscriptions)に関するページを参照してください。|
   |**リソース グループ**|以前の手順で作成したリソース グループ||
   |**場所**|以前の手順で選択した場所||
   |**Windows ライセンスを既にお持ちの場合**|いいえ |有効なソフトウェア アシュアランス (SA) 付きの Windows ライセンスを所有している場合は、Azure ハイブリッド特典を使ってコンピューティング コストを節約できます|
   ||||

   ![仮想マシンの作成フォーム](./media/sql-database-managed-instance-tutorial/virtual-machine-create-form.png)

3. Click **OK**.
4. VM のサイズを選択します。 その他のサイズも表示するには、**[すべて表示]** を選択するか、**[Supported disk type (サポートされているディスクの種類)]** フィルターを変更します。 このチュートリアルには、1 つの小さな仮想マシンのみが必要です。

    ![VM サイズ](./media/sql-database-managed-instance-tutorial/virtual-machine-size.png)  

5. **[選択]**をクリックします。
6. **[設定]** フォームの **[サブネット]** をクリックし、**vm_subnet** を選択します。 マネージ インスタンスがプロビジョニングされているサブネットではなく、同じ Vnet 内の別のサブネットを選択してください。

    ![VM 設定](./media/sql-database-managed-instance-tutorial/virtual-machine-settings.png)  

7. Click **OK**.
8. [概要] ページでプランの詳細を確認し、**[作成]** をクリックして、仮想マシンのデプロイを開始します。
 
## <a name="connect-to-virtual-machine"></a>仮想マシンへの接続

ここでは、リモート デスクトップ接続を使用して新しく作成した仮想マシンに接続する手順について説明します。

1. デプロイが完了したら、仮想マシン リソースに移動します。

    ![VM](./media/sql-database-managed-instance-tutorial/vm.png)  

2. 仮想マシンのプロパティで、**[接続]** ボタンをクリックします。 リモート デスクトップ プロトコル ファイル (.rdp ファイル) が作成されてダウンロードされます。
3. VM に接続するには、ダウンロードした RDP ファイルを開きます。 
4. メッセージが表示されたら、**[接続]** をクリックします。 Mac では、この[リモート デスクトップ クライアント](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12)のような RDP クライアントを Mac App Store から入手する必要があります。

5. 仮想マシンの作成時に指定したユーザー名とパスワードを入力し、**[OK]** をクリックします。

6. サインイン処理中に証明書の警告が表示される場合があります。 **[はい]** または **[続行]** をクリックして接続処理を続行します。

サーバー マネージャー ダッシュボードには、仮想マシンに接続済みと表示されます。

> [!IMPORTANT]
> マネージ インスタンスが正常にプロビジョニングされるまで、次の手順には進まないでください。 プロビジョニングが完了したら、マネージ インスタンスの **[概要]** タブの **[マネージ インスタンス]** フィールドでインスタンスのホスト名を取得します。 名前は **drfadfadsfd.tr23.westus1-a.worker.database.windows.net** のようになります。

## <a name="install-ssms-and-connect-to-the-managed-instance"></a>SSMS をインストールし、マネージ インスタンスに接続する

ここでは、SSMS をダウンロードしてインストールし、マネージ インスタンスに接続する手順について説明します。

1. サーバー マネージャーの左側のウィンドウで **[ローカル サーバー]** をクリックします。

    ![サーバー マネージャーのプロパティ](./media/sql-database-managed-instance-tutorial/server-manager-properties.png)  

2. **[プロパティ]** ウィンドウで **[オン]** をクリックして、[IE セキュリティ強化の構成] を変更します。
3. **[Internet Explorer セキュリティ強化の構成]** ダイアログ ボックスで、ダイアログ ボックスの [管理者] セクションで **[オフ]** をクリックし、**[OK]** をクリックします。

    ![Internet Explorer セキュリティ強化の構成](./media/sql-database-managed-instance-tutorial/internet-explorer-security-configuration.png)  
4. タスク バーから **Internet Explorer** を開きます。
5. **[お勧めのセキュリティと互換性の設定を使う]** を選択し、**[OK]** をクリックして、Internet Explorer 11 の設定を完了します。
6. URL アドレス ボックスに「https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms」と入力し、**Enter** キーを押します。 
7. SQL Server Management Studio の最新バージョンをダウンロードし、メッセージが表示されたら **[実行]** をクリックします。
8. メッセージが表示されたら、**[インストール]** をクリックしてインストールを開始します。
9. インストールが完了したら、**[閉じる]** をクリックします。
10. SSMS を開きます。
11. **[サーバーに接続]** ダイアログ ボックスで、**[サーバー名]** ボックスにマネージ インスタンスの**ホスト名*を入力し、**[SQL Server 認証]** を選択し、ログイン アカウントとパスワードを入力し、**[接続]** をクリックします。

    ![SSMS 接続](./media/sql-database-managed-instance-tutorial/ssms-connect.png)  

接続後は、データベース ノード内のシステム データベースとユーザー データベースを確認できます。また、セキュリティ、サーバー オブジェクト、レプリケーション、管理、SQL Server エージェント、および XEvent プロファイラー ノードのさまざまなオブジェクトを確認できます。

## <a name="download-the-wide-world-importers---standard-backup-file"></a>Wide World Importers - 標準のバックアップ ファイルをダウンロードする

ここでは、Wide World Importers - 標準のバックアップ ファイルをダウンロードする手順について説明します。

Internet Explorer を使用して、URL アドレス ボックスに https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak と入力し、メッセージが表示されたら **[保存]** をクリックし、このファイルを **Downloads** フォルダーに保存します。

## <a name="create-azure-storage-account-and-upload-backup-file"></a>Azure ストレージ アカウントを作成してバックアップ ファイルをアップロードする

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。
2. **[ストレージ]** を見つけ、**[ストレージ アカウント]** をクリックしてストレージ アカウント フォームを開きます。

   ![ストレージ アカウント](./media/sql-database-managed-instance-tutorial/storage-account.png)

3. 次の表の情報を参考にして、ストレージ アカウントのフォームに必要な情報を入力します。

   | Setting| 推奨値 | Description |
   | ------ | --------------- | ----------- |
   |**名前**|有効な名前|有効なサーバー名については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   |**デプロイ モデル**|リソース モデル||
   |**アカウントの種類**|BLOB ストレージ||
   |**パフォーマンス**|Standard または Premium|磁気ドライブまたは SSD|
   |**レプリケーション**|ローカル冗長ストレージ||
   |**アクセス層 (既定)|クールまたはホット||
   |**安全な転送が必須**|無効||
   |**サブスクリプション**|該当するサブスクリプション|サブスクリプションの詳細については、[サブスクリプション](https://account.windowsazure.com/Subscriptions)に関するページを参照してください。|
   |**[リソース グループ]**|以前の手順で作成したリソース グループ|| 
   |**場所**|以前の手順で選択した場所||
   |**仮想ネットワーク**|無効||

4. **Create** をクリックしてください。

   ![ストレージ アカウントの詳細](./media/sql-database-managed-instance-tutorial/storage-account-details.png)

5. ストレージ アカウントの展開が成功したら、新しいストレージ アカウントを開きます。
6. **[設定]** の **[Shared Access Signature]** をクリックし、Shared Access Signature (SAS) フォームを開きます。

   ![SAS フォーム](./media/sql-database-managed-instance-tutorial/sas-form.png)

7. SAS フォームで、必要に応じて既定値を変更します。 有効期限は既定で 8 時間です。
8. **[SAS の生成]** をクリックします。

   ![SAS フォームの入力](./media/sql-database-managed-instance-tutorial/sas-generate.png)

9. **[SAS トークン]** と **[Blob server SAS URL]\(BLOB サーバーの SAS の URL\)** をコピーして保存します。
10. **[設定]** で **[コンテナー]** をクリックします。

    ![containers](./media/sql-database-managed-instance-tutorial/containers.png)

11. **[+ コンテナー]** をクリックしてバックアップ ファイルを格納するコンテナーを作成します。
12. 次の表の情報を参考にして、コンテナーのフォームに必要な情報を入力します。

    | Setting| 推奨値 | Description |
   | ------ | --------------- | ----------- |
   |**名前**|有効な名前|有効なサーバー名については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   |**パブリック アクセス レベル**|プライベート (匿名アクセスなし)||

    ![コンテナーの詳細](./media/sql-database-managed-instance-tutorial/container-detail.png)

13. Click **OK**.
14. コンテナーが作成されたら、コンテナーを開きます。

    ![container](./media/sql-database-managed-instance-tutorial/container.png)

15. **[コンテナーのプロパティ]** をクリックし、URL をコンテナーにコピーします。

    ![コンテナーの URL](./media/sql-database-managed-instance-tutorial/container-url.png)

16. **[アップロード]** をクリックして **[BLOB のアップロード]** フォームを開きます。

    ![upload](./media/sql-database-managed-instance-tutorial/upload.png)

17. ダウンロード フォルダーを参照し、**AdventureWorks2016.bak** ファイルを選択します。

    ![upload](./media/sql-database-managed-instance-tutorial/upload-bak.png)

18. **[アップロード]**をクリックします。
19. アップロードが完了するまで、次の手順には進まないでください。

    ![アップロード完了](./media/sql-database-managed-instance-tutorial/upload-complete.png)


## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>バックアップ ファイルから Wide World Importers データベースを復元する

SSMS で、以下の手順を使用して、バックアップ ファイルから Wide World Importers データベースをマネージ インスタンスに復元します。

1. SSMS で、新しいクエリ ウィンドウを開きます。
2. 次のスクリプトを使用して SAS 資格情報を作成します。ストレージ アカウント コンテナーと SAS キーの URL を指示に従って入力します。

   ```
CREATE CREDENTIAL [https://<storage_account_name>.blob.core.windows.net/<container>] 
WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
, SECRET = '<shared_access_signature_key_with_removed_first_?_symbol>' 
   ```

    ![資格情報](./media/sql-database-managed-instance-tutorial/credential.png)

3. 次のスクリプトを使用して、SAS 資格情報とバックアップが有効であることを確認します。コンテナーの URL にバックアップ ファイルを指定します。

   ```
   RESTORE FILELISTONLY FROM URL = 
   'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'
   ```

    ![ファイル一覧](./media/sql-database-managed-instance-tutorial/file-list.png)

4. 次のスクリプトを使用して、バックアップ ファイルから Adventure Works 2012 データベースを復元します。コンテナーの URL にバックアップ ファイルを指定します。

   ```
   RESTORE DATABASE [Wide World Importers] FROM URL =
  'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'`
   ```

    ![復元の実行](./media/sql-database-managed-instance-tutorial/restore-executing.png)

5. 復元の状態を追跡するには、新しいクエリ セッションで次のクエリを実行します。

   ```
SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
, dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
FROM sys.dm_exec_requests r 
CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

    ![復元の完了率](./media/sql-database-managed-instance-tutorial/restore-percent-complete.png)

6. 復元が完了したら、オブジェクト エクスプローラーで確認します。 

    ![復元の完了](./media/sql-database-managed-instance-tutorial/restore-complete.png)

## <a name="next-steps"></a>次の手順

- マネージ インスタンスについては、「[What is a Managed Instance?](sql-database-managed-instance.md)」(マネージ インスタンスとは) を参照してください。
- VNet の構成の詳細については、[マネージ インスタンス VNet の構成](sql-database-managed-instance-vnet-configuration.md)に関するページを参照してください。
- アプリケーションの接続については、[アプリケーションの接続](sql-database-managed-instance-connect-app.md)に関するページを参照してください。
- Azure Database Migration Service (DMS) を使用して移行する方法のチュートリアルについては、[DMS を使用したマネージ インスタンスの移行](../dms/tutorial-sql-server-to-managed-instance.md)に関するページを参照してください。
