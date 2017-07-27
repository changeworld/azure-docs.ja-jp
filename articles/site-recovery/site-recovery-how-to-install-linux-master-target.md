---
title: "Azure からオンプレミスへのフェールオーバーに必要な Linux マスター ターゲット サーバーをインストールする方法 | Microsoft Docs"
description: "Linux 仮想マシンを再保護する前に、Linux マスター ターゲット サーバーが必要になります。 そのインストール方法について説明します。"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: c7c50c539149a929b15f50e4b52dc48d92534640
ms.contentlocale: ja-jp
ms.lasthandoff: 07/06/2017


---
# <a name="how-to-install-a-linux-master-target-server"></a>Linux マスター ターゲット サーバーをインストールする方法
仮想マシンをフェールオーバーした後、仮想マシンをオンプレミス サイトにフェールバックできます。 フェールバックするには、Azure からオンプレミス サイトへの仮想マシンを再保護する必要があります。 このプロセスには、トラフィックを受信するオンプレミス マスター ターゲット サーバーが必要です。 保護された仮想マシンが Windows 仮想マシンである場合、Windows マスター ターゲットが必要です。 Linux 仮想マシンには、Linux マスター ターゲットが必要になります。 ここでは、Linux のマスター ターゲットを作成してインストールする方法について説明しています。

> [!IMPORTANT]
> マスター ターゲット サーバー リリース 9.10.0 以降、最新のマスター ターゲット サーバーは Ubuntu 16.04 サーバーにのみインストールできます。 CentOS6.6 サーバーには新規インストールできません。 ただし、9.10.0 バージョンを使って古いマスター ターゲット サーバーのアップグレードを続けることはできます。

## <a name="overview"></a>概要
この記事では、Linux のマスター ターゲットをインストールするために必要な情報と手順を紹介しています。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。

## <a name="prerequisites"></a>前提条件

* マスター ターゲットのデプロイ先となるホストを正しく選ぶためには、フェールバックの切り替え先の判断が必要となります。つまり既存のオンプレミスの仮想マシンにフェールバックするか、オンプレミスの仮想マシンが削除されているために新しい仮想マシンにフェールバックするかを判断します。
    * 既存の仮想マシンでは、マスター ターゲットのホストには、仮想マシンのデータストアへのアクセスが必要です。
    * オンプレミスの仮想マシンが存在しない場合、マスター ターゲットとして同じホスト上にフェールバックの仮想マシンが作成されます。 マスター ターゲットは任意の ESXi ホストにインストールすることができます。
* マスター ターゲットは、プロセス サーバーおよび構成サーバーと通信できるネットワーク上に存在する必要があります。
* マスター ターゲットのバージョンは、プロセス サーバーおよび構成サーバーと同等か、それより前のバージョンにする必要があります。 たとえば、構成サーバーのバージョンが 9.4 である場合、マスター ターゲットのバージョンは 9.4 または 9.3 にすることができますが、9.5 にすることはできません。
* マスター ターゲットは、VMware 仮想マシンにのみすることができますが、物理サーバーにすることはできません。

## <a name="master-target-sizing-guideline"></a>マスター ターゲット サイズのガイドライン

マスター ターゲットは、サイズに関する次のガイドラインに従って作成する必要があります。
    * RAM - 6 GB 以上
    * OS ディスク サイズ - 100 GB 以上 (CentOS6.6 をインストールする場合)
    * リテンション ドライブの追加ディスク サイズ - 1 TB
    * CPU コア数 - 4 コア以上


## <a name="steps-to-deploy-the-master-target-server"></a>マスター ターゲット サーバーをデプロイする手順

**Ubuntu 16.04.2 最小構成をインストールする**

後述の手順に従って Ubuntu 16.04.2 - 64 ビット オペレーティング システムをインストールします。

**手順 1.** 以下のリンクから最も近いミラーを選択して Ubuntu 16.04.2 最小構成 64 ビット ISO をダウンロードします

<https://www.ubuntu.com/download/server/thank-you?version=16.04.2&architecture=amd64>

Ubuntu 16.04.2 最小構成 64 ビット ISO を DVD ドライブに保持し、システムを起動します。

**手順 2:** 優先言語として **[英語]** を選択し、Enter キーを押します。

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image1.png)

**手順 3:** **[Install Ubuntu Server\(Ubuntu Server のインストール\)]** を選択し、Enter キーを押します

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image2.png)

**手順 4:** 優先言語として **[英語]** を選択し、Enter キーを押します

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image3.png)

