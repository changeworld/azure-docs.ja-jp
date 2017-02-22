---
title: "Linux マスター ターゲット サーバーをインストールする方法 | Microsoft Docs"
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
ms.date: 12/20/2016
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: d1a7ed7e182530f81a426a4383297a49505f65ea
ms.openlocfilehash: d76ea0fb27ecece4e8dcd06a2dde9a0794071884


---
# <a name="how-to-install-linux-master-target-server"></a>Linux マスター ターゲット サーバーをインストールする方法
Azure Site Recovery サービスは、仮想マシンと物理サーバーのレプリケーション、フェールオーバー、復旧を調整してビジネス継続性と障害復旧 (BCDR) 戦略に貢献します。 コンピューターを Azure に、またはオンプレミスのセカンダリ データ センターにレプリケートできます。 簡単な概要については、「 [Site Recovery とは](site-recovery-overview.md)

## <a name="overview"></a>概要
この記事は、Site Recovery を使用して保護されている仮想マシンと物理サーバーを復旧するプロセス (フェールオーバーおよびフェールバック) に関する情報とその手順を示しています。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。

## <a name="pre-requisites"></a>前提条件

1. MT をデプロイするホストを正しく選択するには、フェールバックをオンプレミスの既存 VM に行うか、新しい VM に行うかを決定する必要があります (オンプレミスの VM は削除されたため)。 
    * 既存の VM の場合、MT のホストから VM のデータストアにアクセスできる必要があります。
    * VM がオンプレミスに存在しない場合、フェールバック VM は MT と同じホスト上に作成されます。
2. MT は、プロセス サーバーおよび構成サーバーと通信できるネットワーク上に存在する必要があります。
3. MT のバージョンは、プロセス サーバーおよび構成サーバー以下である必要があります  (たとえば、CS が 9.4 上にある場合、MT にできるのは 9.5 ではなく、9.4 および 9.3 です)。
4. MT に使用できるのは VMware VM のみです。物理 VM は使用できません。


## <a name="steps-to-deploy-the-master-target-server"></a>マスター ターゲット サーバーをデプロイする手順

### <a name="install-centos-66-minimal"></a>CentOS 6.6 最小構成をインストールする

後述の手順に従って CentOS 6.6 - 64 ビット オペレーティング システムをインストールします。

**手順 1.** 以下のリンクから最も近いミラーを選択して CentOS 6.6 最小構成 64 ビット ISO をダウンロードします。

<http://archive.kernel.org/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

<http://mirror.symnds.com/distributions/CentOS-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

<http://bay.uchicago.edu/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

<http://mirror.nsc.liu.se/centos-store/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

CentOS 6.6 最小構成 64 ビット ISO を DVD ドライブに保持し、システムを起動します。

![](./media/site-recovery-how-to-install-linux-master-target/media/image1.png)

**手順 2.****[スキップ]** を選択してメディア テストのプロセスを無視します。

![](./media/site-recovery-how-to-install-linux-master-target/media/image2.png)

**手順 3.** インストールのウェルカム画面が表示されます。 ここで **[次へ]** ボタンをクリックします。

![](./media/site-recovery-how-to-install-linux-master-target/media/image3.png)

**手順 4.** 優先言語として **[英語]** を選択し、**[次へ]** をクリックして続行します。

![](./media/site-recovery-how-to-install-linux-master-target/media/image4.png)

**手順 5:**キーボード レイアウトとして **[英語 (米国)]** を選択します。 **[次へ]** をクリックしてインストールを続行します。

![](./media/site-recovery-how-to-install-linux-master-target/media/image5.png)

**手順 6.** インストールするデバイスの種類を選択します。 **[Basic storage Devices (基本ストレージ デバイス)]** を選択します。

**[次へ]** をクリックしてインストールを続行します。

![](./media/site-recovery-how-to-install-linux-master-target/media/image6.png)

**手順 7.** ハード ドライブ内の既存のデータが削除されることを示す警告メッセージが表示されます。 ハード ドライブに重要なデータが保存されていないことを確認し、**[Yes, discard any data (はい、すべてのデータを破棄します)]** をクリックします。

![](./media/site-recovery-how-to-install-linux-master-target/media/image7.png)

**手順 8.** **[ホスト名]** テキスト ボックスにサーバーのホスト名を入力します。
**[ネットワークの構成]** をクリックします。

**[ネットワーク接続]** ウィンドウでネットワーク インターフェイスを選択します。 **[編集]** ボタンをクリックして IPv4 設定を構成します。

![](./media/site-recovery-how-to-install-linux-master-target/media/image8.png)

