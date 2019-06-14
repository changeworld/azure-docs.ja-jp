---
title: Azure portal を使用して Azure Database for PostgreSQL でストレージを自動拡張する - 単一サーバー
description: この記事では、Azure portal を使用して Azure Database for PostgreSQL のストレージの自動拡張を有効にする方法について説明します - 単一サーバー
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 9f88fe3e8a30dd80c5331bd6c8ea7aec401c6fb9
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2019
ms.locfileid: "66576649"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Azure portal を使用して Azure Database for PostgreSQL でストレージを自動拡張する - 単一サーバー
この記事では、ワークロードに影響を与えることなく拡張されるように Azure Database for PostgreSQL サーバーのストレージを構成する方法について説明します。

サーバーが、割り当てられたストレージの上限に達すると、そのサーバーは読み取り専用としてマークされます。 ただし、ストレージの自動拡張を有効にした場合、サーバーのストレージは、増大するデータに合わせて拡張されます。 プロビジョニングされたストレージが 100 GB 未満のサーバーでは、ストレージの空き容量が 1 GB 以下またはプロビジョニングされたストレージの 10% 以下になると、プロビジョニングされたストレージ サイズが 5 GB 単位ですぐに拡張されます。 プロビジョニングされたストレージが 100 GB を超えるサーバーの場合、空きストレージ領域が、プロビジョニングされたストレージ サイズの 5% 未満になると、プロビジョニングされたストレージ サイズが 5% 単位で拡張されます。 [こちら](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage)で指定されているストレージの上限が適用されます。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドを完了するには、次が必要です。
- [Azure Database for PostgreSQL サーバー](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>ストレージの自動拡張を有効にする 

PostgreSQL サーバー ストレージの自動拡張を設定するには、これらの手順に従います。

1. [Azure portal](https://portal.azure.com/) で、ご利用の Azure Database for PostgreSQL サーバーを選択します。

2. PostgreSQL サーバーのページで **[設定]** にある **[価格レベル]** をクリックして、価格レベルのページを開きます。

3. **[自動拡張]** セクションで、 **[はい]** を選択してストレージの自動拡張を有効にします。

    ![Azure Database for PostgreSQL - 価格レベルの設定 - 自動拡張](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. **[OK]** をクリックして変更を保存します。

5. 通知により、自動拡張の有効化に成功したことが確認できます。

    ![Azure Database for PostgreSQL - 自動拡張の成功](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>次の手順

[メトリックに関するアラートを作成する方法](howto-alert-on-metric.md)について学びます。
