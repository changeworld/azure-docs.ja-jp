---
title: 'Azure portal: SQL マネージド インスタンスの作成 | Microsoft Docs'
description: SQL マネージド インスタンス、ネットワーク環境、アクセス用のクライアント VM を作成します。
keywords: SQL データベース チュートリアル, SQL マネージド インスタンスの作成
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: quickstart
ms.date: 08/13/2018
ms.author: jovanpop-msft
ms.openlocfilehash: cb378c2d2773096992ef688653fd77b2625f8754
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "42022689"
---
# <a name="create-an-azure-sql-managed-instance"></a>Azure SQL マネージド インスタンスの作成

このクイック スタートでは、Azure で SQL マネージド インスタンスを作成する方法について説明します。 Azure SQL Database Managed Instance は、Azure クラウドで可用性の高い SQL Server データベースの実行とスケーリングを行える、サービスとしてのプラットフォーム (PaaS) SQL Server データベース エンジン インスタンスです。 このクイック スタートでは、SQL マネージド インスタンスを作成して作業を開始する方法を紹介します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="prepare-network-environment"></a>ネットワーク環境を準備する

SQL Managed Instance は、独自の Azure 仮想ネットワーク (VNet) 内に配置される安全なサービスです。 マネージド インスタンスを作成するには、Azure ネットワーク環境を準備する必要があります。この環境には、以下が含まれます。
 - マネージド インスタンスが配置される Azure VNet。
 - マネージド インスタンスが配置される Azure VNet 内のサブネット。
 - マネージド インスタンスと、インスタンスを制御して管理する Azure サービスとの通信を可能にするユーザー定義ルート。

サブネットはマネージド インスタンス専用であり、このサブネットに他のリソース (Azure 仮想マシンなど) を作成することはできません。 このクイックスタートでは、Azure VNet に 2 つのサブネットを作成します。これは、マネージド インスタンス専用のサブネットにマネージド インスタンスを配置し、既定のサブネットに他のリソースを配置できるようにするためです。

1. 次のボタンをクリックして、Azure SQL Database Managed Instance のために用意された Azure ネットワーク環境をデプロイします。

    <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener"> <img src="http://azuredeploy.net/deploybutton.png"> </a>

    このボタンをクリックすると、デプロイ前にネットワーク環境を構成できるフォームが Azure portal で開かれます。

