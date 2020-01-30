---
title: Azure Database for MariaDB (プレビュー) 用のプライベート リンクのポータルでのセットアップ方法
description: Azure portal から Azure Database for MariaDB 用のプライベート リンクを構成する方法について説明します
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 76e9526ab39cbccd45a48d2cd24e05867c953774
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/20/2020
ms.locfileid: "76280834"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-preview-using-portal"></a>ポータルを使用して Azure Database for MariaDB (プレビュー) 用のプライベート リンクを作成および管理する

プライベート エンドポイントは、Azure におけるプライベート リンクの基本的な構成要素です。 これによって、仮想マシン (VM) などの Azure リソースが Private Link リソースと非公開で通信できるようになります。  この記事では、Azure portal を使用して Azure 仮想ネットワーク内に VM を作成し、Azure プライベート エンドポイントを含む Azure Database for MariaDB サーバーを作成する方法について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

> [!NOTE]
> この機能は、Azure Database for MariaDB で汎用およびメモリ最適化の価格レベルがサポートされているすべての Azure リージョンで使用できます。

## <a name="sign-in-to-azure"></a>Azure へのサインイン
[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-an-azure-vm"></a>Azure VM の作成

このセクションでは、Private Link リソース (Azure 内の MariaDB サーバー) へのアクセスに使用する VM をホストするために、仮想ネットワークとサブネットを作成します。

### <a name="create-the-virtual-network"></a>仮想ネットワークの作成
このセクションでは、Private Link リソースへのアクセスに使用する VM をホストするために、仮想ネットワークとサブネットを作成します。

1. 画面の左上で、 **[リソースの作成]**  >  **[ネットワーキング]**  >  **[仮想ネットワーク]** の順に選択します。
2. **[仮想ネットワークの作成]** に次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | Name | 「*MyVirtualNetwork*」と入力します。 |
    | アドレス空間 | 「*10.1.0.0/16*」を入力します。 |
    | サブスクリプション | サブスクリプションを選択します。|
    | Resource group | **[新規作成]** を選択し、「*myResourceGroup*」と入力して、 **[OK]** を選択します。 |
    | Location | **[西ヨーロッパ]** を選択します。|
    | サブネット - 名前 | 「*mySubnet*」と入力します。 |
    | サブネット アドレス範囲 | 「*10.1.0.0/24*」と入力します。 |
    |||
3. 残りは既定値のままにして、 **[作成]** を選択します。

### <a name="create-virtual-machine"></a>仮想マシンの作成

1. Azure portal の画面の左上で、 **[リソースの作成]**  >  **[Compute]**  >  **[仮想マシン]** を選択します。

2. **[仮想マシンの作成 - 基本]** に次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** | |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | **[myResourceGroup]** を選択します。 これは前のセクションで作成しました。  |
    | **インスタンスの詳細** |  |
    | 仮想マシン名 | 「*myVm*」と入力します。 |
    | リージョン | **[西ヨーロッパ]** を選択します。 |
    | 可用性のオプション | 既定値 **[インフラストラクチャ冗長は必要ありません]** をそのまま使用します。 |
    | Image | **[Windows Server 2019 Datacenter]** を選択します。 |
    | Size | 既定値 **[Standard DS1 v2]** をそのまま使用します。 |
    | **管理者アカウント** |  |
    | ユーザー名 | 任意のユーザー名を入力します。 |
    | Password | 任意のパスワードを入力します。 パスワードは 12 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。|
    | パスワードの確認 | パスワードを再入力します。 |
    | **受信ポートの規則** |  |
    | パブリック受信ポート | 既定値 **[なし]** のままにします。 |
    | **コスト削減** |  |
    | Windows ライセンスを既にお持ちの場合 | 既定値 **[なし]** のままにします。 |
    |||

1. **ディスク** を選択します。

1. **[仮想マシンの作成 - Disk]** で、既定値のままにし、 **[Next: Networking]\(次へ : ネットワーク\)** を選択します。

1. **[仮想マシンの作成 - ネットワーク]** で次の情報を選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | 仮想ネットワーク | 既定値 **[MyVirtualNetwork]** のままにします。  |
    | アドレス空間 | 既定値 **[10.1.0.0/24]** のままにします。|
    | Subnet | 既定値 **[mySubnet (10.1.0.0/24)]** のままにします。|
    | パブリック IP | 既定値 **(new) myVm-ip\((新規) myVm-ip** のままにします。 |
    | パブリック受信ポート | **[選択したポートを許可する]** を選択します。 |
    | 受信ポートの選択 | **[HTTP]** と **[RDP]** を選択します。|
    |||


1. **[Review + create]\(レビュー + 作成\)** を選択します。 **[確認および作成]** ページが表示され、Azure によって構成が検証されます。

1. "**証に成功しました**" というメッセージが表示されたら、 **[作成]** を選択します。

## <a name="create-an-azure-database-for-mariadb"></a>Azure Database for MariaDB を作成する

このセクションでは、Azure に Azure Database for MariaDB サーバーを作成します。 

1. Azure portal の画面の左上で、 **[リソースの作成]**  >  **[データベース]**  >  **[Azure Database for MariaDB]** を選択します。

1. **[Azure Database for MariaDB]** で、次の情報を入力します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** | |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | **[myResourceGroup]** を選択します。 これは前のセクションで作成しました。|
    | **サーバーの詳細** |  |
    |サーバー名  | 「*myserver*」と入力します。 この名前を取得する場合は、一意の名前を作成します。|
    | 管理者ユーザー名| 任意の管理者名を入力します。 |
    | Password | 任意のパスワードを入力します。 パスワードは 8 文字以上で、定義された要件を満たす必要があります。 |
    | Location | MariaDB サーバーを配置する Azure リージョンを選択します。 |
    |Version  | 必要な MariaDB サーバーのデータベース バージョンを選択します。|
    | コンピューティングとストレージ| ワークロードに基づいて、サーバーに必要な価格レベルを選択します。 |
    |||
 
7. **[OK]** を選択します。 
8. **[Review + create]\(レビュー + 作成\)** を選択します。 **[確認および作成]** ページが表示され、Azure によって構成が検証されます。 
9. "検証に成功しました" というメッセージが表示されたら、 **[作成]** を選択します。 
10. "検証に成功しました" というメッセージが表示されたら、[作成] を選択します。 

## <a name="create-a-private-endpoint"></a>プライベート エンドポイントの作成

このセクションでは、MariaDB サーバーへのプライベート エンドポイントを作成します。 

1. Azure portal の画面の左上で、 **[リソースの作成]**  >  **[ネットワーキング]**  >  **[プライベート リンク センター (プレビュー)]** を選択します。
2. **[プライベート リンク センター - 概要]** の**サービスへのプライベート接続を構築する**オプションで、 **[開始]** を選択します。

    ![Private Link の概要](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. **[Create a private endpoint (Preview) - Basics]\(プライベート エンドポイント (プレビュー) の作成 - 基本\)** で次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** | |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | **[myResourceGroup]** を選択します。 これは前のセクションで作成しました。|
    | **インスタンスの詳細** |  |
    | Name | 「*myPrivateEndpoint*」と入力します。 この名前を取得する場合は、一意の名前を作成します。 |
    |リージョン|**[西ヨーロッパ]** を選択します。|
    |||
5. **リソース** を選択します。
6. **[プライベート エンドポイントの作成 - リソース]** で、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    |接続方法  | 自分のディレクトリ内の Azure リソースに接続するように選択します。|
    | サブスクリプション| サブスクリプションを選択します。 |
    | リソースの種類 | **Microsoft.DBforMariaDB/servers** を選択します。 |
    | リソース |*[myServer]* を選択します。|
    |ターゲット サブリソース |*mariadbServer* を選択します|
    |||
7. **構成** を選択します。
8. **[Create a private endpoint (Preview) - Configuration]\(プライベート エンドポイント (プレビュー) の作成 - 構成\)** で次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    |**ネットワーク**| |
    | 仮想ネットワーク| *[MyVirtualNetwork]* を選択します。 |
    | Subnet |  *[mySubnet]* を選択します。 |
    |**プライベート DNS 統合**||
    |プライベート DNS ゾーンとの統合 |**[はい]** を選択します。 |
    |プライベート DNS ゾーン |*[(新規)privatelink.mariadb.database.azure.com]* を選択します。 |
    |||

1. **[Review + create]\(レビュー + 作成\)** を選択します。 **[確認および作成]** ページが表示され、Azure によって構成が検証されます。 
2. "**証に成功しました**" というメッセージが表示されたら、 **[作成]** を選択します。 

    ![作成された Private Link](media/concepts-data-access-and-security-private-link/show-mariadb-private-link.png)

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>リモート デスクトップ (RDP) を使用して VM に接続


**myVm** を作成した後、次のようにインターネットからそれに接続します。 

1. ポータルの検索バーに、「*myVm*」と入力します。

1. **[接続]** を選択します。 **[接続]** ボタンを選択すると、 **[Connect to virtual machine]\(仮想マシンに接続する\)** が開きます。

1. **[RDP ファイルのダウンロード]** を選択します。 リモート デスクトップ プロトコル ( *.rdp*) ファイルが作成され、お使いのコンピューターにダウンロードされます。

1. *downloaded.rdp* ファイルを開きます。

    1. メッセージが表示されたら、 **[Connect]** を選択します。

    1. VM の作成時に指定したユーザー名とパスワードを入力します。

        > [!NOTE]
        > 場合によっては、 **[その他]**  >  **[別のアカウントを使用する]** を選択して、VM の作成時に入力した資格情報を指定する必要があります。

1. **[OK]** を選択します。

1. サインイン処理中に証明書の警告が表示される場合があります。 証明書の警告を受信する場合は、 **[はい]** または **[続行]** を選択します。

1. VM デスクトップが表示されたら最小化してローカル デスクトップに戻ります。

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>VM から MariaDB サーバーにプライベートにアクセスする

1.  *myVM* のリモート デスクトップで、PowerShell を開きます。

2. 「 `nslookup mydemomserver.privatelink.mariadb.database.azure.com`」と入力します。 

    次のようなメッセージが返されます。
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoMariaDBserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4

3. Test the private link connection for the MariaDB server using any available client. In the example below I have used [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) to do the operation.


4. In **New connection**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | Server type| Select **MariaDB**.|
    | Server name| Select *mydemoserver.privatelink.mariadb.database.azure.com* |
    | User name | Enter username as username@servername which is provided during the MariaDB server creation. |
    |Password |Enter a password provided during the MariaDB server creation. |
    |SSL|Select **Required**.|
    ||

5. Select **Test Connection** or **OK**.

6. (Optionally) Browse databases from left menu and Create or query information from the MariaDB database

7. Close the remote desktop connection to myVm.

## Clean up resources
When you're done using the private endpoint, MariaDB server, and the VM, delete the resource group and all of the resources it contains:

1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results.
2. Select **Delete resource group**.
3. Enter myResourceGroup for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## Next steps

In this how-to, you created a VM on a virtual network, an Azure Database for MariaDB, and a private endpoint for private access. You connected to one VM from the internet and securely communicated to the MariaDB server using Private Link. To learn more about private endpoints, see [What is Azure private endpoint](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).
