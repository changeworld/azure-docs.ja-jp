---
title: Azure portal を使用して Azure Database for MySQL フレキシブル サーバーを復元する
description: この記事では、Azure portal を使用して Azure Database for MySQL フレキシブル サーバーで復元操作を実行する方法について説明します
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: cce31cc3d83be03da462c9345c1b8d3d86ef6228
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467926"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql-flexible-server-using-azure-portal"></a>Azure portal を使用した Azure Database for MySQL フレキシブル サーバーのポイントインタイム リストア

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

この記事では、バックアップを使用して、フレキシブル サーバーでポイントインタイム リストアを実行する手順について説明します。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

- Azure Database for MySQL フレキシブル サーバーを所有している必要があります。

## <a name="restore-to-the-latest-restore-point"></a>最新の復元ポイントに復元する

以下の手順に従って、既存の最も古いバックアップを使用して、フレキシブル サーバーを復元します。

1. [Azure portal](https://portal.azure.com/) で、バックアップの復元元のフレキシブル サーバーを選択します。

2. 左側のパネルで **[概要]** をクリックします。

3. [概要] ページで、 **[復元]** をクリックします。

4. [復元] ページには、**最新の復元ポイント** とカスタムの復元ポイントのどちらかを選択するオプションが表示されます。

5. **[最新の復元ポイント]** を選択します。

6. **[新しいサーバーに復元]** フィールドに新しいサーバー名を入力します。

    :::image type="content" source="./media/how-to-restore-server-portal/point-in-time-restore-latest.png" alt-text="最初の復元時刻":::

7. **[OK]** をクリックします。

8. 復元操作が開始されたことを示す通知が表示されます。


## <a name="restore-to-a-fastest-restore-point"></a>最速の復元ポイントに復元する

この手順に従って、既存の完全バックアップを最速の復元ポイントとして使用して、フレキシブル サーバーを復元します。 

1. [Azure portal](https://portal.azure.com/) で、バックアップの復元元のフレキシブル サーバーを選択します。 

2. 左側のパネルで **[概要]** をクリックします。 

3. [概要] ページで、 **[復元]** をクリックします。 

4. [復元] ページには、最新の復元ポイント、カスタムの復元ポイント、最速の復元ポイントのいずれかを選ぶオプションが表示されます。 

5. **[Select fastest restore point (Restore using full backup)]\(最速の復元ポイントを選択する (完全バックアップを使用して復元)\)** オプションを選びます。 

6. **[Fastest Restore Point (UTC)]\(最速の復元ポイント (UTC)\)** ドロップダウン リストから、目的の完全バックアップを選びます。 
 
    :::image type="content" source="./media/how-to-restore-server-portal/fastest-restore-point.png" alt-text="最速の復元ポイント":::

7. **[新しいサーバーに復元]** フィールドに新しいサーバー名を入力します。

8. **[確認および作成]** をクリックします。 

9. **[作成]** をクリックすると、復元操作が開始されたことを示す通知が表示されます。  

## <a name="restore-from-a-full-backup-through-the-backup-and-restore-blade"></a>[バックアップと復元] ブレードを使用して完全バックアップから復元する

以下の手順に従い、既存の完全バックアップを使用してフレキシブル サーバーを復元します。 

1. [Azure portal](https://portal.azure.com/) で、バックアップの復元元のフレキシブル サーバーを選択します。 

2. 左側のパネルで **[バックアップと復元]** をクリックします。 

3. [View Available Backups]\(利用可能なバックアップの表示\) ページには、保持期間内にサーバーに対して作成されたすべての自動バックアップから復元するオプションが表示されます。  

4. 対応する **[復元]** 操作をクリックして、一覧から目的の完全バックアップを選びます。 
 
    :::image type="content" source="./media/how-to-restore-server-portal/view-available-backups.png" alt-text="[View Available Backups]\(利用可能なバックアップの表示\)":::

5. [Fastest Restore Point]\(最速の復元ポイント\) オプションが既定で選ばれ、[View Available Backups]\(利用可能なバックアップの表示\) ページで選んだ目的の完全バックアップ タイムスタンプが表示された [復元] ページが表示されます。 

6. **[新しいサーバーに復元]** フィールドに新しいサーバー名を入力します。

7. **[確認および作成]** をクリックします。 

8. **[作成]** をクリックすると、復元操作が開始されたことを示す通知が表示されます。  


## <a name="geo-restore-to-latest-restore-point"></a>最新の復元ポイントへの geo リストア

1. [Azure portal](https://portal.azure.com/) で、バックアップの復元元のフレキシブル サーバーを選択します。

2. 左側のパネルで **[概要]** をクリックします。

3. [概要] ページで、 **[復元]** をクリックします。

4. [復元] ページには、 **[Geo-redundant restore]\(Geo 冗長リストア\)** を選ぶオプションが表示されます。 geo 冗長バックアップ用にサーバーを構成した場合は、対応する Azure のペアになっているリージョンにサーバーを復元し、geo 冗長リストア オプションを有効にすることができます。 geo 冗長リストア オプションにより、サーバーが最新の UTC Now タイムスタンプに復元されるので、geo 冗長リストアを選んだ後は、ポイントインタイム リストア オプションを同時に選ぶことはできません。

   :::image type="content" source="./media/how-to-restore-server-portal/georestore-flex.png" alt-text="geo リストア オプション":::

   :::image type="content" source="./media/how-to-restore-server-portal/georestore-enabled-flex.png" alt-text="geo リストアの有効化":::

5. [サーバーの詳細] セクションの **[名前]** フィールドに、新しいサーバー名を入力します。

6. **[確認および作成]** を選んで、選択内容を確認します。 

7. 復元操作が開始されたことを示す通知が表示されます。 この操作には数分かかることがあります。 

geo リストアによって作成された新しいサーバーには、復元が開始された時点の既存のサーバーで有効であったサーバー管理者のログイン名とパスワードが設定されています。 このパスワードは、新しいサーバーの [概要] ページで変更できます。 さらに、geo リストア時には、次のセクションで説明するように、仮想ネットワーク設定やファイアウォール規則などの **[ネットワーク]** 設定を構成できます。

## <a name="using-restore-to-move-a-server-from-public-access-to-private-access"></a>復元を使用してサーバーをパブリック アクセスからプライベート アクセスに移動する

以下の手順に従って、既存の最も古いバックアップを使用して、フレキシブル サーバーを復元します。

1. [Azure portal](https://portal.azure.com/) で、バックアップの復元元のフレキシブル サーバーを選択します。

2. [概要] ページで、 **[復元]** をクリックします。

3. [復元] ページには、geo リストアまたはポイントインタイム リストア オプションを選ぶためのオプションが表示されます。

4. **[Geo-restore]\(geo リストア\)** または **[ポイントインタイム リストア]** オプションを選びます。

5. **[新しいサーバーに復元]** フィールドに新しいサーバー名を入力します。

    :::image type="content" source="./media/how-to-restore-server-portal/point-in-time-restore-private-dns-zone.png" alt-text="概要の表示":::

6. **[ネットワーク]** タブに移動し、ネットワーク設定を構成します。

7. **[接続方法]** で、 **[プライベート アクセス (VNet 統合)]** を選択します。 **[仮想ネットワーク]** セクションに移動し、*Microsoft.DBforMySQL/flexibleServers* に委任されている既存の "*仮想ネットワーク*" と "*サブネット*" を選択するか、 *[仮想ネットワークの作成]* リンクをクリックして新たに作成します。
    > [!Note]
    > ドロップ ダウンに表示されるのは、同じリージョンとサブスクリプション内の仮想ネットワークとサブネットのみです。 </br>
    > 選択したサブネットは、*Microsoft.DBforMySQL/flexibleServers* に委任されます。 つまり、Azure Database for MySQL フレキシブル サーバーのみがそのサブネットを使用できます。</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/vnet-creation.png" alt-text="VNet 構成":::

8. 既存の **プライベート DNS ゾーン** を選択するか、新たに作成します。
    > [!NOTE]
    > プライベート DNS ゾーン名は、`mysql.database.azure.com` で終わる必要があります。 </br>
    > 新しいプライベート DNS ゾーンを作成するオプションが表示されない場合は、 **[基本]** タブにサーバー名を入力してください。</br>
    > フレキシブル サーバーを仮想ネットワークとサブネットにデプロイした後は、パブリック アクセス (許可された IP アドレス) に移動することはできません。</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/private-dns-zone.png" alt-text="DNS の構成":::
9. **[確認と作成]** を選択して、フレキシブル サーバーの構成を確認します。
10. **[作成]** を選択して、サーバーをプロビジョニングします。 プロビジョニングには数分かかる場合があります。

11. 復元操作が開始されたことを示す通知が表示されます。


## <a name="perform-post-restore-tasks"></a>復元後のタスクの実行

復元が完了したら、次のタスクを実行してユーザーとアプリケーションを稼働状態に戻す必要があります。

- 元のサーバーを新しいサーバーで置き換える場合は、クライアントとクライアント アプリケーションを新しいサーバーにリダイレクトします。
- ユーザーが接続できるように、適切な VNet 規則が適用されていることを確認する。 これらのルールは配信元のサーバーからはコピーされません。
- 適切なログインとデータベース レベルのアクセス許可が指定されていることを確認します。
- 新しい復元サーバーに対して、必要に応じてアラートを構成します。

## <a name="next-steps"></a>次のステップ

[ビジネス継続性](concepts-business-continuity.md)について説明します。
