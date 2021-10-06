---
title: Azure Purview で Hive メタストア データベースを登録してスキャンを設定する
description: この記事では、Azure Purview で Hive メタストア データベースを登録してスキャンを設定する方法の概要について説明します。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: overview
ms.date: 09/27/2021
ms.openlocfilehash: 7552b562e930e39ba4a0f848ba095762d3838c22
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214107"
---
# <a name="register-and-scan-hive-metastore-database"></a>Hive メタストア データベースを登録してスキャンする

この記事では、Purview に Hive メタストア データベースを登録してスキャンを設定する方法の概要について説明します。

## <a name="supported-capabilities"></a>サポートされる機能

Hive メタストア ソースでは **Hive メタストア データベース** からメタデータを抽出するためのフル スキャンがサポートされており、データ資産間の系列がフェッチされます。 サポートされているプラットフォームは、Apache Hadoop、Cloudera、Hortonworks、Databricks です。

## <a name="prerequisites"></a>前提条件

1.  最新の[セルフホステッド統合ランタイム](https://www.microsoft.com/download/details.aspx?id=39717)を設定します。
    詳細については、「[セルフホステッド統合ランタイムを作成して共有する](../data-factory/create-self-hosted-integration-runtime.md)」を参照してください。

2.  セルフホステッド統合ランタイムがインストールされている仮想マシンに [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) がインストールされていることを確認します。

3.  セルフホステッド統合ランタイム コンピューターに \"Visual C++ 再頒布可能パッケージ 2012 Update 4\" がインストールされていることを確認します。 まだインストールしていない場合は、[こちら](https://www.microsoft.com/download/details.aspx?id=30679)からダウンロードしてください。

4.  セルフホステッド統合ランタイムが実行されている仮想マシンで Hive メタストア データベースの JDBC ドライバーを手動でダウンロードする必要があります。 たとえば、使用されているデータベースが mssql の場合は、[SQL Server 用の Microsoft の JDBC ドライバー](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)をダウンロードします。

    > [!Note]
    > このドライバーは、VM 内のすべてのアカウントからアクセスできる必要があります。 それをユーザー アカウントにインストールしないでください。

5.  サポートされている Hive のバージョンは 2.x から 3.x までです。 サポートされている Databricks のバージョンは 8.0 以降です。 

## <a name="setting-up-authentication-for-a-scan"></a>スキャンでの認証の設定

Hive メタストア データベース用にサポートされている認証は、**基本認証** のみです。

## <a name="register-a-hive-metastore-database"></a>Hive メタストア データベースを登録する

新しい Hive メタストア データベースをデータ カタログに登録するには、次のようにします。

1.  ご自分の Purview アカウントに移動します。

2.  左側のナビゲーションで **[Data Map]** を選択します。

3.  **[登録]** を選択します

4.  [Register sources]\(ソースの登録\) で、Hive **メタストア** を選択します。 **[続行]** を選択します。

    :::image type="content" source="media/register-scan-hive-metastore-source/register-sources.png" alt-text="Hive ソースを登録します" border="true":::

[Register sources (Hive Metastore)]\(ソースの登録 (Hive メタストア)\) 画面で、次のようにします。

1.  データ ソースがカタログに一覧表示されるときの **名前** を入力します。

2.  **[Hive Cluster URL]\(Hive クラスター URL\)** を入力します。 クラスターの URL は、Ambari URL または Databricks ワークスペース URL から取得できます。 例: hive.azurehdinsight.net、adb-19255636414785.5.azuredatabricks.net

3.  **[Hive Metastore Server URL]\(Hive メタストア サーバー URL\)** を入力します。 例: sqlserver://hive.database.windows.net、jdbc:spark://adb-19255636414785.5.azuredatabricks.net:443

4.  コレクションを選択するか、新しいものを作成します (省略可能)

5.  データ ソースの登録を終了します。

       :::image type="content" source="media/register-scan-hive-metastore-source/configure-sources.png" alt-text="Hive ソースを構成する" border="true":::


## <a name="creating-and-running-a-scan"></a>スキャンを作成し、実行する

新しいスキャンを作成して実行するには、次の操作を行います。

1.  管理センターで、[統合ランタイム] を選択します。 セルフホステッド統合ランタイムが設定されていることを確認してください。 設定されていない場合は、[こちら](./manage-integration-runtimes.md)に記載されている手順を使用して、セルフホステッド統合ランタイムを設定します

2.  **[ソース]** に移動します。

3.  登録されている **Hive メタストア** データベースを選択します。

4.  **[+ 新しいスキャン]** を選択します。

5.  次の詳細を指定します。

    1. **[名前]** : スキャンの名前

    1. **[Connect via integration runtime]\(統合ランタイム経由で接続\)** : 構成済みのセルフホステッド統合ランタイムを選択します。

    1. **[資格情報]** : 対象のデータ ソースに接続するための資格情報を選択します。 次のことを確認します。

       - 資格情報を作成するときに [基本認証] を選択します。
       - [ユーザー名] 入力フィールドに Metastore のユーザー名を入力します
       - 秘密鍵にメタストアのパスワードを格納します。

       資格情報の詳細については、[こちら](manage-credentials.md)のリンクを参照してください。 

       **[Databricks usage]\(Databricks の使用状況\)** : [Databricks cluster]\(Databricks クラスター\) -> [アプリ] -> [Launch Web Terminal]\(Web ターミナルの起動\) に移動します。 コマンドレット **cat /databricks/hive/conf/hive-site.xml** を実行します

       次に示すように、ユーザー名とパスワードには 2 つのプロパティからアクセスできます

       :::image type="content" source="media/register-scan-hive-metastore-source/databricks-credentials.png" alt-text="databricks-username-password-details" border="true":::

    1. **[Metastore JDBC Driver Location]\(メタストア JDBC ドライバーの場所\)** : セルフホステッド統合ランタイムが実行されている VM 内の JDBC ドライバーの場所へのパスを指定します。 これは、有効な JAR フォルダーの場所へのパスである必要があります。

       Databricks をスキャンする場合は、後の Databricks に関するセクションを参照してください。

       > [!Note]
       > このドライバーは、VM 内のすべてのアカウントからアクセスできる必要があります。 これをユーザー アカウントにインストールしないようにしてください。

    1. **[Metastore JDBC Driver Class]\(メタストア JDBC ドライバー クラス\)** : 接続ドライバーのクラス名を指定します。 例: \com.microsoft.sqlserver.jdbc.SQLServerDriver。
    
       **[Databricks usage]\(Databricks の使用状況\)** : [Databricks cluster]\(Databricks クラスター\) -> [アプリ] -> [Launch Web Terminal]\(Web ターミナルの起動\) に移動します。 コマンドレット **cat /databricks/hive/conf/hive-site.xml** を実行します
    
       ドライバー クラスには、次に示すようにプロパティからアクセスできます。
    :::image type="content" source="media/register-scan-hive-metastore-source/databricks-driver-class-name.png" alt-text="databricks-driver-class-details" border="true":::

    1. **[Metastore JDBC URL]\(メタストア JDBC URL\)** : 接続 URL の値を指定し、メタストア DB サーバーの URL への接続を定義します。 例: `jdbc:sqlserver://hive.database.windows.net;database=hive;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300`。

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

        リストが空の場合は、使用可能なすべてのスキーマがインポートされます。 SQL LIKE 式の構文を使用したスキーマ名のパターンとして、% の使用も許容されます (例: A%; %B; %C%; D)

        - A で始まる、または    
        - B で終わる、または    
        - C を含む、または    
        - D と等しい

        NOT および特殊文字の使用は許容されません。

     1. **[Maximum memory available]\(使用可能な最大メモリ\):** スキャン プロセスで使用される、顧客の VM で使用可能な最大メモリ (GB 単位)。 これは、スキャンする Hive メタストア データベースのサイズによって異なります。

        :::image type="content" source="media/register-scan-hive-metastore-source/scan.png" alt-text="Hive ソースをスキャンします" border="true":::

6.  **[続行]** を選択します。

7.  **スキャン トリガー** を選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

8.  自分のスキャンを確認し、 **[保存および実行]** を選択します。

## <a name="viewing-your-scans-and-scan-runs"></a>スキャンとスキャンの実行を確認する

1. 管理センターに移動します。 **[Sources and scanning]\(ソースとスキャン\)** セクションの **[データ ソース]** を選択します。

2. 目的のデータ ソースを選択します。 そのデータ ソースに対する既存のスキャンの一覧が表示されます。

3. 結果を表示するスキャンを選択します。

4. このページには、前回のすべてのスキャン実行と、各スキャン実行のメトリックと状態が表示されます。 また、そのスキャンがスケジュールされたスキャンと手動スキャンのどちらかであるか、分類が適用された資産の数、検出された資産の合計数、スキャンの開始時刻と終了時刻、スキャンの実行時間の合計も表示されます。

## <a name="manage-your-scans"></a>スキャンを管理する

スキャンを管理または削除するには、次の操作を行います。

1. 管理センターに移動します。 **[Sources and scanning]\(ソースとスキャン\)** セクションの **[データ ソース]** を選択し、目的のデータ ソースを選択します。

2. 管理するスキャンを選択します。 スキャンを編集するには、 **[編集]** を選択します。

3. スキャンを削除するには、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)
