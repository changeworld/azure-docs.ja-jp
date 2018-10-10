---
title: クライアント VM を接続する - Azure SQL Database Managed Instance | Microsoft Docs
description: SQL Server Management Studio を使用して、Azure 仮想マシンから Azure SQL Database Managed Instance に接続します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab, srbozovi, bonova
manager: craigg
ms.date: 09/13/2018
ms.openlocfilehash: 08b8e37493ea7bc549a2352aaa6714ef7c65bfdb
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159518"
---
# <a name="configure-azure-vm-to-connect-to-an-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance に接続するように Azure VM を構成する

このクイック スタートでは、SQL Server Management Studio (SSMS) を使用して、Azure SQL Database Managed Instance に接続するように Azure 仮想マシンを構成する方法を示します。 ポイント対サイト接続を使用して、オンプレミスのクライアント コンピューターから接続する方法を示すクイック スタートについては、「[ポイント対サイト接続の構成](sql-database-managed-instance-configure-p2s.md)」をご覧ください。 

## <a name="prerequisites"></a>前提条件

このクイック スタートでは、クイック スタート「[マネージド インスタンスを作成する](sql-database-managed-instance-get-started.md)」で作成したリソースを出発点として使用します。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="create-a-new-subnet-in-the-managed-instance-vnet"></a>マネージド インスタンス VNet で新しいサブネットを作成する

次の手順では、Azure 仮想マシンがマネージド インスタンスに接続するための新しいサブネットをマネージド インスタンス VNet に作成します。 マネージド インスタンス サブネットはマネージド インスタンス専用であり、このサブネットに他のリソース (Azure Virtual Machines など) を作成することはできません。 

1. 「[マネージド インスタンスを作成する](sql-database-managed-instance-get-started.md)」クイック スタートで作成したマネージド インスタンス用のリソース グループを開き、マネージド インスタンス用の仮想ネットワークをクリックして、**[サブネット]** をクリックします。

   ![マネージド インスタンスのリソース](./media/sql-database-managed-instance-configure-vm/resources.png)

2. **[サブネット]** の隣の **[+]** 記号をクリックして、新しいサブネットを作成します。

   ![マネージド インスタンスのサブネット](./media/sql-database-managed-instance-configure-vm/subnets.png)

3. 次の表の情報を参考にして、フォームに必要な情報を入力します。

   | Setting| 推奨値 | Description |
   | ---------------- | ----------------- | ----------- | 
   | **名前** | 有効な名前|有効な名前については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   | **アドレス範囲 (CIDR ブロック)** | 有効な範囲 | このクイック スタートでは既定値で問題ありません。|
   | **ネットワーク セキュリティ グループ** | なし | このクイック スタートでは既定値で問題ありません。|
   | **ルート テーブル** | なし | このクイック スタートでは既定値で問題ありません。|
   | **サービス エンドポイント ** | 0 が選択されています | このクイック スタートでは既定値で問題ありません。|
   | **サブネットの委任** | なし | このクイック スタートでは既定値で問題ありません。|
 
   ![クライアント VM 用の新しいマネージド インスタンス サブネット](./media/sql-database-managed-instance-configure-vm/new-subnet.png)

4. **[OK]** をクリックして、マネージ インスタンス VNet にこの追加のサブネットを作成します。

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>VNet の新しいサブネットに仮想マシンを作成する

以下の手順では、マネージド インスタンスに接続するための新しいサブネットに仮想マシンを作成する方法について説明します。 

## <a name="prepare-the-azure-virtual-machine"></a>Azure 仮想マシンを準備する

SQL マネージド インスタンスはプライベート仮想ネットワークに配置されるため、マネージド インスタンスに接続してクエリを実行するための SQL クライアント ツール (SQL Server Management Studio や Azure Data Studio など) がインストールされた Azure VM を作成する必要があります。 このクイック スタートでは、SQL Server Management Studio を使用します。

必要なツールをすべて備えたクライアント仮想マシンを作成する最も簡単な方法は、Azure Resource Manager テンプレートを使用することです。

1. 次のボタンをクリックして、クライアント仮想マシンを作成し、SQL Server Management Studio をインストールします (別のブラウザー タブで、Azure portal にサインインしていることを確認します)。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