**手順 5:** **[Time Zone\(タイム ゾーン\)]** オプション一覧から適切なオプションを選択し、Enter キーを押します

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image4.png)

**手順 6:** 既定のオプションで **[NO\(いいえ\)]** を選択し、Enter キーを押します

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image5.png)

**手順 7:** キーボードの本来の国として **[English (US)\(英語(米国)\)]** を選択し、Enter キーを押します

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image6.png)

**手順 8:** キーボード レイアウトとして **[English (US)\(英語(米国)\)]** を選択し、Enter キーを押します

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image7.png)

**手順 9.** **[Hostname\(ホスト名\)]** テキスト ボックスにサーバーのホスト名を入力し、**[Continue\(続行\)]** ボタンをクリックします

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image8.png)

**手順 10:** **テキスト ボックス**に**ユーザー名**を入力してユーザー アカウントを作成し、**[Continue\(続行\)]** ボタンをクリックします

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image9.png)

**手順 11:** **テキスト ボックス**に新しいユーザー アカウントの**パスワード**を入力し、**[Continue\(続行\)]** ボタンをクリックします

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image10.png)

**手順 12:** **テキスト ボックス**に新しいユーザーの**パスワード**を確認入力し、**[Continue\(続行\)]** ボタンをクリックします

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image11.png)

**手順 13:** 既定のオプションで **[NO\(いいえ\)]** を選択し、**Enter** キーを押します

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image12.png)

**手順 14:** 表示されたタイム ゾーンが正しい場合は既定のオプション **[YES\(はい\)]** を選択し、**Enter** キーを押します。

タイム ゾーンを再設定する場合は **[NO\(いいえ\)]** オプションを選択します。

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image13.png)

**手順 15:** パーティション分割方法オプションで **[Guided -** **Use entire disk\(ガイド付き - ディスク全体を使用する\)]** を選択し、**Enter** キーを押します。

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image14.png)

**手順 16:** **[Select disk to partition\(パーティション分割するディスクを選択してください\)]** オプションで適切なディスクを選択し、**Enter** キーを押します。

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image15.png)

**手順 17:** **[YES\(はい\)]** を選択して変更をディスクに書き込み、**Enter** キーを押します。

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image16.png)

**手順 18:** 既定のオプションを選択し、[Continue\(続行\)] を選択して、Enter キーを押します。

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image17.png)

**手順 19:** システムのマネージャー アップグレードの適切なオプションを選択して、**Enter** キーを押します

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image18.png)

> [!WARNING]
> Azure サイト リカバリー マスター ターゲット サーバーとしての Ubuntu は非常に特定のバージョンが必要なので、仮想マシンのカーネル アップグレードを無効にする必要があります。 有効になっていると、定期的なアップグレードでマスター ターゲット サーバーが正しく機能しなくなります。 [No automatic updates\(自動更新しない\)] オプションを選択する必要があります。


**手順 20:** 既定のオプションのままでかまいません。 openSSH を SSH 接続に使いたい場合は、[OpenSSH server\(OpenSSH サーバー\)] オプションを選択して [Continue\(続行\)] をクリックします。

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image19.png)

**手順 21:** **[YES\(はい\)]** オプションを選択し、**Enter** キーを押します

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image20.png)

**手順 22:** ブート ローダーのインストールに適切なデバイス (推奨は /dev/sda) を選択し、**Enter** キーを押します。

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image21.png)

**手順 23:** **[Continue\(続行\)]** ボタンを選択し、**Enter** キーを押して、**インストールを完了**します。

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image22.png)

インストールが完了した後、 新しいユーザー資格情報 (**手順 10** を参照) を使って VM にログインします

次のスクリーンショットで説明する手順に従って、ROOT ユーザーのパスワードを設定します。さらに操作を行うには ROOT ユーザーとしてログインします。

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image23.png)


### <a name="post-installation-steps"></a>インストール後の手順
次に、マスター ターゲット サーバーとして構成するマシンを準備します。

Linux 仮想マシンの各 SCSI ハード ディスクの ID を取得するには、**disk.EnableUUID = TRUE** パラメーターを有効にする必要があります。

このパラメーターを有効にするには、次の手順を使用します。

1. 仮想マシンをシャットダウンします。

2. 左ウィンドウで仮想マシンのエントリを右クリックし、**[設定の編集]** を選択します。

3. **[Options]** タブをクリックします。

