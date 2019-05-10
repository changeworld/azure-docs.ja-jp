---
title: Azure Cosmos DB 用 Azure PowerShell サンプル
description: Azure PowerShell サンプル - Azure Cosmos DB アカウントの作成と管理で役立つスクリプト。
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/08/2019
ms.author: mjbrown
ms.openlocfilehash: 68e845a05f4ebe2d1f25b55c00042c8925c8109e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65069295"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Azure Cosmos DB 用 Azure PowerShell サンプル

次の表には、Azure Cosmos DB for Core (SQL) API の Azure PowerShell スクリプトのサンプルへのリンクが含まれています。

| |  |
|---|---|
|**Azure Cosmos アカウント**||
|[アカウントの作成](scripts/powershell/sql/ps-account-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos SQL API アカウントを作成します。 |
|[アカウントの取得](scripts/powershell/sql/ps-account-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウントのプロパティを取得します。 |
|[リージョンの追加](scripts/powershell/sql/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウントを取得し、場所の一覧にリージョンを追加します。 |
|[フェールオーバー優先度の変更](scripts/powershell/sql/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 手動フェールオーバー トリガーで、Azure Cosmos アカウントのフェールオーバー優先度を変更します。 |
|[タグの更新](scripts/powershell/sql/ps-account-tags-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウントのタグを更新します。 |
|[アカウント キーの取得](scripts/powershell/sql/ps-account-key-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウントのプライマリ キーとセカンダリ キーを取得します。 |
|[アカウント キーの再生成](scripts/powershell/sql/ps-account-key-regenerate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウントのプライマリ キーとセカンダリ キーを再生成します。 |
|[接続文字列の一覧表示](scripts/powershell/sql/ps-account-connection-string-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウントのプライマリ接続文字列とセカンダリ接続文字列を取得します。 |
|[IP ファイアウォールの作成](scripts/powershell/sql/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウント用の IP ファイアウォールを作成します。 |
|[Azure Cosmos アカウントの削除](scripts/powershell/sql/ps-account-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウントを削除します。 |
|**Azure Cosmos データベース**||
| [データベースの作成](scripts/powershell/sql/ps-database-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure Cosmos アカウント内にデータベースを作成します。|
| [共有およびデータベースレベルのスループットのデータベースの作成](scripts/powershell/sql/ps-database-create-shared.md?toc=%2fpowershell%2fmodule%2ftoc.json) | コンテナーによって共有されるデータベースレベルのスループットを持つ Azure Cosmos データベースを作成します。|
| [すべてのデータベースの一覧表示](scripts/powershell/sql/ps-database-list.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure Cosmos アカウント内のすべてのデータベースを一覧表示します。|
| [データベースの取得](scripts/powershell/sql/ps-database-get.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure Cosmos データベースのプロパティを取得します。|
|**Azure Cosmos コンテナー**||
| [コンテナーの作成](scripts/powershell/sql/ps-container-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 専用スループットを持つ Azure Cosmos コンテナーを作成します。|
| [共有スループットを持つコンテナーの作成](scripts/powershell/sql/ps-container-create-shared.md?toc=%2fpowershell%2fmodule%2ftoc.json) | データベース内の他のコンテナーとスループットを共有する Azure Cosmos コンテナーを作成します。|
| [インデックス ポリシーを持つコンテナーの作成](scripts/powershell/sql/ps-container-create-index-custom.md?toc=%2fpowershell%2fmodule%2ftoc.json) | カスタム インデックス ポリシーを持つ Azure Cosmos コンテナーを作成します。|
| [インデックス ポリシーを持たないコンテナーの作成](scripts/powershell/sql/ps-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | インデックス ポリシーが無効になっている Azure Cosmos コンテナーを作成します。|
| [一意のキーと TTL を持つコンテナーの作成](scripts/powershell/sql/ps-container-create-unique-key-ttl.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 一意キー制約と有効期間が構成された Azure Cosmos コンテナーを作成します。|
| [競合解決を行うコンテナーの作成](scripts/powershell/sql/ps-container-create-conflict-policy.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 最終書き込み者優先競合解決ポリシーを持つ Azure Cosmos コンテナーを作成します。|
| [すべてのコンテナーの一覧表示](scripts/powershell/sql/ps-container-list.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure Cosmos データベース内のすべてのコンテナーを一覧表示します。|
| [コンテナーの取得](scripts/powershell/sql/ps-container-get.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure Cosmos データベース内のコンテナーのプロパティを取得します。|
| [コンテナーの削除](scripts/powershell/sql/ps-container-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure Cosmos データベース内のコンテナーを削除します。|
|||