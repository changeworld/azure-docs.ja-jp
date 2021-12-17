---
title: Azure Database for PostgreSQL フレキシブル サーバーの CLI エラーのトラブルシューティング
description: このトピックでは、PostgreSQL フレキシブル サーバーを使用している場合の Azure CLI に関する一般的な問題のトラブルシューティングについてのガイダンスを提供します。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 08/24/2021
ms.openlocfilehash: dd44e0bf0ffd7ae70cdb2b715561517d5b92a049
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440060"
---
# <a name="troubleshoot-azure-database-for-postgresql-flexible-server-cli-errors"></a>Azure Database for PostgreSQL フレキシブル サーバーの CLI エラーのトラブルシューティング

このドキュメントは、PostgreSQL フレキシブル サーバーを使用している場合の Azure CLI に関する一般的な問題のトラブルシューティングに役立ちます。

## <a name="command-not-found"></a>コマンドが見つからない

 コマンドの **スペルが間違っている、またはシステム によって認識されない** というエラーが表示される場合。 これは、クライアント コンピューター上の CLI バージョンが最新ではない可能性があることを意味する場合があります。 ```az upgrade``` を実行して、最新バージョンにアップグレードします。 CLI バージョンのアップグレードを行うと、API の変更によるコマンドの非互換性に関する問題を解決するのに役立つ可能性があります。
 
## <a name="debug-deployment-failures"></a>デプロイの失敗のデバッグ 
現在、Azure CLI ではデバッグ ログの有効化がサポートされていませんが、以下の手順に従ってデバッグ ログを取得できます。

>[!NOTE]
> - ```examplegroup``` と ```exampledeployment``` を、データベース サーバーの正しいリソース グループとデプロイ名に置き換えます。 
> - デプロイ名は、リソース グループのデプロイ ページで確認できます。 [デプロイ名を見つける方法](../../azure-resource-manager/templates/deployment-history.md?tabs=azure-portal)に関するページを参照してください


1. リソース グループ内のデプロイを一覧表示して PostgreSQL サーバーのデプロイを識別します 
    ```azurecli

        az deployment operation group list \
          --resource-group examplegroup \
          --name exampledeployment
    ```

2. PostgreSQL サーバーのデプロイの要求コンテンツを取得します 
    ```azurecli

        az deployment operation group list \
          --name exampledeployment \
          -g examplegroup \
          --query [].properties.request
    ```
3. 応答の内容を確認します 
    ```azurecli
    az deployment operation group list \
      --name exampledeployment \
      -g examplegroup \
      --query [].properties.response
    ```

## <a name="error-codes"></a>エラー コード

| エラー コード | 対応策 |
| ---------- | ---------- | 
|MissingSubscriptionRegistration|リソース プロバイダーにサブスクリプションを登録してください。 コマンド ```az provider register --namespace Microsoft.DBPostgreSQL``` を実行して、問題を解決します。|
|InternalServerError| サーバーのアクティビティ ログを表示して、詳細情報があるかどうかを確認してください。 コマンド ```az monitor activity-log list --correlation-id <enter correlation-id>``` を実行します。 数分後に同じ CLI コマンドを試してみることができます。 問題が解決しない場合は、[それを報告する](https://github.com/Azure/azure-cli/issues)か、Microsoft サポートにお問い合わせください。|
|ResourceNotFound| 参照されているリソースが見つかりません。  リソースのプロパティを確認するか、リソースが削除されたかどうかを確認するか、リソースが別のサブスクリプションであるかどうかを確認できます。 |
|LocationNotAvailableForResourceType| - [Azure リージョン](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql)での Azure Database for Postgres フレキシブル サーバーの可用性を確認します。 <br>- Azure DB for PostgreSQL リソースの種類がサブスクリプションに登録されているかどうかを確認します。 |
|ResourceGroupBeingDeleted| リソース グループが削除中です。 削除が完了するまで待ちます。|
|PasswordTooLong| 指定されたパスワードが長すぎます。 8 ～ 128 文字にする必要があります。 パスワードには、英大文字、英小文字、数字 (0 から 9)、英数字以外の文字 (!、$、#、% など) のうち、3 つのカテゴリの文字が含まれている必要があります。|
|PasswordNotComplex| 指定されたパスワードの複雑さが十分ではありません。  8 ～ 128 文字にする必要があります。 パスワードには、英大文字、英小文字、数字 (0 から 9)、英数字以外の文字 (!、$、#、% など) のうち、3 つのカテゴリの文字が含まれている必要があります。|
|PasswordTooShort| 8 ～ 128 文字にする必要があります。 パスワードには、英大文字、英小文字、数字 (0 から 9)、英数字以外の文字 (!、$、#、% など) のうち、3 つのカテゴリの文字が含まれている必要があります。|
|SubscriptionNotFound| 要求されたサブスクリプションが見つかりませんでした。 ```az account list all``` を実行して、現在のすべてのサブスクリプションを確認します。|
|InvalidParameterValue| パラメーターに無効な値が指定されました。[CLI リファレンス ドキュメント](/cli/azure/postgres/flexible-server?view=azure-cli-latest&preserve-view=true)を確認して、引数でサポートされている正しい値を確認してください。|
|InvalidLocation| 無効な場所が指定されました。 [Azure リージョン](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql)での Azure Database for Postgres フレキシブル サーバーの可用性を確認します |
|InvalidServerName|無効なサーバー名が識別されました。 サーバー名を確認してください。 コマンド [az mysql flexible-server list](/cli/azure/mysql/flexible-server?view=azure-cli-latest#az_mysql_flexible_server_list&preserve-view=true) を実行して、使用可能なすべてのフレキシブル サーバーの一覧を表示します。|
|InvalidResourceIdSegment| Azure Resource Manager テンプレートで構文エラーが特定されました。 JSON フォーマッタ ツールを使用して JSON を検証し、構文エラーを特定してください。|
|InvalidUserName| 有効なユーザー名を入力してください。 管理者のユーザー名に、azure_superuser、azure_pg_admin, admin、administrator、root、guest、public は使用できません。 pg_ で始めることはできません。|
|BlockedUserName| 管理者のユーザー名に、azure_superuser、azure_pg_admin, admin、administrator、root、guest、public は使用できません。 pg_ で始めることはできません。 管理者の名前にこれらのパターンを使用しないでください。|

## <a name="next-steps"></a>次のステップ

- まだ問題が発生している場合は、[問題を報告](https://github.com/Azure/azure-cli/issues)してください。 
- ご質問がある場合は、Stack Overflow ページ https://aka.ms/azcli/questions を参照してください。 
- このアンケート https://aka.ms/azureclihats でどのように行っているのかをお知らせください。 
