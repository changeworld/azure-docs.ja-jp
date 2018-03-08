---
title: "Azure Database for PostgreSQL のサーバーを復元する方法"
description: "この記事では、Azure Portal を使用して Azure Database for PostgreSQL のサーバーを復元する方法について説明します。"
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 7607a3e60eec39de61c785b8ff75a9f11fa02d0c
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2018
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-portal"></a>Azure Portal を使用して Azure Database for PostgreSQL のサーバーをバックアップおよび復元する方法

## <a name="backup-happens-automatically"></a>自動バックアップ
Azure Database for PostgreSQL サーバーは、復元機能が有効になるように、バックアップが定期的に行われます。 この機能を使用して、新しいサーバー上で、サーバーとそのすべてのデータベースを過去の特定の時点に復元できます。

## <a name="set-backup-configuration"></a>バックアップ構成の設定

サーバーの作成時に、**[価格レベル]** ウィンドウで、ローカル冗長バックアップまたは地理冗長バックアップのどちらでサーバーを構成するかを選択します。

> [!NOTE]
> サーバーの作成後は、冗長の種類 (地理冗長とローカル冗長) を切り替えることはできません。
>

Azure Portal でサーバーを作成するときに、**[価格レベル]** ウィンドウで、使用しているサーバーのバックアップとして **[ローカル冗長]** または **[地理冗長]** のいずれかを選択します。 また、このウィンドウの **[バックアップの保有期間]** で、サーバーのバックアップを保存する期間 (日数) を選択します。

   ![価格レベル - バックアップ冗長の選択](./media/howto-restore-server-portal/pricing-tier.png)

作成中のこれらの値の設定について詳しくは、[Azure Database for PostgreSQL サーバーのクイック スタート](quickstart-create-server-database-portal.md)に関するページをご覧ください。

サーバーのバックアップのリテンション期間は、以下の手順で変更できます。
1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. Azure Database for PostgreSQL サーバーを選択します。 この操作で、**[概要]** ページが開きます。
3. **[設定]** で、メニューから **[価格レベル]** を選択します。 スライダーを使用して、**バックアップの保有期間**を 7 ～ 35 日の間で希望の値に変更します。
次のスクリーンショットでは 34 日に変更されています。
![長くしたバックアップのリテンション期間](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. **[OK]** をクリックして変更を確定します。

バックアップのリテンション期間によって、現在からどのくらい遡ってポイントインタイム リストアを取得できるかが管理されます。ポイントインタイム リストアは使用可能なバックアップに基づいているためです。 ポイントインタイム リストアについては、次のセクションで詳しく説明します。 

## <a name="point-in-time-restore-in-the-azure-portal"></a>Azure Portal でのポイントインタイム リストア
Azure Database for PostgreSQL では、サーバーの過去の特定時点まで遡り、これをサーバーの新しいコピーに復元できます。 この新しいサーバーを使用してデータを復旧したり、クライアント アプリケーションにこの新しいサーバーを参照させたりすることができます。

たとえば、本日正午にテーブルが誤って削除された場合、正午前の時点まで復元し、削除されたテーブルとデータを新しいサーバーのコピーから取得できます。 ポイントインタイム リストアは、データベース レベルではなく、サーバー レベルで行われます。

次の手順では、サンプルのサーバーを特定の時点まで復元します。
1. Azure Portal で、ご利用の Azure Database for PostgreSQL サーバーを選択します。 

2. サーバーの **[概要]** ページのツール バーで **[復元]** を選択します。

   ![Azure Database for PostgreSQL - 概要 - 復元ボタン](./media/howto-restore-server-portal/2-server.png)

3. [復元] フォームに必要な情報を入力します。

   ![Azure Database for PostgreSQL - 情報の復元 ](./media/howto-restore-server-portal/3-restore.png)
  - **復元ポイント**: 復元先の特定の時点を選択します。
  - **対象サーバー**: 新しいサーバーの名前を指定します。
  - **場所**: リージョンを選択することはできません。 既定では、ソース サーバーと同じになります。
  - **価格レベル**: ポイントインタイム リストアを行うときは、これらのパラメーターを変更することはできません。 ソース サーバーと同じレベルになります。 

4. **[OK]** をクリックして、特定の時点までサーバーを復元します。 

5. 復元が完了したら、作成した新しいサーバーを検索して、想定どおりにデータベースが復元できたかどうかを確認します。

>[!Note]
>ポイントインタイム リストアによって作成された新しいサーバーには、選択した特定の時点の既存のサーバーに対して有効であったサーバー管理者のログイン名とパスワードが設定されています。 このパスワードは、新しいサーバーの **[概要]** ページで変更できます。

## <a name="next-steps"></a>次の手順
- サービスの[バックアップ](concepts-backup.md)の詳細を確認します。
- [ビジネス継続性](concepts-business-continuity.md)オプションについて確認します。
