---
title: Azure データ エクスプローラーでクラスターに接続できない
description: この記事では、Azure データ エクスプローラーでクラスターに接続する操作のトラブルシューティング手順について説明します。
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f66dcc55b01b407c59c65ea300757ab4ee1002ac
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965060"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>トラブルシューティング: Azure データ エクスプローラーでクラスターに接続できない

Azure データ エクスプローラーでクラスターに接続できない場合は、次の手順に従います。

1. 接続文字列が正しいことを確認します。 形式は `https://<ClusterName>.<Region>.kusto.windows.net` でなければなりません (例: `https://docscluster.westus.kusto.windows.net`)。

1. 適切なアクセス許可があることを確認します。 ない場合は、「*許可されていません*」という応答が返されます。

    アクセス許可の詳細については、[データベースのアクセス許可の管理](manage-database-permissions.md)を参照してください。 必要な場合は、クラスター管理者に連絡し、適切な役割を割り当ててもらいます。

1. クラスターが削除されていないことを確認します。サブスクリプションのアクティビティ ログを調べてください。

1. [Azure サービス正常性ダッシュボード](https://azure.microsoft.com/status/>)を確認します。 クラスターに接続しようしているリージョンの Azure データ エクスプローラーの状態を探します。

    状態が**正常** (緑色のチェック マーク) でない場合は、状態が改善されてから、クラスターに接続してみてください。

1. 問題を解決するために手助けが必要な場合は、[Azure ポータル](https://portal.azure.com) でサポート リクエストを提出してください。