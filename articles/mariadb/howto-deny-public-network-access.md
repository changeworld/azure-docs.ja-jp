---
title: パブリック ネットワーク アクセスを拒否する - Azure portal - Azure Database for MariaDB
description: Azure portal を使用して Azure Database for MariaDB でパブリック ネットワーク アクセスを拒否するように構成する方法について説明します
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 87b6033e486e9009573436628d0183c8a022aced
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372769"
---
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>Azure portal を使用して Azure Database for MariaDB でパブリック ネットワーク アクセスを拒否する

この記事では、Azure Database for MariaDB サーバーを、すべてのパブリック構成を拒否し、プライベート エンドポイント経由の接続のみを許可するように構成して、ネットワーク セキュリティを強化する方法について説明します。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

* [Azure Database for MariaDB](quickstart-create-MariaDB-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>パブリック ネットワーク アクセスの拒否を設定する

パブリック ネットワーク アクセスを拒否するように MariaDB サーバーを設定するには、次の手順のようにします。

1. [Azure portal](https://portal.azure.com/) で、既存の Azure Database for MariaDB サーバーを選択します。

1. MariaDB サーバー ページの **[設定]** で、 **[接続のセキュリティ]** をクリックして接続のセキュリティの構成ページを開きます。

1. [Deny Public Network Access]\(パブリック ネットワーク アクセスを拒否する\) で **[はい]** を選択して、MariaDB サーバーのパブリック アクセスの拒否を有効にします。

    ![Azure Database for MariaDB のネットワーク アクセスの拒否](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. **[保存]** をクリックして変更を保存します。

1. 接続のセキュリティの設定が正常に有効化されたことを確認する通知が表示されます。

    ![Azure Database for MariaDB のネットワーク アクセスの拒否の成功](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>次のステップ

[メトリックに関するアラートを作成する方法](howto-alert-metric.md)について学習します。