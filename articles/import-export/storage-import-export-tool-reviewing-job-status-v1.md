---
title: Azure Import/Export でのインポートとエクスポートからコピー ログを確認する | Microsoft Docs
description: データのコピーとアップロードの問題について、インポートとエクスポートからエラーやコピー ログを確認する方法について説明します。
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 10/01/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 03f4cf19922f808decfd84fe0538930dee807b5d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129709240"
---
# <a name="review-copy-logs-from-imports-and-exports-via-azure-importexport"></a>Azure Import/Export によるインポートとエクスポートからコピー ログを確認する
Microsoft Azure Import/Export サービスは、インポート ジョブまたはエクスポート ジョブに関連付けられたドライブを処理する場合に、BLOB のインポートまたはエクスポートに使用されたストレージ アカウントにコピー ログ ファイルを書き込みます。 

ログ ファイルには、インポートまたはエクスポートされた各ファイルの状態の詳細が含まれます。 

完了したジョブの状態を問い合わせると、サービスによって各コピー ログ ファイルの URL が返されます。 詳細については、[ジョブの取得](/rest/api/storageimportexport/Jobs/Get)に関するページを参照してください。  

## <a name="example-urls"></a>URL の例

2 つのドライブを使用したインポート ジョブのコピー ログ ファイルの URL の例は次の通りです。  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 <!--See [Import/Export service Log File Format](/previous-versions/azure/storage/common/storage-import-export-file-format-log) for the format of copy logs and the full list of status codes. ARCHIVED-->  

## <a name="next-steps"></a>次の手順

<!--* [Setting Up the Azure Import/Export Tool](storage-import-export-tool-setup-v1.md) ARCHIVED-->
 * [インポート ジョブ用のハード ドライブを準備する](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)   
<!--* [Repairing an import job](./storage-import-export-tool-repairing-an-import-job-v1.md)-->  
<!--* [Repairing an export job](./storage-import-export-tool-repairing-an-export-job-v1.md)-->