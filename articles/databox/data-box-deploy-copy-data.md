---
title: Microsoft Azure Data Box にデータをコピーする | Microsoft Docs
description: Azure Data Box にデータをコピーする方法について説明します。
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2018
ms.author: alkohli
ms.openlocfilehash: 9bc84a9b08c4cfbdf7f24416c923e0dbd7076556
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2018
ms.locfileid: "47161932"
---
# <a name="tutorial-copy-data-to-azure-data-box"></a>チュートリアル: データを Azure Data Box にコピーする 

このチュートリアルでは、ローカル Web UI を使用してホスト コンピューターに接続し、そこからデータをコピーして、Data Box を発送する準備をする方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Data Box に接続する
> * Data Box にデータをコピーする
> * Data Box の発送を準備する

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

1. [Azure Data Box のセットアップに関するチュートリアル](data-box-deploy-set-up.md)を完了していること。
2. Data Box の受け取りが済んでいて、ポータルで注文の状態が **[配信済み]** に更新されていること。
3. Data Box にコピーするデータが格納されているホスト コンピューターがあること。 このホスト コンピューターは次の条件を満たしている必要があります。
    - [サポート対象のオペレーティング システム](data-box-system-requirements.md)が実行されていること。
    - 高速ネットワークに接続していること。 少なくとも 1 本の 10 GbE 接続を利用することを強くお勧めします。 10 GbE 接続を利用できない場合は、1 GbE データ リンクを使用できますが、コピーの速度が影響を受けます。 

## <a name="connect-to-data-box"></a>Data Box に接続する

選択したストレージ アカウントに基づいて、Data Box では最大で次のものが作成されます。
- GPv1 および GPv2 に対して関連付けられているストレージ アカウントごとに 3 つの共有。
- Premium または BLOB ストレージ アカウントに対して 1 つの共有。 

ブロック BLOB とページ BLOB の共有では、第 1 レベルのエンティティはコンテナーであり、第 2 レベルのエンティティは BLOB です。 Azure Files の共有では、第 1 レベルのエンティティは共有であり、第 2 レベルのエンティティはファイルです。

各データ メンバー フィールドが JSON オブジェクトにマップされ、フィールド名がオブジェクトの "key" 部分にマップされ、"value" 部分がオブジェクトの値の部分に再帰的にマップされます。 

- ストレージ アカウント: *Mystoracct*
- ブロック BLOB 用の共有: *Mystoracct_BlockBlob/my-container/blob*
- ページ BLOB 用の共有: *Mystoracct_PageBlob/my-container/blob*
- ファイル用の共有: *Mystoracct_AzFile/my-share*

Data Box が Windows Server ホスト コンピューターまたは Linux ホストのどちらに接続されているかにより、接続してコピーする手順が異なることがあります。

### <a name="connect-via-smb"></a>SMB 経由で接続する 

Windows Server ホスト コンピューターを使用している場合は、次の手順を実行して Data Box に接続します。