4. 左ウィンドウで **[詳細設定] &gt; [全般]** を選択し、右側に表示される **[構成パラメーター]** ボタンをクリックします。

    ![[Options] タブ](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

    **[構成パラメーター]** オプションは、マシンの実行時には使用できません。 このタブをアクティブにするには、仮想マシンをシャットダウンします。

5. **[disk.EnableUUID]** と表示される行が存在するかどうかを確認します。

    - その値が存在していて **False** に設定されている場合、**True** に値を変更します (値の大文字小文字は区別されません)。

    - その値が存在していて **True** に設定されている場合、**[キャンセル]** をクリックします。

    - そのような値が存在しない場合、**[行の追加]** をクリックします。

    - **[名前]** 列に **disk.EnableUUID** を追加し、その値を **TRUE** に設定します。

    ![[disk.EnableUUID] が存在するかどうかを確認する](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="disable-kernel-upgrades"></a>**カーネルのアップグレードを無効にする**

Azure サイト リカバリー マスター ターゲット サーバーとしての Ubuntu は非常に特定のバージョンが必要なので、仮想マシンのカーネル アップグレードを無効にする必要があります。 有効になっていると、定期的なアップグレードでマスター ターゲット サーバーが正しく機能しなくなります。 カーネルのアップグレードを無効にするには以下の手順のようにします。
> [!IMPORTANT]
> スクリプトの手順をここに記述する必要があります

#### <a name="download-and-install-additional-packages"></a>その他のパッケージをダウンロードおよびインストールする

> [!NOTE]
> 追加パッケージをダウンロードしてインストールするには、インターネットに接続できることを確認してください。 インターネットに接続できない場合は、これらの RPM パッケージを手動で検索してインストールする必要があります。

```
apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx
```

### <a name="get-the-installer-for-setup"></a>セットアップに使用するインストーラーの入手

マスター ターゲットからインターネットに接続できる場合は、以下の手順でインストーラーをダウンロードできます。 インターネットに接続できない場合は、プロセス サーバーからインストーラーをコピーしてインストールできます。

#### <a name="download-the-master-target-installation-packages"></a>マスター ターゲットのインストール パッケージをダウンロードする

[最新の Linux マスター ターゲット インストール ビットをダウンロードします](https://aka.ms/latestlinuxmobsvc)。

Linux を使用してこれをダウンロードするには、次のように入力します。

```
wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz
```

ダウンロードして、必ずホーム ディレクトリでインストーラーを解凍してください。 /usr/Local に解凍すると、インストールは失敗します。


#### <a name="access-the-installer-from-the-process-server"></a>プロセス サーバーからインストーラーにアクセスする

1. プロセス サーバーの C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository に移動します。

2. プロセス サーバーから必要なインストーラー ファイルをコピーし、自分の home ディレクトリに latestlinuxmobsvc.tar.gz という名前で保存します。


### <a name="apply-custom-configuration-changes"></a>カスタム構成変更を適用する

カスタム構成変更を適用するには、次の手順を使用します。


1. 次のコマンドを実行して、バイナリを解凍します。
    ```
    tar -zxvf latestlinuxmobsvc.tar.gz
    ```
    ![実行されるコマンドのスクリーンショット](./media/site-recovery-how-to-install-linux-master-target/image16.png)

2. 次のコマンドを実行して、アクセス許可を付与します。
    ```
    chmod 755 ./ApplyCustomChanges.sh
    ```

3. 次のコマンドを実行して、スクリプトを実行します。
    ```
    ./ApplyCustomChanges.sh
    ```
> [!NOTE]
> スクリプトは、サーバー上で 1 回のみ実行してください。 サーバーをシャットダウンします。 以降の手順で指定されているディスクを追加した後、サーバーを再起動してください。

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>リテンション ディスクを Linux マスター ターゲット仮想マシンに追加する

リテンション ディスクを作成するには、次の手順を使用します。

1. 新しい **1 TB** ディスクを Linux マスター ターゲット 仮想マシンに接続し、マシンを**起動**します。

2. **multipath -ll** コマンドを使用し、リテンション ディスクのマルチパス ID を確認します。

    ```
    multipath -ll
    ```

    ![リテンション ディスクのマルチパス ID](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

3. ドライブをフォーマットし、新しいドライブにファイル システムを作成します。

    ```
    mkfs.ext4 /dev/mapper/<Retention disk's multipath id>
    ```
    ![ドライブにファイル システムを作成する](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

4. ファイル システムを作成したら、リテンション ディスクをマウントします。
    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

    ![リテンション ディスクをマウントする](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

5. **fstab** エントリを作成し、毎回起動中にリテンション ドライブをマウントします。
    ```
    vi /etc/fstab
    ```
    **Ins** キーを押してファイルの編集を開始します。 新しい行を作成し、次のテキストを挿入します。 前述のコマンドでハイライトされているマルチパス ID に基づいてディスクのマルチパス ID を編集します。

    **/dev/mapper/<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    **Esc** キーを押して「**:wq**」(write and quit) と入力し、エディター ウィンドウを閉じます。

### <a name="install-the-master-target"></a>マスター ターゲットをインストールする

> [!IMPORTANT]
> マスター ターゲット サーバーのバージョンは、プロセス サーバーおよび構成サーバーと同等か、それより前のバージョンにする必要があります。 この条件が満たされない場合、再保護は成功しますが、レプリケーションで失敗します。


> [!NOTE]
> マスター ターゲット サーバーをインストールする前に、仮想マシンの /etc/hosts ファイルに、ローカル ホスト名を、すべてのネットワーク アダプターに関連付けられた IP アドレスにマップするエントリが含まれることを確認します。

1. 構成サーバーの C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase からパスフレーズをコピーし、次のコマンドを実行して同じローカル ディレクトリの passphrase.txt に保存します。

    ```
    echo <passphrase> >passphrase.txt
    ```
    例: echo itUx70I47uxDuUVY >passphrase.txt

2. 構成サーバーの IP アドレスをメモします。 次の手順で必要になります。

3. 次のコマンドを実行してマスター ターゲット サーバーをインストールし、さらに構成サーバーに登録します。

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    例: /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt

    スクリプトが完了するまで待機します。 マスター ターゲットは、正常に登録されていれば、ポータルの [Site Recovery インフラストラクチャ] ページに表示されます。


#### <a name="install-the-master-target-by-using-interactive-install"></a>対話型インストールによりマスター ターゲットをインストールする

1. 次のコマンドを実行してマスター ターゲットをインストールします。 エージェント ロールとして **マスター ターゲット** を選択します。

    ```
    ./install
    ```

2. インストールの既定の場所を選択します。続行するには、**Enter** キーを押します。

    ![マスター ターゲットのインストールの既定の場所を選択する](./media/site-recovery-how-to-install-linux-master-target/image17.png)

インストールが完了した後、コマンド ラインを使って構成サーバーを登録する必要があります。

1. 構成サーバーの IP アドレスをメモします。 次の手順で必要になります。

2. 次のコマンドを実行してマスター ターゲット サーバーをインストールし、さらに構成サーバーに登録します。

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    例: /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt

    スクリプトが完了するまで待機します。 マスター ターゲットは、正常に登録されていれば、ポータルの [Site Recovery インフラストラクチャ] ページに表示されます。


### <a name="upgrade-the-master-target"></a>マスター ターゲットをアップグレードする

インストーラーを実行します。 マスター ターゲットにエージェントがインストールされていることが自動的に検出されます。 "Y" を選択してアップグレードします。 セットアップが完了した後、次のコマンドを使って、インストールされているマスター ターゲットのバージョンを確認できます。

    ```
        cat /usr/local/.vx_version
    ```

[VERSION] フィールドにマスター ターゲットのバージョン番号が表示されます。

### <a name="install-vmware-tools-on-the-master-target-server"></a>マスター ターゲット サーバーに VMware ツールをインストールする

VMware ツールは、データストアを検出できるように、マスター ターゲット上にインストールする必要があります。 ツールをインストールしていない場合は、再保護画面にデータストアが表示されません。 VMware ツールのインストール後、再起動する必要があります。

## <a name="next-steps"></a>次のステップ
マスター ターゲットは、インストールと登録が完了すると、**[Site Recovery インフラストラクチャ]** の **[マスター ターゲット]** セクション (構成サーバーの概要の下) に表示されます。

[再保護](site-recovery-how-to-reprotect.md)とフェールバックの作業に進んでください。

## <a name="common-issues"></a>一般的な問題

* マスター ターゲットをはじめとする管理コンポーネントでは一切 Storage vMotion を有効にしないでください。 再保護に成功した後でマスター ターゲットが移動されると、仮想マシン ディスク (VMDK) を切断できず、フェールバックが失敗します。
* マスター ターゲットの仮想マシンには、スナップショットが一切存在していないことが必要です。 スナップショットが存在するとフェールバックが失敗します。
* NIC の構成がカスタマイズされていることが原因で、起動中にネットワーク インターフェイスが無効となり、マスター ターゲット エージェントを初期化できない場合があります。 以下のプロパティが正しく設定されていることを確認してください。 イーサネット カード ファイルの /etc/sysconfig/network-scripts/ifcfg-eth* で次のプロパティを確認します。
    * BOOTPROTO=dhcp
    * ONBOOT=yes

