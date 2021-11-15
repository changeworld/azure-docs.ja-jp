---
title: 停止または開始する - Azure portal - Azure Database for MySQL フレキシブル サーバー
description: この記事では、Azure portal を使用して Azure Database for MySQL で操作を停止または開始する方法について説明します。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: e17b788c3852d104d82b456e03c93420f3ea9f3d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467853"
---
# <a name="stopstart-an-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL を停止または開始する - フレキシブル サーバー

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

この記事では、フレキシブル サーバーの停止および開始を実行する手順を説明します。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

-   Azure Database for MySQL フレキシブル サーバーを所有している必要があります。

## <a name="stop-a-running-server"></a>実行中のサーバーを停止する

1.  [Azure portal](https://portal.azure.com/) で、停止するフレキシブル サーバーを選択します。

2.  **[概要]** ページのツール バーで **[停止]** ボタンをクリックします。

    :::image type="content" source="media/how-to-stop-start-server-portal/stop-server.png" alt-text="フレキシブル サーバーを停止します。":::

3.  **[はい]** をクリックして、サーバーが停止していることを確認します。

    :::image type="content" source="media/how-to-stop-start-server-portal/confirm-stop.png" alt-text="フレキシブル サーバーが停止していることを確認します。":::

> [!NOTE]
> サーバーが停止すると、そのフレキシブル サーバーに対して他の管理操作は実行できません。

## <a name="start-a-stopped-server"></a>停止したサーバーを起動する

1.  [Azure portal](https://portal.azure.com/) で、開始するフレキシブル サーバーを選択します。

2.  **[概要]** ページのツール バーで **[起動]** ボタンをクリックします。

    :::image type="content" source="media/how-to-stop-start-server-portal/start-server.png" alt-text="フレキシブル サーバーを起動します。":::

> [!NOTE]
> サーバーが起動した後、すべての管理操作をフレキシブル サーバーで使用できるようになります。

## <a name="next-steps"></a>次のステップ
- [Azure Database for MySQL フレキシブル サーバーでのネットワーク](./concepts-networking.md)の詳細を確認します
- [Azure portal を使用して Azure Database for MySQL フレキシブル サーバーの仮想ネットワークを作成および管理します](./how-to-manage-virtual-network-portal.md)。

