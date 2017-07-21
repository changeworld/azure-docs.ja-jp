---
title: "Azure Linux 仮想マシンで Oracle ASM をセットアップする | Microsoft Docs"
description: "Azure 環境で Oracle ASM をすばやく稼動させます。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/2/2017
ms.author: v-shiuma
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 39f6acd0def9fa5c2de470268cda6666aa572e5d
ms.contentlocale: ja-jp
ms.lasthandoff: 06/20/2017

---

# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Azure Linux 仮想マシンで Oracle ASM をセットアップする  

この記事では、Azure の Oracle Linux 仮想マシン (VM) に Oracle 自動ストレージ管理 (Oracle ASM) をインストールしてセットアップする方法を説明します。

始める前に、Azure CLI がインストールされていることを確認します。 詳細については、[Azure CLI のインストール ガイド](https://docs.microsoft.com/cli/azure/install-azure-cli)を参照してください。

## <a name="prepare-the-environment"></a>環境の準備

### <a name="sign-in-to-azure"></a>Azure へのサインイン 

Azure CLI で、Azure サブスクリプションにサインインするには、[az login](/cli/azure/#login) コマンドを使用します。 その後、画面の指示に従います。

```azurecli
az login
```

### <a name="create-a-resource-group"></a>リソース グループの作成

リソース グループを作成するには、[az group create](/cli/azure/group#create) コマンドを使用します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次の例では、myResourceGroup という名前のリソース グループを場所 westus に作成します。

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-a-vm"></a>VM の作成

Oracle ASM で使用する VM を作成するには、次の手順を実行します。

#### <a name="1--to-create-a-virtual-machine-use-the-az-vm-createcliazurevmcreate-command"></a>1.仮想マシンを作成するには、[az vm create](/cli/azure/vm#create) コマンドを使用します。 

  次の例では、myVM という名前の VM を作成します。 また、既定のキーの場所にまだ SSH キーが存在しない場合は SSH キーも作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。  

  ```azurecli
  az vm create --resource-group myResourceGroup --name myVM --image Oracle:Oracle-Database-Ee:12.1.0.2:latest --size Standard_DS2_v2 --generate-ssh-keys
  ```

  VM を作成すると、Azure CLI によって次の例のような情報が表示されます。 `publicIpAddress` の値をメモします。 このアドレスは、VM へのアクセスに使用します。

  ```azurecli
  {
    "fqdns": "",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
    "location": "westus",
    "macAddress": "00-0D-3A-36-2F-56",
    "powerState": "VM running",
    "privateIpAddress": "10.0.0.4",
    "publicIpAddress": "13.64.104.241",
    "resourceGroup": "myResourceGroup"
  }
  ```

#### <a name="2--add-disks-to-use-for-your-oracle-asm-configuration"></a>2.Oracle ASM 構成に使用するディスクを追加します。

  ```azurecli
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk2 --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk3 --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk4 --new --size-gb 50
  ```

### <a name="connect-to-the-vm"></a>VM に接続します

VM との SSH セッションを作成するには、次のコマンドを使用します。 IP アドレスを、VM の `publicIpAddress` 値に置き換えます。

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Oracle ASM のインストール

Oracle ASM をインストールするには、次の手順を実行します。 

Oracle ASM のインストールの詳細については、「[Oracle ASMLib Downloads for Oracle Linux 6 (Oracle Linux 6 用の Oracle ASMLib のダウンロード)](http://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html)」を参照してください。  

### <a name="1-run-yum-list"></a>1.`yum list` を実行します。

  ```bash
  $ sudo su -
  # yum list
  ```
  `yum list` を初めて実行する場合は読み込みに数分かかることがあります。

### <a name="2--run-these-additional-commands"></a>2.さらに次のコマンドを実行します。

  ```bash
  # yum list | grep oracleasm
  # yum -y install kmod-oracleasm.x86_64
  # yum -y install oracleasm-support.x86_64
  # wget http://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm
  # yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm
  # rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
  ```

### <a name="3--verify-that-oracle-asm-is-installed"></a>手順 3.Oracle ASM がインストールされていることを確認します。

  ```bash
  # rpm -qa |grep oracleasm
  oracleasm-support-2.1.10-4.el6.x86_64
  kmod-oracleasm-2.0.8-15.el6_9.x86_64
  oracleasmlib-2.0.12-1.el6.x86_64
  ```

### <a name="4--add-users-and-groups"></a>4.ユーザーとグループを追加します。

  ```bash
  # groupadd -g 54345 asmadmin
  # groupadd -g 54346 asmdba
  # groupadd -g 54347 asmoper
  # useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid
  # usermod -g oinstall -G dba,asmdba,asmadmin oracle
  ```

### <a name="5--verify-users-and-groups"></a>5.ユーザーとグループを確認します。

  ```bash
  # id grid
  uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
  ```

### <a name="6--create-a-folder-and-change-owner"></a>6.フォルダーを作成し、所有者を変更します。

  ```bash
  # mkdir /u01/app/grid
  # chown grid:oinstall /u01/app/grid
  ```

## <a name="set-up-oracle-asm"></a>Oracle ASM のセットアップ

このチュートリアルでは、既定のユーザーは *grid*、既定のグループは *asmadmin* です。 *oracle* ユーザーが asmadmin グループに属していることを確認します。 Oracle ASM インストールをセットアップするには、次の手順を実行します。

### <a name="1--set-the-oracle-asm-library-driver"></a>1.Oracle ASM ライブラリ ドライバーを設定します。

  ```bash
  # /usr/sbin/oracleasm configure -i

  Configuring the Oracle ASM library driver.

  This will configure the on-boot properties of the Oracle ASM library
  driver. The following questions will determine whether the driver is
  loaded on boot and what permissions it will have. The current values
  will be shown in brackets ('[]'). Hitting <ENTER> without typing an
  answer will keep that current value. Ctrl-C will abort.

  Default user to own the driver interface []: grid
  Default group to own the driver interface []: asmadmin
  Start Oracle ASM library driver on boot (y/n) [n]: y
  Scan for Oracle ASM disks on boot (y/n) [y]: y
  Writing Oracle ASM library driver configuration: done
  ```

### <a name="2--view-the-disk-configuration"></a>2.ディスク構成を表示します。
  ```bash
  # cat /proc/partitions
  ```

### <a name="3--format-the-disk"></a>手順 3.ディスクをフォーマットします。

  ```bash
  # fdisk /dev/sdc
  Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
  Building a new DOS disklabel with disk identifier 0xf865c6ca.
  Changes will remain in memory only, until you decide to write them.
  After that, of course, the previous content won't be recoverable.

  Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

  The device presents a logical sector size that is smaller than
  the physical sector size. Aligning to a physical sector (or optimal
  I/O) size boundary is recommended, or performance may be impacted.

  WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
          switch off the mode (command 'c') and change display units to
          sectors (command 'u').

  Command (m for help): n
  Command action
    e   extended
    p   primary partition (1-4)
  p
  Partition number (1-4): 1
  First cylinder (1-6527, default 1):
  Using default value 1
  Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
  Using default value 6527

  Command (m for help): w
  The partition table has been altered!

  Calling ioctl() to re-read partition table.
  Syncing disks.
  ```

### <a name="4--repeat-the-preceding-step-for-devsdd-devsde-and-devsdf"></a>4./dev/sdd、/dev/sde、/dev/sdf に対して前の手順を繰り返します。

### <a name="5--check-the-disk-configuration"></a>5.ディスク構成を確認します。

```bash
# cat /proc/partitions
major minor  #blocks  name

  8       16   14680064 sdb
  8       17   14678976 sdb1
  8       32   52428800 sdc
  8       33   52428096 sdc1
  8       48   52428800 sdd
  8       49   52428096 sdd1
  8       64   52428800 sde
  8       65   52428096 sde1
  8       80   52428800 sdf
  8       81   52428096 sdf1
  8        0   52428800 sda
  8        1     512000 sda1
  8        2   51915776 sda2
  11        0    1048575 sr0
```

### <a name="6--check-the-oracle-asm-service-status"></a>6.Oracle ASM サービスの状態を確認します。

```bash
# service oracleasm status
Checking if ASM is loaded: no
Checking if /dev/oracleasm is mounted: no
```

### <a name="7--start-the-oracle-asm-service"></a>7.Oracle ASM サービスを開始します。

```bash
# service oracleasm start
Initializing the Oracle ASMLib driver:                     [  OK  ]
Scanning the system for Oracle ASMLib disks:               [  OK  ]
```

### <a name="8--create-oracle-asm-disks"></a>8.Oracle ASM ディスクを作成します。

```bash
# service oracleasm createdisk ASMSP /dev/sdc1
Marking disk "ASMSP" as an ASM disk:                       [  OK  ]

# service oracleasm createdisk DATA /dev/sdd1
Marking disk "DATA" as an ASM disk:                        [  OK  ]

# service oracleasm createdisk DATA1 /dev/sde1
Marking disk "DATA1" as an ASM disk:                       [  OK  ]

# service oracleasm createdisk FRA /dev/sdf1
Marking disk "FRA" as an ASM disk:                         [  OK  ]
```

### <a name="9--list-oracle-asm-disks"></a>9.Oracle ASM ディスクを一覧表示します。

```bash
# service oracleasm listdisks
ASMSP
DATA
DATA1
FRA
```

### <a name="10-change-the-passwords-for-the-root-oracle-and-grid-users-you-use-the-passwords-later-during-installation"></a>10.ユーザー root、oracle、grid のパスワードを変更します (パスワードは、後でインストール時に使用します)。

```bash
# passwd oracle
# passwd grid
# passwd root
```

### <a name="11-change-the-folder-permission"></a>11.フォルダーのアクセス許可を変更します。

```bash
# chmod -R 775 /opt
# chown grid:oinstall /opt
# chown oracle:oinstall /dev/sdc1
# chown oracle:oinstall /dev/sdd1
# chown oracle:oinstall /dev/sde1
# chown oracle:oinstall /dev/sdf1
# chmod 600 /dev/sdc1
# chmod 600 /dev/sdd1
# chmod 600 /dev/sde1
# chmod 600 /dev/sdf1
```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Oracle Grid Infrastructure のダウンロードと準備

Oracle Grid Infrastructure ソフトウェアをダウンロードして準備するには、次の手順を実行します。

### <a name="1--download-oracle-grid-infrastructure-from-the-oracle-asm-download-pagehttpwwworaclecomtechnetworkdatabaseenterprise-editiondownloadsdatabase12c-linux-download-2240591html"></a>1.[Oracle ASM のダウンロード ページ](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html)から Oracle Grid Infrastructure をダウンロードします。 

  "**Oracle Database 12c Release 1 Grid Infrastructure (12.1.0.2.0) for Linux x86-64**" というダウンロード タイトルの下に、ダウンロードする .zip ファイルが 2 つあります。

### <a name="2--after-you-download-the-zip-files-to-your-client-computer-you-can-use-secure-copy-protocol-scp-to-copy-the-files-to-your-vm"></a>2.これらの .zip ファイルをクライアント コンピューターにダウンロードしたら、Secure Copy Protocol (SCP) を使用してそのファイルを VM にコピーすることができます。

```bash
scp *.zip <publicIpAddress>:<folder>
```

### <a name="3--move-the-zip-files-to-the-opt-folder-then-change-the-owner-of-the-files"></a>手順 3..zip ファイルを /opt フォルダーに移動します。 その後、ファイルの所有者を変更します。

```bash
# mv <folder>/*.zip /opt
# cd /opt
# chown grid:oinstall linuxamd64_12102_grid_1of2.zip
# chown grid:oinstall linuxamd64_12102_grid_2of2.zip
```
### <a name="4--unzip-the-files-install-the-linux-unzip-utility-if-its-not-already-installed"></a>4.ファイルを解凍します (Linux のファイル解凍ユーティリティがまだインストールされていない場合はインストールします)。
```bash
# yum install unzip
# unzip linuxamd64_12102_grid_1of2.zip
# unzip linuxamd64_12102_grid_2of2.zip
```
### <a name="5--change-permission"></a>5.アクセス許可を変更します。
```bash
# chown -R grid:oinstall /opt/grid
```
### <a name="6--turn-off-the-firewall"></a>6.ファイアウォールを無効にします。
```bash
# service iptables status
# service iptables stop
```

### <a name="7--check-available-swap-space-you-need-at-lease-68-gb-of-swap-space-to-install-grid-by-default-linux-azure-vms-will-not-have-swap-enabled-and-configured-by-default"></a>7.使用可能なスワップ領域を確認します。 グリッドをインストールするには、6.8 GB 以上のスワップ領域が必要です。既定では、Linux Azure VM でスワップを有効にしたり、構成したりすることはありません。

常に短期ディスク (一時ディスク) 内に作成できるように、スワップ領域を構成するには、waagent を使用することを強くお勧めします。手順の詳細については、次のリンクを確認してください。 

* [Linux Azure Virtual Machines でスワップ ファイルを追加する方法](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines)

## <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>X11 を実行するクライアントと VM の準備 (Windows クライアントの場合のみ)
これは省略可能な手順です。Linux クライアントを使用している場合、または X11 を既にセットアップしている場合は、この手順をスキップできます。

### <a name="1--download-putty-and-xming-to-your-windows-computer"></a>1.PuTTY と Xming を Windows コンピューターにダウンロードします。

  * [PuTTY のダウンロード](http://www.putty.org/)
  * [Xming のダウンロード](https://xming.en.softonic.com/)

### <a name="2--after-you-install-putty-in-the-putty-folder-for-example-cprogram-filesputty-run-puttygenexe-putty-key-generator"></a>2.PuTTY をインストールしたら、PuTTY フォルダー (C:\Program Files\PuTTY など) にある puttygen.exe (PuTTY Key Generator) を実行します。

### <a name="3--in-putty-key-generator"></a>手順 3.PuTTY Key Generator で、次の手順を実行します。

- キーを生成するために、**[Generate]\(生成\)** ボタンを選択します。
- キーの内容をコピーします (Ctrl + C キー)。
- **[Save private key]\(秘密キーの保存\)** ボタンを選択します。
- 表示される警告を無視し、**[OK]** を選択します。

  ![PuTTY Key Generator ページのスクリーンショット](./media/oracle-asm/puttykeygen.png)

### <a name="4--in-your-vm-run-these-commands"></a>4.VM で、次のコマンドを実行します。

```bash
# sudo su - grid
$ mkdir .ssh (if not already created)
$ cd .ssh
```

### <a name="5--create-a-file-named-authorizedkeys-paste-the-contents-of-the-key-in-this-file-and-then-save-the-file"></a>5.authorized_keys という名前のファイルを作成します。 このファイルにキーの内容を貼り付けて、ファイルを保存します。

> [!NOTE]
> キーには、`ssh-rsa` という文字列を含める必要があります。 また、キーの内容は 1 行のテキストである必要があります。
>  

### <a name="6--start-putty-in-the-category-pane-go-to-connection--ssh--auth-in-the-private-key-file-for-authentication-box-browse-to-the-key-that-you-generated-earlier"></a>6.PuTTY を起動します。 **[Category]\(カテゴリ\)** ウィンドウで、**[Connection]\(接続\)** > **[SSH]** > **[Auth]\(認証\)** の順に移動します。 **[Private key file for authentication]\(認証のための秘密キー ファイル\)** ボックスに、先ほど生成したキーを参照します。

  ![秘密キーを設定するページのスクリーンショット](./media/oracle-asm/setprivatekey.png)

### <a name="7--in-the-category-pane-go-to-connection--ssh--x11-select-the-enable-x11-forwarding-box"></a>7.**[Category]\(カテゴリ\)** ウィンドウで、**[Connection]\(接続\)** > **[SSH]** > **[X11]** の順に移動します。 **[Enable X11 forwarding]\(X11 フォワーディングを有効にする\)** チェック ボックスをオンにします。

  ![X11 を有効にするページのスクリーンショット](./media/oracle-asm/enablex11.png)

### <a name="8-in-the-category-pane-go-to-session-enter-the-host-information-and-then-select-open"></a>8.**[Category]\(カテゴリ\)** ウィンドウで、**[Session]\(セッション\)** に移動します。 ホスト情報を入力し、**[Open]\(開く\)** を選択します。

  ![セッション ページのスクリーンショット](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Oracle Grid Infrastructure のインストール

Oracle Grid Infrastructure をインストールするには、次の手順を実行します。

### <a name="1-sign-in-as-grid-you-should-be-able-to-sign-in-without-being-prompted-for-a-password"></a>1.grid としてサインインします  (パスワードの入力を要求されることなくサインインできるはずです)。 

> [!NOTE]
> インストールを開始する前に、Xming が実行されていることを確認してください。

```bash
$ cd /opt/grid
$ ./runInstaller
```

  Oracle Grid Infrastructure 12c Release 1 のインストーラーが開きます  (インストーラーが起動するまでに数分かかる場合があります)。

### <a name="2-on-the-select-installation-option-page-select-install-and-configure-oracle-grid-infrastructure-for-a-standalone-server"></a>2.**[インストール オプションの選択]** ページで、**[スタンドアロン サーバー用に Oracle Grid Infrastructure をインストールおよび構成]** を選択します。

  ![インストーラーの [インストール オプションの選択] ページのスクリーンショット](./media/oracle-asm/install01.png)

### <a name="3-on-the-select-product-languages-page-select-english-or-the-language-that-you-want"></a>手順 3.**[製品言語の選択]** ページで、**[英語]** または好きな言語を選択します。

  ![インストーラーの [製品言語の選択] ページのスクリーンショット](./media/oracle-asm/install02.png)

### <a name="4-on-the-create-asm-disk-group-page"></a>4.**[ASM ディスク グループの作成]** ページで、次の手順を実行します。
- ディスク グループの名前を入力します。
- **[冗長性]** で、**[外部]** を選択します。
- **[割当て単位サイズ]** で、**[4]** を選択します。
- **[ディスクの追加]** で、**ORCLASMSP** を選択します。

  ![インストーラーの [ASM ディスク グループの作成] ページのスクリーンショット](./media/oracle-asm/install03.png)

### <a name="5-on-the-specify-asm-password-page-select-the-use-same-passwords-for-these-accounts-option-and-enter-a-password"></a>5.**[ASM パスワードの指定]** ページで、**[これらのアカウントごとに、同じパスワードを使用]** を選択し、パスワードを入力します。

  ![インストーラーの [ASM パスワードの指定] ページのスクリーンショット](./media/oracle-asm/install04.png)

### <a name="6-optional-on-the-specify-management-options-page-select-the-register-with-enterprise-manager-em-cloud-control-box"></a>6.(省略可能) **[Specify Management Options]\(管理オプションの指定\)** ページで、**[Register with Enterprise Manager (EM) Cloud Control]\(Enterprise Manager (EM) Cloud Control への登録\)** チェック ボックスをオンにします。

  ![インストーラーの [Specify Management Options]\(管理オプションの指定\) ページのスクリーンショット](./media/oracle-asm/install05.png)

### <a name="7-on-the-privileged-operating-system-groups-page-use-the-default-settings"></a>7.**[権限付きオペレーティング システム グループ]** ページで、既定の設定を使用します。

  ![インストーラーの [権限付きオペレーティング システム グループ] ページのスクリーンショット](./media/oracle-asm/install06.png)

### <a name="8-on-the-specify-installation-location-page-use-default-settings"></a>8.**[インストール場所の指定]** ページで、既定の設定を使用します。

  ![インストーラーの [インストール場所の指定] ページのスクリーンショット](./media/oracle-asm/install07.png)

### <a name="9-on-the-create-inventory-page-enter-or-browse-to-the-folder-location"></a>9.**[インベントリの作成]** ページで、フォルダーの場所を入力するか参照します。

  ![インストーラーの [インベントリの作成] ページのスクリーンショット](./media/oracle-asm/install08.png)

### <a name="10-on-the-root-script-execution-configuration-page-select-the-automatically-run-configuration-scripts-box-then-select-the-use-root-user-credential-option-and-enter-the-root-user-password"></a>10.**[root スクリプトの実行構成]** ページで、**[構成スクリプトを自動的に実行]** チェック ボックスをオンにします。 次に、**[root ユーザーの資格証明を使用]** オプションを選択し、root ユーザーのパスワードを入力します。

  ![インストーラーの [root スクリプトの実行構成] ページのスクリーンショット](./media/oracle-asm/install09.png)

### <a name="11-on-the-perform-prerequisite-checks-page-select-fix--check-again"></a>11.**[前提条件チェックの実行]** ページで、**[修正および再チェック]** を選択します。

  ![インストーラーの [前提条件チェックの実行] ページのスクリーンショット](./media/oracle-asm/install10.png)

### <a name="12-on-the-fixup-script-page-select-ok"></a>12.**[修正スクリプトの実行]** ページで、**[OK]** を選択します。

  ![インストーラーの [修正スクリプトの実行] ページのスクリーンショット](./media/oracle-asm/install11.png)

### <a name="13-on-the-summary-page-review-your-settings-selections-and-then-select-install"></a>13.**[サマリー]** ページで、選択した設定を確認し、**[インストール]** を選択します。

  ![インストーラーの [サマリー] ページのスクリーンショット](./media/oracle-asm/install12.png)

### <a name="14-a-warning-dialog-box-appears-select-yes-to-continue"></a>14.警告のダイアログ ボックスが表示されます。 **[はい]** を選択して続行します。

  ![警告のダイアログ ボックスのスクリーンショット](./media/oracle-asm/install14.png)

### <a name="15-on-the-finish-page-select-close-to-finish-the-installation"></a>15.**[終了]** ページで、**[閉じる]** を選択してインストールを終了します。

  ![インストーラーの [終了] ページのスクリーンショット](./media/oracle-asm/install16.png)

## <a name="set-up-your-oracle-asm-installation"></a>Oracle ASM インストールのセットアップ

Oracle ASM インストールをセットアップするには、次の手順を実行します。

### <a name="1--sign-in-as-grid-from-your-x11-session"></a>1.X11 セッションから、grid としてサインインします。

  ```bash
  $ cd /u01/app/grid/product/12.1.0/grid/bin
  $ ./asmca
  ```

  Oracle ASM コンフィギュレーション アシスタントが開きます。

### <a name="2--on-the-configure-asm-disk-groups-page-select-the-create-button-and-then-select-show-advanced-options"></a>2.**[ASM の構成: ディスク グループ]** ページで、**[作成]** ボタンを選択し、**[拡張オプションの表示]** を選択します。

  ![[ASM の構成: ディスク グループ] ページのスクリーンショット](./media/oracle-asm/asm01.png)

### <a name="3--on-the-create-disk-group-page"></a>手順 3.**[ディスク グループの作成]** ページで、次の手順を実行します。

- ディスク グループ名を入力します。
- **[メンバー ディスクの選択]** で、**ORCL_DATA** と **ORCL_DATA1** を選択します。
- **[割当て単位サイズ]** で、**[4]** を選択します。

  ![[ディスク グループの作成] ページのスクリーンショット](./media/oracle-asm/asm02.png)

### <a name="4--on-the-configure-asm-disk-groups-page-select-the-create-button-and-then-select-show-advanced-options"></a>4.**[ASM の構成: ディスク グループ]** ページで、**[作成]** ボタンを選択し、**[拡張オプションの表示]** を選択します。

  ![[ASM の構成: ディスク グループ] ページのスクリーンショット](./media/oracle-asm/asm03.png)

### <a name="5--on-the-create-disk-group-page"></a>5.**[ディスク グループの作成]** ページで、次の手順を実行します。

- ディスク グループ名を入力します。
- **[冗長性]** で、**[外部]** を選択します。
- **[メンバー ディスクの選択]** で、**ORCL_FRA** を選択します。
- **[割当て単位サイズ]** で、**[4]** を選択します。

  ![[ディスク グループの作成] ページのスクリーンショット](./media/oracle-asm/asm04.png)

### <a name="6--select-exit-to-close-asm-configuration-assistant"></a>6.**[終了]** を選択して ASM コンフィギュレーション アシスタントを閉じます。

  ![[終了] ボタンがある [ASM の構成: ディスク グループ] ページのスクリーンショット](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>データベースの作成

Oracle ソフトウェアは既に Azure Marketplace イメージにインストールされています。 データベースをインストールするには、次の手順を実行します。

### <a name="1--switch-users-to-the-oracle-superuser-and-then-initialize-the-listener-for-logging"></a>1.ユーザーを oracle スーパー ユーザーに切り替え、ログ用にリスナーを初期化します。

  ```bash
  $ su - oracle
  Password:
  $ cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
  $ ./dbca
   ```
   Database Configuration Assistant が開きます。

### <a name="2--on-the-database-operation-page-select-create-database"></a>2.**[データベース操作]** ページで、**[データベースの作成]** を選択します。

  ![[データベース操作] ページのスクリーンショット](./media/oracle-asm/createdb01.png)
  
### <a name="3-on-the-creation-mode-page"></a>手順 3.**[作成モード]** ページで、次の手順を実行します。

- データベースの名前を入力します。
- **[記憶域のタイプ]** で、**[Automatic Storage Management (ASM)]\(自動ストレージ管理 (ASM)\)** を選択します。
- **[データベース ファイルの位置]** で、使用するフォルダーを参照します。
- **[高速リカバリ領域]** で、使用するフォルダーを参照します。

  ![[作成モード] ページのスクリーンショット](./media/oracle-asm/createdb02.png)

### <a name="4--on-the-summary-page-review-your-settings-selections-and-then-select-finish-to-create-the-database"></a>4.**[サマリー]** ページで、選択した設定を確認し、**[終了]** を選択してデータベースを作成します。

  ![[サマリー] ページのスクリーンショット](./media/oracle-asm/createdb03.png)

### <a name="5---optional-on-the-finish-page-to-change-the-passwords-select-password-management"></a>5. (省略可能) **[終了]** ページで、パスワードを変更するには、**[パスワード管理]** を選択します。

  ![[終了] ページのスクリーンショット](./media/oracle-asm/createdb04.png)


## <a name="delete-the-vm"></a>VM の削除

VM が必要なくなったら、次のコマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

[チュートリアル: 可用性が高い仮想マシンを作成する](../../linux/create-cli-complete.md)

[VM デプロイ Azure CLI サンプルを探索する](../../linux/cli-samples.md)

