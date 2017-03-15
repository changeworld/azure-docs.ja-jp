---
title: "Azure からオンプレミスへのフェールオーバーに必要な Linux マスター ターゲット サーバーをインストールする方法 | Microsoft Docs"
description: "Linux VM を再保護する前に、Linux マスター ターゲット サーバーが必要になります。 そのインストール方法について説明します。"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 02/13/2017
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: c4fb25880584add0832464d9049dc6c78059c48b
ms.openlocfilehash: 8ac18526e6781f189444233a191aa1d6c5b863ff
ms.lasthandoff: 02/22/2017


---
# <a name="how-to-install-linux-master-target-server"></a>Linux マスター ターゲット サーバーをインストールする方法
フェールオーバーした VM は、その後フェールバックでオンプレミスに戻すことができます。 フェールバックを行うにはまず、Azure からオンプレミスへの再保護操作を通じて、仮想マシンを保護された状態にする必要があります。 そのためには、オンプレミス側でトラフィックを受信するマスター ターゲット サーバーが必要です。 保護の対象となる仮想マシンが Windows VM であれば Windows のマスター ターゲットが、 Linux VM であれば Linux のマスター ターゲットが再保護には必要となります。 ここでは、Linux のマスター ターゲットを作成してインストールする方法について説明しています。

## <a name="overview"></a>概要
この記事では、Linux のマスター ターゲットをインストールするために必要な情報と手順を紹介しています。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。

## <a name="pre-requisites"></a>前提条件

* MT のデプロイ先となるホストを正しく選ぶためには、フェールバックの切り替え先の判断が必要となります。つまりオンプレミスにある既存の VM にフェールバックするか、(オンプレミスの VM が削除されているために) 新しい VM にフェールバックするかを判断します。 
    * 既存の VM にフェールバックする場合、そのデータストアに MT のホストからアクセスできる必要があります。
    * VM がオンプレミスに存在しない場合、フェールバック VM は MT と同じホスト上に作成されます。 MT は任意の ESXi ホストにインストールすることができます。
* MT は、プロセス サーバーおよび構成サーバーと通信できるネットワーク上に存在する必要があります。
* MT のバージョンは、プロセス サーバーおよび構成サーバー以下である必要があります  (たとえば、CS が 9.4 上にある場合、MT にできるのは 9.5 ではなく、9.4 および 9.3 です)。
* MT に使用できるのは VMware VM のみです。物理 VM は使用できません。


## <a name="steps-to-deploy-the-master-target-server"></a>マスター ターゲット サーバーをデプロイする手順

### <a name="install-centos-66-minimal"></a>CentOS 6.6 最小構成をインストールする

後述の手順に従って CentOS 6.6 - 64 ビット オペレーティング システムをインストールします。

1. 以下のリンクから最も近いミラーを選択して CentOS 6.6 最小構成 64 ビット ISO をダウンロードします。

    <http://archive.kernel.org/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.symnds.com/distributions/CentOS-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://bay.uchicago.edu/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.nsc.liu.se/centos-store/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    CentOS 6.6 最小構成 64 ビット ISO を DVD ドライブに保持し、システムを起動します。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image1.png)

2. **[スキップ]** を選択してメディア テストのプロセスを無視します。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image2.png)

3. インストールのウェルカム画面が表示されます。 ここで **[次へ]** ボタンをクリックします。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image3.png)

4. 優先言語として **[英語]** を選択し、**[次へ]** をクリックして続行します。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image4.png)

5. キーボード レイアウトとして **[英語 (米国)]** を選択します。 **[次へ]** をクリックしてインストールを続行します。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image5.png)

6. インストールするデバイスの種類を選択します。 **[Basic storage Devices (基本ストレージ デバイス)]** を選択します。 **[次へ]** をクリックしてインストールを続行します。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image6.png)

7. ハード ドライブ内の既存のデータが削除されることを示す警告メッセージが表示されます。 ハード ドライブに重要なデータが保存されていないことを確認し、**[Yes, discard any data (はい、すべてのデータを破棄します)]** をクリックします。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image7.png)

8. **[ホスト名]** ボックスにサーバーのホスト名を入力します。 **[ネットワークの構成]** をクリックし、**[ネットワーク接続]** ウィンドウでネットワーク インターフェイスを選択します。 **[編集]** ボタンをクリックして IPv4 設定を構成します。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image8.png)

