---
title: AzCopy v10 (プレビュー) を使用して Azure Storage にデータをコピーまたは移動する | Microsoft Docs
description: AzCopy v10 (プレビュー) ユーティリティを使用して、BLOB、テーブル、およびファイル コンテンツ間でデータを移動またはコピーします。 ローカル ファイルから Azure ストレージにデータをコピーする、またはストレージ アカウント内またはその間でデータをコピーします。 Azure Storage にデータを簡単に移行します。
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 10/09/2018
ms.author: artemuwka
ms.component: common
ms.openlocfilehash: 2ab933506ea03ae72198113d70888460e5001a6d
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52958424"
---
# <a name="transfer-data-with-the-azcopy-v10-preview"></a>AzCopy v10 (プレビュー) を使用してデータを転送する

AzCopy v10 (プレビュー) は、Microsoft Azure BLOB および File Storage に対して、またはこれらからデータをコピーするための次世代コマンド ライン ユーティリティであり、高性能で信頼性の高いデータ転送のために再設計されたコマンド ライン インターフェイスと新しいアーキテクチャを提供します。 AzCopy を使用すると、ファイル システムとストレージ アカウント間、またはストレージ アカウント間でデータをコピーできます。

## <a name="whats-new-in-azcopy-v10"></a>AzCopy v10 の新機能

