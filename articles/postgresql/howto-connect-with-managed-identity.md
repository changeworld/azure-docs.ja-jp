---
title: マネージド ID を使用して接続する - Azure Database for PostgreSQL - Single Server
description: Azure Database for PostgreSQL での認証のための、マネージド ID を使用した接続および認証の方法について説明します。
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 05/19/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 8d7e05ba1e4a21ded5d90de6bea301e4cd94451a
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547650"
---
# <a name="connect-with-managed-identity-to-azure-database-for-postgresql"></a>マネージド ID を使用して Azure Database for PostgreSQL に接続する

システム割り当てとユーザー割り当ての両方のマネージド ID を使用して、Azure Database for PostgreSQL に対する認証を行うことができます。 この記事では、Azure 仮想マシン (VM) のシステム割り当てマネージド ID を使って、Azure Database for PostgreSQL サーバーにアクセスする方法について説明します。 マネージド ID は Azure によって自動的に管理され、それを使用すると、資格情報をコードに挿入しなくても、Azure AD 認証をサポートするサービスで認証を行うことができます。

学習内容は次のとおりです。
- VM に Azure Database for PostgreSQL サーバーへのアクセスを許可する
- VM のシステム割り当て ID を表すユーザーをデータベースに作成する
- VM ID を使用してアクセス トークンを取得し、それを使用して Azure Database for PostgreSQL サーバーにクエリを実行する
- C# サンプル アプリケーションにトークン取得を実装する

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID 機能に慣れていない場合は、こちらの[概要](../../articles/active-directory/managed-identities-azure-resources/overview.md)を参照してください。 Azure アカウントをお持ちでない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- 必要なリソース作成およびロール管理を実行するため、お使いのアカウントには、適切な範囲 (サブスクリプションまたはリソース グループ) を対象とする "所有者" アクセス許可が必要となります。 ロールの割り当てに関するサポートが必要な場合は、[Azure ロールの割り当てによる Azure サブスクリプション リソースへのアクセスの管理](../../articles/role-based-access-control/role-assignments-portal.md)に関するページをご覧ください。
- マネージド ID を使用したデータベースへのアクセスに使用する Azure VM (Ubuntu Linux を実行しているものなど) が必要
- [Azure AD 認証](howto-configure-sign-in-aad-authentication.md)が構成されている Azure Database for PostgreSQL データベース サーバーが必要
- C# のサンプルを理解するため、[C# を使用した接続](connect-csharp.md)方法に関するガイドにまず目を通している

## <a name="creating-a-system-assigned-managed-identity-for-your-vm"></a>VM 用のシステム割り当てマネージド ID の作成

[az vm identity assign](/cli/azure/vm/identity/) と `identity assign` コマンドを使用して、既存の VM に対するシステム割り当て ID を有効にします。

```azurecli-interactive
az vm identity assign -g myResourceGroup -n myVm
```

システム割り当てマネージド ID のアプリケーション ID を取得します。これは、次のいくつかの手順で必要です。

```azurecli
# Get the client ID (application ID) of the system-assigned managed identity
az ad sp list --display-name vm-name --query [*].appId --out tsv
```

## <a name="creating-a-postgresql-user-for-your-managed-identity"></a>マネージド ID の PostgreSQL ユーザーを作成する

次に、Azure AD 管理者ユーザーとして PostgreSQL データベースに接続し、次の SQL ステートメントを実行します。`CLIENT_ID` は、システム割り当てマネージド ID 用に取得したクライアント ID に置き換えます。

```sql
SET aad_validate_oids_in_tenant = off;
CREATE ROLE myuser WITH LOGIN PASSWORD 'CLIENT_ID' IN ROLE azure_ad_user;
```

これで、マネージド ID に、ユーザー名 `myuser` (任意の名前に置き換えてください) を使用して認証するときのアクセス権が付与されました。

## <a name="retrieving-the-access-token-from-azure-instance-metadata-service"></a>Azure Instance Metadata Service からアクセス トークンを取得する

この段階で、アプリケーションは Azure Instance Metadata Service からアクセス トークンを取得して、データベースでの認証に使用できるようになっています。

