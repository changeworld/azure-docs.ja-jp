---
title: Oracle への接続と管理
description: このガイドでは、Azure Purview で Oracle に接続する方法と、Purview の機能を使用して Oracle ソースをスキャンおよび管理する方法について説明します。
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 935fcf05624fd1849ae62109dbc22a97d4987676
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847282"
---
# <a name="connect-to-and-manage-oracle-in-azure-purview"></a>Azure Purview での Oracle への接続と管理

この記事では、Azure Purview で、Oracle を登録する方法と Oracle を認証および操作する方法について説明します。 Azure Purview の詳細については、[概要の記事](overview.md)を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

|**メタデータの抽出**|  **フル スキャン**  |**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|
| [あり](#register)| [あり](#scan)| いいえ | いいえ | いいえ | いいえ| [はい**](how-to-lineage-oracle.md)|

\** データセットが [Data Factory Copy アクティビティ](how-to-link-azure-data-factory.md)でソースまたはシンクとして使用される場合、系列はサポートされています 

サポートされている Oracle サーバーのバージョンは、6i から 19c です

Oracle ソースをスキャンする場合、プロキシ サーバーはサポートされていません。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

* Purview Studio でソースを登録して管理するには、データ ソース管理者およびデータ閲覧者である必要があります。 詳細については、[Azure Purview のアクセス許可](catalog-permissions.md)に関するページを参照してください。

* 最新の[セルフホステッド統合ランタイム](https://www.microsoft.com/download/details.aspx?id=39717)を設定します。 詳細については、[セルフホステッド統合ランタイムの作成および構成ガイド](../data-factory/create-self-hosted-integration-runtime.md)に関する記事を参照してください。

* セルフホステッド統合ランタイムがインストールされている仮想マシンに [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) がインストールされていることを確認します。

* セルフホステッド統合ランタイム マシンに Visual Studio 2012 Update 4 の Visual C++ 再頒布可能パッケージがインストールされていることを確認します。 この更新プログラムがインストールされていない場合は、[ここからダウンロードできます](https://www.microsoft.com/download/details.aspx?id=30679)。

* セルフホステッド統合ランタイムが実行されている仮想マシンに、[こちら](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html)から Oracle JDBC ドライバーを手動でダウンロードします。

    > [!Note]
    > このドライバーは、VM 内のすべてのアカウントからアクセスできる必要があります。 それをユーザー アカウントにインストールしないでください。

## <a name="register"></a>登録

このセクションでは、[Purview Studio](https://web.purview.azure.com/) を使用して Azure Purview に Oracle を登録する方法について説明します。

### <a name="prerequisites-for-registration"></a>登録の前提条件

システム テーブルへの読み取り専用アクセスが必要です。

ユーザーには、セッションを作成するためのアクセス許可、および SELECT\_CATALOG\_ROLE というロールが割り当てられている必要があります。 または、このコネクタでメタデータを照会する各システム テーブルに対する SELECT アクセス許可をユーザーに付与することもできます。

```sql
grant create session to [user];
grant select on all_users to [user];
grant select on dba_objects to [user];
grant select on dba_tab_comments to [user];
grant select on dba_external_locations to [user];
grant select on dba_directories to [user];
grant select on dba_mviews to [user];
grant select on dba_clu_columns to [user];
grant select on dba_tab_columns to [user];
grant select on dba_col_comments to [user];
grant select on dba_constraints to [user];
grant select on dba_cons_columns to [user];
grant select on dba_indexes to [user];
grant select on dba_ind_columns to [user];
grant select on dba_procedures to [user];
grant select on dba_synonyms to [user];
grant select on dba_views to [user];
grant select on dba_source to [user];
grant select on dba_triggers to [user];
grant select on dba_arguments to [user];
grant select on dba_sequences to [user];
grant select on dba_dependencies to [user];
grant select on dba_type_attrs to [user];
grant select on V_$INSTANCE to [user];
grant select on v_$database to [user];
```

### <a name="authentication-for-registration"></a>登録の認証

Oracle ソースでサポートされている認証は **基本認証** のみです。

### <a name="steps-to-register"></a>登録するための手順

新しい Oracle ソースをデータ カタログに登録するには、次のようにします。

1. [Purview Studio](https://web.purview.azure.com/resource/) で Purview アカウントに移動します。
1. 左側のナビゲーションで **[Data Map]** を選択します。
1. **[登録]** を選択します
1. [Register sources]\(ソースの登録\) で、 **[Oracle]** を選択します。 **[続行]** をクリックします。

    :::image type="content" source="media/register-scan-oracle-source/register-sources.png" alt-text="ソースを登録する" border="true":::

**[Register sources (Oracle)]\(ソースの登録 (Oracle)\)** 画面で、次のようにします。

1. データ ソースがカタログに一覧表示されるときの **名前** を入力します。

1. Oracle ソースに接続するための **ホスト** 名を入力します。 これは、次のいずれかにすることができます。
    * データベース サーバーに接続するために JDBC で使用されるホスト名。 例: `MyDatabaseServer.com`
    * IP アドレス。 例: `192.169.1.2`
    * 完全修飾の JDBC 接続文字列。 たとえば、次のように入力します。

         ```
        jdbc:oracle:thin:@(DESCRIPTION=(LOAD_BALANCE=on)(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver1)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver2)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver3)(PORT=1521))(CONNECT_DATA=(SERVICE_NAME=orcl)))
        ```

1. データベース サーバーに接続するために JDBC で使用される **ポート番号** (Oracle の場合、既定では 1521) を入力します。

1. データベース サーバーに接続するために JDBC で使用される **Oracle サービス名** を入力します。

1. コレクションを選択するか、新しいものを作成します (省略可能)

1. データ ソースの登録を終了します。

    :::image type="content" source="media/register-scan-oracle-source/register-sources-2.png" alt-text="ソースの登録のオプション" border="true":::

## <a name="scan"></a>スキャン

次の手順に従って Oracle をスキャンし、自動的に資産を識別してデータを分類します。 スキャン全般の詳細については、[スキャンとインジェストの概要](concept-scans-and-ingestion.md)に関するページを参照してください。

### <a name="create-and-run-scan"></a>スキャンの作成と実行

新しいスキャンを作成して実行するには、次の操作を行います。

1. 管理センターで、[統合ランタイム] を選択します。 セルフホステッド統合ランタイムが設定されていることを確認してください。 設定されていない場合は、[こちら](./manage-integration-runtimes.md)に記載されている手順を使用して、セルフホステッド統合ランタイムを作成します。

1. **[ソース]** に移動します。

1. 登録されている Oracle ソースを選択します。

1. **[+ 新しいスキャン]** を選択します。

1. 次の詳細を指定します。

    1. **[名前]** : スキャンの名前

    1. **[統合ランタイム経由で接続]** : 構成済みのセルフホステッド統合ランタイムを選択します

    1. **[資格情報]** : 対象のデータ ソースに接続するための資格情報を選択します。 次のことを確認します。
        * 資格情報を作成するときに [基本認証] を選択します。
        * [ユーザー名] 入力フィールドで、データベース サーバーに接続するために JDBC で使用されるユーザー名を指定します。
        * データベース サーバーに接続するために JDBC で使用されるユーザー パスワードを、秘密鍵に格納します。

    1. **[スキーマ]** : インポートするスキーマのサブセットをセミコロン区切りのリストとして指定します。 たとえば、「 `schema1; schema2` 」のように入力します。 リストが空の場合は、すべてのユーザー スキーマがインポートされます。 既定では、すべてのシステム スキーマ (SysAdmin など) とオブジェクトが無視されます。 リストが空の場合は、使用可能なすべてのスキーマがインポートされます。

        SQL LIKE 式の構文を使用したスキーマ名のパターンとして、% の使用も許容されます。 たとえば: `A%; %B; %C%; D`
        * A で始まる、または
        * B で終わる、または
        * C を含む、または
        * D と等しい

        NOT および特殊文字の使用は許容されません。

    1. **[Driver location]\(ドライバーの場所\)** : セルフホステッド統合ランタイムが実行されている VM 内の JDBC ドライバーの場所へのパスを指定します。 これは、有効な JAR フォルダーの場所へのパスである必要があります。

        > [!Note]
        > このドライバーは、VM 内のすべてのアカウントからアクセスできる必要があります。 これをユーザー アカウントにインストールしないようにしてください。

    1. **[Maximum memory available]\(使用可能な最大メモリ\):** スキャン プロセスで使用される、顧客の VM で使用可能な最大メモリ (GB 単位)。 これは、スキャンする Oracle ソースのサイズによって異なります。

        > [!Note]
        > 経験則として、テーブル 1,000 個ごとに 1 GB のメモリを用意してください

        :::image type="content" source="media/register-scan-oracle-source/scan.png" alt-text="oracle をスキャンする" border="true":::

1. **[続行]** を選択します。

1. **スキャン トリガー** を選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

1. 自分のスキャンを確認し、 **[保存および実行]** を選択します。

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>次のステップ

ソースの登録が完了したので、以下のガイドに従って Azure Purview とご利用のデータの詳細について学習します。

- [Azure Purview のデータ分析情報](concept-insights.md)
- [Azure Purview のデータ系列](catalog-lineage-user-guide.md)
- [Data Catalog の検索](how-to-search-catalog.md)
