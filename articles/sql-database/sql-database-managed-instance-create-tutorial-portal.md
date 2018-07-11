---
title: 'Azure Portal: SQL Database マネージド インスタンスを作成する | Microsoft Docs'
description: VNet で Azure SQL Database マネージド インスタンスを作成します。
keywords: SQL Database チュートリアル, SQL Database マネージド インスタンスの作成
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 05/09/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: e337a5c7c203e2e1048149dfeff71436a4d2752f
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850612"
---
# <a name="create-an-azure-sql-database-managed-instance-in-the-azure-portal"></a>Azure Portal で Azure SQL Database マネージド インスタンスを作成する

このチュートリアルでは、仮想ネットワーク (VNet) の専用サブネットで Azure Portal を使用して Azure SQL Database マネージド インスタンス (プレビュー) を作成し、同じ VNet 内の仮想マシンで SQL Server Management Studio を使用してマネージド インスタンスに接続する方法を示します。

> [!div class="checklist"]
> * サブスクリプションをホワイトリストに登録する
> * 仮想ネットワーク (VNet) を構成する
> * 新しいルート テーブルとルートを作成する
> * マネージド インスタンスのサブネットにルート テーブルを適用する
> * マネージド インスタンスを作成する
> * 仮想マシンの VNet に新しいサブネットを作成する
> * VNet の新しいサブネットに仮想マシンを作成する
> * 仮想マシンへの接続
> * SSMS をインストールし、マネージド インスタンスに接続する


Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

