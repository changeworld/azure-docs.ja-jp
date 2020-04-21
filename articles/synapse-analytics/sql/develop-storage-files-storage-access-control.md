---
title: SQL オンデマンド (プレビュー) のストレージ アカウント アクセスを制御する
description: SQL オンデマンド (プレビュー) が Azure Storage にアクセスする方法と、Azure Synapse Analytics で SQL オンデマンドのストレージ アクセスを制御する方法について説明します。
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0d2683091898e9c84457b3b538776f0e6b0469d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420056"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics で SQL オンデマンド (プレビュー) のストレージ アカウント アクセスを制御する

SQL オンデマンド (プレビュー) のクエリは、Azure Storage から直接ファイルを読み取ります。 ストレージ アカウントは SQL オンデマンド リソースの外部のオブジェクトであるため、適切な資格情報が必要になります。 必要な資格情報を使用するために、ユーザーには、適切なアクセス許可が付与されている必要があります。 この記事では、使用できる資格情報の種類と、SQL および Azure AD のユーザーに対して資格情報の参照がどのように実行されるかについて説明します。

## <a name="supported-storage-authorization-types"></a>サポートされているストレージ承認の種類

SQL オンデマンド リソースにログインしたユーザーは、Azure Storage 内のファイルにアクセスしてクエリを実行する権限を持っている必要があります。 次の 3 種類の承認がサポートされています。

