---
title: ソース管理の統合
description: Azure Repos (Git および GitHub) を使用したネイティブなソース管理の統合による、SQL プールのエンタープライズ クラスのデータベース DevOps エクスペリエンス。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: integration
ms.date: 08/23/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 28ce56675b8ba6b0f699b93af4411d360e1e582d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "80148061"
---
# <a name="source-control-integration-for-sql-pool"></a>SQL プールのためのソース管理の統合

このチュートリアルでは、SQL Server Data Tools (SSDT) データベース プロジェクトをソース管理と統合する方法について概説します。  ソース管理の統合は、Azure Synapse Analytics の SQL プール リソースを使用して継続的インテグレーションと継続的デプロイのパイプラインを構築するための最初の手順です。 

## <a name="before-you-begin"></a>開始する前に

- [Azure DevOps 組織](https://azure.microsoft.com/services/devops/)にサインアップします
- [作成と接続](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal)に関するチュートリアルを実行します
-  [Visual Studio 2019 をインストール](https://visualstudio.microsoft.com/vs/older-downloads/)します 

## <a name="set-up-and-connect-to-azure-devops"></a>Azure DevOps を設定して接続する

1. Azure DevOps 組織内で、Azure Repo リポジトリを介して SSDT データベース プロジェクトをホストするプロジェクトを作成します

   ![プロジェクトの作成](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Create Project")

2. Visual Studio を開き、[接続の管理] を選択して、手順 1 で作成した Azure DevOps 組織とプロジェクトに接続します

   ![接続の管理](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "接続の管理")

   ![のインスタンスに接続するときには、](./media/sql-data-warehouse-source-control-integration/3-connect.png "接続する")

3. プロジェクトからお使いのローカル コンピューターに Azure Repo リポジトリを複製します

   ![リポジトリの複製](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "リポジトリをクローンする")

## <a name="create-and-connect-your-project"></a>プロジェクトを作成して接続する

1. Visual Studio で、**ローカルの複製されたリポジトリ**に、ディレクトリとローカル Git リポジトリの両方を含む新しい SQL Server データベース プロジェクトを作成します

   ![新しいプロジェクトの作成](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "新しいプロジェクトの作成")  

2. 空の sqlproject を右クリックし、お使いのデータ ウェアハウスをデータベース プロジェクトにインポートします

   ![プロジェクトのインポート](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "プロジェクトのインポート")  

3. Visual Studio のチーム エクスプローラーで、ローカル Git リポジトリに対するすべての変更をコミットします 

   ![コミット](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Commit")  

4. これで、複製されたリポジトリで変更がローカルにコミットされたので、Azure DevOps プロジェクトの Azure Repo リポジトリに変更を同期してプッシュします。

   ![同期とプッシュ - ステージング](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "同期とプッシュ - ステージング")

   ![同期とプッシュ](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "同期とプッシュ")  

## <a name="validation"></a>検証

1. Visual Studio SQL Server Data Tools (SSDT) から対象のデータベース プロジェクトのテーブル列を更新して、変更が Azure Repo にプッシュされたことを確認します

   ![更新列を確認する](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "更新列を確認する")

2. 変更をコミットしてローカル リポジトリから Azure Repo にプッシュします

   ![変更をプッシュする](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "変更をプッシュする")

3. Azure Repo リポジトリに変更がプッシュされたことを確認します

   ![確認](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "変更を確認する")

4. (**オプション**) SSDT を使用して、スキーマ比較を使用し、お使いのターゲット データ ウェアハウスへの変更を更新して、Azure Repo リポジトリおよびローカル リポジトリ内のオブジェクト定義にお使いのデータ ウェアハウスが反映されていることを確認します

## <a name="next-steps"></a>次のステップ

- [SQL プールのための開発](sql-data-warehouse-overview-develop.md)
