---
title: 最小限のダウンタイムでの Azure Database for MySQL への移行
description: この記事では、Azure Database Migration Service を使用して、MySQL データベースを Azure Database for MySQL に最小限のダウンタイムで移行する方法について説明します。
services: mysql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 10/11/2018
ms.openlocfilehash: cb2e1a34748875de9c7f8dd59be27c408ec82818
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2018
ms.locfileid: "49090333"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>最小限のダウンタイムでの Azure Database for MySQL への移行
[Azure Database Migration Service](https://aka.ms/get-dms) (DMS) に新たに導入された**継続的同期機能**を使用すると、最小限のダウンタイムで MySQL を Azure Database for MySQL に移行できます。 この機能で、アプリケーションによって発生するダウンタイムの長さが短くなります。

## <a name="overview"></a>概要
DMS は、オンプレミスから Azure Database for MySQL への初期読み込みを実行してから、アプリケーションは実行中のままで、新しいトランザクションを Azure に継続的に同期します。 データがターゲットの Azure 側に追いついた後、アプリケーションを短時間停止し (最小限のダウンタイム)、データの最後のバッチがターゲットに追いつくまで (アプリケーションを停止してから、アプリケーションが事実上新しいトラフィックを受け取ることができなくなるまで) 待ってから、Azure を指すように接続文字列を更新します。 完了すると、アプリケーションは Azure 上で動作します。

![Azure Database Migration Service での継続的同期](./media/howto-migrate-online/ContinuousSync.png)

- MySQL ソースの DMS 移行は現在パブリック プレビュー段階です。 MySQL のワークロードを移行するサービスを試す場合は、ポータルに進みましょう。 お客様のフィードバックは、サービスをさらに改善するために大切です。

## <a name="next-steps"></a>次の手順
- [MySQL/PostgreSQL アプリを Azure マネージド サービスに簡単に移行する方法](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201)のビデオをご覧ください。MySQL アプリを Azure Database for MySQL に移行する方法を示すデモが含まれています。
- 「[DMS を使用して MySQL をオンラインの Azure Database for MySQL に移行する](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online)」のチュートリアルを参照してください。
