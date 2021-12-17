---
title: AzCopy (Azure Storage) でログを使用してエラーを検出し、ジョブを再開する | Microsoft Docs
description: ログを使用してエラーを診断し、プラン ファイルを使用して一時停止されているジョブを再開する方法について説明します。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 8fc9934c5d524550929c3400af9f257c4cbcccc8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665912"
---
# <a name="find-errors-and-resume-jobs-by-using-log-and-plan-files-in-azcopy"></a>AzCopy でログとプラン ファイルを使用してエラーの検出とジョブの再開を行う

AzCopy は、ストレージ アカウント間の BLOB またはファイル コピーに利用できるコマンドライン ユーティリティです。 この記事は、ログを使用してエラーを診断し、プラン ファイルを使用してジョブを再開するのに役立ちます。 また、この記事では、ログとプラン ファイルの詳細レベルと既定の格納場所を変更してそれらを構成する方法についても説明します。

> [!NOTE]
> AzCopy の使用の開始に役立つコンテンツを探している場合は、「[AzCopy を使ってみる](storage-use-azcopy-v10.md)」を参照してください。 この記事は、AzCopy のバージョンのうち、現在サポートしている **V10** を対象としています。 古いバージョンの AzCopy を使用する必要がある場合は、[古いバージョンの AzCopy の使用](storage-use-azcopy-v10.md#previous-version)に関する記事をご覧ください。

## <a name="log-and-plan-files"></a>ログとプラン ファイル

AzCopy では、ジョブごとに "*ログ*" と "*プラン*" ファイルが作成されます。 これらのログを使用することで、潜在的な問題を調査してトラブルシューティングできます。

ログには、エラーの状態 (`UPLOADFAILED`、`COPYFAILED`、および `DOWNLOADFAILED`)、完全なパス、エラーの理由が含まれます。

既定では、ログとプラン ファイルは、Windows では `%USERPROFILE%\.azcopy` ディレクトリに、Mac および Linux では `$HOME$\.azcopy` ディレクトリにありますが、その場所は変更できます。

関連するエラーは、必ずしもファイルに表示される最初のエラーではありません。 ネットワーク エラー、タイムアウト、サーバー ビジー エラーなどのエラーの場合、AzCopy により最大 20 回まで再試行され、通常は再試行プロセスが成功します。  最初に表示されるエラーは、正常に再試行された無害なものである可能性があります。  そのため、ファイルの最初のエラーを確認するのではなく、`UPLOADFAILED`、`COPYFAILED`、または `DOWNLOADFAILED` の近くにあるエラーを探します。

> [!IMPORTANT]
> Microsoft サポートに要求を送信するとき (または、サード パーティが関わる問題のトラブルシューティングを行うとき) は、実行したいコマンドの修正済みバージョンを共有します。 これにより、SAS が誤って誰かと共有されることがなくなります。 修正済みバージョンは、ログ ファイルの先頭にあります。

## <a name="review-the-logs-for-errors"></a>ログでエラーを確認する

次のコマンドでは、`04dc9ca9-158f-7945-5933-564021086c79` ログから状態が `UPLOADFAILED` であるすべてのエラーが取得されます。

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

## <a name="view-and-resume-jobs"></a>ジョブを表示および再開する

各転送操作で AzCopy ジョブが作成されます。 ジョブの履歴を表示するには、次のコマンドを使用します。

```
azcopy jobs list
```

ジョブの統計情報を表示するには、次のコマンドを使います。

```
azcopy jobs show <job-id>
```

状態で転送をフィルター処理するには、次のコマンドを使います。

```
azcopy jobs show <job-id> --with-status=Failed
```

失敗したジョブまたは取り消されたジョブを再開するには、次のコマンドを使用します。 このコマンドでは、SAS トークンと共に識別子を使用します。SAS トークンは、セキュリティ上の理由で、永続的ではないためです。

```
azcopy jobs resume <job-id> --source-sas="<sas-token>" --destination-sas="<sas-token>"
```

> [!TIP]
> SAS トークンなどのパス引数は単一引用符 ('') で囲みます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

ジョブを再開すると、AzCopy がジョブ プラン ファイルを確認します。 プラン ファイルには、ジョブが最初に作成されたときに処理対象として識別されたすべてのファイルの一覧が表示されます。 ジョブを再開すると、AzCopy は、プラン ファイルの一覧に表示されているファイルのうち、まだ転送されていないファイルをすべて転送しようとします。

## <a name="change-the-location-of-plan-files"></a>プラン ファイルの場所を変更する

これらのコマンドのいずれかを使用します。

| オペレーティング システム | コマンド  |
|--------|-----------|
| **Windows** | PowerShell:`$env:AZCOPY_JOB_PLAN_LOCATION="<value>"` <br> コマンド プロンプトでは次を使用します: `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **macOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

この変数の現在の値を確認するには、`azcopy env` を使用します。 値が空白の場合、プラン ファイルは既定の場所に書き込まれます。

## <a name="change-the-location-of-log-files"></a>ログ ファイルの場所を変更する

これらのコマンドのいずれかを使用します。

| オペレーティング システム | コマンド  |
|--------|-----------|
| **Windows** | PowerShell:`$env:AZCOPY_LOG_LOCATION="<value>"` <br> コマンド プロンプトでは次を使用します: `set AZCOPY_LOG_LOCATION=<value>`|
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **macOS** | `export AZCOPY_LOG_LOCATION=<value>` |

この変数の現在の値を確認するには、`azcopy env` を使用します。 値が空白の場合、ログは既定の場所に書き込まれます。

## <a name="change-the-default-log-level"></a>既定のログ レベルを変更する

既定では、AzCopy ログ レベルは `INFO` に設定されます。 ディスク領域を節約するためにログ詳細度を下げたい場合は、``--log-level`` オプションを使用してこの設定を上書きます。

使用可能なログ レベルは、`NONE`、`DEBUG`、`INFO`、`WARNING`、`ERROR`、`PANIC`、および `FATAL` です。

## <a name="remove-plan-and-log-files"></a>プラン ファイルとログ ファイルを削除する

ディスク領域を節約するために、すべてのプラン ファイルとログ ファイルをローカル コンピューターから削除する場合は、`azcopy jobs clean` コマンドを使用します。

1 つのジョブのみに関連付けられているプラン ファイルとログ ファイルを削除するには、`azcopy jobs rm <job-id>` を使用します。 この例の `<job-id>` プレースホルダーは、ジョブのジョブ ID に置き換えてください。

## <a name="see-also"></a>関連項目

- [AzCopy を使ってみる](storage-use-azcopy-v10.md)