2. 必要に応じて、VNet とサブネットの名前を変更して、ネットワーク リソースに関連付けられた IP 範囲を調整します。 [購入] ボタンを押して、環境の作成と構成を行います。

    ![マネージド インスタンスの環境の作成](./media/sql-database-managed-instance-get-started/create-mi-network-arm.png)

    > [!Note]
    > この Azure Resource Manager デプロイでは、2 つのサブネットが VNet 内に作成されます。1 つは **ManagedInstances** という名前で、マネージド インスタンス用です。もう 1 つは、**Default** という名前で、マネージド インスタンスに接続するために使用できるクライアント仮想マシンなど、その他のリソースに使用します。 サブネットが 2 つ必要でない場合、後者の既定のサブネットを削除できます。ただしその場合は、このクイックスタート ガイドの手順 3、「[クライアント マシンを準備する](#prepare-client-machine)」を完了することができません。

    > [!Note]
    > VNet とサブネットの名前を変更したら、新しい名前を覚えておいてください。これは、以下のセクションで新しい名前が必要になるためです。 このチュートリアルの残りの部分では、**MyNewVNet** という名前の VNet、SQL マネージド インスタンス用の **ManagedInstances** サブネット、仮想マシンおよび他のリソース用の **Default** サブネットが作成してあることを前提とします。

## <a name="create-a-managed-instance"></a>マネージド インスタンスを作成する

ここでは、プレビューが承認された後、マネージド インスタンスを作成する手順について説明します。

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。
2. 
  **[マネージド インスタンス]** を探し、**[Azure SQL Database Managed Instance (preview)]\(Azure SQL Database マネージド インスタンス (プレビュー)\)** を選択します。
3. **Create** をクリックしてください。

   ![マネージド インスタンスの作成](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

4. サブスクリプションを選択し、プレビューの使用条件が **[同意済み]** と表示されていることを確認します。

   ![マネージド インスタンスのプレビューの同意済み](./media/sql-database-managed-instance-tutorial/preview-accepted.png)

5. 次の表の情報を参考にして、マネージド インスタンスのフォームに必要な情報を入力します。

   | Setting| 推奨値 | 説明 |
   | ------ | --------------- | ----------- |
   |**マネージド インスタンス名**|有効な名前|有効な名前については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   |**マネージド インスタンス管理者ログイン**|任意の有効なユーザー名|有効な名前については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。 "serveradmin" は予約済みのサーバー レベルのロールであるため、使用しないでください。| 
   |**パスワード**|有効なパスワード|パスワードは 16 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。|
   |**リソース グループ**|以前の手順で作成したリソース グループ||
   |**場所**|以前の手順で選択した場所|リージョンについては、「[Azure リージョン](https://azure.microsoft.com/regions/)」を参照してください。|
   |**Virtual Network**|以前の手順で作成した仮想ネットワーク| 前の手順で名前を変更しなかった場合、**MyNewVNet/ManagedInstances** 項目を選択します。 そうでない場合は、前のセクションで入力した VNet 名とマネージド インスタンス サブネットを選択します。 **マネージド インスタンスをホストするよう構成されていないため、既定のサブネットは使用しないでください**。 |

   ![マネージド インスタンスの作成フォーム](./media/sql-database-managed-instance-tutorial/managed-instance-create-form.png)

6. **[価格レベル]** をクリックしてコンピューティング リソースとストレージ リソースのサイズを指定し、価格レベルのオプションを確認します。 インスタンスには既定で 32 GB の記憶域スペースが無料で提供されますが、**アプリケーションによっては足りない可能性があります**。
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
> サブネット内の最初のインスタンスの場合、通常、デプロイ時間は後続のインスタンスよりもはるかに長くなり、完了するまでに 24 時間以上かかることがあります。 デプロイ操作が予想以上に長くなった場合でも、デプロイ操作を取り消さないでください。 最初のインスタンスをデプロイするときにかかるこの時間の長さは、一時的な問題です。 パブリック プレビューの開始から間もなく、デプロイ時間が大幅に短縮される予定です。 サブネット内に 2 つ目のマネージド インスタンスを作成するときにかかる時間は数分です。

## <a name="prepare-client-machine"></a>クライアント マシンを準備する

SQL マネージド インスタンスがプライベート仮想ネットワークに配置されるため、マネージド インスタンスに接続してクエリを実行するための SQL クライアント ツール (SQL Server Management Studio や SQL Operations Studio など) がインストールされた Azure VM を作成する必要があります。

> [!Note]
> クライアント Azure 仮想マシンの代わりに、[ポイント対サイト](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)接続を構成して、ローカル コンピューターからマネージド インスタンスに接続することもできます。

すべての必要なツールを備えたクライアント仮想マシンを作成するうえで最も簡単なのは、Azure Resource Manager テンプレートを使用する方法です。

1. 次のボタンをクリックします (別のブラウザー タブで Azure portal にサインインしておいてください)。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

2. 開かれるフォームで、仮想マシンの名前、管理者ユーザー名、VM に接続するために使用するパスワードを入力します。

    ![クライアント VM の作成](./media/sql-database-managed-instance-get-started/create-client-sql-vm.png)

    VNet 名と既定のサブネットを変更しなかった場合、最後の 2 つのパラメーターを変更する必要はありません。変更した場合、これらの値は、ネットワーク環境を設定する際に入力した値に変更する必要があります。

3. [購入] ボタンをクリックすると、Azure VM が、準備したネットワーク内にデプロイされます。

4. リモート デスクトップ接続を使用して VM に接続し、VM に自動でインストールされた SQL Server Management Studio または SQL Operation Studio を見つけます。

5. SSMS を開いて、**[サーバーへの接続]** ダイアログ ボックスで、マネージド インスタンスの**ホスト名**を **[サーバー名]** ボックスに入力し、**[SQL Server 認証]** を選択してから、ログインとパスワードを入力します。そして、**[接続]** をクリックします。

    ![SSMS 接続](./media/sql-database-managed-instance-tutorial/ssms-connect.png)  

接続後は、データベース ノード内のシステム データベースとユーザー データベースを確認できます。また、セキュリティ、サーバー オブジェクト、レプリケーション、管理、SQL Server エージェント、および XEvent プロファイラー ノードのさまざまなオブジェクトを確認できます。

## <a name="next-steps"></a>次の手順

 - [アプリケーションを Managed Instance に接続する](sql-database-managed-instance-connect-app.md)。
 - [オンプレミスから Managed Instance にデータベースを移行する](sql-database-managed-instance-migrate.md)。