**手順 9.** 次の **[Editing System etho (System etho の編集)]** ウィンドウが表示されます。 **[自動的に接続する]** チェック ボックスをオンにします。 [IPv4 設定] タブで、方法として **[手動]** を選択し、**[追加]** ボタンをクリックします。 静的 IP、ネットマスク、ゲートウェイ、および DNS サーバーの詳細を指定します。 **[適用]** をクリックして詳細情報を保存します。

![](./media/site-recovery-how-to-install-linux-master-target/media/image9.png)

**手順 10.** コンボ ボックスからローカルのタイム ゾーンを選択し、**[次へ]** をクリックして続行します。

![](./media/site-recovery-how-to-install-linux-master-target/media/image10.png)

**手順 11.** **[ルート パスワード]** にパスワードを入力して確認し、**[次へ]** をクリックして続行します。

![](./media/site-recovery-how-to-install-linux-master-target/media/image11.png)

**手順 12.** パーティションのモードとして **[カスタム レイアウトの作成]** を選択し、**[次へ]** をクリックして続行します。

![](./media/site-recovery-how-to-install-linux-master-target/media/image12.png)

**手順 13.** **空き**パーティションを選択し、ファイル システムの種類として **ext4** を指定して **/**、**/var/crash**、および **/home** の各パーティションを作成するために **[作成]** をクリックします。 ファイル システムの種類として **swap** を指定して、**スワップ パーティション**を作成します。 パーティションのサイズを割り当てるには、次の表に示すサイズの割り当て式に従います。

注: Linux マスター ターゲット (MT) システムでは、LVM をルートまたはリテンション期間ストレージ スペースとして使用しないでください。 Linux MT は、既定で、LVM パーティション/ディスク検出を回避するように構成されます。

![](./media/site-recovery-how-to-install-linux-master-target/media/image13.png)

**手順 14.** パーティションの作成後、**[次へ]** をクリックして続行します。

![](./media/site-recovery-how-to-install-linux-master-target/media/image14.png)

**手順 15.** 既存のデバイスが見つかった場合、フォーマットによってデータが消去されることを警告するメッセージが表示されます。

**[フォーマット]** をクリックし、最新のパーティション テーブルを使用してハード ドライブをフォーマットします。

![](./media/site-recovery-how-to-install-linux-master-target/media/image15.png)



**手順 16.** **[変更をディスクに書き込む]** をクリックして、パーティションの変更をディスクに適用します。

![](./media/site-recovery-how-to-install-linux-master-target/media/image16.png)

**手順 17.** **[ブート ローダーをインストールする]** オプションを確認して、**[次へ]** をクリックしてルート パーティションにブート ローダーをインストールします。

![](./media/site-recovery-how-to-install-linux-master-target/media/image17.png)



**手順 18.** インストール プロセスが始まります。 インストールの進行状況を監視できます。

![](./media/site-recovery-how-to-install-linux-master-target/media/image18.png)

**手順 19.** インストールが正常に完了すると次の画面が表示されます。 **[再起動]** をクリックします。

![](./media/site-recovery-how-to-install-linux-master-target/media/image19.png)


### <a name="post-installation-steps"></a>インストール後の手順

Linux 仮想マシンの各 SCSI ハード ディスクの SCSI ID を取得するには、パラメーター "disk.EnableUUID = TRUE" を有効にする必要があります。

このパラメーターを有効にするには、次の手順を実行します。

a. 仮想マシンをシャットダウンします。

b. 左側のパネルで VM のエントリを右クリックし、**[設定の編集]** を選択します。

c. **[Options]** タブをクリックします。

d. 左側で **[詳細設定] &gt; [全般]** を選択し、右側に表示される **[構成パラメーター]** をクリックします。

