---
title: Azure portal を使用して Azure Database for MySQL フレキシブル サーバーを復元する
description: この記事では、Azure portal を使用して Azure Database for MySQL フレキシブル サーバーで復元操作を実行する方法について説明します
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 687d9386d330e5b09366e22ace8f7fd8666ee9d9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781065"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>Azure portal を使用した Azure Database for MySQL - フレキシブル サーバー (プレビュー) のポイントインタイム リストア

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL - フレキシブル サーバーは現在、パブリック プレビュー段階にあります。

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

## <a name="using-restore-to-move-a-server-from-public-access-to-private-access"></a>復元を使用してサーバーをパブリック アクセスからプライベート アクセスに移動する

以下の手順に従って、既存の最も古いバックアップを使用して、フレキシブル サーバーを復元します。

1. [Azure portal](https://portal.azure.com/) で、バックアップの復元元のフレキシブル サーバーを選択します。

2. [概要] ページで、 **[復元]** をクリックします。

3. [復元] ページに、最も古い復元ポイントとカスタム復元ポイントのどちらかを選択するオプションが表示されます。

4. **[最初の復元ポイント]** または **[カスタム復元ポイント]** のどちらかを選択します。

5. **[新しいサーバーに復元]** フィールドに新しいサーバー名を入力します。

6. **[新しいサーバーに復元]** フィールドに新しいサーバー名を入力します。

    :::image type="content" source="./media/how-to-restore-server-portal/point-in-time-restore-private-dns-zone.png" alt-text="概要の表示":::

7. **[ネットワーク]** タブに移動し、ネットワーク設定を構成します。

8. **[接続方法]** で、 **[プライベート アクセス (VNet 統合)]** を選択します。 **[仮想ネットワーク]** セクションに移動し、*Microsoft.DBforMySQL/flexibleServers* に委任されている既存の "*仮想ネットワーク*" と "*サブネット*" を選択するか、 *[仮想ネットワークの作成]* リンクをクリックして新たに作成します。
    > [!Note]
    > ドロップ ダウンに表示されるのは、同じリージョンとサブスクリプション内の仮想ネットワークとサブネットのみです。 </br>
    > 選択したサブネットは、*Microsoft.DBforMySQL/flexibleServers* に委任されます。 つまり、Azure Database for MySQL フレキシブル サーバーのみがそのサブネットを使用できます。</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/vnet-creation.png" alt-text="VNet 構成":::

9. 既存の **プライベート DNS ゾーン** を選択するか、新たに作成します。
    > [!NOTE]
    > プライベート DNS ゾーン名は、`mysql.database.azure.com` で終わる必要があります。 </br>
    > 新しいプライベート DNS ゾーンを作成するオプションが表示されない場合は、 **[基本]** タブでサーバー名を入力してください。</br>
    > フレキシブル サーバーを仮想ネットワークとサブネットにデプロイした後は、パブリック アクセス (許可された IP アドレス) に移動することはできません。</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/private-dns-zone.png" alt-text="DNS の構成":::
10. **[確認と作成]** を選択して、フレキシブル サーバーの構成を確認します。
11. **[作成]** を選択して、サーバーをプロビジョニングします。 プロビジョニングには数分かかる場合があります。

12. 復元操作が開始されたことを示す通知が表示されます。

## <a name="perform-post-restore-tasks"></a>復元後のタスクの実行

復元が完了したら、次のタスクを実行してユーザーとアプリケーションを稼働状態に戻す必要があります。

- 元のサーバーを新しいサーバーで置き換える場合は、クライアントとクライアント アプリケーションを新しいサーバーにリダイレクトします。
- ユーザーが接続できるように、適切な VNet 規則が適用されていることを確認する。 これらのルールは配信元のサーバーからはコピーされません。
- 適切なログインとデータベース レベルのアクセス許可が指定されていることを確認します。
- 新しい復元サーバーに対して、必要に応じてアラートを構成します。

## <a name="next-steps"></a>次のステップ

[ビジネス継続性](concepts-business-continuity.md)について説明します。
