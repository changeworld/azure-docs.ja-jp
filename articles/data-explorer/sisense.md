---
title: Sisense を使用して Azure Data Explorer のデータを視覚化する
description: この記事では、Azure Data Explorer を Sisense のデータ ソースとして設定し、データを視覚化する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: f0840b90e1036c23fa58d94515bfeb035299c07f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "66358007"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>Sisense で Azure Data Explorer のデータを視覚化する

Sisense は、高度な対話型のユーザー エクスペリエンスを提供する分析アプリをビルドできる分析ビジネス インテリジェンス プラットフォームです。 ビジネス インテリジェンスとダッシュボード レポート ソフトウェアにより、数回のクリックでデータにアクセスして、データを組み合わせることができます。 構造化および非構造化データ ソースに接続し、最小限のスクリプトとコーディングで複数のソースからのテーブルを結合して、対話型の Web ダッシュボードとレポートを作成できます。 この記事では、Azure Data Explorer を Sisense のデータ ソースとして設定し、サンプル クラスターのデータを視覚化する方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* [Sisense アプリをダウンロードしてインストールします](https://documentation.sisense.com/latest/getting-started/download-install.htm) 

* StormEvents サンプル データを含むクラスターとデータベースを作成します。 詳細については、「[クイック スタート: Azure Data Explorer クラスターとデータベースを作成する](create-cluster-database-portal.md)」、および「[Azure のデータ エクスプローラーにサンプル データを取り込む](ingest-sample-data.md)」を参照してください。

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>Azure Data Explorer JDBC コネクタを使用して Sisense ダッシュ ボードに接続する

1. 次の jar ファイルの最新バージョンをダウンロードして、 *..\Sisense\DataConnectors\jdbcdrivers\adx* にコピーします 

    * activation-1.1.jar
    * adal4j-1.6.0.jar
    * commons-codec-1.10.jar
    * commons-collections4-4.1.jar
    * commons-lang3-3.5.jar
    * gson-2.8.0.jar
    * jcip-annotations-1.0-1.jar
    * json-smart-1.3.1.jar
    * lang-tag-1.4.4.jar
    * mail-1.4.7.jar
    * mssql-jdbc-7.2.1.jre8.jar
    * nimbus-jose-jwt-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar
    
1. **Sisense** アプリを開きます。
1. **[データ]** タブを選択し、 **[+ ElastiCube]** を選択して、新しい ElastiCube モデルを作成します。
    
    ![ElastiCube を選択する](media/sisense/data-select-elasticube.png)

1. **[Add new ElastiCube Model]\(新しい ElastiCube モデルの追加\)** で、ElastiCube モデルに名前を付けて、 **[Save]\(保存\)** で保存します。
   
    ![新しい ElastiCube モデルの追加](media/sisense/add-new-elasticube-model.png)

1. **[+ Data]\(+ データ\)** を選択します。

    ![データ ボタンを選択する](media/sisense/select-data.png)

1. **[Select Connector]\(コネクタの選択\)** タブで、 **[Generic JDBC]\(汎用 JDBC\)** コネクタを選択します。

    ![JDBC コネクタを選択する](media/sisense/select-connector.png)

1. **[Connect]\(接続\)** タブで、 **[Generic JDBC]\(汎用 JDBC\)** コネクタの次のフィールドに入力し、 **[Next]\(次へ\)** を選択します。

    ![JDBC コネクタの設定](media/sisense/jdbc-connector.png)

    |フィールド |説明 |
    |---------|---------|
    |接続文字列     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |JDBC JAR フォルダー  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |ドライバーのクラス名    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |[ユーザー名]   |    AAD ユーザー名     |
    |Password     |   AAD ユーザー パスワード      |

1. **[Select Data]\(データの選択\)** タブで、 **[Select Database]\(データベースの選択\)** を選択して、アクセス許可がある関連データベースを選択します。 この例では、 *[test1]* を選択します。

    ![データベースを選択する](media/sisense/select-database.png)

1. *test* (データベース名) ウィンドウで:
    1. テーブル名を選択してテーブルをプレビューし、テーブル列名を確認します。 不要な列を削除できます。
    1. 関連テーブルのチェック ボックスを選択して、そのテーブルを選択します。 
    1. **[Done]** を選択します。

    ![テーブルを選択する](media/sisense/select-table-see-columns.png)    

1. **[Build]\(ビルド\)** を選択して、データセットをビルドします。 

    * **[Build]\(ビルド\)** ウィンドウで **[Build]\(ビルド\)** を選択します。

      ![[Build]\(ビルド\) ウィンドウ](media/sisense/build-window.png)

    * ビルド プロセスが完了するまで待ち、 **[Build Succeeded]\(ビルドが成功しました\)** を選択します。

      ![ビルドが成功しました](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>Sisense ダッシュ ボードを作成する

1. **[Analytics]\(分析\)** タブで、 **+**  >  **[New Dashboard]\(新しいダッシュボード\)** を選択して、このデータセットでダッシュボードを作成します。

    ![新しいダッシュボード](media/sisense/new-dashboard.png)

1. ダッシュ ボードを選択し、 **[Create]\(作成\)** を選択します。 

    ![ダッシュボードの作成](media/sisense/create-dashboard.png)

1. **[New Widget]\(新しいウィジェット\)** で、 **[+ Select Data]\(+ データの選択\)** を選択して、新しいウィジェットを作成します。 

    ![StormEvents ダッシュ ボードにフィールドを追加する](media/sisense/storm-dashboard-add-field.png)  

1. **[+ Add More Data]\(+ データの追加\)** を選択し、グラフに列を追加します。 

    ![グラフにデータを追加する](media/sisense/add-more-data.png)

1. **[+ Widget]\(+ ウィジェット\)** を選択し、別のウィジェットを作成します。 ウィジェットをドラッグ アンド ドロップして、ダッシュ ボードを再配置します。

    ![Storm ダッシュボード](media/sisense/final-dashboard.png)

ビジュアル分析によって、データを調査し、追加のダッシュボードを作成して、データをアクションにつながる分析情報に変換して、ビジネスに効果をもたらすことができるようになりました。

## <a name="next-steps"></a>次のステップ

* [Azure Data Explorer のクエリを記述する](write-queries.md)

