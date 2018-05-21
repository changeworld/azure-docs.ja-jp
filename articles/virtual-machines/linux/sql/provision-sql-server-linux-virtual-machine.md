---
title: Azure で Linux SQL Server 2017 VM を作成する | Microsoft Docs
description: このチュートリアルでは、Azure Portal で Linux SQL Server 2017 仮想マシンを作成する方法について説明します。
services: virtual-machines-linux
author: rothja
ms.author: jroth
manager: jhubbard
ms.date: 05/11/2018
ms.topic: hero-article
tags: azure-service-management
ms.devlang: na
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.technology: database-engine
ms.openlocfilehash: b86dd47c112c38bc65c045158787d19b470899a0
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2018
---
# <a name="provision-a-linux-sql-server-virtual-machine-in-the-azure-portal"></a>Azure Portal での Linux SQL Server 仮想マシンのプロビジョニング

> [!div class="op_single_selector"]
> * [Linux](provision-sql-server-linux-virtual-machine.md)
> * [Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)

このクイック スタート チュートリアルでは、Azure Portal を使用して、SQL Server 2017 がインストールされている Linux 仮想マシンを作成します。

このチュートリアルでは、次のことについて説明します。

* [ギャラリーからの Linux SQL VM の作成](#create)
* [SSH による新しい VM への接続](#connect)
* [SA パスワードの変更](#password)
* [リモート接続の構成](#remote)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free) を作成してください。

## <a id="create"></a>SQL Server がインストールされた Linux VM の作成

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. 左側のウィンドウで、**[リソースの作成]** をクリックします。

1. **[リソースの作成]** ウィンドウで、**[コンピューティング]** をクリックします。

1. **[おすすめ]** 見出しの横にある **[すべて表示]** をクリックします。

   ![VM イメージをすべて表示する](./media/provision-sql-server-linux-virtual-machine/azure-compute-blade.png)

1. 検索ボックスに「**SQL Server 2017**」と入力し、**Enter** キーを押して検索を開始します。

1. **[フィルター]** アイコンをクリックし、検索を **Linux ベース**の **Microsoft** のイメージに制限してから、**[完了]** をクリックします。

    ![SQL Server 2017 VM イメージの検索フィルター](./media/provision-sql-server-linux-virtual-machine/searchfilter.png)

1. 検索結果から SQL Server 2017 Linux イメージを選択します。 このチュートリアルでは、**Free SQL Server License: SQL Server 2017 Developer on Red Hat Enterprise Linux 7.4** を使用します。

   > [!TIP]
   > Developer エディションでは、Enterprise エディションの機能を使用してテストまたは開発を行うことができますが、SQL Server のライセンス コストはかかりません。 料金は、Linux VM の実行コストに対してのみ支払います。

1. **Create** をクリックしてください。

1. **[基本]** ウィンドウで、Linux VM の詳細を入力します。 

    ![[基本] ウィンドウ](./media/provision-sql-server-linux-virtual-machine/basics.png)

    > [!Note]
    > 認証には、SSH 公開キーまたはパスワードを使用する選択肢があります。 安全性が高いのは SSH です。 SSH キーを生成する方法の手順については、[Azure の Linux VM と Mac for Linux VM に SSH キーを作成する方法](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys)に関するページを参照してください。

1. Click **OK**.

1. **[サイズ]** ウィンドウで、マシンのサイズを選択します。 VM マシン サイズの詳細については、[Linux VM のサイズ](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes)に関するページを参照してください。

    ![VM サイズを選択する](./media/provision-sql-server-linux-virtual-machine/vmsizes.png)

   > [!TIP]
   > 開発と機能テストには、**DS2** 以上の VM サイズをお勧めします。 パフォーマンス テストには、**DS13** 以上を使用してください。

1. **[選択]** をクリックします。

1. **[設定]** ウィンドウの **[Select public inbound ports]\(パブリック受信ポートの選択\)** の一覧で、**[SSH (22)]** ポートを選択します。 このクイック スタートでは、SQL Server に接続し、構成を完了するために、この操作が必要です。 リモートで SQL Server に接続する場合は、**[MS SQL (1433)]** も選択して、インターネット経由での接続用にポート 1433 を開きます。

   ![受信ポート](./media/provision-sql-server-linux-virtual-machine/port-settings.png)

1. 他の設定を変更したり、既定の設定のままにしたりすることができます。 次に、 **[OK]** をクリックします

1. **[概要]** ページで **[購入]** をクリックし、VM を作成します。

## <a id="connect"></a>Linux VM への接続

BASH シェルを既に使用している場合、**ssh** コマンドを使用して Azure VM に接続します。 次のコマンドの VM ユーザー名と IP アドレスを置き換えて、Linux VM に接続します。

```bash
ssh azureadmin@40.55.55.555
```

VM の IP アドレスは、Azure Portal で確認できます。

![Azure Portal の IP アドレス](./media/provision-sql-server-linux-virtual-machine/vmproperties.png)

Windows を使用していて BASH シェルがない場合、PuTTY などの SSH クライアントをインストールできます。

1. [PuTTY をダウンロードしてインストールします](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

1. PuTTY を実行します。

1. PuTTY の構成画面で、VM のパブリック IP アドレスを入力します。

1. [開く] をクリックし、プロンプトでユーザー名とパスワードを入力します。

Linux VM への接続の詳細については、[ポータルを使用して Azure に Linux VM を作成する方法](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-portal#ssh-to-the-vm)に関するページを参照してください。

## <a id="password"></a>SA パスワードの変更

新しい仮想マシンには、ランダム SA パスワードで SQL Server がインストールされます。 このパスワードは、SA ログインで SQL Server に接続する前にリセットする必要があります。

1. Linux VM に接続した後、新しいコマンド ターミナルを開きます。

1. 次のコマンドで SA パスワードを変更します。

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   求められたら、新しい SA パスワードとパスワードの確認を入力します。

1. SQL Server サービスを再起動します。

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>パスへのツールの追加 (オプション)

既定では、SQL Server コマンドライン ツール パッケージなど、いくつかの SQL Server [パッケージ](sql-server-linux-virtual-machines-overview.md#packages)がインストールされています。 このツール パッケージには、**sqlcmd** ツールと **bcp** ツールが含まれています。 便宜上、必要に応じてツールのパス `/opt/mssql-tools/bin/` を **PATH** 環境変数に追加することができます。

1. 次のコマンドを実行して、ログイン セッションと対話型/非ログイン セッションの両方の **PATH** を変更します。

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a id="remote"></a>リモート接続の構成

Azure VM 上の SQL Server にリモートで接続する必要がある場合、ネットワーク セキュリティ グループで受信規則を構成する必要があります。 この規則により、SQL Server がリッスンするポート (既定では 1433) のトラフィックが許可されます。 次の手順では、この手順に Azure Portal を使用する方法について説明します。

> [!TIP]
> プロビジョニング時に設定で受信ポート **[MS SQL (1433)]** を選択した場合、これらの変更が自動的に行われています。 ファイアウォールの構成方法に関する次のセクションに進むことができます。

1. ポータルで、 **[仮想マシン]** を選び、SQL Server VM を選びます。

1. プロパティの一覧で、**[ネットワーク]** を選択します。

1. **[ネットワーク]** ウィンドウで、**[受信ポートの規則]** の下にある **[追加]** ボタンをクリックします。

   ![受信ポートの規則](./media/provision-sql-server-linux-virtual-machine/networking.png)

1. **[サービス]** の一覧で **[MS SQL]** を選択します。

    ![MS SQL セキュリティ グループ規則](./media/provision-sql-server-linux-virtual-machine/sqlnsgrule.png)

1. **[OK]** をクリックして VM の規則を保存します。

### <a name="open-the-firewall-on-rhel"></a>RHEL でファイアウォールを開く

このチュートリアルでは、Red Hat Enterprise Linux (RHEL) VM を作成するよう説明してきました。 RHEL VM にリモートで接続する場合、Linux ファイアウォールのポート 1433 を開くことも必要になります。

1. RHEL VM に[接続](#connect)します。

1. BASH シェルで、次のコマンドを実行します。

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>次の手順

Azure に SQL Server 2017 仮想マシンを作成できたので、**sqlcmd** を使用してローカルで接続し、Transact-SQL クエリを実行できます。

SQL Server のリモート接続用に Azure VM を構成した場合は、リモート接続も可能になります。 Windows から SQL Server on Linux にリモートで接続する方法の例については、[Windows 上の SSMS を使用した SQL Server on Linux への接続](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms)に関するページを参照してください。 Visual Studio Code を使用して接続するには、「[Use Visual Studio Code to create and run Transact-SQL scripts for SQL Server (Visual Studio Code を使用して SQL Server の Transact-SQL スクリプトを作成して実行する)](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode)」を参照してください。

SQL Server on Linux の全般的な情報については、[SQL Server 2017 on Linux の概要](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)に関するページを参照してください。 SQL Server 2017 Linux 仮想マシンの使用の詳細については、[Azure の SQL Server 2017 仮想マシンの概要](sql-server-linux-virtual-machines-overview.md)に関するページを参照してください。
