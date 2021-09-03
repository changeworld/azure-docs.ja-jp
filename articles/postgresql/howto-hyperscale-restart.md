---
title: サーバーを再起動する - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL - Hyperscale (Citus) でデータベースを再起動する方法
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 7/9/2021
ms.openlocfilehash: 0de8108cde0b5017221bf3ab038d98c98bb94546
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598719"
---
# <a name="restart-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus) を再起動する

Hyperscale (Citus) サーバー グループを再起動する場合は、Azure portal のグループの **[概要]** ページから実行できます。 上部バーの **[再起動]** ボタンを選択します。 構成ダイアログが表示されます。 **[Restart all]\(すべて再起動\)** を選択して続行します。

> [!NOTE]
> サーバー グループに対して [再起動] ボタンがまだ表示されない場合は、サーバー グループを再起動するための Azure サポート リクエストを開いてください。

サーバー グループの再起動は、すべてのノードに適用されます。個々のノードを選択的に再起動することはできません。 再起動は、PostgreSQL サーバー インスタンスだけでなく、ノードの仮想マシン全体に適用されます。 再起動の間、データベースを使用しようとしたアプリケーションには、接続のダウンタイムが発生します。

**次の手順**

- 一部のサーバー パラメーターを変更すると、再起動が必要になります。 Hyperscale (Citus) で構成できる[すべてのサーバー パラメーター](reference-hyperscale-parameters.md)一覧を参照してください。
