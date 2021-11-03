---
title: アセット正規化
description: Azure Purview において資産の正規化を通じてデータ マップ内の資産の重複を防ぐ方法について説明します
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 07/23/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 470bfa9fc111002dbb7518833d71f918df265b45
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131010975"
---
# <a name="asset-normalization"></a>アセット正規化

Azure Purview データ マップに資産を取り込む際、同じデータ資産を更新するさまざまなソースから、一部のみが異なる類似している修飾名が送信される場合があります。 これらの修飾名は同じ資産を表しますが、付加された文字や大文字と小文字の違いなどのわずかな違いによって表面上これらの資産が異なって表示される場合があります。 重複するエントリが格納されることでデータ カタログを使用するときに混乱が生じるのを防ぐために、Azure Purview では、インジェスト中に正規化を適用して、同じエンティティ型のすべての完全修飾名が同じ形式になるようにします。

たとえば、修飾名が `https://myaccount.file.core.windows.net/myshare/folderA/folderB/my-file.parquet` の Azure BLOB をスキャンするとします。 この BLOB は、Azure Data Factory パイプラインによっても使用され、資産に系統情報が追加されます。 ADF パイプラインは、ファイルを `https://myAccount.file.core.windows.net//myshare/folderA/folderB/my-file.parquet` として読み取るように構成できます。 修飾名は異なりますが、この ADF パイプラインは同じデータを使用しています。 正規化により、Azure Blob Storage と Azure Data Factory の両方からのすべてのメタデータが単一の資産 `https://myaccount.file.core.windows.net/myshare/folderA/folderB/my-file.parquet` に表示されるようになります。

## <a name="normalization-rules"></a>正規化規則

Azure Purview によって適用される正規化規則を次に示します。

### <a name="encode-curly-brackets"></a>中かっこをエンコードする
適用対象: すべての資産

変更前: `https://myaccount.file.core.windows.net/myshare/{folderA}/folder{B/`

変更後: `https://myaccount.file.core.windows.net/myshare/%7BfolderA%7D/folder%7BB/`

### <a name="trim-section-spaces"></a>セクションの空白を削除する
適用対象: Azure Blob、Azure Files、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure Data Factory、Azure SQL Database、Azure SQL Database Managed Instance、Azure SQL プール、Azure Cosmos DB、Azure Cognitive Search、Azure Data Explorer、Azure Data Share、Amazon S3

変更前: `https://myaccount.file.core.windows.net/myshare/  folder A/folderB   /`

変更後: `https://myaccount.file.core.windows.net/myshare/folder A/folderB/`

### <a name="remove-hostname-spaces"></a>ホスト名の空白を削除する
適用対象: Azure Blob、Azure Files、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL Database、Azure SQL Database Managed Instance、Azure SQL プール、Azure Cosmos DB、Azure Cognitive Search、Azure Data Explorer、Azure Data Share、Amazon S3

変更前: `https://myaccount .file. core.win dows. net/myshare/folderA/folderB/`

変更後: `https://myaccount.file.core.windows.net/myshare/folderA/folderB/`

### <a name="remove-square-brackets"></a>角かっこを削除する 
適用対象: Azure SQL Database、Azure SQL Database Managed Instance、Azure SQL プール

変更前: `mssql://foo.database.windows.net/[bar]/dbo/[foo bar]`

変更後: `mssql://foo.database.windows.net/bar/dbo/foo%20bar`

> [!NOTE]
> 2 つの角かっこの間の空白はエンコードされます

### <a name="lowercase-scheme"></a>スキームを小文字にする
適用対象: Azure Blob、Azure Files、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL Database、Azure SQL Database Managed Instance、Azure SQL プール、Azure Cosmos DB、Azure Cognitive Search、Azure Data Explorer、Amazon S3

変更前: `HTTPS://myaccount.file.core.windows.net/myshare/folderA/folderB/`

変更後: `https://myaccount.file.core.windows.net/myshare/folderA/folderB/`

### <a name="lowercase-hostname"></a>ホスト名を小文字にする
適用対象: Azure Blob、Azure Files、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL Database、Azure SQL Database Managed Instance、Azure SQL プール、Azure Cosmos DB、Azure Cognitive Search、Azure Data Explorer、Amazon S3

変更前: `https://myAccount.file.Core.Windows.net/myshare/folderA/folderB/`

変更後: `https://myaccount.file.core.windows.net/myshare/folderA/folderB/`

### <a name="lowercase-file-extension"></a>ファイル名拡張子を小文字にする
適用対象: Azure Blob、Azure Files、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Amazon S3

変更前: `https://myAccount.file.core.windows.net/myshare/folderA/data.TXT`

変更後: `https://myaccount.file.core.windows.net/myshare/folderA/data.txt`

### <a name="remove-duplicate-slash"></a>重複するスラッシュを削除する
適用対象: Azure Blob、Azure Files、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure Data Factory、Azure SQL Database、Azure SQL Database Managed Instance、Azure SQL プール、Azure Cosmos DB、Azure Cognitive Search、Azure Data Explorer、Azure Data Share、Amazon S3

変更前: `https://myAccount.file.core.windows.net//myshare/folderA////folderB/`

変更後: `https://myaccount.file.core.windows.net/myshare/folderA/folderB/`

### <a name="lowercase-adf-sections"></a>ADF セクションを小文字にする
適用対象: Azure Data Factory

変更前: `/subscriptions/01234567-abcd-9876-0000-ba9876543210/resourceGroups/fooBar/providers/Microsoft.DataFactory/factories/fooFactory/pipelines/barPipeline/activities/barFoo`

変更後: `/subscriptions/01234567-abcd-9876-0000-ba9876543210/resourceGroups/foobar/providers/microsoft.datafactory/factories/foofactory/pipelines/barpipeline/activities/barfoo`

### <a name="convert-to-adl-scheme"></a>ADL スキームに変換する
適用対象: Azure Data Lake Storage Gen1

変更前: `https://mystore.azuredatalakestore.net/folderA/folderB/abc.csv`

変更後: `adl://mystore.azuredatalakestore.net/folderA/folderB/abc.csv`

## <a name="next-steps"></a>次のステップ

Azure Purview データ マップ用に [Azure Blob Storage](register-scan-azure-blob-storage-source.md) アカウントをスキャンします。 