1. 最初に、認証を行ってセッションを開始します。 **[接続とコピー]** に移動します。 **[資格情報の取得]** をクリックして、ストレージ アカウントに関連付けられている共有用のアクセス資格情報を取得します。 

    ![共有の資格情報を取得する 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. [共有にアクセスしてデータをコピーする] ダイアログ ボックスで、共有に対応する **[ユーザー名]** と **[パスワード]** をコピーします。 Click **OK**.
    
    ![共有の資格情報を取得する 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. ストレージ アカウント (次の例では Mystoracct) に関連付けられている共有にアクセスします。 `\\<IP of the device>\ShareName` パスを使用して、共有にアクセスします。 データの形式によっては、次のアドレスで共有 (共有名を使用) に接続します。 
    - *\\<IP address of the device>\Mystoracct_Blob*
    - *\\<IP address of the device>\Mystoracct_Page*
    - *\\<IP address of the device>\Mystoracct_AzFile*
    
    ホスト コンピューターから共有に接続するには、コマンド ウィンドウを開きます。 コマンド プロンプトに、次のコマンドを入力します。

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    メッセージに従って共有のパスワードを入力します。 上記のコマンドを使用して共有に接続する例を次に示します。

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. Windows キーを押しながら R キーを押します。**[ファイル名指定して実行]** ウィンドウで、「`\\<device IP address>`」と入力します。 Click **OK**. これによりエクスプローラーが開きます。
    
    ![エクスプローラーで共有に接続する 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

5. フォルダーとして共有が表示されます。 コピーするファイル用のフォルダーを作成します (この例では templates)。 フォルダーにグレーのバツ印が表示されることがあります。 このバツ印はエラー状態を示すものではありません。 フォルダーは、状態を追跡するようアプリケーションでフラグを設定されています。
    
    ![エクスプローラーで共有に接続する 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) ![エクスプローラーで共有に接続する 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) 

### <a name="connect-via-nfs"></a>NFS 経由で接続する 

Linux ホスト コンピューターを使用している場合は、次の手順を実行して、NFS クライアントへのアクセスを許可するように Data Box を構成します。

1. 共有にアクセスできる許可するクライアントの IP アドレスを指定します。 ローカル Web UI で、**[接続とコピー]** ページに移動します。 **[NFS の設定]** で、**[NFS のクライアント アクセス]** をクリックします。 

    ![NFS のクライアント アクセスを構成する 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. NFS クライアントの IP アドレスを指定して、**[追加]** をクリックします。 この手順を繰り返すことにより、複数の NFS クライアントに対するアクセスを構成できます。 Click **OK**.

    ![NFS のクライアント アクセスを構成する 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Linux ホスト コンピューターに[サポートされているバージョン](data-box-system-requirements.md)の NFS クライアントがインストールされていることを確認します。 お使いの Linux ディストリビューションの特定のバージョンを使用します。 

3. NFS クライアントをインストールした後、次のコマンドを使用して、Data Box デバイスに NFS 共有をマウントします。

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    NFS 経由で Data Box の共有に接続する方法の例を次に示します。 Data Box デバイスの IP は `10.161.23.130` で、共有 `Mystoracct_Blob` は ubuntuVM にマウントされており、マウント ポイントは `/home/databoxubuntuhost/databox` です。

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box"></a>Data Box にデータをコピーする

Data Box の共有に接続したら、次にデータをコピーします。 データをコピーする前に、以下の考慮事項を確認してください。

- 適切なデータ形式に対応する共有にデータをコピーする必要があります。 たとえば、ブロック BLOB データは、ブロック BLOB 用の共有にコピーしてください。 データ形式が適切な共有の種類と一致しない場合、後続の手順で、Azure へのデータのアップロードに失敗します。
-  データのコピー中は、そのサイズが [Azure Storage と Data Box の制限](data-box-limits.md)に関するページに記載されたサイズ制限に準拠していることを確認してください。 
- Data Box によってアップロードされているデータが、Data Box の外部で別のアプリケーションによって同時にアップロードされた場合、アップロード ジョブ エラーやデータの破損が生じる可能性があります。
- SMB と NFS の両方を同時に使用しないこと、または同じデータを Azure 上の同じ最終コピー先にコピーしないことをお勧めします。 このような場合は、最終的な結果がわからなくなります。

### <a name="copy-data-via-smb"></a>SMB 経由でデータをコピーする

SMB 共有に接続した後、データのコピーを開始します。 

Robocopy などの SMB 互換ファイル コピー ツールを使用して、データをコピーできます。 Robocopy を使用して、複数のコピー ジョブを開始できます。 次のコマンドを使用します。
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
 属性については次の表で説明します。
    
|Attribute  |説明  |
|---------|---------|
|/e     |サブディレクトリをコピーします (空のディレクトリを含む)。         |
|/r:     |失敗したコピーの再試行回数を指定します。         |
|/w:     |再試行間の待ち時間 (秒) を指定します。         |
|/is     |同じファイルを含めます。         |
|/nfl     |ファイル名をログに記録しないように指定します。         |
|/ndl    |ディレクトリ名をログに記録しないように指定します。        |
|/np     |コピー操作の進行状況 (それまでにコピーされたファイルまたはディレクトリの数) が表示されないように指定します。 進行状況を表示すると、パフォーマンスが大幅に低下します。         |
|/MT     | マルチスレッドを使用します (推奨は 32 または 64 スレッド)。 このオプションは、暗号化されたファイルでは使用されません。 暗号化されたファイルと暗号化されていないファイルを分離することが必要な場合があります。 ただし、シングルスレッドのコピーでは、パフォーマンスが大幅に低下します。           |
|/fft     | 任意のファイル システムのタイムスタンプの細分性を下げるために使用します。        |
|/b     | バックアップ モードでファイルをコピーします。        |
|/z    | 再起動モードでファイルをコピーします。環境が不安定な場合はこれを使用します。 このオプションでは、追加ログによりスループットが低下します。      |
| /zb     | 再起動モードを使用します。 アクセスが拒否された場合、このオプションはバックアップ モードを使用します。 このオプションでは、チェックポイント処理のためにスループットが低下します。         |
|/efsraw     | すべての暗号化されたファイルを EFS raw モードでコピーします。 暗号化されたファイルでのみ使用します。         |
|log+:<LogFile>| 既存のログ ファイルに出力を追加します。|    
 
次のサンプルは、Data Box にファイルをコピーする robocopy コマンドの出力を示しています。
    
    C:\Users>robocopy
        -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:53 PM
            Simple Usage :: ROBOCOPY source destination /MIR
    
                    source :: Source Directory (drive:\path or \\server\share\path).
            destination :: Destination Dir  (drive:\path or \\server\share\path).
                    /MIR :: Mirror a complete directory tree.
    
        For more usage information run ROBOCOPY /?    
    
    ****  /MIR can DELETE files as well as copy them !
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:32
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
            Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
        Options : *.* /DCOPY:DA /COPY:DAT /MT:32 /R:5 /W:60
    
    ------------------------------------------------------------------------------
    
    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------
    
                    Total    Copied   Skipped  Mismatch    FAILED    Extras
        Dirs :         1         1         1         0         0         0
        Files :        17        17         0         0         0         0
        Bytes :     3.9 k     3.9 k         0         0         0         0          
    C:\Users>
       

パフォーマンスを最適化するには、データをコピーするときに、次の robocopy パラメーターを使用します。

|    プラットフォーム    |    ほとんどが小さいファイル (< 512 KB)                           |    ほとんどが中規模のファイル (512 KB ～ 1 MB)                      |    ほとんどが大きいファイル (> 1 MB)                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
|    Data Box         |    2 Robocopy セッション <br> セッションあたり 16 スレッド    |    3 Robocopy セッション <br> セッションあたり 16 スレッド    |    2 Robocopy セッション <br> セッションあたり 24 スレッド    |  |


Robocopy コマンドについて詳しくは、「[Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx)」(Robocopy といくつかの例) をご覧ください。

コピー先フォルダーを開いて、コピー済みのファイルを表示し、確認します。 コピー処理中にエラーが発生した場合は、トラブルシューティングのためにエラー ファイルをダウンロードします。

データの整合性を保証するため、データがコピーされるときにインラインでチェックサムが計算されます。 コピーが完了したら、デバイスで使用済み領域と空き領域を確認します。
    
   ![ダッシュボードで空き領域と使用済み領域を確認する](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

### <a name="copy-data-via-nfs"></a>NFS 経由でデータをコピーする

Linux ホスト コンピューターを使用している場合は、Robocopy のようなコピー ユーティリティを使用します。 Linux で使用できる代替手段は、[rsync](https://rsync.samba.org/)、[FreeFileSync](https://www.freefilesync.org/)、[Unison](https://www.cis.upenn.edu/~bcpierce/unison/)、[Ultracopier](https://ultracopier.first-world.info/) などです。  

`cp` コマンドは、ディレクトリをコピーするのに最適なオプションの 1 つです。 使用方法について詳しくは、[cp の man ページ](http://man7.org/linux/man-pages/man1/cp.1.html)をご覧ください。

マルチスレッドのコピーに rsync オプションを使用する場合は、以下のガイドラインに従ってください。

 - Linux クライアントで使用されているファイル システムに応じて、**CIFS Utils** または **NFS Utils** パッケージをインストールします。

    `sudo apt-get install cifs-utils` `sudo apt-get install nfs-utils`

 -  **Rsync** および **Parallel** をインストールします (Linux ディストリビューションのバージョンによって異なります)。

    `sudo apt-get install rsync`
    `sudo apt-get install parallel` 

 - マウント ポイントを作成します。

    `sudo mkdir /mnt/databox`

 - ボリュームをマウントします。

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

 - フォルダーのディレクトリ構造をミラー化します。  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

 - ファイルをコピーします。 

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     j は並列化の数を指定し、X は並列コピーの数です。

     16 並列コピーから始めて、使用可能なリソースに応じてスレッドの数を増やすことをお勧めします。

## <a name="prepare-to-ship"></a>発送の準備をする

最後に、発送するデバイスを準備します。 このステップでは、デバイスのすべての共有がオフラインにされます。 デバイスの出荷準備を始めた後は、共有にアクセスできません。
1. **[発送準備]** に移動し、**[準備の開始]** をクリックします。 
   
    ![発送の準備をする 1](media/data-box-deploy-copy-data/prepare-to-ship1.png)

2. 既定では、チェックサムは発送準備中にインラインで計算されます。 チェックサムの計算は、データのサイズによっては時間がかかる場合があります。 **[準備の開始]** をクリックします。
    1. 発送を準備するときは、デバイスの共有はオフラインになり、デバイスはロックされます。
        
        ![発送の準備をする 1](media/data-box-deploy-copy-data/prepare-to-ship2.png) 
   
    2. デバイスの準備が完了すると、デバイスの状態が "*発送する準備ができました*" に更新されます。 
        
        ![発送の準備をする 1](media/data-box-deploy-copy-data/prepare-to-ship3.png)

    3. このプロセスでコピーされたファイルのリスト (マニフェスト) をダウンロードします。 後でこのリストを使用して、Azure にアップロードされたファイルを確認できます。
        
        ![発送の準備をする 1](media/data-box-deploy-copy-data/prepare-to-ship4.png)

3. デバイスをシャットダウンします。 **[シャットダウンまたは再起動]** ページに移動し、**[シャットダウン]** をクリックします。 確認を求められたら、**[OK]** をクリックして続行します。
4. ケーブルを取り外します。 次の手順では、Microsoft にデバイスを発送します。

 
<!--## Appendix - robocopy parameters

This section describes the robocopy parameters used when copying the data to optimize the performance.

|    Platform    |    Mostly small files < 512 KB                           |    Mostly medium  files 512 KB-1 MB                      |    Mostly large files > 1 MB                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
|    Data Box         |    2 Robocopy sessions <br> 16 threads per sessions    |    3 Robocopy sessions <br> 16 threads per sessions    |    2 Robocopy sessions <br> 24 threads per sessions    |  |
|    Data Box Heavy     |    6 Robocopy sessions <br> 24 threads per sessions    |    6 Robocopy sessions <br> 16 threads per sessions    |    6 Robocopy sessions <br> 16 threads per sessions    |   
|    Data Box Disk         |    4 Robocopy sessions <br> 16 threads per sessions             |    2 Robocopy sessions <br> 16 threads per sessions    |    2 Robocopy sessions <br> 16 threads per sessions    |   
-->

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Data Box に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * Data Box に接続する
> * Data Box にデータをコピーする
> * Data Box の発送を準備する

次のチュートリアルに進み、Data Box でデータをセットアップしてコピーする方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Data Box を Microsoft に発送する](./data-box-deploy-picked-up.md)

