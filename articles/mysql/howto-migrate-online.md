---
title: 最小限のダウンタイムでの移行 - Azure Database for MySQL
description: この記事では、MySQL データベースを Azure Database for MySQL に最小限のダウンタイムで移行する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.custom: ''
ms.date: 6/19/2021
ms.openlocfilehash: 728c1d76539eb34efe5e5fc475837810ea5a5111
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113088586"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>最小限のダウンタイムでの Azure Database for MySQL への移行

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

アプリケーションによって発生するダウンタイムの時間を制限するデータイン レプリケーションを利用することで、MySQL を Azure Database for MySQL に最小限のダウンタイムで移行できます。

データベースを Azure Database for MySQL に移行する方法の詳細と使用例については、[データベースの移行ガイド](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)も参照してください。 このガイドでは、Azure への MySQL 移行の計画と実行を成功させるためのガイダンスが提供されています。

## <a name="overview"></a>概要

データイン レプリケーションを利用することで、アプリケーションの実行中に新しいトランザクションがあれば、それが Azure に継続的に同期されるよう、プライマリとしてソースを、レプリカとしてターゲットを構成できます。 データがターゲットの Azure 側に追いついた後、アプリケーションを短時間停止し (最小限のダウンタイム)、データの最後のバッチがターゲットに追いつくまで (アプリケーションを停止してから、アプリケーションが事実上新しいトラフィックを受け取ることができなくなるまで) 待ってから、Azure を指すように接続文字列を更新します。 完了すると、アプリケーションは Azure 上で動作します。

## <a name="next-steps"></a>次のステップ

- Azure Database for MySQL へのデータベースの移行については、「[データベース移行ガイド](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)」をご覧ください。