9. 次の **[Editing System eth0 (System eth0 の編集)]** ウィンドウが表示されます。 **[自動的に接続する]** チェック ボックスをオンにします。 [IPv4 設定] タブで、方法として **[手動]** を選択し、**[追加]** ボタンをクリックします。 静的 IP、ネットマスク、ゲートウェイ、および DNS サーバーの詳細を指定します。 **[適用]** をクリックして詳細情報を保存します。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image9.png)

10. コンボ ボックスからローカルのタイム ゾーンを選択し、**[次へ]** をクリックして続行します。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image10.png)

11. **[ルート パスワード]** にパスワードを入力して確認し、**[次へ]** をクリックして続行します。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image11.png)

12. パーティションのモードとして **[カスタム レイアウトの作成]** を選択し、**[次へ]** をクリックして続行します。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image12.png)

13. **空き**パーティションを選択し、ファイル システムの種類として **ext4** を指定して **/**、**/var/crash**、および **/home** の各パーティションを作成するために **[作成]** をクリックします。 ファイル システムの種類として **swap** を指定して、**スワップ パーティション**を作成します。 パーティションのサイズを割り当てるには、次の表に示すサイズの割り当て式に従います。

    > [!NOTE]
    > Linux マスター ターゲット (MT) システムでは、LVM をルートまたはリテンション期間ストレージ スペースとして使用しないでください。 Linux MT は、既定で、LVM パーティション/ディスク検出を回避するように構成されます。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image13.png)

14. パーティションの作成後、**[次へ]** をクリックして続行します。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image14.png)

15. 既存のデバイスが見つかった場合、フォーマットによってデータが消去されることを警告するメッセージが表示されます。 **[フォーマット]** をクリックし、最新のパーティション テーブルを使用してハード ドライブをフォーマットします。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image15.png)

16. **[変更をディスクに書き込む]** をクリックして、パーティションの変更をディスクに適用します。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image16.png)

17. **[ブート ローダーをインストールする]** オプションを確認して、**[次へ]** をクリックしてルート パーティションにブート ローダーをインストールします。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image17.png)


18. インストール プロセスが始まります。 インストールの進行状況を監視できます。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image18.png)

19. インストールが正常に完了すると次の画面が表示されます。 **[再起動]** をクリックします。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image19.png)


### <a name="post-installation-steps"></a>インストール後の手順
次に、マスター ターゲット サーバーとして構成するマシンを準備します。

Linux 仮想マシンの各 SCSI ハード ディスクの SCSI ID を取得するには、パラメーター "disk.EnableUUID = TRUE" を有効にする必要があります。

このパラメーターを有効にするには、次の手順を実行します。

a. 仮想マシンをシャットダウンします。

b. 左側のパネルで VM のエントリを右クリックし、**[設定の編集]** を選択します。

c. **[Options]** タブをクリックします。

d. 左側で **[詳細設定] &gt; [全般]** を選択し、右側に表示される **[構成パラメーター]** をクリックします。