このトークンの取得は、`http://169.254.169.254/metadata/identity/oauth2/token` に対して HTTP 要求を実行し、次のパラメーターを渡すことによって行われます。

* `api-version` = `2018-02-01`
* `resource` = `https://ossrdbms-aad.database.windows.net`
* `client_id` = `CLIENT_ID` (前の手順で取得したもの)

`access_token` フィールドを含む JSON の結果が返されます。この長いテキスト値がマネージド ID アクセス トークンで、データベースに接続する際にこれをパスワードとして使用する必要があります。

テストのために、シェルで次のコマンドを実行できます。 `curl`、`jq`、`psql` クライアントがインストールされている必要があることにご注意ください。

```bash
# Retrieve the access token
export PGPASSWORD=`curl -s 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=CLIENT_ID' -H Metadata:true | jq -r .access_token`

# Connect to the database
psql -h SERVER --user USER@SERVER DBNAME
```

これで、先ほど構成したデータベースに接続できました。

## <a name="connecting-using-managed-identity-in-c"></a>C# でマネージド ID を使用して接続する

このセクションでは、VM のユーザー割り当てマネージド ID を使用してアクセス トークンを取得し、それを使用して Azure Database for PostgreSQL を呼び出す方法を説明します。 Azure Database for PostgreSQL では Azure AD 認証がネイティブにサポートされるため、Azure リソースのマネージド ID を使用して取得されたアクセス トークンを直接受け入れることができます。 PostgreSQL への接続を作成する場合は、[パスワード] フィールドにアクセス トークンを渡します。

アクセス トークンを使用して PostgreSQL への接続を開く .NET のコード例を次に示します。 システム割り当てマネージド ID を使用して、Azure AD からアクセス トークンを取得するには、このコードを VM 上で実行する必要があります。 HOST、USER、DATABASE、CLIENT_ID の値は置き換えてご使用ください。

```csharp
using System;
using System.Net;
using System.IO;
using System.Collections;
using System.Collections.Generic;
using System.Text.Json;
using System.Text.Json.Serialization;
using Npgsql;
using Azure.Identity;

namespace Driver
{
    class Script
    {
        // Obtain connection string information from the portal for use in the following variables
        private static string Host = "HOST";
        private static string User = "USER";
        private static string Database = "DATABASE";

        static async Task Main(string[] args)
        {
            //
            // Get an access token for PostgreSQL.
            //
            Console.Out.WriteLine("Getting access token from Azure AD...");

            // Azure AD resource ID for Azure Database for PostgreSQL is https://ossrdbms-aad.database.windows.net/
            string accessToken = null;

            try
            {
                // Call managed identities for Azure resources endpoint.
                var sqlServerTokenProvider = new DefaultAzureCredential();
                accessToken = (await sqlServerTokenProvider.GetTokenAsync(
                    new Azure.Core.TokenRequestContext(scopes: new string[] { "https://ossrdbms-aad.database.windows.net/.default" }) { })).Token;

            }
            catch (Exception e)
            {
                Console.Out.WriteLine("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                System.Environment.Exit(1);
            }

            //
            // Open a connection to the PostgreSQL server using the access token.
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4}; SSLMode=Prefer",
                    Host,
                    User,
                    Database,
                    5432,
                    accessToken);

            using (var conn = new NpgsqlConnection(connString))
            {
                Console.Out.WriteLine("Opening connection using access token...");
                conn.Open();

                using (var command = new NpgsqlCommand("SELECT version()", conn))
                {

                    var reader = command.ExecuteReader();
                    while (reader.Read())
                    {
                        Console.WriteLine("\nConnected!\n\nPostgres version: {0}", reader.GetString(0));
                    }
                }
            }
        }
    }
}
```

このコマンドを実行すると、次のような出力が表示されます。

```
Getting access token from Azure AD...
Opening connection using access token...

Connected!

Postgres version: PostgreSQL 11.11, compiled by Visual C++ build 1800, 64-bit
```

## <a name="next-steps"></a>次のステップ

* [Azure Database for PostgreSQL を使用した Azure Active Directory 認証](concepts-aad-authentication.md)の全体的な概念を確認する
