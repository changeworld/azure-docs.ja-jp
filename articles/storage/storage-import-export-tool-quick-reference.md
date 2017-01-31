---
title: "Azure Import/Export ツールのインポート ジョブのコマンドのクイック リファレンス | Microsoft Docs"
description: "インポート ジョブで頻繁に使用される Azure Import/Export ツールのコマンドのコマンド リファレンスです"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 41bc5585f3d9dea2a08dc8a6bc1e4fdf9f0c8fc4
ms.openlocfilehash: 79b1d3f92671638ba43bac2f5428a1f903d11080


---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>インポート ジョブで頻繁に使用するコマンドのクイック リファレンス

この記事では、頻繁に使用するいくつかのコマンドのクイック リファレンスを提供します。 詳細な使用方法については、「[インポート ジョブ用のハード ドライブを準備する](storage-import-export-tool-preparing-hard-drives-import.md)」を参照してください。

## <a name="import-job-quick-reference"></a>インポート ジョブのクイック リファレンス

最初のセッション:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

2 番目のセッション:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /DataSet:dataset-2.csv
```

最新のセッションの中止:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /AbortSession
```

最新の中断されたセッションの再開:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /ResumeSession
```

最新のセッションへのドライブの追加:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /AdditionalDriveSet:driveset-2.csv
```

## <a name="next-steps"></a>次のステップ

[インポート ジョブ用のハード ドライブを準備するためのサンプル ワークフロー](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)



<!--HONumber=Dec16_HO3-->


