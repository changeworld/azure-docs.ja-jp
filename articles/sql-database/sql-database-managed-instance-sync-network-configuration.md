---
title: Azure App Service - ネットワーク構成の同期 | Microsoft Docs
description: この記事では、Azure App Service のホスティング プラン用にネットワーク構成を同期する方法について説明します。
ms.service: sql-database
author: srdjan-bozovic
manager: craigg
ms.custom: managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 5b139b1279776acfca63def25a9fdae0f627a727
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>ネットワーク構成を Azure App Service のホスティング プラン用に同期する

場合によっては、[アプリを Azure Virtual Network と統合したにもかかわらず](../app-service/web-sites-integrate-with-vnet.md)、マネージ インスタンスへの接続を確立できないことがあります。 その場合の対処の 1 つとして、使用するサービス プラン用にネットワーク構成を更新するという対処方法があります。 

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>ネットワーク構成を App Service のホスティング プラン用に同期する

そのためには、次の手順に従います。  

1. Web アプリの App Service プランに移動します。
 
   ![App Service プラン](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. **[ネットワーク]** をクリックし、**[管理するにはここをクリック]** をクリックします。
 
   ![サービス プランの管理](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. 目的の **VNet** を選択し、**[ネットワークの同期]** をクリックします。 
 
   ![ネットワークの同期](./media/sql-database-managed-instance-sync-networking/sync.png)

4. 同期が完了するまで待機します。
  
   ![同期の完了](./media/sql-database-managed-instance-sync-networking/sync-done.png)

これで、マネージ インスタンスへの接続を再確立する準備が整いました。

## <a name="next-steps"></a>次の手順

- VNet をマネージ インスタンス用に構成する方法について詳しくは、[マネージ インスタンスの VNet 構成](sql-database-managed-instance-vnet-configuration.md)に関する記事をご覧ください。
