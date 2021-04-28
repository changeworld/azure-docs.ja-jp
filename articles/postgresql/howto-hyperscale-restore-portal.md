---
title: 復元 – Hyperscale (Citus) - Azure Database for PostgreSQL - Azure portal
description: この記事では、Azure portal を使用して Azure Database for PostgreSQL - Hyperscale (Citus) で復元操作を実行する方法について説明します。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/14/2021
ms.openlocfilehash: 99e507fed35e5b74aa44bc2713550f7cf9d7b2e5
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518883"
---
# <a name="point-in-time-restore-of-a-hyperscale-citus-server-group"></a>Hyperscale (Citus) サーバー グループのポイントインタイム リストア

この記事では、バックアップを使用して Hyperscale (Citus) サーバー グループの[特定の時点への復旧](concepts-hyperscale-backup.md#restore)を実行する詳細な手順について説明します。 保有期間中の最初のバックアップ、またはカスタム復元ポイントへの復元を実行できます。

## <a name="restoring-to-the-earliest-restore-point"></a>最初の復元ポイントへの復元

次の手順に従って、Hyperscale (Citus) サーバー グループをその最初の既存のバックアップに復元します。

1.  [Azure portal](https://portal.azure.com/) で、復元するサーバー グループを選択します。

2.  左側のパネルから **[概要]** をクリックし、 **[復元]** をクリックします。

    > [!IMPORTANT]
    > サーバー グループに対して **[復元]** ボタンがまだ表示されない場合は、Azure サポート リクエストを開いてください。

3.  [復元] ページでは、**最初** と **カスタム** のいずれかの復元ポイントを選択するように求めるメッセージが表示され、最も早い日付が表示されます。

4.  **[最初の復元ポイント]** を選択します。

5.  **[新しいサーバーに復元]** フィールドに新しいサーバー グループ名を入力します。 その他のフィールド (サブスクリプション、リソース グループ、場所) は表示されますが、編集できません。

6.  **[OK]** をクリックします。

7.  復元操作が開始されたことを示す通知が表示されます。

最後に、[復元後のタスク](#post-restore-tasks)を進めます。

## <a name="restoring-to-a-custom-restore-point"></a>カスタム復元ポイントへの復元

次の手順に従って、選択した日時への Hyperscale (Citus) サーバー グループの復元を行います。

1.  [Azure portal](https://portal.azure.com/) で、復元するサーバー グループを選択します。

2.  左側のパネルから **[概要]** をクリックし、 **[復元]** をクリックします。

    > [!IMPORTANT]
    > サーバー グループに対して **[復元]** ボタンがまだ表示されない場合は、Azure サポート リクエストを開いてください。

3.  [復元] ページでは、**最初** と **カスタム** のいずれかの復元ポイントを選択するように求めるメッセージが表示され、最も早い日付が表示されます。

4.  **[カスタム復元ポイント]** を選択します。

5.  **[復元ポイント (UTC)]** で日時を選択し、 **[新しいサーバーに復元]** フィールドに新しいサーバー グループ名を入力します。 その他のフィールド (サブスクリプション、リソース グループ、場所) は表示されますが、編集できません。
 
6.  **[OK]** をクリックします。

7.  復元操作が開始されたことを示す通知が表示されます。

最後に、[復元後のタスク](#post-restore-tasks)を進めます。

## <a name="post-restore-tasks"></a>復元後のタスク

復元後、ユーザーとアプリケーションを元に戻して動作させるには、次のことを行う必要があります。

* 元のサーバーを新しいサーバーで置き換える場合は、クライアントとクライアント アプリケーションを新しいサーバーにリダイレクトする
* ユーザーが接続できるように、適切なサーバーレベルのファイアウォールが適用されていることを確認します。 これらのルールは元のサーバー グループからはコピーされません。
* 必要に応じて、PostgreSQL サーバー パラメーターを調整します。 パラメーターは元のサーバー グループからはコピーされません。
* 適切なログインとデータベース レベルのアクセス許可が指定されていることを確認します。
* 必要に応じて、アラートを構成する。

## <a name="next-steps"></a>次のステップ

* Hyperscale (Citus) での[バックアップと復元](concepts-hyperscale-backup.md)の詳細を理解します。
* Hyperscale (Citus) サーバー グループで [推奨されるアラート](./howto-hyperscale-alert-on-metric.md#suggested-alerts)を設定します。
