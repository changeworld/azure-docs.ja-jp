---
title: Azure Data Explorer クラスターの接続障害のトラブルシューティング
description: この記事では、Azure データ エクスプローラーでクラスターに接続する操作のトラブルシューティング手順について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: c71af799f614e9cd28221d79634666cbc3b2c987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "60827038"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>トラブルシューティング: Azure データ エクスプローラーでクラスターに接続できない

Azure データ エクスプローラーでクラスターに接続できない場合は、次の手順に従います。

1. 接続文字列が正しいことを確認します。 形式は `https://<ClusterName>.<Region>.kusto.windows.net` でなければなりません (例: `https://docscluster.westus.kusto.windows.net`)。

1. 適切なアクセス許可を持っていることを確認します。 ない場合は、「*許可されていません*」という応答が返されます。

    アクセス許可について詳しくは、「[データベースのアクセス許可を管理する](manage-database-permissions.md)」をご覧ください。 必要な場合は、クラスター管理者に連絡し、適切な役割を割り当ててもらいます。

1. クラスターが削除されていないことを確認します。サブスクリプションのアクティビティ ログを調べてください。

1. [Azure サービス正常性ダッシュボード](https://azure.microsoft.com/status/)を確認します。 クラスターに接続しようしているリージョンの Azure データ エクスプローラーの状態を探します。

    状態が**正常** (緑色のチェック マーク) でない場合は、状態が改善されてから、クラスターに接続してみてください。

1. 問題解決にさらに手助けが必要な場合は、[Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) でサポート リクエストを作成してください。