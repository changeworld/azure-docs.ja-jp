---
title: 'チュートリアル: Azure Static Web Apps から Mongoose を使用して Cosmos DB のデータにアクセスする'
description: Azure Static Web Apps API 関数から Mongoose を使用して Cosmos DB のデータにアクセスする方法について説明します。
author: GeekTrainer
ms.author: chrhar
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 01/25/2021
ms.openlocfilehash: f64cc67ad6f0296ad289d858795ee783943f3daf
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259877"
---
# <a name="tutorial-access-data-in-cosmos-db-using-mongoose-with-azure-static-web-apps"></a>チュートリアル: Azure Static Web Apps から Mongoose を使用して Cosmos DB のデータにアクセスする

[Mongoose](https://mongoosejs.com/) とは、Node.js で最もよく使われている ODM (Object Document Mapping) クライアントです。 Mongoose では、データ構造を設計したり検証を適用したりできるため、Mongoose API をサポートするデータベースとの対話操作に必要なあらゆるツールが用意されています。 [Cosmos DB](../cosmos-db/mongodb-introduction.md) は、必要な Mongoose API をサポートしており、Azure のバックエンド サーバー オプションとして利用できます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> - Cosmos DB サーバーレス アカウントを作成する
> - Azure 静的 Web アプリを作成する
> - 接続文字列を格納するようにアプリケーション設定を更新する

Azure サブスクリプションを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="prerequisites"></a>前提条件

- [Azure アカウント](https://azure.microsoft.com/free/)
- [GitHub アカウント](https://github.com/join)

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-a-cosmos-db-serverless-database"></a>Cosmos DB サーバーレス データベースを作成する

まず、[Cosmos DB サーバーレス](https://docs.microsoft.com/azure/cosmos-db/serverless) アカウントを作成します。 サーバーレス アカウントを使用すれば、支払いはリソースが使用されたときにのみ発生し、また、完全なインフラストラクチャを作成せずに済みます。

1. [https://portal.azure.com](https://portal.azure.com) に移動します
2. **[リソースの作成]** をクリックします
3. 検索ボックスに「**Azure Cosmos DB**」と入力します
4. **[Azure Cosmos DB]** をクリックします
5. **[作成]**
6. 次の情報を使用して Azure Cosmos DB アカウントを構成します
    - [サブスクリプション]: 使用するサブスクリプションを選択します
    - [リソース]: **[新規作成]** をクリックし、名前を **aswa-mongoose** に設定します
    - [アカウント名]: 一意の値を入力する必要があります
    - [API]: **Azure Cosmos DB for MongoDB API**
    - [Notebooks (プレビュー)]: **オフ**
    - [場所]: **米国西部 2**
    - [容量モード]: **サーバーレス (プレビュー)**
    - [バージョン]: **3.6**
    - [可用性ゾーン]: **無効**
:::image type="content" source="media/add-mongoose/cosmos-db.png" alt-text="新しい Cosmos DB インスタンスを作成する":::
7. **[確認と作成]** をクリックします。
8. **[作成]**

この作成プロセスには数分かかります。 後述する手順でデータベースに戻り、接続文字列を収集します。

## <a name="create-a-static-web-app"></a>静的 Web アプリを作成する

このチュートリアルでは、アプリケーションの作成に役立つ GitHub テンプレート リポジトリを使用します。

1. [スターター テンプレート](https://github.com/login?return_to=/staticwebdev/mongoose-starter/generate)に移動します
2. **所有者** を選択します (メイン アカウント以外の組織を使用している場合)
3. リポジトリに **aswa-mongoose-tutorial** という名前を付けます
4. **[Create repository from template]\(テンプレートからリポジトリを作成する\)** をクリックします
5. [Azure portal](https://portal.azure.com) に戻ります
6. **[リソースの作成]** をクリックします
7. 検索ボックスに「**静的 Web アプリ**」と入力します
8. **[静的 Web アプリ (プレビュー)]** を選択します
9. **[作成]**
10. 次の情報を使用して Azure 静的 Web アプリを構成します
    - [サブスクリプション]: 先ほどと同じサブスクリプションを選択します
    - [リソース グループ]: **[aswa-mongoose]** を選択します
    - [名前]: **aswa-mongoose-tutorial**
    - [リージョン]: **米国西部 2**
    - **[GitHub アカウントでサインイン]** をクリックします
    - デプロイを可能にする GitHub アクションの作成を Azure Static Web Apps に許可するよう求められたら、 **[承認]** をクリックします
    - [組織]: アカウント名
    - [リポジトリ]: **aswa-mongoose-tutorial**
    - [ブランチ]: **main**
    - [ビルドのプリセット]: **[Custom]\(カスタム\)** を選択します
    - [App location]\(アプリの場所\): **/public**
    - [Api location]\(API の場所\): **api**
    - [App artifact location]\(アプリ成果物の場所\): "*空白のままにします*"
    :::image type="content" source="media/add-mongoose/azure-static-web-apps.png" alt-text="入力済みの Azure 静的 Web アプリ フォーム":::
11. **[確認と作成]** をクリックします
12. **[作成]**

この作成プロセスには数分かかりますが、アプリがプロビジョニングされたら **[リソースに移動]** をクリックしてかまいません。

## <a name="configure-database-connection-string"></a>データベース接続文字列を構成する

Web アプリがデータベースと通信を行えるように、データベース接続文字列がアプリケーション設定として格納されます。 Node.js では、`process.env` オブジェクトを使用して設定値にアクセスできます。

1. Azure portal の左上隅にある **[ホーム]** をクリックします (または [https://portal.azure.com](https://portal.azure.com) に戻ります)
2. **[リソース グループ]** をクリックします
3. **[aswa-mongoose]** をクリックします
4. データベース アカウントの名前をクリックします。その種類は、 **[Azure Cosmos DB アカウント]** となります
5. **[設定]** で **[接続文字列]** をクリックします
6. **[プライマリ接続文字列]** に表示されている接続文字列をコピーします
7. 階層リンクの **[aswa-mongoose]** をクリックします
8. **[aswa-mongoose-tutorial]** をクリックして Web サイト インスタンスに戻ります
9. **[設定]** の **[構成]** をクリックします
10. **[追加]** をクリックして、次の値で新しいアプリケーション設定を作成します
    - [名前]: **CONNECTION_STRING**
    - [値]: 先ほどコピーした接続文字列を貼り付けます
11. **[OK]**
12. **[保存]**

## <a name="navigate-to-your-site"></a>サイトに移動する

では、この静的 Web アプリを詳しく見ていきましょう。

1. **[概要]** をクリックします
1. 右上に表示されている URL をクリックします
    1. これは `https://calm-pond-05fcdb.azurestaticapps.net` のようになります
1. タイトルを入力し、 **[タスクの追加]** をクリックして、新しいタスクを作成します
1. タスクが表示されたことを確認します (しばらく時間がかかる場合があります)
1. **チェック ボックスをオン** にして、タスクを完了としてマークします
1. **ページを最新の情報に更新** して、データベースが使用されていることを確認します

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、次の手順でリソース グループ全体を削除します。

1. [Azure portal](https://portal.azure.com) に戻ります
2. **[リソース グループ]** をクリックします
3. **[aswa-mongoose]** をクリックします
4. **[リソース グループの削除]** をクリックします
5. テキストボックスに「**aswa-mongoose**」と入力します
6. **[削除]** をクリックします

## <a name="next-steps"></a>次のステップ

次の記事に進み、ローカル開発の構成方法を学習してください。
> [!div class="nextstepaction"]
> [ローカル開発の設定](./local-development.md)
 
