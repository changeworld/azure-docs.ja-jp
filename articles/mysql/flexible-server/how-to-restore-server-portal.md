---
title: Azure portal を使用して Azure Database for MySQL フレキシブル サーバーを復元する
description: この記事では、Azure portal を使用して Azure Database for MySQL フレキシブル サーバーで復元操作を実行する方法について説明します
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 962a2cbdbcc238517616c9ade235eed9b8cae6f7
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502047"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>Azure portal を使用した Azure Database for MySQL - フレキシブル サーバー (プレビュー) のポイントインタイム リストア


> [!IMPORTANT]
> Azure Database for MySQL - フレキシブル サーバーは現在、パブリック プレビュー段階にあります。

この記事では、バックアップを使用して、フレキシブル サーバーでポイントインタイム リストアを実行する手順について説明します。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

-   Azure Database for MySQL フレキシブル サーバーを所有している必要があります。

## <a name="restore-to-the-latest-restore-point"></a>最新の復元ポイントに復元する

以下の手順に従って、既存の最も古いバックアップを使用して、フレキシブル サーバーを復元します。

1.  [Azure portal](https://portal.azure.com/) で、バックアップの復元元のフレキシブル サーバーを選択します。

2.  左側のパネルで **[概要]** をクリックします。

3.  [概要] ページで、 **[復元]** をクリックします。

4.  [復元] ページには、**最新の復元ポイント** とカスタムの復元ポイントのどちらかを選択するオプションが表示されます。

5.  **[最新の復元ポイント]** を選択します。

6.  **[新しいサーバーに復元]** フィールドに新しいサーバー名を入力します。

    :::image type="content" source="./media/concept-backup-restore/restore-blade-latest.png" alt-text="最初の復元時刻":::

8.  **[OK]** をクリックします。

9.  復元操作が開始されたことを示す通知が表示されます。

## <a name="restoring-to-a-custom-restore-point"></a>カスタム復元ポイントへの復元

以下の手順に従って、既存の最も古いバックアップを使用して、フレキシブル サーバーを復元します。

1.  [Azure portal](https://portal.azure.com/) で、バックアップの復元元のフレキシブル サーバーを選択します。

2.  [概要] ページで、 **[復元]** をクリックします。

3.  [復元] ページに、最も古い復元ポイントとカスタム復元ポイントのどちらかを選択するオプションが表示されます。

4.  **[カスタム復元ポイント]** を選択します。

5.  日付と時刻を選択します。

6.  **[新しいサーバーに復元]** フィールドに新しいサーバー名を入力します。

6.  **[新しいサーバーに復元]** フィールドに新しいサーバー名を入力します。

    :::image type="content" source="./media/concept-backup-restore/restore-blade-custom.png" alt-text="概要の表示":::

7.  **[OK]** をクリックします。

8.  復元操作が開始されたことを示す通知が表示されます。


## <a name="perform-post-restore-tasks"></a>復元後のタスクの実行
復元が完了したら、次のタスクを実行してユーザーとアプリケーションを稼働状態に戻す必要があります。

- 元のサーバーを新しいサーバーで置き換える場合は、クライアントとクライアント アプリケーションを新しいサーバーにリダイレクトします。
- ユーザーが接続できるように、適切な VNet 規則が適用されていることを確認する。 これらのルールは配信元のサーバーからはコピーされません。
- 適切なログインとデータベース レベルのアクセス許可が指定されていることを確認します。
- 新しい復元サーバーに対して、必要に応じてアラートを構成します。


## <a name="next-steps"></a>次のステップ
[ビジネス継続性](concepts-business-continuity.md)について説明します。
