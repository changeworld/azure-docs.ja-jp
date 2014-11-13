<properties urlDisplayName="Configure RAID on Linux" pageTitle="Azure 内の Linux を実行する仮想マシンでのソフトウェア RAID の構成" metaKeywords="raid in Azure, madadm Azure, stripe disks in Azure" description="mdadm を使用して Azure 内の Linux で RAID を構成する方法について説明します。" metaCanonical="http://www.windowsazure.com/ja-jp/manage/linux/articles/virtual-machines-linux-configure-raid" services="virtual-machines" documentationCenter="" title="" authors="szark" solutions="" writer="szark" manager="timlt" editor=""  />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="szark" />

# Linux でのソフトウェア RAID の構成

一般的なシナリオは、Azure 内の Linux 仮想マシンでソフトウェア RAID を使用して、複数のデータ ディスクを 1 つの RAID デバイスとしてアタッチすることです。このシナリオを使用すると通常、1 つのみのディスクを使用するシナリオよりもパフォーマンスとスループットが向上します。

## データ ディスクをアタッチする

2 つ以上の空のデータ ディスクが通常、RAID デバイスの構成に必要になります。この記事では、データ ディスクを Linux 仮想マシンにアタッチする方法については詳しく説明しません。Azure 内の Linux 仮想マシンに空のデータ ディスクをアタッチする方法の詳細については、Windows Azure の記事「[Attach an empty disk (空のディスクをアタッチする)][Attach an empty disk (空のディスクをアタッチする)]」を参照してください。

> [WACOM.NOTE] XS の VM サイズでは、仮想マシンへの複数のデータ ディスクのアタッチはサポートされていません。サポートされている VM のサイズとデータ ディスクの数の詳細については、「[Virtual Machine and Cloud Service Sizes for Windows Azure (Windows Azure の仮想マシンとクラウド サービスのサイズ)][Virtual Machine and Cloud Service Sizes for Windows Azure (Windows Azure の仮想マシンとクラウド サービスのサイズ)]」を参照してください。

## mdadm ユーティリティをインストールする

-   **Ubuntu**

        # sudo apt-get update
        # sudo apt-get install mdadm

-   **CentOS と Oracle Linux**

        # sudo yum install mdadm

-   **SLES と openSUSE**

        # zypper install mdadm

## ディスク パーティションを作成する

この例では、/dev/sdc に 1 つのディスク パーティションを作成します。その後、新しいディスク パーティションに /dev/sdc1 という名前を付けます。

-   fdisk を使用してパーティションの作成を開始する

        # sudo fdisk /dev/sdc
        Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
        Building a new DOS disklabel with disk identifier 0xa34cb70c.
        Changes will remain in memory only, until you decide to write them.
        After that, of course, the previous content won't be recoverable.

        WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
                 switch off the mode (command 'c') and change display units to
                 sectors (command 'u').

-   プロンプトが表示されたら N キーを押して、新しいパーティションを作成します。

        Command (m for help): n

-   次に、P キーを押して、プライマリ パーティションを作成します。

        Command action
            e   extended
            p   primary partition (1-4)
        p

-   1 キーを押して、パーティション番号 1 を選択します。

        Partition number (1-4): 1

-   新しいパーティションの開始位置を選択するか、`<enter>` キーを押して既定の設定をそのまま使用します。既定では、ドライブの空き領域の先頭にパーティションが配置されます。

        First cylinder (1-1305, default 1):
        Using default value 1

-   パーティションのサイズとして、たとえば「+10G」と入力して、10 GB のパーティションを作成します。または、`<enter>` キーを押して、ドライブ全体にまたがる 1 つのパーティションを作成します。

        Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
        Using default value 1305

-   次に、パーティションの ID とタイプを変更します (コマンド 't')。既定の ID '83' (Linux) から ID 'fd' (Linux raid auto) に変更してください。

        Command (m for help): t
        Selected partition 1
        Hex code (type L to list codes): fd

-   最後に、パーティション テーブルをドライブに書き込み、fdisk を終了します。

        Command (m for help): w
        The partition table has been altered!

## RAID アレイを作成する

