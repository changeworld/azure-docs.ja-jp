<properties
   pageTitle="Azure Data Lake Analytics U-SQL カタログの紹介 |Azure"
   description="Azure Data Lake Analytics U-SQL カタログの紹介"
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# U-SQL カタログの使用

U-SQL カタログを使用して、U-SQL スクリプトで共有できるように、データとコードを構成します。カタログでは、Azure Data Lake のデータを使用して可能な限り最高のパフォーマンスを実現できます。

各 Azure Data Lake Analytics アカウントには、必ず 1 つの U-SQL カタログが関連付けられています。U-SQL カタログを削除することはできません。現在、U-SQL カタログを Data Lake Store アカウント間で共有することはできません。

各 U-SQL カタログには、**Master** というデータベースが含まれています。Master データベースを削除することはできません。各 U-SQL カタログには、追加でさらにデータベースを含めることができます。

U-SQL データベースには以下のものが含まれています。

- アセンブリ - U-SQL スクリプト間で .NET コードを共有します。
- テーブル値関数 – U-SQL スクリプト間で U-SQL コードを共有します。
- テーブル - U-SQL スクリプト間でデータを共有します。
- スキーマ - U-SQL スクリプト間でテーブル スキーマを共有します。

## カタログの管理
各 Azure Data Lake Analytics アカウントには、既定の Azure Data Lake Store アカウントが関連付けられています。この Data Lake Store アカウントを既定の Data Lake Store アカウントと呼びます。U-SQL カタログは、/catalog フォルダーの下の既定の Data Lake Store アカウントに格納されます。/catalog フォルダー内のファイルは削除しないでください。

### Azure ポータルを使用

[ポータルを使用する Data Lake Analytics の管理](data-lake-analytics-use-portal.md#view-u-sql-catalog)に関する記述を参照してください。


### Data Lake Tools for Visual Studio の使用

Data Lake Tools for Visual Studio を使用して、カタログを管理できます。ツールの詳細については、[Data Lake Tools for Visual Studio の使用](data-lake-analytics-data-lake-tools-get-started.md)に関する記述を参照してください。

**カタログを管理するには**

1. Visual Studio を開き、Azure に接続します。手順については、「[Azure への接続](data-lake-analytics-data-lake-tools-get-started.md#connect-to-azure)」を参照してください。
1. **Ctrl + Alt + S** キーを押して、**サーバー エクスプローラー**を開きます。
2. **サーバー エクスプ ローラー**で、**[Azure]**、**[Data Lake Analytics]**、[Data Lake Analytics アカウント]、**[データベース]**、**[master]** の順に展開します。



    - 新しいデータベースを追加するには、**[データベース]** を右クリックしてから **[データベースの作成]** をクリックします。
    - 新しいアセンブリを追加するには、**[アセンブリ]** を右クリックしてから **[アセンブリの登録]** をクリックします。
    - 新しいスキーマを追加するには、**[スキーマ]** を右クリックしてから [スキーマの作成] をクリックします。
    - 新しいテーブルを追加するには、**[テーブル]** を右クリックしてから [テーブルの作成] をクリックします。
    - 新しいテーブル値関数を追加するには、「[Azure Data Lake Analytics ジョブの U-SQL ユーザー定義演算子の開発](data-lake-analytics-u-sql-develop-user-defined-operators.md)」を参照してください。


![U-SQL Visual Studio カタログの参照](./media/data-lake-analytics-use-u-sql-catalog/data-lake-analytics-browse-catalogs.png)


## 関連項目

- 作業開始
    - [Azure ポータルで Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
    - [Azure PowerShell で Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-powershell.md)
    - [Azure .NET SDK で Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-net-sdk.md)
    - [Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)
    - [Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)

- U-SQL と開発
    - [Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)
    - [Azure Data Lake Analytics ジョブに U-SQL ウィンドウ関数を使用する](data-lake-analytics-use-window-functions.md)
    - [Data Lake Analytics ジョブの U-SQL ユーザー定義演算子の開発](data-lake-Analytics-u-sql-user-defined-operators.md)

- 管理
    - [Azure ポータルを使用する Azure Data Lake Analytics の管理](data-lake-analytics-use-portal.md)
    - [Azure PowerShell を使用する Azure Data Lake Analytics の管理](data-lake-analytics-use-powershell.md)
    - [Azure ポータルを使用する Azure Data Lake Analytics ジョブの監視とトラブルシューティング](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

- エンド ツー エンド チュートリアル
    - [Azure Data Lake Analytics の対話型チュートリアルの使用](data-lake-analytics-use-interactive-tutorials.md)
    - [Azure Data Lake Analytics を使用する Web サイト ログの分析](data-lake-analytics-analyze-weblogs.md)

<!---HONumber=AcomDC_0914_2016-->