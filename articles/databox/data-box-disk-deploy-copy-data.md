---
title: Microsoft Azure Data Box Disk にデータをコピーする | Microsoft Docs
description: このチュートリアルでは、Azure Data Box Disk にデータをコピーする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 10/09/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 7bc8b3ba415f8fe701098a9fa7e51d60ffb9df4e
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092458"
---
# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>チュートリアル: Azure Data Box Disk にデータをコピーして確認する

このチュートリアルでは、ホスト コンピューターからデータをコピーして、データ整合性を確認するためのチェックサムを生成する方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * データを Data Box ディスクにコピーする
> * データの確認

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。
- [Azure Data Box Disk のインストールと構成に関するチュートリアル](data-box-disk-deploy-set-up.md)を完了していること。
- ディスクは、ロックが解除されてクライアント コンピューターに接続されます。
- ディスクにデータをコピーするために使用するクライアント コンピューターでは、[サポートされているオペレーティング システム](data-box-disk-system-requirements.md)が実行されている必要があります。


## <a name="copy-data-to-disks"></a>ディスクにデータをコピーする

ご利用のコンピューターから Data Box Disk に接続してデータをコピーするには、次の手順を実行します。

1. ロックを解除したドライブの内容を表示します。 

    ![ドライブの内容を表示する](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. ブロック BLOB としてインポートするデータを BlockBlob フォルダーにコピーします。 同様に、VHD/VHDX などのデータを PageBlob フォルダーにコピーします。 

    Azure Storage アカウントには、BlockBlob フォルダー下および PageBlob フォルダー下のサブフォルダーごとにコンテナーが 1 つ作成されます。 BlockBlob フォルダー下のファイルと PageBlob フォルダー下のファイルはすべて、Azure Storage アカウントの既定のコンテナー `$root` にコピーされます。 `$root` コンテナー内のファイルは常にブロック BLOB としてアップロードされます。

    ルート ディレクトリにファイルやフォルダーが存在する場合は、それらを別のフォルダーに移動してからデータのコピーを開始してください。

    コンテナーと BLOB の名前については、Azure の名前付けの要件に従ってください。

    #### <a name="azure-naming-conventions-for-container-and-blob-names"></a>コンテナーと BLOB の名前に関する Azure の名前付け規則
    |エンティティ   |規則  |
    |---------|---------|
    |ブロック BLOB とページ BLOB のコンテナー名     |先頭には文字または数字を使用する必要があります。使用できるのは、小文字、数字、ハイフンのみ (-) のみです。 すべてのハイフン (-) は、その直前または直後に文字または数字が使用されている必要があります。 連続するハイフンを名前に使用することはできません。 <br>3 文字から 63 文字の有効な DNS 名にする必要があります。          |
    |ブロック BLOB とページ BLOB の BLOB 名    |BLOB 名は、大文字と小文字が区別されます。また、名前には任意の組み合わせの文字を使用できます。 <br>BLOB 名は 1 文字から 1,024 文字にする必要があります。<br>URL の予約文字は適切にエスケープしてください。<br>BLOB 名を構成するパスのセグメントの数が 254 個を超えないようにしてください。 パスのセグメントは、仮想ディレクトリの名前に対応した連続する区切り記号文字 (スラッシュ "/" など) の間の文字列です。         |

    > [!IMPORTANT] 
    > すべてのコンテナーと BLOB は、[Azure の名前付け規則](data-box-disk-limits.md#azure-block-blob-and-page-blob-naming-conventions)に準拠している必要があります。 これらの規則に従っていない場合、Azure へのデータのアップロードに失敗します。

3. ファイルをコピーするとき、ファイルが約 4.7 TiB (ブロック BLOB の場合) および約 8 TiB (ページ BLOB の場合) を超えないようにしてください。 
4. エクスプローラーとの間で、ドラッグ アンド ドロップを使ってデータをコピーできます。 Robocopy などの SMB 互換ファイル コピー ツールを使用して、データをコピーすることもできます。 次の Robocopy コマンドを使用すると、複数のコピー ジョブを開始することができます。

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    次の表は、コマンドのパラメーターとオプションを一覧にしたものです。
    
    |パラメーター/オプション  |説明 |
    |--------------------|------------|
    |ソース            | コピー元ディレクトリのパスを指定します。        |
    |変換先       | コピー先ディレクトリのパスを指定します。        |
    |/E                  | サブディレクトリをコピーします (空のディレクトリを含む)。 |
    |/MT[:N]             | スレッド数 N のマルチスレッド コピーを作成します。N は 1 から 128 の整数です。 <br>N の既定値は 8 です。        |
    |/R: <N>             | 失敗したコピーの再試行回数を指定します。 N の既定値は 1,000,000 です (再試行回数 100 万回)。        |
    |/W: <N>             | 再試行間の待ち時間 (秒) を指定します。 N の既定値は 30 です (待ち時間 30 秒)。        |
    |/NFL                | ファイル名をログに記録しないように指定します。        |
    |/NDL                | ディレクトリ名をログに記録しないように指定します。        |
    |/FFT                | FAT ファイル時間 (2 秒の精度) を想定します。        |
    |/Log:<Log File>     | 状態出力をログ ファイルに書き込みます (既存のログ ファイルを上書きします)。         |

    複数のディスクを並列に使用しながら、各ディスクで複数のジョブを実行することができます。 

6. ジョブが進行しているときにコピーの状態をチェックします。 次のサンプルは、Data Box Disk にファイルをコピーする robocopy コマンドの出力を示しています。

    ```
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
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:64 /E /R:1 /W:1 /FFT 
    -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:58 PM
       Source : C:\Git\azure-docs-pr\contributor-guide\
         Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
      Options : *.* /DCOPY:DA /COPY:DAT /MT:8 /R:1000000 /W:30
    
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
       Times :   0:00:05   0:00:00                       0:00:00   0:00:00
        
       Speed :                5620 Bytes/sec.
       Speed :               0.321 MegaBytes/min.
       Ended : Thursday, March 8, 2018 2:34:59 PM
        
    C:\Users>
    ```
 
    
7. コピー先フォルダーを開いて、コピー済みのファイルを表示し、確認します。 コピー処理中にエラーが発生した場合は、トラブルシューティングのためにログ ファイルをダウンロードしてください。 ログ ファイルは、robobopy コマンドで指定した場所に格納されます。
 


> [!IMPORTANT]
> - データはお客様の責任で、適切なデータ形式に対応するフォルダーにコピーする必要があります。 たとえば、ブロック BLOB データは、ブロック BLOB 用のフォルダーにコピーしてください。 データ形式が適切なフォルダー (ストレージの種類) と一致しない場合、後続の手順で、Azure へのデータのアップロードに失敗します。
> -  データのコピー中は、そのサイズが [Azure Storage と Data Box Disk の制限](data-box-disk-limits.md)に関するページに記載されたサイズ制限に準拠していることを確認してください。 
> - Data Box Disk によってアップロードされているデータが、Data Box Disk の外部で別のアプリケーションによって同時にアップロードされた場合、アップロード ジョブ エラーやデータの破損が生じる可能性があります。

### <a name="split-and-copy-data-to-disks"></a>データを分割してディスクにコピーする

このオプションの手順は、複数のディスクを使用しており、大きなデータセットをそれらのすべてのディスクに分割してコピーする必要がある場合に使用できます。 Data Box 分割コピー ツールは、Windows コンピューター上でデータを分割してコピーするのに役立ちます。

1. Windows コンピューターで、Data Box 分割コピー ツールがダウンロードされ、ローカル フォルダーに抽出されていることを確認します。 このツールは、Windows 用の Data Box Disk ツールセットをダウンロードしたときにダウンロードされました。
2. エクスプローラーを開きます。 Data Box Disk に割り当てられたデータ ソース ドライブと ドライブ文字をメモしておきます。 

     ![データの分割コピー ](media/data-box-disk-deploy-copy-data/split-copy-1.png)
 
3. コピーするソース データを特定します。 たとえば、この場合:
    - 以下のブロック BLOB データが特定されました。

         ![データの分割コピー ](media/data-box-disk-deploy-copy-data/split-copy-2.png)    

    - 以下のページ BLOB データが特定されました。

         ![データの分割コピー ](media/data-box-disk-deploy-copy-data/split-copy-3.png)
 
4. ソフトウェアが抽出されるフォルダーに移動します。 そのフォルダー内で SampleConfig.json ファイルを見つけます。 これは、変更して保存できる読み取り専用ファイルです。

   ![データの分割コピー ](media/data-box-disk-deploy-copy-data/split-copy-4.png)
 
5. SampleConfig.json ファイルを変更します。
 
    - ジョブ名を指定します。 これにより、Data Box Disk にフォルダーが作成され、最終的にこれらのディスクに関連付けられた Azure ストレージ アカウント内のコンテナーになります。 ジョブ名は、Azure コンテナーの名前付け規則に従う必要があります。 
    - ソース パスを指定して SampleConfigFile.json のパス形式をメモします。 
    - ターゲット ディスクに対応するドライブ文字を入力します。 データはソース パスから取得され、複数のディスクにコピーされます。
    - ログ ファイルのパスを指定します。 既定では、.exe がある現在のディレクトリに送信されます。

     ![データの分割コピー ](media/data-box-disk-deploy-copy-data/split-copy-5.png)

6. ファイル形式を検証するには、JSONlint に移動します。 ConfigFile.json として保存します。 

     ![データの分割コピー ](media/data-box-disk-deploy-copy-data/split-copy-6.png)
 
7. コマンド プロンプト ウィンドウを開きます。 

8. DataBoxDiskSplitCopy.exe を実行します。 type

    `DataBoxDiskSplitCopy.exe PrepImport /config:<Your-config-file-name.json>`

     ![データの分割コピー ](media/data-box-disk-deploy-copy-data/split-copy-7.png)
 
9. Enter キーを押してスクリプトを続行します。

    ![データの分割コピー ](media/data-box-disk-deploy-copy-data/split-copy-8.png)
  
10. データセットを分割してコピーすると、コピー セッションの分割コピー ツールの概要が表示されます。 サンプル出力を次に示します。

    ![データの分割コピー ](media/data-box-disk-deploy-copy-data/split-copy-9.png)
 
11. データがターゲット ディスク間で分割されていることを確認します。 
 
    ![データの分割コピー](media/data-box-disk-deploy-copy-data/split-copy-10.png)
    ![データの分割コピー](media/data-box-disk-deploy-copy-data/split-copy-11.png)
     
    n: ドライブの内容をさらに調べると、ブロック BLOB およびページ BLOB 形式データに対応して 2 つのサブフォルダーが作成されていることがわかります。
    
     ![データの分割コピー ](media/data-box-disk-deploy-copy-data/split-copy-12.png)

12. コピー セッションが失敗した場合、復旧して再開するには、次のコマンドを使用します。

    `DataBoxDiskSplitCopy.exe PrepImport /config:<configFile.json> /ResumeSession`


データのコピーが完了したら、次にデータを検証します。 


## <a name="validate-data"></a>データの検証 

データを確認するには、次の手順を実行します。

1. ドライブの *AzureImportExport* フォルダーで、チェックサムの検証のために `DataBoxDiskValidation.cmd` を実行します。 
    
    ![Data Box Disk 検証ツールの出力](media/data-box-disk-deploy-copy-data/data-box-disk-validation-tool-output.png)

2. 適切なオプションを選択してください。 **オプション 2 を選択して、常にファイルの検証とチェックサムの生成を行うことをお勧めします**。 データのサイズによっては、この手順にしばらく時間がかかることがあります。 スクリプトが完了したら、コマンド ウィンドウを終了します。 検証とチェックサムの生成の間にエラーが発生した場合は、通知が表示され、エラー ログへのリンクも提供されます。

    ![チェックサムの出力](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

    > [!TIP]
    > - 2 回の実行の間にツールをリセットします。
    > - オプション 1 を使用して、小さなファイル (KB 単位まで) を含む大きなデータセットだけを扱うファイルを検証します。 これらの場合、チェックサムの生成に非常に長い時間がかかり、パフォーマンスが非常に低いことがあります。

3. 複数のディスクを使用している場合は、ディスクごとにコマンドを実行します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Data Box Disk に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * データを Data Box ディスクにコピーする
> * データ整合性の確認

次のチュートリアルに進み、Data Box Disk を返送する方法および Azure へのデータのアップロードを確認する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Data Box を Microsoft に返送する](./data-box-disk-deploy-picked-up.md)

