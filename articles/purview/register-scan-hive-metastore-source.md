---
title: Hive メタストア データベースに接続して管理する
description: このガイドでは、Azure Purview で Hive メタストア データベースに接続する方法と、Purview の機能を使用して Hive メタストア データベース ソースをスキャンおよび管理する方法について説明します。
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 2e40125d65dd2b4b701d5b1049444a52e6cb7dc4
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850662"
---
# <a name="connect-to-and-manage-hive-metastore-databases-in-azure-purview"></a>Azure Purview で Hive メタストア データベースに接続して管理する

この記事では、Azure Purview で、Hive メタストア データベースを登録する方法、および Hive メタストア データベースを認証して操作する方法について説明します。 Azure Purview の詳細については、[概要の記事](overview.md)を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

|**メタデータの抽出**|  **フル スキャン**  |**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|
| [あり](#register)| [あり](#scan)| いいえ | いいえ | いいえ | いいえ| はい** |

\** データセットが [Data Factory Copy アクティビティ](how-to-link-azure-data-factory.md)でソース/シンクとして使用される場合、系列はサポートされています 

> [!Important]
> サポートされているプラットフォームは、Apache Hadoop、Cloudera、Hortonworks、Databricks です。
> サポートされている Hive のバージョンは 2.x から 3.x までです。 サポートされている Databricks のバージョンは 8.0 以降です。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

* Purview Studio でソースを登録して管理するには、データ ソース管理者およびデータ閲覧者である必要があります。 詳細については、[Azure Purview のアクセス許可](catalog-permissions.md)に関するページを参照してください。

* 最新の[セルフホステッド統合ランタイム](https://www.microsoft.com/download/details.aspx?id=39717)を設定します。 詳細については、[セルフホステッド統合ランタイムの作成および構成ガイド](../data-factory/create-self-hosted-integration-runtime.md)に関する記事を参照してください。

* セルフホステッド統合ランタイムがインストールされている仮想マシンに [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) がインストールされていることを確認します。

* セルフホステッド統合ランタイム マシンに Visual Studio 2012 Update 4 の Visual C++ 再頒布可能パッケージがインストールされていることを確認します。 この更新プログラムがインストールされていない場合は、[ここからダウンロードできます](https://www.microsoft.com/download/details.aspx?id=30679)。

* セルフホステッド統合ランタイムが実行されているマシン上に、Hive メタストア データベースの JDBC ドライバーをダウンロードしてインストールします。 たとえば、使用されているデータベースが mssql の場合は、[SQL Server 用の Microsoft の JDBC ドライバー](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)をダウンロードします。

    > [!Note]
    > このドライバーは、VM 内のすべてのアカウントからアクセスできる必要があります。 それをユーザー アカウントにインストールしないでください。

## <a name="register"></a>登録

このセクションでは、[Purview Studio](https://web.purview.azure.com/) を使用して Azure Purview に Hive メタストア データベースを登録する方法について説明します。

Hive メタストア データベース用にサポートされている認証は、**基本認証** のみです。

### <a name="steps-to-register"></a>登録する手順

1. ご自分の Purview アカウントに移動します。

1. 左側のナビゲーションで **[Data Map]** を選択します。

1. **[登録]** を選択します

1. [Register sources]\(ソースの登録\) で、Hive **メタストア** を選択します。 **[続行]** を選択します。

    :::image type="content" source="media/register-scan-hive-metastore-source/register-sources.png" alt-text="Hive ソースを登録します" border="true":::

[Register sources (Hive Metastore)]\(ソースの登録 (Hive メタストア)\) 画面で、次のようにします。

1. データ ソースがカタログに一覧表示されるときの **名前** を入力します。

1. **[Hive Cluster URL]\(Hive クラスター URL\)** を入力します。 クラスターの URL は、Ambari URL または Databricks ワークスペース URL から取得できます。 例: hive.azurehdinsight.net、adb-19255636414785.5.azuredatabricks.net

1. **[Hive Metastore Server URL]\(Hive メタストア サーバー URL\)** を入力します。 例: sqlserver://hive.database.windows.net、jdbc:spark://adb-19255636414785.5.azuredatabricks.net:443

1. コレクションを選択するか、新しいものを作成します (省略可能)。

1. データ ソースの登録を終了します。

    :::image type="content" source="media/register-scan-hive-metastore-source/configure-sources.png" alt-text="Hive ソースを構成する" border="true":::

## <a name="scan"></a>スキャン

次の手順に従って、Hive メタストア データベースをスキャンし、自動的に資産を識別してデータを分類します。 スキャン全般の詳細については、[スキャンとインジェストの概要](concept-scans-and-ingestion.md)に関するページを参照してください。

### <a name="create-and-run-scan"></a>スキャンの作成と実行

1. 管理センターで、[統合ランタイム] を選択します。 セルフホステッド統合ランタイムが設定されていることを確認してください。 設定されていない場合は、[こちら] (./manage-integration-runtimes.md) に記載されている手順に従って、セルフホステッド統合ランタイムを設定します。

1. **[ソース]** に移動します。

1. 登録されている **Hive メタストア** データベースを選択します。

1. **[+ 新しいスキャン]** を選択します。

1. 次の詳細を指定します。

    1. **[名前]** : スキャンの名前

    1. **[Connect via integration runtime]\(統合ランタイム経由で接続\)** : 構成済みのセルフホステッド統合ランタイムを選択します。

    1. **[資格情報]** : 対象のデータ ソースに接続するための資格情報を選択します。 次のことを確認します。

       * 資格情報を作成するときに [基本認証] を選択します。
       * [ユーザー名] 入力フィールドに Metastore のユーザー名を入力します
       * 秘密鍵にメタストアのパスワードを格納します。

       資格情報の詳細については、[こちら](manage-credentials.md)のリンクを参照してください。

       **[Databricks usage]\(Databricks の使用状況\)** : [Databricks cluster]\(Databricks クラスター\) -> [アプリ] -> [Launch Web Terminal]\(Web ターミナルの起動\) に移動します。 コマンドレット **cat /databricks/hive/conf/hive-site.xml** を実行します

       次に示すように、ユーザー名とパスワードには 2 つのプロパティからアクセスできます。

       :::image type="content" source="media/register-scan-hive-metastore-source/databricks-credentials.png" alt-text="databricks-username-password-details" border="true":::

    1. **[Metastore JDBC Driver Location]\(メタストア JDBC ドライバーの場所\)** : セルフホステッド統合ランタイムが実行されている VM 内の JDBC ドライバーの場所へのパスを指定します。 これは、有効な JAR フォルダーの場所へのパスである必要があります。

       Databricks をスキャンする場合は、後の Databricks に関するセクションを参照してください。

       > [!Note]
       > このドライバーは、VM 内のすべてのアカウントからアクセスできる必要があります。 これをユーザー アカウントにインストールしないようにしてください。

    1. **[Metastore JDBC Driver Class]\(メタストア JDBC ドライバー クラス\)** : 接続ドライバーのクラス名を指定します。 例: \com.microsoft.sqlserver.jdbc.SQLServerDriver。

       **[Databricks usage]\(Databricks の使用状況\)** : [Databricks cluster]\(Databricks クラスター\) -> [アプリ] -> [Launch Web Terminal]\(Web ターミナルの起動\) に移動します。 コマンドレット **cat /databricks/hive/conf/hive-site.xml** を実行します

       ドライバー クラスには、次に示すようにプロパティからアクセスできます。

        :::image type="content" source="media/register-scan-hive-metastore-source/databricks-driver-class-name.png" alt-text="databricks-driver-class-details" border="true":::

    1. **[Metastore JDBC URL]\(メタストア JDBC URL\)** : 接続 URL の値を指定し、メタストア DB サーバーの URL への接続を定義します。 たとえば、 `jdbc:sqlserver://hive.database.windows.net;database=hive;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300`と指定します。

       **[Databricks usage]\(Databricks の使用状況\)** : [Databricks cluster]\(Databricks クラスター\) -> [アプリ] -> [Launch Web Terminal]\(Web ターミナルの起動\) に移動します。 コマンドレット **cat /databricks/hive/conf/hive-site.xml** を実行します

       JDBC の URL には、次に示すように接続 URL プロパティからアクセスできます。

       :::image type="content" source="media/register-scan-hive-metastore-source/databricks-jdbc-connection.png" alt-text="databricks-jdbc-url-details" border="true":::

       > [!NOTE]
       > *hive-site.xml* から URL をコピーする場合は、文字列から `amp;` を削除してください。削除しないとスキャンに失敗します。

       この URL に、SSL 証明書が配置されている VM 上の場所へのパスを追加します。 SSL 証明書は、[こちら](../mysql/howto-configure-ssl.md)からダウンロードできます。

       メタストア JDBC の URL は次のようになります。

       `jdbc:mariadb://consolidated-westus2-prod-metastore-addl-1.mysql.database.azure.com:3306/organization1829255636414785?trustServerCertificate=true&amp;useSSL=true&sslCA=D:\Drivers\SSLCert\BaltimoreCyberTrustRoot.crt.pem`

    1. **メタストア データベース名**: Hive メタストア データベースの名前を指定します。

       Databricks をスキャンする場合は、後の Databricks に関するセクションを参照してください。

       **[Databricks usage]\(Databricks の使用状況\)** : [Databricks cluster]\(Databricks クラスター\) -> [アプリ] -> [Launch Web Terminal]\(Web ターミナルの起動\) に移動します。 コマンドレット **cat /databricks/hive/conf/hive-site.xml** を実行します

       データベースの名前には、次に示すように JDBC URL プロパティからアクセスできます。 例: organization1829255636414785

       :::image type="content" source="media/register-scan-hive-metastore-source/databricks-data-base-name.png" alt-text="databricks-database-name-details" border="true":::

    1. **[スキーマ]** : インポートする Hive スキーマのリストを指定します。 たとえば、schema1; schema2 のようにします。

        リストが空の場合は、すべてのユーザー スキーマがインポートされます。 既定では、すべてのシステム スキーマ (SysAdmin など) とオブジェクトが無視されます。

        リストが空の場合は、使用可能なすべてのスキーマがインポートされます。 SQL LIKE 式の構文を使用したスキーマ名のパターンとして、% の使用も許容されます。 例: A%; %B; %C%; D

        * A で始まる、または
        * B で終わる、または
        * C を含む、または
        * D と等しい

        NOT および特殊文字の使用は許容されません。

    1. **[Maximum memory available]\(使用可能な最大メモリ\):** スキャン プロセスで使用される、顧客の VM で使用可能な最大メモリ (GB 単位)。 これは、スキャンする Hive メタストア データベースのサイズによって異なります。

        :::image type="content" source="media/register-scan-hive-metastore-source/scan.png" alt-text="Hive ソースをスキャンします" border="true":::

1. **[続行]** を選択します。

1. **スキャン トリガー** を選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

1. 自分のスキャンを確認し、 **[保存および実行]** を選択します。

## <a name="next-steps"></a>次のステップ

ソースの登録が完了したので、以下のガイドに従って Azure Purview とご利用のデータの詳細について学習します。

- [Azure Purview のデータ分析情報](concept-insights.md)
- [Azure Purview のデータ系列](catalog-lineage-user-guide.md)
- [Data Catalog の検索](how-to-search-catalog.md)