![](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

[Configuration Parameters] オプションは、マシンの動作中には無効になります。 このタブを有効にするには、マシンをシャットダウンします。

e. **[disk.EnableUUID]** と表示される行が存在するかどうかを確認します。

f. その値が存在していて False に設定されている場合、True の値で上書きします (True と False の値では、大文字小文字は区別されません)。

g. その値が存在していて True に設定されている場合、[キャンセル] をクリックします。
    
h. そのような値が存在しない場合、**[行の追加]** をクリックします。

i. [名前] 列に disk.EnableUUID を追加し、その値を TRUE に設定します。

   * そのような行が存在し、値が False の場合、True の値で上書きします (True と False の値では、大文字小文字は区別されません)。

   * そのような行が存在し、値が True の場合、[Cancel] をクリックし、ゲスト オペレーティング システムが起動した後に、その OS 内で SCSI コマンドをテストします。

f. そのような行が存在しない場合、**[行の追加]** をクリックします。

  [Name] 列に disk.EnableUUID を追加します。

  その値を TRUE に設定します。



![](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="download-and-install-the-additional-packages"></a>その他のパッケージをダウンロードおよびインストールする

> [!NOTE]
> 追加パッケージをダウンロードしてインストールする前に、システムからインターネットに接続できることを確認してください。インターネットに接続できない場合は、それらのパッケージの RPM を手動で入手してインストールする必要があります。

```
yum install -y xfsprogs perl lsscsi rsync wget kexec-tools
```

このコマンドは、次に示す 15 のパッケージを CentOS 6.6 リポジトリからダウンロードしてインストールします。 インターネットにアクセスできない場合は、次の RPM をダウンロードする必要があります。


bc-1.06.95-1.el6.x86\_64.rpm

busybox-1.15.1-20.el6.x86\_64.rpm

elfutils-libs-0.158-3.2.el6.x86\_64.rpm

kexec-tools-2.0.0-280.el6.x86\_64.rpm

lsscsi-0.23-2.el6.x86\_64.rpm

lzo-2.03-3.1.el6\_5.1.x86\_64.rpm

perl-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-Module-Pluggable-3.90-136.el6\_6.1.x86\_64.rpm

perl-Pod-Escapes-1.04-136.el6\_6.1.x86\_64.rpm

perl-Pod-Simple-3.13-136.el6\_6.1.x86\_64.rpm

perl-libs-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-version-0.77-136.el6\_6.1.x86\_64.rpm

rsync-3.0.6-12.el6.x86\_64.rpm

snappy-1.1.0-1.el6.x86\_64.rpm

wget-1.12-5.el6\_6.1.x86\_64.rpm


#### <a name="install-additional-packages-for-specific-operating-systems"></a>特定のオペレーティング システムに必要な追加パッケージのインストール

> [!NOTE]
> ソースの保護されたマシンで、ルートまたはブート デバイスとして Reiser または XFS のファイル システムが使用されている場合、保護する前に、次の追加パッケージを Linux マスター ターゲットにダウンロードしてインストールする必要があります。
 

"***ReiserFS (Suse11SP3 で使用されている場合。ただし、ReiserFS は Suse11SP3 の既定のファイル システムではありません)***"

```
cd /usr/local

wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm
reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm
```

***XFS (RHEL、CentOS 7 以降)***

```
cd /usr/local

wget
<http://archive.kernel.org/centos-vault/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

yum install device-mapper-multipath 
```
これは、MT サーバーでマルチパス パッケージを有効にするために必要です。

### <a name="get-the-installer-for-setup"></a>セットアップに使用するインストーラーの入手

マスター ターゲット サーバーからインターネットにアクセスできる場合は、以下の手順でインストーラーをダウンロードできます。インターネットにアクセスできない場合は、プロセス サーバーからインストーラーをコピーしてインストールしてください。

#### <a name="download-the-mt-installation-packages"></a>MT のインストール パッケージをダウンロードする

こちら ([https://aka.ms/latestlinuxmobsvc](https://aka.ms/latestlinuxmobsvc)) から最新の Linux マスター ターゲット インストール ビットをダウンロードします。

Linux を使用してこれをダウンロードするには、次のように入力します。 

```
wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz
```

ダウンロードして、必ずホーム ディレクトリのみでインストーラーを解凍してください。 /usr/Local に解凍すると、インストールは失敗します。


#### <a name="access-the-installer-from-the-process-server"></a>プロセス サーバーからインストーラーにアクセスする

プロセス サーバーの C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository ディレクトリに移動します。

プロセス サーバーから必要なインストーラー ファイルをコピーし、自分の home ディレクトリに latestlinuxmobsvc.tar.gz という名前で保存します。


### <a name="apply-custom-configuration-changes"></a>カスタム構成変更を適用する

カスタム構成変更を適用するには、次の手順を実行します。


1. 次のコマンドを実行して、バイナリを解凍します。
    ```
    tar -zxvf latestlinuxmobsvc.tar.gz
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/image16.png)
    
2. 次のコマンドを実行して、アクセス許可を付与します。
    ```
    chmod 755 ./ApplyCustomChanges.sh
    ```

3. 次のコマンドを実行して、スクリプトを実行します。
    ```
    ./ApplyCustomChanges.sh
    ```
> [!NOTE]
> スクリプトは、サーバー上で&1; 回だけ実行します。 サーバーをシャットダウンし、 以降の手順で指定されているディスクを追加した後、再起動してください。

### <a name="add-retention-disk-to-linux-mt-vm"></a>リテンション ディスクを Linux MT VM に追加する 

以下に示す手順に従ってリテンション ディスクを作成します。

1. 新しい **1 TB** ディスクを Linux MT VM に接続し、マシンを**起動**します。

2. **multipath -ll** コマンドを呼び出し、リテンション ディスクのマルチパス ID を確認します。

    ```
    multipath -ll
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

3. ドライブをフォーマットし、新しいドライブにファイルシステムを作成します。 
    
    ```
    mkfs.ext4 /dev/mapper/<Retention disk's multipath id>
    ```
    ![](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

4. ファイル システムを作成したら、リテンション ディスクをマウントします。
    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

5. 最後に fstab エントリを作成し、毎回起動中にリテンション ドライブをマウントします。
    ```
    vi /etc/fstab 
    ```
    Ins キーを押してファイルの編集を開始します。 新しい行を作成し、そこに次のテキストを挿入します。 前述のコマンドでハイライトされているマルチパス ID に基づいてディスクのマルチパス ID を編集します。

    **/dev/mapper/<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    Esc キーを押して「:wq」(write and quit) と入力し、エディター ウィンドウを閉じます。

### <a name="install-master-target"></a>マスター ターゲットをインストールする

> [!NOTE]
> マスター ターゲット サーバーのバージョンは、プロセス サーバーおよび構成サーバー以下である必要があります。 MT のバージョンの方が高い場合、再保護は成功しますが、レプリケーションで失敗します。
 

> [!NOTE]
> マスター ターゲット サーバーをインストールする前に、VM の /etc/hosts ファイルに、ローカル ホスト名を、すべてのネットワーク アダプターに関連付けられた IP アドレスにマップするエントリが含まれることを確認します。
 
1. 構成サーバーの **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** からパスフレーズをコピーし、次のコマンドを実行して同じローカル ディレクトリの passphrase.txt に保存します。

    ```
    echo <passphrase> >passphrase.txt
    ```
    例: echo itUx70I47uxDuUVY >passphrase.txt

2. 構成サーバーの IP アドレスをメモします。 次の手順で必要になります。

3. 次のコマンドを実行してマスター ターゲット サーバーをインストールし、さらに構成サーバーに登録します。
    
    ```
    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i <Configuration Server IP Address> -p 443 -s y -c https -P passphrase.txt
    ```

    例: ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

    スクリプトの実行が完了するまで待機します。 マスター ターゲットは、正常に登録されていれば、ポータルの [Site Recovery インフラストラクチャ] ページで確認できます。

#### <a name="installing-master-target-using-interactive-install"></a>対話型インストールによるマスター ターゲットのインストール

1. 次のコマンドを実行してマスター ターゲットをインストールします。 エージェント ロールとして "マスター ターゲット" を選択します。

    ```
    ./install
    ```

2. インストールの既定の場所を選択します。続行するには、Enter キーを押します。
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image17.png)
    

3. 構成するグローバル設定を選択します。

    ![](./media/site-recovery-how-to-install-linux-master-target/image18.png)
    
4. CS サーバーの IP アドレスを指定します。

5. CS サーバーのポートを 443 として指定します。
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image19.png)
    
6. 構成サーバーの C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase から CS のパスフレーズをコピーし、パスフレーズ ボックスに指定します。 コピーして貼り付けた後も、ここには何も表示されません。

7. メニューの [Quit (終了)] に移動します。

8. インストールと登録を完了します。

### <a name="install-vmware-tools-on-the-master-target-server"></a>マスター ターゲット サーバーに VMware ツールをインストールする

VMware ツールは、データストアを検出できるように、MT 上にインストールする必要があります。 ツールをインストールしていない場合は、再保護画面にデータストアが表示されません。 VMware ツールのインストール後、再起動する必要があります。

## <a name="next-steps"></a>次のステップ
マスター ターゲットは、インストールと登録が完了すると、[Site Recovery インフラストラクチャ] の [マスター ターゲット] セクション (構成サーバーの概要の下) に表示されます。

[再保護](site-recovery-how-to-reprotect.md)とフェールバックの作業に進んでください。

## <a name="common-issues"></a>一般的な問題

* MT をはじめとする管理コンポーネントでは一切 Storage vMotion を有効にしないでください。 再保護に成功した後で MT が移動されると、VMDK を切断できず、フェールバックが失敗します。
* MT の仮想マシンには、スナップショットが一切存在していないことが必要です。 スナップショットが存在するとフェールバックが失敗します。
* NIC の構成がカスタマイズされていることが原因で、起動中にネットワーク インターフェイスが無効となり、MT エージェントを初期化できない場合があります。 以下のプロパティが正しく設定されていることを確認してください。
    * イーサネット カード ファイル /etc/sysconfig/network-scripts/ifcfg-eth* で次の&2; つのプロパティを確認します。
        * BOOTPROTO=dhcp 
        * ONBOOT=yes


