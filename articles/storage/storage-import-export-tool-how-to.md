---
title: "Azure Import-Export ツール - v1 の使用 | Microsoft Docs"
description: "Import-Export ツールを使用してインポート ジョブのハード ドライブを準備したり、インポート ジョブやエクスポート ジョブを修復する方法について説明します。"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f77535bb-d577-438a-bdd3-e15a82e0c543
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: ca2d4bf2b1e1cb56910fa3ee7af6e45b6b77b450
ms.openlocfilehash: 7c628022a6de01de50f50256bd2ea0cdba2c2555


---

# <a name="using-the-azure-importexport-tool"></a>Azure Import/Export ツールの使用 

Azure Import/Export ツール (WAImportExport.exe) は、Azure Import/Export サービスのジョブを作成および管理するために使用されます。これによりお客様は、Azure Blob Storage との間で大量のデータを転送できるようになります。

このドキュメントは、Azure Import/Export ツールの最新バージョンを対象としています。 v1 ツールの使用方法については、「[Azure Import/Export ツール (v1) の使用](storage-import-export-tool-how-to-v1.md)」をご覧ください。

これらの記事では、このツールを使用して次のことを行う方法について説明します。  

- Import/Export ツールをインストールし設定する。
- お客様のドライブから Azure Blob Storage にデータをインポートするジョブのために、ハード ドライブを準備する。
- コピー ログ ファイルを使用して、ジョブの状態を確認する。 
- インポート ジョブを修復する。 
- エクスポート ジョブを修復する。 
- Azure Import/Export ツールのトラブルシューティングを行う (処理中に問題が発生した場合)。 



<!--HONumber=Dec16_HO3-->