- ファイル システムを Azure BLOB に、またはその逆に、同期します。 `azcopy sync <source> <destination>`を使用します。 増分コピーのシナリオに最適です。
- Azure Data Lake Storage Gen2 API をサポートします。 ADLS Gen2 API を呼び出す URI としては `myaccount.dfs.core.windows.net` を使用します。
- 別のアカウントへのアカウント全体のコピーをサポートします (Blob service のみ)。
- アカウント間のコピーには、新しい [Put from URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API を使用します。 クライアントへのデータ転送は必要なく、転送が高速化されます。
- 特定のパスのファイルおよび BLOB を一覧表示/削除します。
- パスのワイルドカード パターンおよび --include フラグと --exclude フラグをサポートします。
- 回復性の向上: すべての AzCopy インスタンスでジョブの順序および関連するログ ファイルが作成されます。 以前のジョブを表示および再起動し、失敗したジョブを再開できます。 AzCopy は、障害後の転送の自動再試行も行います。
- 全般的なパフォーマンスの向上。

## <a name="download-and-install-azcopy"></a>AzCopy のダウンロードとインストール

### <a name="latest-preview-version-v10"></a>最新のプレビュー バージョン (v10)

AzCopy の最新のプレビュー バージョンは以下からダウンロードできます。
- [Windows](https://aka.ms/downloadazcopy-v10-windows)
- [Linux](https://aka.ms/downloadazcopy-v10-linux)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac)

### <a name="latest-production-version-v81"></a>最新の製品バージョン (v8.1)

[AzCopy for Windows の最新の製品バージョン](https://aka.ms/downloadazcopy)をダウンロードできます。

### <a name="azcopy-supporting-table-storage-service-v73"></a>Table Storage サービスをサポートする AzCopy (v7.3)

[Microsoft Azure Table Storage サービスとの間でのデータのコピーをサポートする AzCopy v7.3](https://aka.ms/downloadazcopynet) をダウンロードできます。

## <a name="post-installation-steps"></a>インストール後の手順

AzCopy v10 では、インストールは必要ありません。 任意のコマンド ライン アプリケーションを開き、`azcopy.exe` 実行可能ファイルが配置されているフォルダーに移動します。 必要に応じて、AzCopy のフォルダーの場所をシステム パスに追加できます。

## <a name="authentication-options"></a>認証オプション

AzCopy v10 では、Azure Storage での認証時に、次のオプションを使用することができます。
- Azure Active Directory。 Azure Active Directory を使用し、```.\azcopy login``` を使ってサインインします。  Azure Active Directory 認証を使用して Blob Storage に書き込むには、ユーザーに ["ストレージ BLOB データ共同作成者" ロールが割り当てられている](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)必要があります。
- BLOB のパスに追加する必要がある SAS トークン。 Azure Portal、[Storage Explorer](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)、[PowerShell](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestorageblobsastoken?view=azurermps-6.9.0)、またはその他の好みのツールを使用して、SAS トークンを生成できます。 詳しくは、[例](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)をご覧ください。

## <a name="getting-started"></a>使用の開始

AzCopy v10 は、単純な自己文書化された構文を備えています。 一般的な構文は次のとおりです。

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>
# Example:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/containersastoken" --recursive=true
```

使用可能なコマンドの一覧を取得する方法を次に示します。

```azcopy
.\azcopy -help
# Using the alias instead
.\azcopy -h
```

特定のコマンドのヘルプ ページと例を表示するには、次のコマンドを実行します。

```azcopy
.\azcopy <cmd> -help
# Example:
.\azcopy cp -h
```

## <a name="create-a-file-system-azure-data-lake-storage-gen2-only"></a>ファイル システムを作成する (Azure Data Lake Storage Gen2 のみ)

BLOB ストレージ アカウントで階層型名前空間を有効にしている場合、次のコマンドを使用し、新しいファイル システムを作成し、それにダウンロード ファイルをアップロードできます。

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name" --recursive=true
```

この文字列の ``account`` 部分は、ストレージ アカウントの名前です。 この文字列の ``top-level-resource-name`` 部分は、作成するファイル システムの名前です。

## <a name="copy-data-to-azure-storage"></a>Azure Storage にデータをコピーする

コピー元からコピー先にデータを転送するには、copy コマンドを使用します。 コピー元/コピー先には以下を使用できます。
- ローカル ファイル システム
- Azure BLOB/仮想ディレクトリ/コンテナー URI
- Azure File/ディレクトリ/ファイル共有 URI
- Azure Data Lake Storage Gen2 ファイル システム/ディレクトリ/ファイル URI

> [!NOTE]
> 現時点では、AzCopy v10 は 2 つのストレージ アカウント間のブロック BLOB のみのコピーをサポートしています。

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using alias instead
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

次のコマンドは、C:\local\path フォルダーの下にあるすべてのファイルを再帰的にコンテナー "mycontainer1" にアップロードします。

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

BLOB ストレージ アカウントで階層型名前空間を有効にしている場合、次のコマンドを使用し、ファイルをファイル システムにアップロードできます。

```azcopy
.\azcopy cp "C:\local\path" "https://myaccount.dfs.core.windows.net/myfolder<sastoken>" --recursive=true
```

次のコマンドは、C:\local\path フォルダーにあるすべてのファイルを (サブディレクトリに再帰しないで) コンテナー "mycontainer1" にアップロードします。

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>"
```

BLOB ストレージ アカウントで階層型名前空間を有効にしている場合、次のコマンドを使用できます。

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/myfolder<sastoken>"
```

他の例を見るには、次のコマンドを使用します。

```azcopy
.\azcopy cp -h
```

## <a name="copy-data-between-two-storage-accounts"></a>2 つのストレージ アカウント間でデータをコピーする

2 つのストレージ アカウント間でデータをコピーするには、[Put Block From URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API を使用し、クライアント マシンのネットワーク帯域幅は使用しません。 データは 2 つの Azure Storage サーバー間で直接コピーされ、コピー操作は AzCopy によって調整されます。 

2 つのストレージ アカウント間でデータをコピーするには、次のコマンドを使用します。
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

階層型名前空間を有効にしている BLOB ストレージ アカウントを使用するには、上のサンプルの文字列 ``blob.core.windows.net`` を ``dfs.core.windows.net`` に変更します。

> [!NOTE]
> このコマンドは、すべての BLOB コンテナーを列挙し、コピー先のアカウントにコピーします。 現時点では、AzCopy v10 は 2 つのストレージ アカウント間のブロック BLOB のみのコピーをサポートしています。 他のすべてのストレージ アカウント オブジェクト(追加 BLOB、ページ BLOB、ファイル、テーブル、キュー) はスキップされます。

## <a name="copy-a-vhd-image-to-a-storage-account"></a>VHD イメージをストレージ アカウントにコピーする

既定では、AzCopy v10 はデータをブロック BLOB にアップロードします。 ただし、コピー元ファイルの拡張子が vhd の場合は、AzCopy v10 は既定でページ BLOB にアップロードします。 この動作は構成できません。

## <a name="sync-incremental-copy-and-delete"></a>同期: 増分コピーと削除

> [!NOTE]
> sync コマンドでは同期元から同期先にコンテンツが同期され、これには同期元に存在しないファイルの同期先での削除が含まれます。 目的の同期先であることを確認してください。

ストレージ アカウントにローカル ファイル システムを同期するには、次のコマンドを使用します。

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

同じ方法で、ローカル ファイル システムに BLOB コンテナーを同期できます。

```azcopy
# If you're using Azure Active Directory authentication the sastoken is not required
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

このコマンドでは、最終変更タイムスタンプに基づいて、同期元を同期先に増分的に同期できます。 同期元でファイルを追加または削除すると、AzCopy v10 は同期先で同じことを行います。

[!NOTE] 階層型名前空間を有効にしている BLOB ストレージ アカウントを使用するには、上のサンプルの文字列 ``blob.core.windows.net`` を ``dfs.core.windows.net`` に変更します。

## <a name="advanced-configuration"></a>詳細な構成

### <a name="configure-proxy-settings"></a>プロキシ設定の構成

AzCopy v10 のプロキシ設定を構成するには、次のコマンドを使用して環境変数 https_proxy を設定します。

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>スループットを最適化する

同時要求の数を構成し、スループットのパフォーマンスとリソースの消費量を制御するには、AZCOPY_CONCURRENCY_VALUE 環境変数を設定します。 既定では、値は 300 に設定されます。 値を小さくすると、AzCopy v10 で使用される CPU と帯域幅が制限されます。

```cmd
# For Windows:
set AZCOPY_CONCURRENCY_VALUE=<value>
# For Linux:
export AZCOPY_CONCURRENCY_VALUE=<value>
# For MacOS
export AZCOPY_CONCURRENCY_VALUE=<value>
```

## <a name="troubleshooting"></a>トラブルシューティング

AzCopy v10 では、すべてのジョブに対してログ ファイルとプラン ファイルが作成されます。 ログを使用することで、潜在的な問題を調査してトラブルシューティングできます。 ログには、エラーの状態 (UPLOADFAILED、COPYFAILED、DOWNLOADFAILED)、完全なパス、エラーの理由が含まれます。 ジョブのログとプラン ファイルは、%USERPROFILE\\.azcopy フォルダーにあります。

### <a name="review-the-logs-for-errors"></a>ログでエラーを確認する

次のコマンドでは、04dc9ca9-158f-7945-5933-564021086c79 ログから状態が UPLOADFAILED であるすべてのエラーが取得されます。

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```

### <a name="view-and-resume-jobs"></a>ジョブを表示および再開する

各転送操作で AzCopy ジョブが作成されます。 次のコマンドを使用して、ジョブの履歴を表示することができます。

```azcopy
.\azcopy jobs list
```

ジョブの統計情報を表示するには、次のコマンドを使います。

```azcopy
.\azcopy jobs show <job-id>
```

状態で転送をフィルター処理するには、次のコマンドを使います。

```azcopy
.\azcopy jobs show <job-id> --with-status=Failed
```

失敗した/取り消されたジョブを、その識別子と SAS トークン (セキュリティ上の理由で永続化されません) を使用して再開できます。

```azcopy
.\azcopy jobs resume <jobid> --sourcesastokenhere --destinationsastokenhere
```

## <a name="next-steps"></a>次の手順

ご意見をお待ちしております。 質問、問題、一般的なフィードバックは、 https://github.com/Azure/azure-storage-azcopy でお送りください。 よろしくお願いいたします。