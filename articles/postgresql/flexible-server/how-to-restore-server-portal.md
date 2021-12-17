---
title: 復元 - Azure portal - Azure Database for PostgreSQL - フレキシブル サーバー
description: この記事では、Azure portal を使用して Azure Database for PostgreSQL で復元操作を実行する方法について説明します。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/18/2021
ms.openlocfilehash: a9d09169b81e274202e9bf3df5a91844cf34807b
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721946"
---
# <a name="point-in-time-restore-of-a-flexible-server"></a>フレキシブル サーバーのポイントインタイム リストア

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

この記事では、バックアップを使用して、フレキシブル サーバーでポイントインタイム リストアを実行する手順について説明します。 保有期間内に、最新の復元ポイントまたはカスタム復元ポイントへの復元を実行できます。

## <a name="pre-requisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

-   Azure Database for PostgreSQL - フレキシブル サーバーを所有している必要があります。 同じ手順は、ゾーン冗長性が構成されたフレキシブル サーバーにも適用できます。

## <a name="restoring-to-the-latest-restore-point"></a>最新の復元ポイントへの復元

以下の手順に従い、既存のバックアップを使用してフレキシブル サーバーを復元します。

1.  [Azure portal](https://portal.azure.com/) で、バックアップの復元元のフレキシブル サーバーを選択します。

2.  左側のパネルから **[概要]** をクリックし、 **[復元]** をクリックします。
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="復元の概要":::

3.  [復元] ページには、最新の復元ポイントとカスタムの復元ポイントのどちらかを選択するオプションが表示されます。

4.  **[最新の復元ポイント]** を選択し、 **[新しいサーバーに復元]** フィールドに新しいサーバー名を入力します。 必要に応じて、復元先の可用性ゾーンを選択できます。
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-latest.png" alt-text="最新の復元時刻":::

5.  **[OK]** をクリックします。

6.  復元操作が開始されたことを示す通知が表示されます。

## <a name="restoring-to-a-custom-restore-point"></a>カスタム復元ポイントへの復元

以下の手順に従い、既存のバックアップを使用してフレキシブル サーバーを復元します。

1.  [Azure portal](https://portal.azure.com/) で、バックアップの復元元のフレキシブル サーバーを選択します。

2.  [概要] ページで、 **[復元]** をクリックします。
 :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="復元の概要":::
    
3.  [復元] ページには、最新の復元ポイントとカスタムの復元ポイントのどちらかを選択するオプションが表示されます。

4.  **[カスタム復元ポイント]** を選択します。

5.  日時を選択し、 **[新しいサーバーに復元]** フィールドに新しいサーバー名を入力します。 新しいサーバー名を指定します。必要に応じて、復元先の **可用性ゾーン** を選択できます。
   
:::image type="content" source="./media/how-to-restore-server-portal/restore-custom-2.png" alt-text="カスタム復元時刻":::
 
6.  **[OK]** をクリックします。

7.  復元操作が開始されたことを示す通知が表示されます。

## <a name="performing-geo-restore-preview"></a>geo リストアの実行 (プレビュー)

ソース サーバーが geo 冗長バックアップを使用して構成されている場合は、ペアになっているリージョンにサーバーを復元できます。 最初に復元するときは、ソース サーバーが作成された後、少なくとも1時間待機してください。

1.  [Azure portal](https://portal.azure.com/) で、バックアップの geo リストア元のフレキシブル サーバーを選択します。

2.  [概要] ページで、 **[復元]** をクリックします。
 :::image type="content" source="./media/how-to-restore-server-portal/geo-restore-click.png" alt-text="[復元] をクリックします":::

3. [復元] ページで、[Geo-Redundant restore]\(Geo 冗長復元\) を選択して、ペアになっているリージョンに復元します。 
 :::image type="content" source="./media/how-to-restore-server-portal/geo-restore-choose-checkbox.png" alt-text="[Geo リストア] の選択":::
 
4. リージョンとデータベースのバージョンがあらかじめ選択されています。 これは、ペアのリージョンで使用可能な最後のデータに復元されます。 復元先のリージョン内の **可用性ゾーン** を選択できます。

5. 既定では、復元されたサーバーのバックアップは Geo 冗長バックアップを使用して構成されます。 Geo 冗長バックアップが必要ではない場合は、 **[サーバーの構成]** をクリックして、Geo 冗長バックアップをオフにします。

6. ソース サーバーが **プライベート アクセス** で構成されている場合、リモート リージョンの別の VNET にしか復元できません。 既存の VNET を選択するか、新しい VNET を作成し、その VNET にサーバーを復元することができます。  

## <a name="next-steps"></a>次のステップ

-   [ビジネス継続性](./concepts-business-continuity.md)について確認する
-   [ゾーン冗長による高可用性](./concepts-high-availability.md)について確認する
-   [バックアップと復旧](./concepts-backup-restore.md)について確認する
