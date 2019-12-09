---
title: Azure Database for PostgreSQL を管理する - Azure portal
description: Azure portal から Azure Database for PostgreSQL サーバーを管理する方法について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: bcddd64afca29ac9fdd5d284fc8f809ff9e2477d
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534887"
---
# <a name="manage-an-azure-database-for-postgresql-server-using-the-azure-portal"></a>Azure portal を使用して Azure Database for PostgreSQL サーバーを管理する
この記事では、Azure Database for PostgreSQL サーバーを管理する方法を示します。 管理タスクには、コンピューティングとストレージのスケーリング、管理者パスワードのリセット、サーバーの詳細の表示が含まれます。

## <a name="sign-in"></a>サインイン
[Azure Portal](https://portal.azure.com) にサインインします。

## <a name="create-a-server"></a>サーバーの作成
Azure Database for PostgreSQL サーバーを作成して実行する方法については、[クイックスタート](quickstart-create-server-database-portal.md)を参照してください。

## <a name="scale-compute-and-storage"></a>コンピューティングとストレージのスケーリング

サーバーの作成後、ニーズの変化に応じて、General Purpose (汎用) レベルと Memory Optimized (メモリ最適化) レベルの間でスケーリングを実行できます。 仮想コアを増減してコンピューティングとメモリをスケーリングすることもできます。 ストレージをスケールアップできます (ただし、ストレージのスケールダウンはできません)。

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>General Purpose レベルと Memory Optimized レベル間のスケーリング

General Purpose から Memory Optimized に (また、その逆方向に) スケーリングできます。 サーバーの作成後に、Basic レベルへの変更や Basic レベルからの変更はサポートされていません。 

1. Azure portal で、お使いのサーバーを選択します。 **[設定]** セクションにある **[Pricing tier]\(価格レベル\)** を選択します。

2. スケーリングの目的に応じて、 **[General Purpose]\(汎用\)** または **[メモリ最適化]** を選択します。 

    ![change-pricing-tier](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > レベルを変更すると、サーバーが再起動されます。

4. **[OK]** を選択して変更を保存します。


### <a name="scale-vcores-up-or-down"></a>仮想コアのスケールアップまたはスケールダウン

1. Azure portal で、お使いのサーバーを選択します。 **[設定]** セクションにある **[Pricing tier]\(価格レベル\)** を選択します。

2. スライダーを目的の値に動かして、 **[vCore]** の設定を変更します。

    ![scale-compute](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > 仮想コアをスケーリングすると、サーバーが再起動されます。

3. **[OK]** を選択して変更を保存します。


### <a name="scale-storage-up"></a>ストレージのスケールアップ

1. Azure portal で、お使いのサーバーを選択します。 **[設定]** セクションにある **[Pricing tier]\(価格レベル\)** を選択します。

2. スライダーを目的の値まで動かして、 **[ストレージ]** の設定を変更します。

    ![scale-storage](./media/howto-create-manage-server-portal/scaling-storage.png)

    > [!NOTE]
    > ストレージのスケールダウンはできません。

3. **[OK]** を選択して変更を保存します。


## <a name="update-admin-password"></a>管理パスワードを更新する
Azure portal を使用して管理者ロールのパスワードを変更できます。

1. Azure portal で、お使いのサーバーを選択します。 **[概要]** ウィンドウで **[パスワードのリセット]** を選択します。

   ![概要](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. 新しいパスワードを入力し、そのパスワードを確認します。 テキストボックスに、パスワードの複雑さの要件についてのプロンプトが表示されます。

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. **[OK]** を選択して新しいパスワードを保存します。


## <a name="delete-a-server"></a>サーバーの削除

不要になったサーバーは削除できます。 

1. Azure portal で、お使いのサーバーを選択します。 **[概要]** ウィンドウで **[削除]** を選択します。

    ![delete](./media/howto-create-manage-server-portal/overview-delete.png)

2. 入力ボックスにサーバーの名前を入力して、これが削除するサーバーであることを確認します。

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > サーバーの削除は元に戻せません。

3. **[削除]** を選択します。


## <a name="next-steps"></a>次の手順
- [バックアップとサーバーの復元](howto-restore-server-portal.md)について学ぶ
- [Azure Database for PostgreSQL のチューニングと監視のオプション](concepts-monitoring.md)について学ぶ
