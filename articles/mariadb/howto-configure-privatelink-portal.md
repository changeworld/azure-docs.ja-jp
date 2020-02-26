---
title: Azure Database for MariaDB (プレビュー) 用のプライベート リンクのポータルでのセットアップ方法
description: Azure portal から Azure Database for MariaDB 用のプライベート リンクを構成する方法について説明します
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 67b045ff0661e8d0f8e20656a012e85d01e83d7b
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425920"
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

    | 設定 | Value |
    | ------- | ----- |
    | 名前 | 「*MyVirtualNetwork*」と入力します。 |
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

    | 設定 | Value |
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

    | 設定 | Value |
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

    | 設定 | Value |
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

    | 設定 | Value |
    | ------- | ----- |
    | **プロジェクトの詳細** | |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | **[myResourceGroup]** を選択します。 これは前のセクションで作成しました。|
    | **インスタンスの詳細** |  |
    | 名前 | 「*myPrivateEndpoint*」と入力します。 この名前を取得する場合は、一意の名前を作成します。 |
    |リージョン|**[西ヨーロッパ]** を選択します。|
    |||
5. **リソース** を選択します。
6. **[プライベート エンドポイントの作成 - リソース]** で、次の情報を入力または選択します。

    | 設定 | Value |
    | ------- | ----- |
    |接続方法  | 自分のディレクトリ内の Azure リソースに接続するように選択します。|
    | サブスクリプション| サブスクリプションを選択します。 |
    | リソースの種類 | **Microsoft.DBforMariaDB/servers** を選択します。 |
    | リソース |*[myServer]* を選択します。|
    |ターゲット サブリソース |*mariadbServer* を選択します|
    |||
7. **構成** を選択します。
8. **[Create a private endpoint (Preview) - Configuration]\(プライベート エンドポイント (プレビュー) の作成 - 構成\)** で次の情報を入力または選択します。

    | 設定 | Value |
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
    ```

3. 利用可能な任意のクライアントを使用して、MariaDB サーバーのプライベート リンク接続をテストします。 次の例では、[MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) を使用して操作を行いました。


4. **[新しい接続]** で、この情報を入力または選択します。

    | 設定 | Value |
    | ------- | ----- |
    | サーバーの種類| **[MariaDB]** を選択します。|
    | サーバー名| *mydemoserver.privatelink.mariadb.database.azure.com* を選択します。 |
    | ユーザー名 | MariaDB サーバーの作成時に指定したユーザー名を username@servername 形式で入力します。 |
    |Password |MariaDB サーバーの作成時に指定したパスワードを入力します。 |
    |SSL|**[必須]** を選択します。|
    ||

5. **[テスト接続]** または **[OK]** を選択します。

6. (省略可能) 左側のメニューからデータベースを参照して、MariaDB データベースからの情報を作成または照会します

7. myVm へのリモート デスクトップ接続を閉じます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする
プライベート エンドポイント、MariaDB サーバー、VM を使い終えたら、リソース グループとそこに含まれるすべてのリソースを削除します。

1. ポータルの上部にある**検索**ボックスに「 *myResourceGroup*」と入力し、検索結果から  *myResourceGroup* を選択します。
2. **[リソース グループの削除]** を選択します。
3. **[TYPE THE RESOURCE GROUP NAME]\(リソース グループ名を入力してください\)** に「myResourceGroup」と入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

この記事では、仮想ネットワーク上の VM、Azure Database for MariaDB、およびプライベート アクセス用のプライベート エンドポイントを作成しました。 インターネットから 1 台の VM に接続し、Private Link を使用して MariaDB サーバーと安全に通信を行いました。 プライベート エンドポイントの詳細については、「[Azure プライベート エンドポイントとは](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)」を参照してください。
