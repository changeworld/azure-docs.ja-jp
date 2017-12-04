---
title: "Azure ポータルを使用して Azure Database Migration Service インスタンスを作成する | Microsoft Docs"
description: "Azure ポータルを使用して Azure Database Migration Service のインスタンスを作成します。"
services: database-migration
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/17/2017
ms.openlocfilehash: 9faac0716334d627cdde4c0ef16262670333b5d4
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2017
---
# <a name="create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Azure Portal を使用して Azure Database Migration Service のインスタンスを作成する
このクイック スタートでは、Azure ポータルを使用して Azure Database Migration Service のインスタンスを作成します。  サービスを作成すると、そのサービスを使用してオンプレミスの SQL Server から Azure SQL データベースにデータを移行することができます。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする
Web ブラウザーを開いて、[Microsoft Azure Portal](https://portal.azure.com/) にアクセスします。 資格情報を入力してポータルにサインインします。 既定のビューはサービス ダッシュボードです。

## <a name="register-the-resource-provider"></a>リソース プロバイダーの登録
最初の Database Migration Service を作成する前に、Microsoft.DataMigration リソース プロバイダーを登録する必要があります。

1. Azure Portal で、**[すべてのサービス]** を選択してから、**[サブスクリプション]** を選択します。

1. Azure Database Migration Service のインスタンスを作成するサブスクリプションを選択して、**[リソース プロバイダー]** を選択します。

1. 移行を検索し、Microsoft.DataMigration の右側にある **[登録]** を選択します。

![リソース プロバイダーの登録](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-azure-database-migration-service"></a>Azure Database Migration Service を作成する
1. 新しいサービスを作成するには、**+** をクリックします。  Database Migration Service はまだプレビュー段階にあります。  

1. マーケットプレースで「移行」のキーワードで検索し、[Database Migration Service (preview)]\(Database Migration Service (プレビュー)\) を選択して **[作成]** をクリックします。

    ![移行サービスの作成](media/quickstart-create-data-migration-service-portal/dms-create-service.png)

    - 覚えやすく、Azure Database Migration Service インスタンスを識別する一意の **[サービス名]** を選択します。
    - Database Migration Service を作成する Azure **サブスクリプション**を選択します。
    - 一意の名前の新しい**ネットワーク**を作成します。
    - ソースまたはターゲット サーバーに最も近い **[場所]** を選択します。
    - **[価格レベル]** に [Basic: 1 vCore]\(Basic: 1 仮想コア\) を選択します。

1. **Create** をクリックしてください。

しばらくすると、作成した Azure Database Migration Service が使用できるようになります。  Database Migration Service はイメージとして表示されます。

![作成した Migration Service](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ
クイックスタートで作成したリソースは、[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を削除することでクリーンアップできます。  リソース グループを削除するには、作成した Database Migration Service に移動して、**[リソース グループ]** 名をクリックしてから、**[リソース グループの削除]** を選択します。  この操作により、リソース グループの中にあるすべてのアセットだけでなく、グループ自体も削除されます。

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [SQL Server をオンプレミスの Azure SQL DB に移行する](tutorial-sql-server-to-azure-sql.md)