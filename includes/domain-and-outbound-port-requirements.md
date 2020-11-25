---
title: インクルード ファイル
description: インクルード ファイル
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: f7b11f813232f593be3598a87b6f1ad7a57054b0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023358"
---
| ドメイン名                  | 送信ポート | 説明                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | セルフホステッド統合ランタイムがインタラクティブな作成を行うために必要です。 |
| `{datafactory}.{region}.datafactory.azure.net`<br> または `*.frontend.clouddatahub.net` | 443            | セルフホステッド統合ランタイムが Data Factory サービスに接続するために必要です。 <br>新しく作成されたデータ ファクトリの場合は、{datafactory}.{region}.datafactory.azure.net という形式のセルフホステッド統合ランタイム キーから FQDN を見つけてください。 古いデータ ファクトリの場合、セルフホステッド統合キーに FQDN が表示されないときは、代わりに *.frontend.clouddatahub.net を使用してください。 |
| `download.microsoft.com`    | 443            | セルフホステッド統合ランタイムが更新プログラムをダウンロードするために必要です。 自動更新を無効にしている場合は、このドメインの構成をスキップできます。 |
| `*.core.windows.net`          | 443            | [ステージング コピー](../articles/data-factory/copy-activity-performance.md#staged-copy)機能を使用する場合に、セルフホステッド統合ランタイムが Azure ストレージ アカウントに接続するために使用します。 |
| `*.database.windows.net`      | 1433           | Azure SQL Database または Azure Synapse Analytics との間でコピーするときにのみ必要です。その他の場合は省略可能です。 ポート 1433 を開かずに SQL Database または Synapse Analytics にデータをコピーするには、ステージング コピー機能を使用します。 |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Azure Data Lake Store との間でコピーするときにのみ必要です。その他の場合は省略可能です。 |