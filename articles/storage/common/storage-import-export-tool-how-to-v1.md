---
title: Azure Import-Export ツール - v1 の使用 | Microsoft Docs
description: Import-Export ツールを使用してインポート ジョブのハード ドライブを準備したり、インポート ジョブやエクスポート ジョブを修復する方法について説明します。
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 1/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: aaceee65ae91a22dc658d185a874e2040b1e0f04
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "75975666"
---
# <a name="using-the-azure-importexport-tool-classic-deployment-model"></a>Azure Import/Export ツールの使用 (クラシック デプロイ モデル)

Azure Import/Export ツール (WAImportExport.exe) は、Azure Import/Export サービスのジョブを作成、管理するために使用されます。これによりお客様は、Azure Blob Storage との間で大量のデータを転送できるようになります。

このドキュメントは、Azure Import/Export ツールのクラシック デプロイ モデルを対象としています。 同ツールの最新バージョンの使用方法については、「[Azure Import/Export ツールの使用](../storage-import-export-tool-how-to.md)」をご覧ください。

次の記事では、以下の方法について説明します。

- Import/Export ツールをインストールし設定する。
- お客様のドライブから Azure Blob Storage にデータをインポートするジョブのために、ハード ドライブを準備する。
- コピー ログ ファイルを使用して、ジョブの状態を確認する。
- インポート ジョブを修復する。
- エクスポート ジョブを修復する。
- Azure Import/Export ツールのトラブルシューティングを行う (処理中に問題が発生した場合)。

## <a name="next-steps"></a>次のステップ

* [Setting up the WAImportExport tool](../storage-import-export-tool-how-to.md) (WAImportExport ツールの設定)