2. 次の表の情報を参考にして、フォームに必要な情報を入力します。

   | Setting| 推奨値 | 説明 |
   | ---------------- | ----------------- | ----------- |
   | **サブスクリプション** | 有効なサブスクリプション | 新しいリソースを作成するアクセス許可があるサブスクリプションでなければなりません |
   | **リソース グループ** |「[マネージド インスタンスを作成する](sql-database-managed-instance-get-started.md)」クイック スタートで指定したリソース グループ。|VNet が存在するリソース グループでなければなりません。|
   | **場所** | リソース グループの場所 | この値は、選択したリソース グループに基づいて設定されます | 
   | **仮想マシン名**  | 有効な名前 | 有効な名前については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   |**管理ユーザー名**|任意の有効なユーザー名|有効な名前については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。 "serveradmin" は予約済みのサーバー レベルのロールであるため、使用しないでください。| 
   |**パスワード**|有効なパスワード|パスワードは 12 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。|
   | **仮想マシン サイズ** | 任意の有効なサイズ | このクイック スタートでは、このテンプレートの既定値 **Standard_B2s で十分です。 |
   | **場所**|[resourceGroup().location]| この値は変更しないでください |
   | **仮想ネットワーク名**|以前の手順で選択した場所|リージョンについては、「[Azure リージョン](https://azure.microsoft.com/regions/)」を参照してください。|
   | **サブネット名**|前の手順で作成したサブネットの名前| マネージド インスタンスを作成したサブネットは選択しないでください|
   | **artifacts Location (成果物の場所)** | [deployment().properties.templateLink.uri]  この値は変更しないでください |
   | **artifacts Location Sas token (成果物の場所 SAS トークン)** | 空白のままにする | この値は変更しないでください |

   ![クライアント VM の作成](./media/sql-database-managed-instance-configure-vm/create-client-sql-vm.png)

   「[マネージド インスタンスを作成する](sql-database-managed-instance-get-started.md)」で推奨される VNet 名と既定のサブネットを使用した場合、最後の 2 つのパラメーターを変更する必要はありません。 それ以外の場合は、ネットワーク環境を設定するときに入力した値に、これらの値を変更する必要があります。

3. **[上記の使用条件に同意する]** チェック ボックスをオンにします。
4. **[購入]** をクリックして、ネットワークに Azure VM をデプロイします。
5. **[通知]** アイコンをクリックしてデプロイの状態を表示します。
   
   Azure 仮想マシンが作成されるまで続行しないでください。 

## <a name="connect-to-virtual-machine"></a>仮想マシンへの接続

ここでは、リモート デスクトップ接続を使用して新しく作成した仮想マシンに接続する手順について説明します。

1. デプロイが完了したら、仮想マシン リソースに移動します。

    ![VM](./media/sql-database-managed-instance-configure-vm/vm.png)  

2. **[接続]** をクリックします。 
   
   リモート デスクトップ プロトコル ファイル (.rdp ファイル) のフォームが表示され、仮想マシンのパブリック IP アドレスとポート番号が示されます。 

   ![RDP フォーム](./media/sql-database-managed-instance-configure-vm/rdp.png)  

3. **[RDP ファイルのダウンロード]** をクリックします。
 
   > [!NOTE]
   > SSH を使用して VM に接続することもできます。

4. **[仮想マシンに接続する]** フォームを閉じます。
5. VM に接続するには、ダウンロードした RDP ファイルを開きます。 
6. メッセージが表示されたら、**[接続]** をクリックします。 Mac では、この[リモート デスクトップ クライアント](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12)のような RDP クライアントを Mac App Store から入手する必要があります。

6. 仮想マシンの作成時に指定したユーザー名とパスワードを入力し、**[OK]** をクリックします。

7. サインイン処理中に証明書の警告が表示される場合があります。 **[はい]** または **[続行]** をクリックして接続処理を続行します。

サーバー マネージャー ダッシュボードには、仮想マシンに接続済みと表示されます。

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>SSMS を使用してマネージド インスタンスに接続する

1. 仮想マシンで、SQL Server Management Studio (SSMS) を開きます。
 
   SSMS を始めて起動するので構成を完了する必要があるため、開くまでにしばらくかかります。
2. **[サーバーに接続]** ダイアログ ボックスで、**[サーバー名]** ボックスにマネージド インスタンスの完全修飾**ホスト名**を入力し、**[SQL Server 認証]** を選択し、ログイン アカウントとパスワードを入力し、**[接続]** をクリックします。

    ![SSMS 接続](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

接続後は、データベース ノード内のシステム データベースとユーザー データベースを確認できます。また、セキュリティ、サーバー オブジェクト、レプリケーション、管理、SQL Server エージェント、および XEvent プロファイラー ノードのさまざまなオブジェクトを確認できます。

## <a name="next-steps"></a>次の手順

- ポイント対サイト接続を使用して、オンプレミスのクライアント コンピューターから接続する方法を示すクイック スタートについては、[ポイント対サイト接続の構成](sql-database-managed-instance-configure-p2s.md)に関するページをご覧ください。
- アプリケーションの接続オプションの概要については、「[Azure SQL Database Managed Instance にアプリケーションを接続する](sql-database-managed-instance-connect-app.md)」をご覧ください。
- 既存の SQL Server データベースをオンプレミスからマネージド インスタンスに復元するには、[移行用の Azure Database Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md) を使用してデータベース バックアップ ファイルから復元するか、[T-SQL RESTORE コマンド](sql-database-managed-instance-get-started-restore.md)を使用してデータベース バックアップ ファイルから復元します。
