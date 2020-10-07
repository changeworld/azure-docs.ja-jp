---
title: クイック スタート:Azure DB for MySQL フレキシブル サーバーを作成する - Azure portal
description: この記事では、Azure portal を使用して、数分ですばやく Azure Database for MySQL フレキシブル サーバーを作成する手順について説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/29/2020
ms.openlocfilehash: 70f2cf183a9bd93b6066516cb68e99ee21cdc1ac
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "91569622"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>クイック スタート:Azure portal を使用して Azure Database for MySQL フレキシブル サーバーを作成する

Azure Database for MySQL フレキシブル サーバーは、高可用性のある MySQL データベースをクラウドで実行し、管理し、スケーリングするために使用されるマネージド サービスです。 このクイックスタートでは、Azure portal を使用して短時間でフレキシブル サーバーを作成する方法について説明します。

> [!IMPORTANT] 
> Azure Database for MySQL フレキシブル サーバーは現在、パブリック プレビュー段階にあります

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする
Web ブラウザーを開き、[Azure Portal](https://portal.azure.com/) に移動します。 資格情報を入力してポータルにサインインします。 既定のビューはサービス ダッシュボードです。

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL フレキシブル サーバーを作成する

フレキシブル サーバーは、定義済みの一連の[コンピューティング リソースとストレージ リソース](./concepts-compute-storage.md)を使って作成します。 サーバーは、[Azure リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)内に作成します。

フレキシブル サーバーを作成するには、次の手順に従います。

1. ポータルの左上隅にある **[リソースの作成]** (+) を選択します。

2. **[データベース]**  >  **[Azure Database for MySQL]** の順に選択します。 検索ボックスに「**MySQL**」と入力してサービスを検索することもできます。

    > :::image type="content" source="./media/quickstart-create-server-portal/navigate-to-mysql.png" alt-text="Azure Database for MySQL オプション":::

3. デプロイ オプションとして **[フレキシブル サーバー]** を選択します。
     
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="Azure Database for MySQL オプション":::    

4. **[基本]** フォームに以下の情報を入力します。 

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="Azure Database for MySQL オプション" は、それぞれ **[コンピューティング レベル]** 、 **[コンピューティング サイズ]** 、 **[ストレージ]** 、および **[バックアップの保有期間]** の既定値です。 これらのスライダーはそのままにすることも、調整することもできます。 このコンピューティングとストレージの選択を保存するには、 **[保存]** を選択して構成を続行します。 次のスクリーンショットは、コンピューティングとストレージのオプションを示しています。|
    
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="Azure Database for MySQL オプション" に変更することはできません。 VNet 統合を使用してサーバーに安全にアクセスするには、プライベート アクセスを指定してサーバーを作成することを強くお勧めします。 プライベート アクセスの詳細については、[概念に関する記事](./concepts-networking.md)をご覧ください。

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="Azure Database for MySQL オプション":::  

6. **[確認と作成]** を選択して、フレキシブル サーバーの構成を確認します。

7. **[作成]** を選択して、サーバーをプロビジョニングします。 プロビジョニングには数分かかる場合があります。

8. デプロイ プロセスを監視するには、ツール バーの **[通知]** (ベル アイコン) を選択します。 デプロイが完了したら、 **[ダッシュボードにピン留めする]** を選択できます。これにより、このフレキシブル サーバーのタイルが、サーバーの **[概要]** ページへのショートカットとして、Azure portal ダッシュボードに作成されます。 **[リソースに移動]** を選択すると、サーバーの **[概要]** ページが開きます。

既定では、データベース **information_schema**、**mysql**、**performance_schema**、および **sys** は、ご利用のサーバーに作成されます。

> [!NOTE]
> 接続の問題が発生しないように、Azure Database for MySQL フレキシブル サーバーで使用されるポート 3306 経由のアウトバウンド トラフィックが、ネットワークによって許可されているかどうかを確認します。  

## <a name="connect-to-using-mysql-command-line-client"></a>mysql コマンドライン クライアントを使用して接続する

"*プライベート アクセス (VNet 統合)* " を使用してフレキシブル サーバーを作成した場合は、サーバーと同じ VNet 内のリソースからサーバーに接続する必要があります。 仮想マシンを作成し、それをフレキシブル サーバーと共に作成された VNet に追加できます。

"*パブリック アクセス (使用できる IP アドレス)* " を使用してフレキシブル サーバーを作成した場合は、サーバー上のファイアウォール規則の一覧にローカル IP アドレスを追加できます。

ローカル環境からサーバーに接続するには、[mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) または [MySQL Workbench](./connect-workbench.md) を選択できます。 

mysql.exe では、次のコマンドを使用して接続します。 値は実際のサーバー名とパスワードに置き換えてください。 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```
## <a name="clean-up-resources"></a>リソースのクリーンアップ
リソース グループに Azure Database for MySQL フレキシブル サーバーが正常に作成されました。  これらのリソースが今後不要である思われる場合は、リソース グループを削除してリソースを削除することも、単にこの MySQL サーバーを削除することもできます。 リソース グループを削除するには、次の手順を実行します。

1. Azure portal で、「**リソース グループ**」を検索して選択します。
1. リソース グループの一覧で、リソース グループの名前を選択します。
1. リソース グループの [概要] ページで、 **[リソース グループの削除]** を選択します。
1. 確認のダイアログ ボックスでリソース グループの名前を入力し、 **[削除]** を選択します。

サーバーを削除するには、次に示すように、サーバーの **[概要]** ページで **[削除]** ボタンをクリックします。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="Azure Database for MySQL オプション":::

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [MySQL を使用した PHP (Laravel) Web アプリを作成する](tutorial-php-database-app.md)
