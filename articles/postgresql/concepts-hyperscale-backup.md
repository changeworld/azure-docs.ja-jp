---
title: バックアップと復元 – Hyperscale (Citus) - Azure Database for PostgreSQL
description: 不慮の破損または削除からのデータの保護
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/14/2021
ms.openlocfilehash: 7681e9c28bbbbcec06bcc1cf2bf469f1b4189d79
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520175"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus) でのバックアップと復元

Azure Database for PostgreSQL – Hyperscale (Citus) では、各ノードのバックアップを自動的に作成し、ローカル冗長ストレージに格納します。 バックアップを使用して、指定した時間への Hyperscale (Citus) サーバー グループを復元することができます。
不慮の破損または削除からデータを保護するバックアップと復元は、ビジネス継続性戦略の最も重要な部分です。

## <a name="backups"></a>バックアップ

少なくとも 1 日に 1 回、Azure Database for PostgreSQL では、データ ファイルとデータベース トランザクション ログのスナップショット バックアップが作成されます。 バックアップを使用すると、サーバーを、保持期間内の任意の時点に復元できます (保有期間は現在、すべてのサーバー グループで 35 日です)。すべてのバックアップが、AES 256 ビット暗号化を使用して暗号化されます。

可用性ゾーンをサポートする Azure リージョンでは、バックアップ スナップショットは 3 つの可用性ゾーンに格納されます。 少なくとも 1 つの可用性ゾーンがオンラインになっている限り、Hyperscale (Citus) サーバー グループは復元可能です。

バックアップ ファイルをエクスポートすることはできません。 Azure Database for PostgreSQL の復元操作にのみ使用できます。

### <a name="backup-storage-cost"></a>バックアップ ストレージのコスト

現在のバックアップ ストレージの価格については、Azure Database for PostgreSQL - Hyperscale (Citus) の[価格に関するページ](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)を参照してください。

## <a name="restore"></a>復元

Hyperscale (Citus) サーバー グループは、過去 35 日以内の任意の時点に復元できます。  ポイントインタイム リストアは複数のシナリオで役に立ちます。 たとえば、ユーザーが誤ってデータを削除したとき、重要なテーブルやデータベースを削除したとき、またはアプリケーションで適切なデータが不適切なデータで誤って上書きされた場合などです。

> [!IMPORTANT]
> 削除された Hyperscale (Citus) サーバー グループは復元できません。 サーバー グループを削除すると、そのサーバー グループに属するすべてのノードが削除され、復旧できなくなります。 サーバー グループのリソースがデプロイ後に誤って削除されたり、想定外に変更されたりすることを防ぐために、管理者は[管理ロック](../azure-resource-manager/management/lock-resources.md)を利用できます。

復元プロセスにより、元のものと同じ Azure リージョン、サブスクリプション、およびリソース グループに新しいサーバー グループが作成されます。 サーバー グループは元の構成を保持します。つまり、ノード数、仮想コア数、ストレージ サイズ、ユーザー ロール、PostgreSQL バージョン、Citus 拡張機能のバージョンは同じです。

ファイアウォール設定と PostgreSQL サーバーのパラメーターは元のサーバー グループから保持されず、既定値にリセットされます。 ファイアウォールによって、すべての接続が阻止されます。 復元後にこれらの設定を手動で調整する必要があります。 一般的には、推奨される[復元後のタスク](howto-hyperscale-restore-portal.md#post-restore-tasks)の一覧を参照してください。

## <a name="next-steps"></a>次のステップ

* Azure portal で[サーバーグループを復元](howto-hyperscale-restore-portal.md)する手順を確認します。
*  [Azure 可用性ゾーン](../availability-zones/az-overview.md)について学習します。