> [!IMPORTANT]
> 現在マネージ インスタンスが提供されているリージョンの一覧については、「[Migrate your databases to a fully managed service with Azure SQL Database Managed Instance](https://azure.microsoft.com/blog/migrate-your-databases-to-a-fully-managed-service-with-azure-sql-database-managed-instance/)」(Azure SQL Database マネージ インスタンスを使用してデータベースを完全管理型サービスに移行する) を参照してください。
 
## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする

[Azure Portal](https://portal.azure.com/#create/Microsoft.SQLManagedInstance) にログインします。

## <a name="whitelist-your-subscription"></a>サブスクリプションをホワイトリストに登録する

Managed Instance は、初期段階で、サブスクリプションをホワイトリストに登録する必要がある公開プレビューとしてリリースされています。 サブスクリプションがまだホワイトリストに登録されていない場合は、以下の手順でプレビューの使用条件に同意し、ホワイトリストに登録するための要求を送信します。

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。
2. **[マネージ インスタンス]** を探し、**[Azure SQL Database Managed Instance (preview)]\(Azure SQL Database マネージ インスタンス (プレビュー)\)** を選択します。
3. **Create** をクリックしてください。

   ![マネージド インスタンスの作成](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

3. サブスクリプションを選択し、**[プレビューの使用条件]** をクリックし、必要な情報を入力します。

   ![Managed Instance のプレビューの使用条件](./media/sql-database-managed-instance-tutorial/preview-terms.png)

5. 完了したら、**[OK]** をクリックします。

   ![Managed Instance のプレビューの使用条件](./media/sql-database-managed-instance-tutorial/preview-approval-pending.png)

   > [!NOTE]
   > プレビューの承認を待つ間、このチュートリアルの次のいくつかのセクションを続けて完了することができます。

## <a name="configure-a-virtual-network-vnet"></a>仮想ネットワーク (VNet) を構成する

ここでは、マネージド インスタンスで使用する新しい[Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md) 仮想ネットワーク (VNet) を作成する手順について説明します。 VNet の構成の詳細については、[Managed Instance VNet の構成](sql-database-managed-instance-vnet-configuration.md)に関するページを参照してください。

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。
2. **[Virtual Network]** を見つけてクリックし、**[Resource Manager]** がデプロイ モードとして選択されていることを確認してから、**[作成]** をクリックします。

   ![仮想ネットワークの作成](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. 次の表の情報を参考にして、仮想ネットワークのフォームに必要な情報を入力します。

   | 設定| 推奨値 | 説明 |
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

   | 設定| 推奨値 | 説明 |
   | ------ | --------------- | ----------- |
   |**名前**|有効な名前|有効なサーバー名については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   |**サブスクリプション**|該当するサブスクリプション|サブスクリプションの詳細については、[サブスクリプション](https://account.windowsazure.com/Subscriptions)に関するページを参照してください。|
   |**リソース グループ**|前の手順で作成したリソース グループを選択します|有効なサーバー名については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   |**場所**|前の手順で指定した場所を選択します| リージョンについては、「[Azure リージョン](https://azure.microsoft.com/regions/)」を参照してください。|
   |**BCP ルートの伝達を無効にする**|有効||
   ||||

   ![ルート テーブルの作成フォーム](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

4. **Create** をクリックしてください。
5. ルート テーブルが作成されたら、新しく作成されたルート テーブルを開きます。

   ![ルート テーブル](./media/sql-database-managed-instance-tutorial/route-table.png)

6. **[ルート]** をクリックし、**[追加]** をクリックします。

   ![ルート テーブルの追加](./media/sql-database-managed-instance-tutorial/route-table-add.png)

7.  次の表の情報を参考にして、**[0.0.0.0/0 Next Hop Internet route]\(0.0.0.0/0 次ホップ インターネット ルート\)** を**唯一**のルートとして追加します。

    | 設定| 推奨値 | 説明 |
    | ------ | --------------- | ----------- |
    |**ルート名**|有効な名前|有効なサーバー名については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
    |**アドレス プレフィックス**|0.0.0.0/0|このルートが適用される CIDR 表記の宛先 IP アドレス。|
    |**次ホップの種類**|インターネット|次ホップは、このルートに一致するパケットを処理します|
    |||

    ![ルート](./media/sql-database-managed-instance-tutorial/route.png)

8. **[OK]** をクリックします。

## <a name="apply-the-route-table-to-the-managed-instance-subnet"></a>マネージド インスタンスのサブネットにルート テーブルを適用する

ここでは、マネージド インスタンスのサブネット上に新しいルート テーブルを設定する手順について説明します。

1. マネージド インスタンスのサブネット上にルート テーブルを設定するには、以前に作成した仮想ネットワークを開きます。
2. **[サブネット]** をクリックし、マネージド インスタンスのサブネット (次のスクリーンショットの **mi_subnet**) をクリックします。

    ![サブネット](./media/sql-database-managed-instance-tutorial/subnet.png)

11. **[ルート テーブル]** をクリックし、**myMI_route_table** を選択します。

    ![ルート テーブルの設定](./media/sql-database-managed-instance-tutorial/set-route-table.png)

12. **[保存]**

    ![ルート テーブルの設定の保存](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)

## <a name="create-a-managed-instance"></a>マネージド インスタンスを作成する

ここでは、プレビューが承認された後、マネージド インスタンスを作成する手順について説明します。

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。
2. **[マネージ インスタンス]** を探し、**[Azure SQL Database Managed Instance (preview)]\(Azure SQL Database マネージ インスタンス (プレビュー)\)** を選択します。
3. **Create** をクリックしてください。

   ![マネージド インスタンスの作成](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

4. サブスクリプションを選択し、プレビューの使用条件が **[同意済み]** と表示されていることを確認します。

   ![Managed Instance のプレビューの同意済み](./media/sql-database-managed-instance-tutorial/preview-accepted.png)

5. 次の表の情報を参考にして、マネージド インスタンスのフォームに必要な情報を入力します。

   | 設定| 推奨値 | 説明 |
   | ------ | --------------- | ----------- |
   |**マネージド インスタンス名**|有効な名前|有効なサーバー名については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   |**マネージド インスタンス管理者ログイン**|任意の有効なユーザー名|有効なサーバー名については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。 "serveradmin" は予約済みのサーバー レベルのロールであるため、使用しないでください。| 
   |**パスワード**|有効なパスワード|パスワードは 16 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。|
   |**リソース グループ**|以前の手順で作成したリソース グループ||
   |**場所**|以前の手順で選択した場所|リージョンについては、「[Azure リージョン](https://azure.microsoft.com/regions/)」を参照してください。|
   |**Virtual Network**|以前の手順で作成した仮想ネットワーク|

   ![マネージド インスタンスの作成フォーム](./media/sql-database-managed-instance-tutorial/managed-instance-create-form.png)

6. **[価格レベル]** をクリックしてコンピューティング リソースとストレージ リソースのサイズを指定し、価格レベルのオプションを確認します。 インスタンスには既定で 32 GB の記憶域スペースが無料で提供されますが、アプリケーションによっては足りない可能性があります。
7. スライダーまたはテキスト ボックスを使用して、記憶域のサイズと仮想コアの数を指定します。 
   ![マネージド インスタンスの価格レベル](./media/sql-database-managed-instance-tutorial/managed-instance-pricing-tier.png)

8. 完了したら **[適用]** をクリックして選択内容を保存します。  
9. **[作成]** をクリックしてマネージド インスタンスをデプロイします。
10. **[通知]** アイコンをクリックしてデプロイの状態を表示します。
 
   ![デプロイの進行状況](./media/sql-database-managed-instance-tutorial/deployment-progress.png)

11. **[デプロイを実行しています]** をクリックして [マネージド インスタンス] ウィンドウを開き、デプロイの進行状況を詳しく監視します。
 
   ![デプロイの進行状況 2](./media/sql-database-managed-instance-tutorial/managed-instance.png)

デプロイが行われている間に、次の手順に進みます。

> [!IMPORTANT]
> サブネット内の最初のインスタンスの場合、通常、デプロイ時間は後続のインスタンスよりもはるかに長くなり、完了するまでに 24 時間以上かかることがあります。 デプロイ操作が予想以上に長くなった場合でも、デプロイ操作を取り消さないでください。 最初のインスタンスをデプロイするときにかかるこの時間の長さは、一時的な問題です。 パブリック プレビューの開始から間もなく、デプロイ時間が大幅に短縮される予定です。

## <a name="create-a-new-subnet-in-the-vnet-for-a-virtual-machine"></a>仮想マシンの VNet に新しいサブネットを作成する

ここでは、仮想マシンの VNet に 2 つ目のサブネットを作成する手順について説明します。この手順の中で、SQL Server Management Studio をインストールし、マネージド インスタンスに接続します。

1. 仮想ネットワーク リソースを開きます。
 
   ![VNet](./media/sql-database-managed-instance-tutorial/vnet.png)

2. **[サブネット]**、**[+ サブネット]** の順にクリックします。
 
   ![サブネットの追加](./media/sql-database-managed-instance-tutorial/add-subnet.png)

3. 次の表の情報を参考にして、サブネットのフォームに必要な情報を入力します。

   | 設定| 推奨値 | 説明 |
   | ------ | --------------- | ----------- |
   |**名前**|有効な名前|有効なサーバー名については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   |**アドレス範囲 (CIDR ブロック)**|VNet 内の任意の有効なアドレス範囲 (既定を使用)||
   |**ネットワーク セキュリティ グループ**|なし||
   |**ルート テーブル**|なし||
   |**サービス エンド ポイント**|なし||

   ![VM サブネットの詳細](./media/sql-database-managed-instance-tutorial/vm-subnet-details.png)

4. Click **OK**.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>VNet の新しいサブネットに仮想マシンを作成する

ここでは、同じ VNet 内にマネージド インスタンスを作成する仮想マシンを作成する手順について説明します。 

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。
2. **[コンピューティング]** を選択し、**[Windows Server 2016 Datacenter]** または **[Windows 10]** を選択します。 このチュートリアルのセクションでは、Windows Server を使用しています。 基本的に Windows 10 を構成する方法も同様です。 

   ![コンピューティング](./media/sql-database-managed-instance-tutorial/compute.png)

3. 次の表の情報を参考にして、仮想マシンのフォームに必要な情報を入力します。

   | 設定| 推奨値 | 説明 |
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

4. Click **OK**.
5. VM のサイズを選択します。 その他のサイズも表示するには、**[すべて表示]** を選択するか、**[Supported disk type (サポートされているディスクの種類)]** フィルターを変更します。 このチュートリアルには、1 つの小さな仮想マシンのみが必要です。

    ![VM サイズ](./media/sql-database-managed-instance-tutorial/virtual-machine-size.png)  

6. **[選択]** をクリックします。
7. **[設定]** フォームの **[サブネット]** をクリックし、**vm_subnet** を選択します。 マネージド インスタンスがプロビジョニングされているサブネットではなく、同じ Vnet 内の別のサブネットを選択してください。

    ![VM 設定](./media/sql-database-managed-instance-tutorial/virtual-machine-settings.png)  

8. Click **OK**.
9. [概要] ページでプランの詳細を確認し、**[作成]** をクリックして、仮想マシンのデプロイを開始します。
 
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
> マネージド インスタンスが正常にプロビジョニングされるまで、次の手順には進まないでください。 プロビジョニングが完了したら、マネージド インスタンスの **[概要]** タブの **[マネージド インスタンス]** フィールドでインスタンスのホスト名を取得します。 名前は **drfadfadsfd.tr23.westus1-a.worker.database.windows.net** のようになります。

## <a name="install-ssms-and-connect-to-the-managed-instance"></a>SSMS をインストールし、マネージド インスタンスに接続する

ここでは、SSMS をダウンロードしてインストールし、マネージド インスタンスに接続する手順について説明します。

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
11. **[サーバーに接続]** ダイアログ ボックスで、**[サーバー名]** ボックスにマネージド インスタンスの**ホスト名*を入力し、**[SQL Server 認証]** を選択し、ログイン アカウントとパスワードを入力し、**[接続]** をクリックします。

    ![SSMS 接続](./media/sql-database-managed-instance-tutorial/ssms-connect.png)  

接続後は、データベース ノード内のシステム データベースとユーザー データベースを確認できます。また、セキュリティ、サーバー オブジェクト、レプリケーション、管理、SQL Server エージェント、および XEvent プロファイラー ノードのさまざまなオブジェクトを確認できます。

> [!NOTE]
> 既存の SQL データベースをマネージド インスタンスに復元するには、[移行用の Azure Database Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md) を使用してデータベース バックアップ ファイルから復元するか、[T-SQL RESTORE コマンド](sql-database-managed-instance-restore-from-backup-tutorial.md)を使用してデータベース バックアップ ファイルから復元するか、[BACPAC ファイルからインポート](sql-database-import.md)します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、仮想ネットワーク (VNet) の専用サブネットで Azure Portal を使用して Azure SQL Database マネージド インスタンス (プレビュー) を作成し、同じ VNet 内の仮想マシンで SQL Server Management Studio を使用してマネージド インスタンスに接続する方法を学習しました。  以下の方法について学習しました。 

> [!div class="checklist"]
> * サブスクリプションをホワイトリストに登録する
> * 仮想ネットワーク (VNet) を構成する
> * 新しいルート テーブルとルートを作成する
> * マネージド インスタンスのサブネットにルート テーブルを適用する
> * マネージド インスタンスを作成する
> * 仮想マシンの VNet に新しいサブネットを作成する
> * VNet の新しいサブネットに仮想マシンを作成する
> * 仮想マシンへの接続
> * SSMS をインストールし、マネージド インスタンスに接続する

データベース バックアップを Azure SQL Database マネージド インスタンスに復元する方法を学習するには、次のチュートリアルに進みます。

> [!div class="nextstepaction"]
>[Azure SQL Database マネージド インスタンスにデータベース バックアップを復元する](sql-database-managed-instance-restore-from-backup-tutorial.md)