1.  次の例では、3 つの個別のデータ ディスク (sdc1、sdd1、sde1) にある 3 つのパーティションを "ストライピング" (RAID レベル 0) にします。

        # sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
          /dev/sdc1 /dev/sdd1 /dev/sde1

この例では、このコマンドを実行した後、新しい RAID デバイスが **/dev/md127** という名前で作成されます。これらのデータ ディスクが前に別の無効 RAID アレイの一部となっていた場合は、必要に応じて `--force` パラメーターを `mdadm` コマンドに追加してください。

1.  新しい RAID デバイスにファイル システムを作成します。

    **CentOS、Oracle Linux、openSUSE、Ubuntu**

        # sudo mkfs -t ext4 /dev/md127

    **SLES**

        # sudo mkfs -t ext3 /dev/md127

2.  **SLES と openSUSE** - boot.md を有効にし、mdadm.conf を作成します。

        # sudo -i chkconfig --add boot.md
        # sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf

    > [WACOM.NOTE] SUSE システムでこれらの変更を行った後は再起動が必要になる場合があります。

## 新しいファイル システムを /etc/ fstab に追加する

**注意事項:** /etc/fstab ファイルを不適切に編集すると、システムが起動できなくなる可能性があります。編集方法がはっきりわからない場合は、このファイルを適切に編集する方法について、ディストリビューションのドキュメントを参照してください。編集する前に、/etc/fstab ファイルのバックアップを作成することもお勧めします。

1.  新しいファイル システムに必要なマウント ポイントを作成します。たとえば、次のようになります。

        # sudo mkdir /data

2.  /etc/fstab を編集するとき、**UUID** は、デバイス名ではなくファイル システムを参照するために使用する必要があります。新しいファイル システムの UUID を調べるには、`blkid` ユーティリティを使用します。

        # sudo /sbin/blkid
        ...........
        /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"

3.  テキスト エディターで /etc/fstab を開き、新しいファイル システムのエントリを追加します。たとえば、次のようになります。

        UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2

    または、**SLES と openSUSE** の場合は、次のようになります。

        /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2

    その後、/etc/fstab を保存して閉じます。

4.  /etc/fstab のエントリが正しいことをテストします。

        # sudo mount -a

    このコマンドによりエラー メッセージが表示された場合は、/etc/fstab ファイル内の構文を確認してください。

    次に、`mount` コマンドを実行して、ファイル システムがマウントされていることを確認します。

        # mount
        .................
        /dev/md127 on /data type ext4 (rw)

5.  省略可能なパラメーター

    多くのディストリビューションでは、`nobootwait` または `nofail` のいずれかのマウント パラメーターが /etc/fstab ファイルに追加されている場合があります。これらのパラメーターにより、特定のファイル システムをマウントしているときのエラーが許容されます。RAID ファイル システムを適切にマウントできない場合でも、Linux システムの起動を続行できるようになります。これらのパラメーターの詳細については、使用しているディストリビューションのドキュメントを参照してください。

    例 (Ubuntu):

        UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2

    これらのパラメーターのほかにも、カーネル パラメーター "`bootdegraded=true`" では、RAID が破損または劣化として認識された場合、たとえばデータ ドライブが誤って仮想マシンから削除された場合でも、システムを起動できるようになります。既定では、この場合はシステムが起動できなくなる可能性があります。

    カーネル パラメーターの適切な編集方法については、使用しているディストリビューションのドキュメントを参照してください。たとえば、多くディストリビューション (CentOS、Oracle Linux、SLES 11) では、これらのパラメーターを "`/boot/grub/menu.lst`" ファイルに手動で追加することもできます。Ubuntu では、このパラメーターを "/etc/default/ grub" の `GRUB_CMDLINE_LINUX_DEFAULT` 変数に追加できます。

  [Attach an empty disk (空のディスクをアタッチする)]: http://www.windowsazure.com/ja-jp/documentation/articles/storage-windows-attach-disk/#attachempty
  [Virtual Machine and Cloud Service Sizes for Windows Azure (Windows Azure の仮想マシンとクラウド サービスのサイズ)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn197896.aspx
