---
title: AzCopy を使用したオンプレミス データの Azure Storage への移行 | Microsoft Docs
description: AzCopy を使用して、データを移行したり、BLOB、テーブル、およびファイルのコンテンツとの間でデータをコピーしたりできます。 ローカル ストレージから Azure Storage にデータを簡単に移行できます。
services: storage
author: roygara
ms.service: storage
ms.devlang: azcopy
ms.topic: tutorial
ms.date: 12/14/2017
ms.author: rogarana
ms.component: common
ms.openlocfilehash: f5b678f9936a6d405c06ff085a802fcd76328ebe
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525656"
---
#  <a name="migrate-on-premises-data-to-cloud-storage-by-using-azcopy"></a>AzCopy を使用したオンプレミス データのクラウド ストレージへの移行

AzCopy は、単純なコマンドを使用して Azure Blob Storage、Azure Files、および Azure Table Storage との間でデータをコピーするためのコマンドライン ツールです。 コマンドは、最適なパフォーマンスが得られるように設計されています。 ファイル システムとストレージ アカウント間、またはストレージ アカウント間でデータをコピーできます。  

ダウンロードできる AzCopy には、2 つのバージョンがあります。

* [AzCopy on Linux](storage-use-azcopy-linux.md) は、.NET Core Framework で構築されています。 その対象プラットフォームは Linux で、POSIX スタイルのコマンドライン オプションが用意されています。 
* [AzCopy on Windows](storage-use-azcopy.md) は、.NET Framework で構築されています。 このバージョンには、Windows スタイルのコマンドライン オプションが用意されています。 
 
このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * ストレージ アカウントを作成します。 
> * AzCopy を使用してすべてのデータをアップロードします。
> * テスト目的でデータを変更します。
> * アップロードする新しいファイルを特定するための、スケジュールされたタスクまたは cron ジョブを作成します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、AzCopy on [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#download-and-install-azcopy) または [Windows](http://aka.ms/downloadazcopy) の最新バージョンをダウンロードしてください。 

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

>[!NOTE]
>セカンダリ リージョンからローカル ストレージに、またはその逆方向に BLOB をダウンロードする場合は、**[レプリケーション]** を **[読み取りアクセス geo 冗長ストレージ]** に設定します。 このオプションを選択すると、[geo 冗長ストレージ](https://docs.microsoft.com/azure/storage/common/storage-redundancy#geo-redundant-storage) アカウントが作成されます。 
>
>

## <a name="create-a-container"></a>コンテナーを作成する

BLOB は常にコンテナーにアップロードされます。 コンテナーを使用すると、コンピューター上のファイルをフォルダーに整理するかのように、BLOB のグループを整理できます。 

コンテナーを作成するには、次の手順に従います。

1. メイン ページで **[ストレージ アカウント]** ボタンを選択し、作成したストレージ アカウントを選択します。
2. **[サービス]** で **[BLOB]** を選択し、**[コンテナー]** を選択します。 

   ![コンテナーを作成する](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
コンテナー名は文字または数字で始まる必要があります。 コンテナー名には、文字、数字、およびハイフン文字 (-) のみを使用できます。 BLOB とコンテナーの名前付けの詳細については、「[Naming and referencing containers, blobs, and metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」(コンテナー、BLOB、メタデータの名前付けと参照) を参照してください。

## <a name="upload-all-files-in-a-folder-to-blob-storage"></a>フォルダー内のすべてのファイルを Blob Storage にアップロードする

AzCopy を使用すると、フォルダー内のすべてのファイルを [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage) または [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#blob-download) 上の Blob Storage にアップロードできます。 フォルダー内のすべての BLOB をアップロードするには、次の AzCopy コマンドを入力します。

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
        --source /mnt/myfolder \
        --destination https://myaccount.blob.core.windows.net/mycontainer \
        --dest-key <key> \
        --recursive

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:<key> /S
---

`<key>` と `key` をアカウント キーで置き換えてください。 Azure Portal でアカウント キーを取得するには、ストレージ アカウントの **[設定]** の **[アクセス キー]** を選択します。 キーを選択し、AzCopy コマンドに貼り付けます。 存在しない宛先コンテナーを指定すると、AzCopy によってコンテナーが作成され、そのコンテナーにファイルがアップロードされます。 データ ディレクトリへのソース パスを更新し、宛先 URL の **myaccount** をストレージ アカウント名で置き換えます。

指定したディレクトリの内容を Blob Storage に再帰的にアップロードするには、`--recursive` (Linux) または `/S` (Windows) オプションを指定します。 これらのオプションのいずれかを使用して AzCopy を実行すると、すべてのサブフォルダーとサブフォルダー内のファイルもアップロードされます。

## <a name="upload-modified-files-to-blob-storage"></a>変更されたファイルを Blob Storage にアップロードする
AzCopy を使用して、最終更新時刻に基づいて[ファイルをアップロード](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#other-azcopy-features)することができます。 これを試すには、テストのためにソース ディレクトリ内のファイルを変更するか、または新しいファイルを作成します。 更新されたファイルまたは新しいファイルのみをアップロードするには、`--exclude-older` (Linux) または `/XO` (Windows) パラメーターを AzCopy コマンドに追加します。

宛先に存在しないソース リソースのみをコピーする場合は、AzCopy コマンドに `--exclude-older` と `--exclude-newer` (Linux) または `/XO` と `/XN` (Windows) の両方のパラメーターを指定します。 AzCopy は、タイムスタンプに基づいて、更新されたデータのみをアップロードします。
 
# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
    --source /mnt/myfolder \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive \
    --exclude-older

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:<key> /S /XO
---

## <a name="create-a-scheduled-task-or-cron-job"></a>スケジュールされたタスクまたは cron ジョブを作成する 
AzCopy コマンド スクリプトを実行するスケジュールされたタスクまたは cron ジョブを作成できます。 このスクリプトは、特定の時間間隔で新しいオンプレミス データを識別してクラウド ストレージにアップロードします。 

AzCopy コマンドをテキスト エディターにコピーします。 AzCopy コマンドのパラメーター値を適切な値に更新します。 ファイルを AzCopy 用に `script.sh` (Linux) または `script.bat` (Windows) として保存します。 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy --source /mnt/myfiles --destination https://myaccount.blob.core.windows.net/mycontainer --dest-key <key> --recursive --exclude-older --exclude-newer --verbose >> Path/to/logfolder/`date +\%Y\%m\%d\%H\%M\%S`-cron.log

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    cd C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy
    AzCopy /Source: C:\myfolder  /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:<key> /V /XO /XN >C:\Path\to\logfolder\azcopy%date:~-4,4%%date:~-7,2%%date:~-10,2%%time:~-11,2%%time:~-8,2%%time:~-5,2%.log
---

AzCopy は、詳細オプション `--verbose` (Linux) または `/V` (Windows) で実行されます。 出力はログ ファイルにリダイレクトされます。 

このチュートリアルでは、Windows 上でスケジュールされたタスクを作成するために [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) を使用します。 Linux 上で cron ジョブを作成するには、[Crontab](http://crontab.org/) コマンドを使用します。 
 **Schtasks** は、管理者がローカルまたはリモート コンピューター上でスケジュールされたタスクを作成、削除、クエリ、変更、実行、および終了することを可能にします。 **Cron** は、Linux および Unix ユーザーが [cron 式](https://en.wikipedia.org/wiki/Cron#CRON_expression)を使用して、指定された日時にコマンドまたはスクリプトを実行することを可能にします。


# <a name="linuxtablinux"></a>[Linux](#tab/linux)
Linux 上で cron ジョブを作成するには、端末で次のコマンドを入力します。 

```bash
crontab -e 
*/5 * * * * sh /path/to/script.sh 
```

コマンドの cron 式 `*/5 * * * * ` は、シェル スクリプト `script.sh` を 5 分ごとに実行することを指定します。 スクリプトは、毎日、毎月、または毎年特定の時刻に実行するようにスケジュールすることができます。 ジョブの実行日時の設定の詳細については、[cron 式](https://en.wikipedia.org/wiki/Cron#CRON_expression)に関するページを参照してください。 

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
Windows 上でスケジュールされたタスクを作成するには、コマンド プロンプトまたは PowerShell で次のコマンドを入力します。

```cmd 
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\Users\username\Documents\script.bat
```

このコマンドでは、次のパラメーターを使用しています。
- `/SC` パラメーター: 分単位でスケジュールを指定します。
- `/MO` パラメーター: 5 分の間隔を指定します。
- `/TN` パラメーター: タスク名を指定します。
- `/TR` パラメーター: `script.bat` ファイルへのパスを指定します。 

Windows 上でスケジュールされたタスクを作成する方法の詳細については、「[Schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes)」を参照してください。

---
 
スケジュールされたタスクまたは cron ジョブが正常に実行されることを確認するために、`myfolder` ディレクトリ内に新しいファイルを作成します。 新しいファイルがストレージ アカウントにアップロードされたことを確認するために、5 分間待ちます。 ログ ディレクトリに移動して、スケジュールされたタスクまたは cron ジョブの出力ログを確認します。 

オンプレミスと Azure Storage との間でデータを移動する方法については、「[Azure Storage との間でのデータの移動](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」を参照してください。  

## <a name="next-steps"></a>次の手順
Azure Storage および AzCopy の詳細については、以下のリソースを参照してください。

* [Azure ストレージの概要](../storage-introduction.md)
* [AzCopy on Windows を使ったデータの転送](storage-use-azcopy.md) 
* [AzCopy on Linux を使ったデータの転送](storage-use-azcopy-linux.md) 
* [ストレージ アカウントの作成](../storage-create-storage-account.md)
* [ストレージ エクスプローラーを使用した BLOB の管理](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)  






 
 

