---
title: Azure portal を使用して移行する (Data Lake Storage Gen1 から Gen2)
description: Azure portal を使用することで、Azure Data Lake Storage Gen1 と Azure Data Lake Storage Gen2 の間の移行タスクを簡略化できます。
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 07/13/2021
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 3d78ba06112fce8dff64f2091e434d8f502634d2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662744"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2-by-using-the-azure-portal-preview"></a>Azure portal を使用して、Gen1 から Gen2 に Azure Data Lake Storage を移行する (プレビュー)

Azure portal を使用することで、移行を完了するのに必要な手順の数を減らせます。 データとメタデータ (タイムスタンプや ACL など) は、自動的に Gen2 対応アカウントに移動されます。 完全な移行を行う場合、要求は自動的にリダイレクトされるため、ワークロードを Gen2 にポイントする必要はありません。

> [!IMPORTANT]
> Azure portal を使用した Gen1 から Gen2 への移行は、現在プレビュー段階です。
> ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

この記事では、次のタスクについて説明します。

- 手順 1: プレビューに登録する

- 手順 2: Gen2 機能を持つストレージ アカウントを作成する

- 手順 3: RBAC ロールの割り当てを確認する

- 手順 4: 移行する

- 手順 5. アプリケーションをテストする

- 手順 6: 移行を完了する

必ず、Gen1 から Gen2 への移行方法に関する一般的なガイダンスをお読みください。 詳細については、「[Azure Data Lake Storage を Gen1 から Gen2 に移行する](data-lake-storage-migrate-gen1-to-gen2.md)」を参照してください。

> [!NOTE]
> 読みやすくするため、この記事では、Azure Data Lake Storage Gen1 を *Gen1* と呼び、Azure Data Lake Storage Gen2 を *Gen2* と呼びます。

## <a name="enroll-in-the-preview"></a>プレビューに登録する

プレビューに登録するには、[こちらのフォーム](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4SeyCfCfrtBlHWFupvoz_BUMEFNQzBSQTE0OU1aM0hXMDlBNEwzVTYyRy4u&wdLOR=cBC075B83-9324-4399-B94E-05A919D007C9)を参照してください。 登録後に、Gen2 機能を持つストレージ アカウントを作成します (次のセクションを参照)。

新しいアカウントが移行のために有効になっていることを確かめるため、約 7 日後に Microsoft からご連絡いたします。

## <a name="create-a-storage-account-with-gen2-capabilities"></a>Gen2 機能を持つストレージ アカウントを作成する

Azure Data Lake Storage Gen2 は、専用のストレージ アカウントやサービスの種類ではありません。 これは、Azure ストレージ アカウントの **階層的な名前空間** 機能を有効にすることによって取得できる一連の機能です。 Gen2 機能を持つアカウントを作成する場合は、「[Azure Data Lake Storage Gen2 で使用するストレージ アカウントを作成する](create-data-lake-storage-account.md)」を参照してください。

アカウントを作成するときは、必ず次の値を使用して設定を構成してください。

| 設定 | 値 |
|--|--|
| **Storage account name \(ストレージ アカウント名\)** | 任意の名前。 この名前は、Gen1 アカウントの名前と一致する必要はなく、任意のサブスクリプションで使用できます。 |
| **場所** | Data Lake Storage Gen1 アカウントで使用されているのと同じリージョン |
| **レプリケーション** | LRS または ZRS |
| **TLS の最小バージョン** | 1.0 |
| **NFS v3** | 無効 |
| **階層構造の名前空間** | Enabled |

> [!NOTE]
> Azure portal の移行ツールでは、アカウントの設定は移動されません。 そのため、アカウントを作成した後に、暗号化、ネットワーク ファイアウォール、データ保護などの設定を手動で構成する必要があります。

## <a name="verify-rbac-role-assignments"></a>RBAC ロールの割り当てを確認する

Gen2 の場合は、[ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)ロールが、ストレージ アカウント、親リソース グループ、またはサブスクリプションのスコープ内の Azure Active Directory (Azure AD) ユーザー ID に割り当てられていることを確かめます。