- [共有アクセス署名](#shared-access-signature)
- [Managed Identity](#managed-identity)
- [ユーザー ID](#user-identity)

> [!NOTE]
> [Azure AD パススルー](#force-azure-ad-pass-through)は、ワークスペースを作成するときの既定の動作です。 これを使用すると、AD ログインを使用してアクセスする各ストレージ アカウントの資格情報を作成する必要がありません。 [この動作を無効にする](#disable-forcing-azure-ad-pass-through)ことができます。

次の表に、サポートされている、または今後サポートされる予定のさまざまな承認の種類を示します。

| 承認の種類                    | *SQL ユーザー*    | *Azure AD ユーザー*     |
| ------------------------------------- | ------------- | -----------    |
| [SAS](#shared-access-signature)       | サポートされています     | サポートされています      |
| [Managed Identity](#managed-identity) | サポートされていません | サポートされていません  |
| [ユーザー ID](#user-identity)       | サポートされていません | サポートされています      |

### <a name="shared-access-signature"></a>共有アクセス署名

**Shared Access Signature (SAS)** を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。 SAS を使用すると、お客様はアカウント キーを共有することなく、クライアントにストレージ アカウントのリソースへのアクセス権を付与できます。 SAS を使用すると、有効期間、付与されるアクセス許可、許容される IP アドレス範囲、受け入れ可能なプロトコル (https/http) など、SAS を使用しているクライアントに付与するアクセス権の種類をきめ細かく制御できます。

SAS トークンを取得するには、**Azure portal -> [ストレージ アカウント] -> [Shared Access Signature] -> [アクセス許可の構成] -> [Generate SAS and connection string]\(SAS と接続文字列を生成する\)** に移動します。

> [!IMPORTANT]
> SAS トークンが生成されると、トークンの先頭に疑問符 ("?") が含まれます。 SQL オンデマンドでトークンを使用するには、資格情報を作成するときに疑問符 ("?") を削除する必要があります。 次に例を示します。
>
> SAS トークン: ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D

### <a name="user-identity"></a>ユーザー ID

**ユーザー ID** ("パススルー" とも呼ばれる) は、SQL オンデマンドにログインしている Azure AD ユーザーの ID がデータ アクセスの承認に使用される承認の種類です。 データにアクセスする前に、Azure Storage の管理者が Azure AD ユーザーにアクセス許可を付与する必要があります。 上の表に示されているように、これは SQL ユーザーの種類ではサポートされていません。

> [!NOTE]
> [Azure AD パススルー](#force-azure-ad-pass-through)を使用する場合は、AD ログインを使用してアクセスする各ストレージ アカウントの資格情報を作成する必要はありません。

> [!IMPORTANT]
> ID を使用してデータにアクセスするには、Storage Blob データの所有者/共同作成者/閲覧者のロールを持っている必要があります。
> ストレージ アカウントの所有者であっても、Storage Blob データのいずれかのロールに自分自身を追加する必要があります。
>
> Azure Data Lake Store Gen2 でのアクセス制御の詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](../../storage/blobs/data-lake-storage-access-control.md)」という記事をご覧ください。
>

### <a name="managed-identity"></a>マネージド ID

**マネージ ID** は MSI とも呼ばれます。 これは、SQL オンデマンドに Azure サービスを提供する Azure Active Directory (Azure AD) の機能です。 また、Azure AD で自動的に管理される ID をデプロイします。 この ID を使用して、Azure Storage でのデータ アクセスの要求を承認できます。

データにアクセスする前に、Azure Storage の管理者が、データにアクセスするためのアクセス許可をマネージド ID に付与する必要があります。 マネージド ID へのアクセス許可の付与は、他の Azure AD ユーザーにアクセス許可を付与するのと同じ方法で行われます。

## <a name="create-credentials"></a>資格情報を作成する

Azure Storage にあるファイルに対してクエリを実行するには、SQL オンデマンド エンドポイントに、認証情報を含むサーバー レベルの資格情報が必要です。 資格情報を追加するには、[CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) を実行します。 CREDENTIAL NAME 引数の指定が必要になります。 それは、ストレージ内のデータへのパスの一部またはパス全体に一致している必要があります (下記参照)。

> [!NOTE]
> FOR CRYPTOGRAPHIC PROVIDER 引数はサポートされていません。

サポートされているすべての承認の種類について、資格情報は、アカウント、コンテナー、任意のディレクトリ (ルート以外)、または 1 つのファイルを指すことができます。

CREDENTIAL NAME は、次の形式で、コンテナー、フォルダー、またはファイルへの完全パスと一致している必要があります。`<prefix>://<storage_account_path>/<storage_path>`

| 外部データ ソース       | Prefix | ストレージ アカウント パス                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>.blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | https  | <storage_account>.dfs.core.windows.net              |

 "<storage_path>" は、読み取り対象のフォルダーまたはファイルを指す、ストレージ内のパスです。

> [!NOTE]
> [Azure AD パススルーを強制する](#force-azure-ad-pass-through)特別な CREDENTIAL NAME  `UserIdentity`  があります。 クエリの実行中に、それが[資格情報の参照](#credential-lookup)に与える影響について確認してください。

必要に応じて、管理者は、ユーザーが資格情報を作成または削除できるようにするために、ユーザーに対して ALTER ANY CREDENTIAL 権限を許可または拒否することができます。

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

### <a name="supported-storages-and-authorization-types"></a>サポートされているストレージと承認の種類

承認と Azure Storage の種類の次の組み合わせを使用できます。

|                     | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *SAS*               | サポートされています      | サポートされていません   | サポートされています     |
| *マネージド ID* | サポートされていません  | サポートされていません    | サポートされていません |
| *ユーザー ID*    | サポートされています      | サポートされています        | サポートされています     |

### <a name="examples"></a>例

[承認の種類](#supported-storage-authorization-types)に応じて、次の T-SQL 構文を使用して資格情報を作成できます。

**Shared Access Signature と Blob Storage**

<*mystorageaccountname*> は、実際のストレージ アカウント名に、<*mystorageaccountcontainername*> は、実際のコンテナー名に置き換えてください。

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**ユーザー ID と Azure Data Lake Storage Gen1**

<*mystorageaccountname*> は、実際のストレージ アカウント名に、<*mystorageaccountcontainername*> は、実際のコンテナー名に置き換えてください。

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.azuredatalakestore.net/webhdfs/v1/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

**ユーザー ID と Azure Data Lake Storage Gen2**

<*mystorageaccountname*> は、実際のストレージ アカウント名に、<*mystorageaccountcontainername*> は、実際のコンテナー名に置き換えてください。

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

## <a name="force-azure-ad-pass-through"></a>Azure AD パススルーを強制する

Azure AD パススルーの強制は、Azure Synapse ワークスペースのプロビジョニング中に自動的に作成される特別な CREDENTIAL NAME `UserIdentity` によって実現される既定の動作です。 これにより、すべての Azure AD ログインの各クエリで Azure AD パススルーの使用が強制されます。これは、他の資格情報が存在するにもかかわらず行われます。

> [!NOTE]
> Azure AD パススルーは既定の動作です。 AD ログインでアクセスされる各ストレージ アカウントの資格情報を作成する必要はありません。

[各クエリに対する Azure AD パススルーの強制を無効にした](#disable-forcing-azure-ad-pass-through)場合、再度有効にするには、次を実行します。

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

特定のユーザーに対して Azure AD パススルーの強制を有効にするには、その特定のユーザーに、資格情報 `UserIdentity` に対する REFERENCE 権限を付与することができます。 次の例は、user_name に対して Azure AD パススルーの強制を有効にします。

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

SQL オンデマンドで、使用する資格情報を見つける方法については、「[資格情報の参照](#credential-lookup)」をご覧ください。

## <a name="disable-forcing-azure-ad-pass-through"></a>Azure AD パススルーの強制を無効にする

[各クエリに対する Azure AD パススルーの強制](#force-azure-ad-pass-through)を無効にすることができます。 無効にするには、次を使用して `Userdentity` 資格情報を削除します。

```sql
DROP CREDENTIAL [UserIdentity];
```

再度有効にするには、「[Azure AD パススルーを強制する](#force-azure-ad-pass-through)」セクションを参照してください。

特定のユーザーに対して Azure AD パススルーの強制を無効にするには、特定のユーザーの資格情報 `UserIdentity` に対する REFERENCE 権限を拒否することができます。 次の例は、user_name に対して Azure AD パススルーの強制を無効にします。

```sql
DENY REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

SQL オンデマンドで、使用する資格情報を見つける方法については、「[資格情報の参照](#credential-lookup)」をご覧ください。

## <a name="grant-permissions-to-use-credential"></a>資格情報を使用するためのアクセス許可を付与する

資格情報を使用するには、ユーザーは特定の資格情報に対する REFERENCES 権限を持っている必要があります。 specific_user の storage_credential に対する REFERENCES 権限を許可するには、次を実行します。

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

スムーズな Azure AD パススルーの実行を確保するために、すべてのユーザーには、既定で `UserIdentity` 資格情報を使用する権限が与えられます。 これは、Azure Synapse ワークスペースのプロビジョニング時に、次のステートメントの自動実行によって実現されます。

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

## <a name="credential-lookup"></a>資格情報の参照

クエリを承認するときは、資格情報の参照を使用してストレージ アカウントにアクセスします。これは、次の規則に基づいています。

- ユーザーが Azure AD ログインとしてログインしている

  - UserIdentity 資格情報が存在し、ユーザーがそれに対する参照権限を持っている場合は、Azure AD パススルーが使用されます。それ以外の場合は、[パスによる資格情報の参照](#lookup-credential-by-path)が使用されます。

- ユーザーが SQL ログインとしてログインしている

  - [パスによる資格情報の参照](#lookup-credential-by-path)を使用します。

### <a name="lookup-credential-by-path"></a>パスによる資格情報の参照

Azure AD パススルーの強制が無効になっている場合、資格情報の参照は、ストレージ パス (深さ優先) と、その特定の資格情報に対する REFERENCES 権限の存在に基づいて行われます。 同じファイルへのアクセスに使用できる資格情報が複数ある場合、SQL オンデマンドでは最も特定のものが使用されます。  

以下に示すのは、次のファイル パスに対するクエリの例です: *account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX.ext*

資格情報の参照は、次の順序で実行されます。

```
account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX
account.dfs.core.windows.net/filesystem/folder1/.../folderN
account.dfs.core.windows.net/filesystem/folder1
account.dfs.core.windows.net/filesystem
account.dfs.core.windows.net
```

ユーザーが資格情報番号 5 に対する REFERENCES 権限を持っていない場合、SQL オンデマンドでは、ユーザーが 1 つ上のレベルの資格情報に対する REFERENCES 権限を持っているかどうかがチェックされます。これは、ユーザーが REFERENCES 権限を持っている資格情報が特定されるまで行われます。 そのようなアクセス許可が見つからない場合は、エラー メッセージが返されます。

### <a name="credential-and-path-level"></a>資格情報とパス レベル

必要なパスのシェイプに応じて、クエリを実行するための次の要件があります。

- クエリが複数のファイル (フォルダー (ワイルドカードの有無にかかわらず)) を対象としている場合、ユーザーは少なくともルート ディレクトリ レベル (コンテナー レベル) の資格情報にアクセスできる必要があります。 リスティング ファイルはルート ディレクトリからの相対パスであるため、このアクセス レベルが必要になります (Azure Storage の制限事項)
- クエリが 1 つのファイルを対象としている場合、ユーザーは任意のレベルの資格情報にアクセスできる必要があります。これは、SQL オンデマンドがファイルに直接 (つまり、フォルダーの一覧表示なしで) アクセスするからです。

|                  | *アカウント* | *ルート ディレクトリ* | *その他のディレクトリ* | *[最近使ったファイル]*        |
| ---------------- | --------- | ---------------- | --------------------- | ------------- |
| *1 つのファイル*    | サポートされています | サポートされています        | サポートされています             | サポートされています     |
| *複数のファイル* | サポートされています | サポートされています        | サポートされていません         | サポートされていません |

## <a name="next-steps"></a>次のステップ

次の記事では、さまざまなフォルダーの種類やファイルの種類に対してクエリを実行する方法、およびビューの作成と使用について説明します。

- [単一の CSV ファイルに対してクエリを実行する](query-single-csv-file.md)
- [フォルダーと複数の CSV ファイルに対してクエリを実行する](query-folders-multiple-csv-files.md)
- [特定のファイルに対してクエリを実行する](query-specific-files.md)
- [Parquet ファイルに対してクエリを実行する](query-parquet-files.md)
- [ビューの作成および使用](create-use-views.md)
- [JSON ファイルに対してクエリを実行する](query-json-files.md)
- [Parquet の入れ子にされた型に対してクエリを実行する](query-parquet-nested-types.md)