![](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

[Configuration Parameters] オプションは、マシンの動作中には無効になります。 このタブを有効にするには、マシンをシャットダウンします。

e. **[disk.EnableUUID]** と表示される行が存在するかどうかを確認します。

  そのような行が存在し、値が False の場合、True の値で上書きします (True と False の値では、大文字小文字は区別されません)。

  そのような行が存在し、値が True の場合、[Cancel] をクリックし、ゲスト オペレーティング システムが起動した後に、その OS 内で SCSI コマンドをテストします。

f. そのような行が存在しない場合、**[行の追加]** をクリックします。

  [Name] 列に disk.EnableUUID を追加します。

  その値を TRUE に設定します。

注: 前述の値を追加する際には、二重引用符で囲まないでください。

![](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="download-and-install-the-additional-packages"></a>その他のパッケージをダウンロードおよびインストールする

注: その他のパッケージをダウンロードおよびインストールする前に、システムがインターネットに接続されていることを確認します。

```
# yum install -y xfsprogs perl lsscsi rsync wget kexec-tools
```

このコマンドは、次に示す 15 のパッケージを CentOS 6.6 リポジトリからダウンロードしてインストールします。


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

注: ソースの保護されたマシンで、ルートまたはブート デバイスとして Reiser または XFS のファイル システムが使用されている場合、保護する前に、次の追加パッケージを Linux マスター ターゲットにダウンロードしてインストールする必要があります。

***ReiserFS (Suse11SP3。ただし Suse11SP3 では ReiserFS は既定のファイル システムではありません)***

```
# cd /usr/local

# wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

# wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

# rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm
reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm
```

***XFS (RHEL、CentOS 7 以降)***

```
# cd /usr/local

# wget
<http://archive.kernel.org/centos-vault/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

# rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

# yum install device-mapper-multipath 
```
これは、MT サーバーでマルチパス パッケージを有効にするために必要です。

### <a name="download-the-mt-installation-packages"></a>MT のインストール パッケージをダウンロードする

こちら ([https://aka.ms/latestlinuxmobsvc](https://aka.ms/latestlinuxmobsvc)) から最新の Linux マスター ターゲット インストール ビットをダウンロードします。

Linux を使用してこれをダウンロードするには、次のように入力します。 

```
    # wget https://aka.ms/latestlinuxmobsvc
```

ダウンロードして、必ずホーム ディレクトリのみでインストーラーを解凍してください。 /usr/Local に解凍すると、インストールは失敗します。

### <a name="apply-custom-configuration-changes"></a>カスタム構成変更を適用する

カスタム構成変更を適用する前に、インストール後の手順が完了していることを確認します。

カスタム構成変更を適用するには、次の手順を実行します。

1. RHEL6-64 ユニファイド エージェント バイナリを、新たに作成した OS にコピーします。

2. 次のコマンドを実行して、バイナリを解凍します。
    ```
    tar -zxvf <File name>
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/image16.png)
    
3. 次のコマンドを実行して、アクセス許可を付与します。
    ```
    # chmod 755 ./ApplyCustomChanges.sh
    ```

4. 次のコマンドを実行して、スクリプトを実行します。
    ```
    # ./ApplyCustomChanges.sh
    ```
注: スクリプトは、サーバー上で&1; 回のみ実行してください。 前述のスクリプトが正常に実行されたら、サーバーを**再起動**します。

### <a name="add-retention-disk-to-linux-mt-vm"></a>リテンション ディスクを Linux MT VM に追加する 

以下に示す手順に従ってリテンション ディスクを作成します。

**手順 1.** 新しい **1 TB** のディスクを Linux MT VM にアタッチし、マルチパス ID を調べます。

**multipath -ll** コマンドを呼び出し、リテンション ディスクのマルチパス ID を確認します。

![](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

**手順 2.** **mkfs.ext4 /dev/mapper/<リテンション ディスクのマルチパス ID>** コマンドを呼び出し、リテンション ディスクにファイルシステムを作成します。

![](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

**手順 3.** ファイル システムの作成が終わったら、リテンション ディスクをマウントする次のコマンドを呼び出します。

![](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

**手順 4.** 最後に fstab エントリを作成します。
```
vi /etc/fstab 
```
次の行を追加します。

** /dev/mapper/36000c2989daa2fe6dddcde67f2079afe /mnt/retention ext4 rw 0 0 **

### <a name="install-master-target"></a>マスター ターゲットをインストールする


> [!NOTE]
> マスター ターゲット サーバーのバージョンは、プロセス サーバーおよび構成サーバー以下である必要があります。 MT のバージョンの方が高い場合、再保護は成功しますが、レプリケーションで失敗します。
> 

> [!NOTE]
> マスター ターゲット サーバーをインストールする前に、VM の /etc/hosts ファイルに、ローカル ホスト名を、すべてのネットワーク アダプターに関連付けられた IP アドレスにマップするエントリが含まれることを確認します。
> 


1. 次のコマンドを実行してマスター ターゲットをインストールします。 エージェント ロールとして "マスター ターゲット" を選択します。
```
# ./install
```

2. インストールの既定の場所を選択します。
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image17.png)
    

3. 構成するグローバル設定を選択します。

    ![](./media/site-recovery-how-to-install-linux-master-target/image18.png)
    
4. CS サーバーの IP アドレスを指定します。

5. CX サーバーのポートを指定します。通常は 9443 です。
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image19.png)
    
6. 構成サーバーの C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase から CS パスフレーズをコピーします。

7. インストールと登録を完了します。
### <a name="install-vmware-tools-on-the-master-target-server"></a>マスター ターゲット サーバーに VMware ツールをインストールする

VMware ツールは、データストアを検出できるように、MT 上にインストールする必要があります。 ツールをインストールしていない場合は、再保護画面にデータストアが表示されません。

## <a name="next-steps"></a>次のステップ
 

## <a name="common-issues"></a>一般的な問題



<!--HONumber=Feb17_HO3-->