Gen1 の場合は、[所有者](../../role-based-access-control/built-in-roles.md#owner)ロールが、Gen1 アカウント、親リソース グループ、またはサブスクリプションのスコープ内の Azure AD ID に割り当てられていることを確かめます。

## <a name="perform-the-migration"></a>移行する

作業を開始する前に、データのみをコピーするか、完全な移行を行うかを決定します。

データのみをコピーする場合は、移行が完了した後も両方のアカウントがアクティブなままになります。 移行中、Gen1 アカウントは読み取り専用になります。 その後、新しい Gen2 対応のストレージ アカウントを使用するようにコンピューティング ワークロードを更新できます。 アプリケーションとワークロードが予期したとおりに動作することを確認したら、Gen1 アカウントを削除できます。 Microsoft ではこのオプションをお勧めします。

完全な移行を行うと、データは Gen1 から Gen2 にコピーされます。 その後、Gen1 URI が Gen2 URI にリダイレクトされます。 移行の完了後は、Gen1 アカウントにアクセスできなくなり、すべての Gen1 要求は Gen2 対応アカウントにリダイレクトされます。 Gen1 アカウントはデータ操作に使用できなくなり、Gen1 アカウントのデータに対して課金されなくなります。 パイプラインが Gen2 対応アカウントで実行されるようになったら、Gen1 アカウントを削除できます。

> [!NOTE]
> Gen2 では Azure Data Lake Analytics アプリケーションはサポートされていません。 いずれかをお持ちの場合は、Gen1 から Gen2 に移行する前に、必ずそれらを Azure Synapse Analytics またはサポートされている他のワークロードに移動してください。

### <a name="option-1-copy-data-from-gen1-to-gen2"></a>オプション 1: Gen1 から Gen2 にデータをコピーする

1. [Azure ポータル](https://portal.azure.com/)にサインインして、作業を開始します。

2. Data Lake Storage Gen1 アカウントを見つけて、そのアカウントの概要を表示します。

3. **[データの移行]** ボタンを選択します。

   > [!div class="mx-imgBorder"]
   > ![移行するためのボタン](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-tool.png)

4. **[Copy data to a new Gen2 account]\(データを新しい Gen2 アカウントにコピーする\)** を選択します。

   > [!div class="mx-imgBorder"]
   > ![データのコピー オプション](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-data-option.png)

5. チェックボックスをオンにして、Microsoft でデータを移行することに同意します。 その後、 **[適用]** ボタンをクリックします。

   > [!div class="mx-imgBorder"]
   > ![同意するためのチェックボックス](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-consent.png)

   データの移行中に、Gen1 アカウントが読み取り専用になり、Gen2 対応アカウントが無効になります。 移行の完了後に、両方のアカウントに対して読み取りと書き込みを行うことができます。

   **[移行の停止]** ボタンを選択することで、いつでも移行を停止できます。

   > [!div class="mx-imgBorder"]
   > ![移行の停止オプション](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-stop.png)

### <a name="option-2-perform-a-complete-migration"></a>オプション 2: 完全な移行を行う

1. [Azure ポータル](https://portal.azure.com/)にサインインして、作業を開始します。

2. Data Lake Storage Gen1 アカウントを見つけて、そのアカウントの概要を表示します。

3. **[データの移行]** ボタンを選択します。

   > [!div class="mx-imgBorder"]
   > ![移行ボタン](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-tool.png)

4. **[Complete migration to a new gen 2 account]\(新しい gen 2 アカウントへの移行を完了する\)** を選択します。

   > [!div class="mx-imgBorder"]
   > ![移行の完了オプション](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-complete-option.png)

5. チェックボックスをオンにして、Microsoft でデータを移行することに同意します。 その後、 **[適用]** ボタンをクリックします。

   > [!div class="mx-imgBorder"]
   > ![同意チェックボックス](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-consent.png)

   - データの移行中に、Gen1 アカウントが読み取り専用になり、Gen2 対応アカウントが無効になります。
   - Gen1 URI のリダイレクト中は、両方のアカウントが無効になります。
   - 移行の完了後に、Gen1 アカウントは無効になり、Gen2 対応アカウントに対して読み取りと書き込みを行うことができます。

   **[移行の停止]** ボタンを選択することで、URI がリダイレクトされる前にいつでも移行を停止できます。

   > [!div class="mx-imgBorder"]
   > ![移行の停止ボタン](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-stop.png)

## <a name="migrate-workloads-and-applications"></a>ワークロードとアプリケーションを移行する

1. Gen2 エンドポイントを指し示すように、[ワークロード内のサービス](./data-lake-storage-supported-azure-services.md)を構成します。

2. Gen2 API を使用するようにアプリケーションを更新します。 これらのガイドを参照してください。

   | 環境 | [アーティクル] |
   |--------|-----------|
   |Azure Storage Explorer |[Azure Storage Explorer を使用して Azure Data Lake Storage Gen2 のディレクトリとファイルを管理する](data-lake-storage-explorer.md)|
   |.NET |[.NET を使用して Azure Data Lake Storage Gen2 でディレクトリとファイルを管理する](data-lake-storage-directory-file-acl-dotnet.md)|
   |Java|[Java を使用して Azure Data Lake Storage Gen2 のディレクトリとファイルを管理する](data-lake-storage-directory-file-acl-java.md)|
   |Python|[Python を使用して Azure Data Lake Storage Gen2 でディレクトリとファイルを管理する](data-lake-storage-directory-file-acl-python.md)|
   |JavaScript (Node.js)|[Node.js の JavaScript SDK を使用して Azure Data Lake Storage Gen2 でディレクトリとファイルを管理する](data-lake-storage-directory-file-acl-javascript.md)|
   |REST API |[Azure Data Lake Store REST API](/rest/api/storageservices/data-lake-storage-gen2)|

3. Data Lake Storage Gen2 の [PowerShell コマンドレット](data-lake-storage-directory-file-acl-powershell.md)および [Azure CLI コマンド](data-lake-storage-directory-file-acl-cli.md)を使用するようにスクリプトを更新します。

4. コード ファイル、Databricks ノートブック、Apache Hive HQL ファイル、またはワークロードの一部として使用されるその他のファイルで、文字列 `adl://` が含まれる URI 参照を検索します。 これらの参照を、新しいストレージ アカウントの [Gen2 形式の URI](data-lake-storage-introduction-abfs-uri.md) に置き換えます。 たとえば、Gen1 の URI `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` は、`abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile` になる可能性があります。

## <a name="leverage-the-gen1-compatibility-layer-optional"></a>Gen1 互換性レイヤーを活用する (省略可能)

Microsoft では、アプリケーションで引き続き Gen1 API を使用して Gen2 対応アカウントのデータを操作できるように、機能を制限してアプリケーションの互換性を提供しています。 互換性レイヤーはサーバー上で実行されるため、インストールするものはありません。

> [!IMPORTANT]
> Microsoft では、ワークロードとアプリケーションを移行する代替手段として、この機能をお勧めしていません。 2024 年 2 月 29 日に Gen1 が廃止されると、Gen1 互換性レイヤーのサポートが終了します。

互換性レイヤーに関する問題の発生数を最小にするために、必ず Gen1 SDK で次のバージョン (またはそれ以降) が使用されていることを確認してください。

   | 言語 | SDK バージョン |
   |--|--|
   | **.NET** | [2.3.9](https://github.com/Azure/azure-data-lake-store-net/blob/master/CHANGELOG.md) |
   | **Java** | [1.1.21](https://github.com/Azure/azure-data-lake-store-java/blob/master/CHANGES.md) |
   | **Python** | [0.0 51](https://github.com/Azure/azure-data-lake-store-python/blob/master/HISTORY.rst) |

次の機能は Gen2 では (したがって互換性レイヤーでも) サポートされていません。

- エントリに ListBefore を適用する ListStatus API オプション。

- 継続トークンのない 4000 を超えるファイルを含む ListStatus API。

- 追加操作のチャンクエンコーディング。

- Azure Active Directory (Azure AD) トークンの対象ユーザーとして https://management.azure.com/ を使用するすべての API 呼び出し。

- スペースまたはタブのみを含むファイルまたはディレクトリの名前。`.` で終わるか、`:` が含まれるか、複数の連続するスラッシュ (`//`) があります。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

#### <a name="how-much-does-the-data-migration-cost"></a>データの移行にはどのくらいのコストがかかりますか?

データの移行中は、Gen1 アカウントのデータ ストレージとトランザクションに対して課金されます。 データのみをコピーするオプションを選択した場合は、移行が完了した後、両方のアカウントのデータ ストレージとトランザクションに対して課金されます。 Gen1 アカウントに対して課金されないようにするには、Gen1 アカウントを削除する必要があります。 アプリケーションの更新が完了した後に、Gen1 アカウントを削除します。 完全な移行を行うことを選択した場合は、移行後に Gen2 対応アカウントのデータ ストレージとトランザクションに対してのみ課金されます。

#### <a name="after-the-migration-completes-can-i-choose-to-go-back-to-using-the-gen1-account"></a>移行の完了後に、Gen1 アカウントの使用に戻すことを選択できますか?

これはサポートされていません。移行の完了後は、Gen1 アカウントのデータにアクセスできなくなります。 Azure portal では引き続き Gen1 アカウントを表示でき、準備ができたら、アカウントを削除できます。

#### <a name="i-would-like-to-enable-geo-redundant-storage-grs-on-the-gen2-account-how-do-i-do-that"></a>Gen2 アカウントで Geo 冗長ストレージ (GRS) を有効にしたいのですが、どうすればよいですか?

[データのコピー] と [移行の完了] オプションの両方での移行が完了したら、先に進み、アプリケーションの互換性レイヤーを使用する予定がない限り、冗長性オプションを GRS に変更できます。 アプリケーションの互換性は、GRS 冗長性を使用するアカウントでは機能しません。

#### <a name="gen1-doesnt-have-containers-and-gen2-has-them---what-should-i-expect"></a>Gen1 にコンテナーがなく、Gen2 にある場合はどうなりますか?

Gen2 対応アカウントにデータをコピーすると、`Gen1` という名前のコンテナーが自動的に作成されます。 データのみをコピーすることを選択した場合は、データのコピーが完了した後にそのコンテナーの名前を変更できます。 完全な移行を行い、アプリケーションの互換性レイヤーを使用する予定がある場合は、コンテナー名を変更しないようにする必要があります。 互換性レイヤーを使用する必要がなくなった場合は、コンテナーの名前を変更できます。

## <a name="next-steps"></a>次のステップ

- 一般的な移行について学習します。 詳細については、「[Azure Data Lake Storage を Gen1 から Gen2 に移行する](data-lake-storage-migrate-gen1-to-gen2.md)」を参照してください。
