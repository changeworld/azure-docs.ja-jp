---
title: インクルード ファイル
description: インクルード ファイル
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 0b2831321c9f4d855a19605c1ce5ace9fa11c88b
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89596118"
---
| ドメイン名                  | 送信ポート | 説明                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | セルフホステッド統合ランタイムが Azure Data Factory のデータ移動サービスに接続するために必要です。 |
| `{datafactory}.{region}.datafactory.azure.net`<br> または `*.frontend.clouddatahub.net` | 443            | セルフホステッド統合ランタイムが Data Factory サービスに接続するために必要です。 <br>新しく作成されたデータ ファクトリの場合は、{datafactory}.{region}.datafactory.azure.net という形式のセルフホステッド統合ランタイム キーから FQDN を見つけてください。 古いデータ ファクトリの場合、セルフホステッド統合キーに FQDN が表示されないときは、代わりに *.frontend.clouddatahub.net を使用してください。 |
| `download.microsoft.com`    | 443            | セルフホステッド統合ランタイムが更新プログラムをダウンロードするために必要です。 自動更新を無効にしている場合は、このドメインの構成をスキップできます。 |
| `*.core.windows.net`          | 443            | [ステージング コピー](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)機能を使用する場合に、セルフホステッド統合ランタイムが Azure ストレージ アカウントに接続するために使用します。 |
| `*.database.windows.net`      | 1433           | Azure SQL Database または Azure Synapse Analytics との間でコピーするときにのみ必要です。その他の場合は省略可能です。 ポート 1433 を開かずに SQL Database または Synapse Analytics にデータをコピーするには、ステージング コピー機能を使用します。 |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Azure Data Lake Store との間でコピーするときにのみ必要です。その他の場合は省略可能です。 |
