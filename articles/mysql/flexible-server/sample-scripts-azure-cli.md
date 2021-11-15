---
title: Azure CLI サンプル - Azure Database for MySQL フレキシブル サーバー
description: この記事では、Azure Database for MySQL - フレキシブル サーバーとのやりとりに使用できる Azure CLI サンプル コードを紹介しています。
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 8ae33838b4dff052006f1124eea3dbec59cc7666
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844271"
---
# <a name="azure-cli-samples-for-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL - フレキシブル サーバーの Azure CLI サンプル

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

次の表には、Azure Database for MySQL - フレキシブル サーバーの Azure CLI スクリプトのサンプルへのリンクが含まれています。

| サンプル リンク | 説明  |
|---|---|
|**サーバーを作成し、それに接続する**||
| [サーバーを作成し、パブリック アクセス接続を有効にする](scripts/sample-cli-create-connect-public-access.md) | Azure Database for MySQL - フレキシブル サーバーを作成し、サーバーレベルのファイアウォール規則 (パブリック アクセス接続方法) を構成してサーバーに接続します。 |
| [サーバーを作成してプライベート アクセス接続を有効にする (VNet 統合)](scripts/sample-cli-create-connect-private-access.md) | VNet で Azure Database for MySQL - フレキシブル サーバー (プライベート アクセス接続方法) を作成し、VNet 内の VM を介してサーバーに接続します。 |
|**監視とスケーリング**||
| [メトリックの監視とサーバーのスケーリング](scripts/sample-cli-monitor-and-scale.md) | 絶えず変化するパフォーマンス ニーズに対応するために、単一の Azure Database for MySQL - フレキシブル サーバーを監視し、スケーリングします。 |
|**バックアップと復元**||
| [サーバーの復元](scripts/sample-cli-restore-server.md) | 単一の Azure Database for MySQL - フレキシブル サーバーを以前の時点に復元します。 |
|**高可用性**||
| [ゾーン冗長による高可用性の構成](scripts/sample-cli-zone-redundant-ha.md) | Azure Database for MySQL - フレキシブル サーバーの作成時に、ゾーン冗長による高可用性を有効にします。|
| [同一ゾーンの高可用性の構成](scripts/sample-cli-same-zone-ha.md) | Azure Database for MySQL - フレキシブル サーバーの作成時に、同一ゾーン高可用性を有効にします。|
|**サーバーを管理する**||
| [サーバーを再起動、停止、起動する](scripts/sample-cli-restart-stop-start.md)| 単一の Azure Database for MySQL - フレキシブル サーバーで再起動、停止、起動操作を実行します。 |
| [サーバー パラメーターを変更する](scripts/sample-cli-change-server-parameters.md) | 単一の Azure Database for MySQL - フレキシブル サーバーのサーバー パラメーターを変更します。 |
|**レプリケーション**||
| [読み取りレプリカの作成](scripts/sample-cli-read-replicas.md) | 単一の Azure Database for MySQL - フレキシブル サーバーで読み取りレプリカを作成し、管理します。 |
|**ログを構成する**||
| [監査ログを構成する](scripts/sample-cli-audit-logs.md) | 単一の Azure Database for MySQL - フレキシブル サーバーで監査ログを構成します。 |
| [低速クエリ ログを構成する](scripts/sample-cli-slow-query-logs.md) | 単一の Azure Database for MySQL - フレキシブル サーバーで低速クエリ ログを構成します。 |

