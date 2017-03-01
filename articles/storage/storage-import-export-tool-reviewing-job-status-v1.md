---
title: "Azure Import/Export ジョブの状態の確認 | Microsoft Docs"
description: "インポートまたはエクスポート ジョブの実行時に作成されたログ ファイルを使用してインポート/エクスポート ジョブの状態を確認する方法について説明します。"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: c69d1d69-6403-4eee-9949-0185faeecfa1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 8de848b1192ff1c10e0375053c4e03f18c06184e
ms.openlocfilehash: f0d445e51ff91f6edc219522ec3c12a86dc4ee98
ms.lasthandoff: 02/16/2017


---

# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>コピー ログ ファイルによる Azure Import/Export ジョブの状態の確認
Microsoft Azure Import/Export サービスは、インポートまたはエクスポート ジョブに関連付けられたドライブを処理する場合、Blob のインポートまたはエクスポートを行うストレージ アカウントにコピー ログ ファイルを書き込みます。 ログ ファイルには、インポートまたはエクスポートされた各ファイルの状態の詳細が含まれます。 各コピー ログ ファイルへの URL は、完了したジョブの状態をクエリした際に返されます。詳細は「[Get Job](/rest/api/storageservices`Get-Job3)」をご覧ください。  
  
 2 つのドライブを使用したインポート ジョブのコピー ログ ファイルの URL の例は次の通りです。  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath /waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  
  
 コピー ログの形式とステータス コードの一覧は、「[インポート/エクスポート サービスのログ ファイルの形式](storage-import-export-file-format-log.md)」をご覧ください。  
  
## <a name="see-also"></a>関連項目  
 [Azure Import/Export ツールの設定](storage-import-export-tool-setup-v1.md)   
 [インポート ジョブ用のハード ドライブを準備する](storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 [インポート ジョブの修復](storage-import-export-tool-repairing-an-import-job-v1.md)   
 [エクスポート ジョブの修復](storage-import-export-tool-repairing-an-export-job-v1.md)   
 [Azure Import/Export ツールのトラブルシューティング](storage-import-export-tool-troubleshooting-v1.md)

