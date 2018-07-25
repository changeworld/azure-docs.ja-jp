---
title: Microsoft Azure Data Box Disk にデータをコピーする | Microsoft Docs
description: このチュートリアルでは、Azure Data Box Disk にデータをコピーする方法について説明します。
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
ms.date: 07/12/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: b0769ba70f495728df5c38b43bae4059b27de88b
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010822"
---
# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>チュートリアル: Azure Data Box Disk にデータをコピーして確認する

このチュートリアルでは、ホスト コンピューターからデータをコピーして、データ整合性を確認するためのチェックサムを生成する方法について説明します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * データを Data Box ディスクにコピーする
> * データ整合性の確認

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。
- [Azure Data Box Disk のインストールと構成に関するチュートリアル](data-box-disk-deploy-set-up.md)を完了していること。
- ディスクが開梱され電源が入っていること。
- ディスクにデータをコピーするためのホスト コンピューターがあること。 このホスト コンピューターは次の条件を満たしている必要があります。
    - [サポート対象のオペレーティング システム](data-box-disk-system-requirements.md)が実行されていること。
    - [Windows PowerShell 4 がインストール済み](https://www.microsoft.com/download/details.aspx?id=40855)であること。
    - [.NET Framework 4.5 がインストール済み](https://www.microsoft.com/download/details.aspx?id=30653)であること。


## <a name="copy-data-to-disks"></a>ディスクにデータをコピーする

ご利用のコンピューターから Data Box Disk に接続してデータをコピーするには、次の手順を実行します。

1. ロックを解除したドライブの内容を表示します。 

    ![ドライブの内容を表示する](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. ブロック BLOB としてインポートするデータを BlockBlob フォルダーにコピーします。 同様に、VHD/VHDX などのデータを PageBlob フォルダーにコピーします。 

    Azure Storage アカウントには、BlockBlob フォルダー下および PageBlob フォルダー下のサブフォルダーごとにコンテナーが 1 つ作成されます。 BlockBlob フォルダー下のファイルと PageBlob フォルダー下のファイルはすべて、Azure Storage アカウントの既定のコンテナー `$root` にコピーされます。 `$root` コンテナー内のファイルは常にブロック BLOB としてアップロードされます。

    ルート ディレクトリにファイルやフォルダーが存在する場合は、それらを別のフォルダーに移動してからデータのコピーを開始してください。

    コンテナーと BLOB の名前については、Azure の名前付けの要件に従ってください。

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
    |<Source>            | コピー元ディレクトリのパスを指定します。        |
    |<Destination>       | コピー先ディレクトリのパスを指定します。        |
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

## <a name="verify-data-integrity"></a>データ整合性の確認

データ整合性を確認するには、次の手順を実行します。

1. チェックサムを検証するために、`AzureExpressDiskService.ps1` を実行します。 エクスプローラーで、ドライブの *AzureImportExport* フォルダーに移動します。 右クリックして **[PowerShell で実行]** を選択します。 

    ![チェックサムの実行](media/data-box-disk-deploy-copy-data/data-box-disk-checksum.png)

2. データのサイズによっては、この手順にしばらく時間がかかることがあります。 スクリプトが完了すると、データ整合性チェック処理の概要が、処理の完了までの時間と共に表示されます。 **Enter** を押して、コマンド ウィンドウを終了します。

    ![チェックサムの出力](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

3. 複数のディスクを使用している場合は、ディスクごとにコマンドを実行します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Data Box Disk に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * データを Data Box ディスクにコピーする
> * データ整合性の確認

次のチュートリアルに進み、Data Box Disk を返送する方法および Azure へのデータのアップロードを確認する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Data Box を Microsoft に返送する](./data-box-disk-deploy-picked-up.md)

