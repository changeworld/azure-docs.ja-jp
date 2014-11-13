Azure ストレージを使用するには、ストレージ アカウントが必要です。ストレージ アカウント
を作成するには、次のステップを実行します (
Azure サービス管理クライアント ライブラリまたはサービス管理 [REST API] を使用してストレージ アカウントを作成することもできます)。

1.  [Azure の管理ポータル][Azure の管理ポータル]にログインします。

2.  ナビゲーション ウィンドウの下部にある **[+新規]** をクリックします。

    ![+new][+new]

3.  **[データ サービス]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。

    ![Quick create dialog][Quick create dialog]

4.  [URL] で、ストレージ アカウントの URI で使用する
    サブドメイン名を入力します。文字数は 3 ～ 24 文字で、アルファベット小文字と
    数字を使用できます。この値は、対応するサブスクリプションの BLOB リソース、
    キュー リソース、またはテーブル リソースのアドレス指定に使用される URL の
    ホスト名になります。

5.  ストレージの配置先となるリージョンまたはアフィニティ グループ
    を選択します。Azure アプリケーションからストレージを
    使用する場合は、アプリケーションを展開するリージョンと同じリージョンを
    選択します。

6.  必要に応じて、アカウントで使用するレプリケーションのタイプを選択できます。既定のレプリケーションのタイプは Geo 冗長レプリケーションで、最高度の持続性が提供されます。レプリケーションのオプションの詳細については、「[Azure Storage 冗長オプション][Azure Storage 冗長オプション]」および「[Azure Storage Team Blog (Azure ストレージ チーム ブログ)][Azure Storage Team Blog (Azure ストレージ チーム ブログ)]」を参照してください。

7.  **[ストレージ アカウントの作成]** をクリックします。

  [Azure の管理ポータル]: http://manage.windowsazure.com
  [+new]: ./media/create-storage-account/plus-new.png
  [Quick create dialog]: ./media/create-storage-account/quick-storage-2.png
  [Azure Storage 冗長オプション]: http://msdn.microsoft.com/ja-jp/library/azure/dn727290.aspx
  [Azure Storage Team Blog (Azure ストレージ チーム ブログ)]: http://blogs.msdn.com/b/windowsazurestorage/
