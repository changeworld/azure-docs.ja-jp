
<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Azure ポータルから接続文字列を取得する
[Azure Portal](https://portal.azure.com/) を使って、クライアント プログラムが Azure SQL Database と対話するために必要な接続文字列を取得します。 

1. **[すべて参照]** > **[SQL データベース]** を選びます。

2. **[SQL データベース]** ブレードの左上近辺にあるフィルター テキスト ボックスにデータベースの名前を入力します。

3. お使いのデータベースの行を選びます。

4. お使いのデータベースのブレードが表示されたら、画面を見やすくするために、**[最小化]** ボタンを選んで、参照とデータベースのフィルター処理に使ったブレードを折りたたみます。 
   
    ![フィルター処理を行ってデータベースを分離する][10-FilterDatabase]
5. お使いのデータベースのブレードで、**[データベース接続文字列の表示]** を選びます。

6. ADO.NET 接続ライブラリを使用する場合は、 **ADO**というラベルが付いた文字列をコピーします。 
   
    ![データベース用の ADO 接続文字列をコピーする][20-CopyAdoConnectionString]
7. 特定の形式の接続文字列情報を、クライアント プログラム コードに貼り付けます。

詳しくは、「[接続文字列と構成ファイル](http://msdn.microsoft.com/library/ms254494.aspx)」をご覧ください。

<!-- Image references. -->

[10-FilterDatabase]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-a.png

[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
