## Azure のストレージ アカウントの作成

Azure ストレージを使用するには、ストレージ アカウントが必要です。ストレージ アカウントを作成するには、次のステップを実行します\(Azure サービス管理クライアント ライブラリまたはサービス管理 [REST API] を使用してストレージ アカウントを作成することもできます\)。

1.  [Azure の管理ポータル]にログインします。

2.  ナビゲーション ウィンドウの下部にある **\[+新規\]** をクリックします。

	![\+new][plus-new]

3.  **\[データ サービス\]**、**\[ストレージ\]**、**\[簡易作成\]** の順にクリックします。

	![Quick create dialog][quick-create-storage]

4.  \[URL\] に、ストレージ アカウントの URI で使用するサブドメイン名を入力します。文字数は 3 ～ 24 文字で、アルファベット小文字と数字を使用できます。この名前は、対応するサブスクリプションの BLOB リソース、キュー リソース、またはテーブル リソースのアドレス指定に使用される URL のホスト名になります。

5.  ストレージの配置先となるリージョンまたはアフィニティ グループを選択します。Azure アプリケーションからストレージを使用する場合は、アプリケーションを展開するリージョンと同じリージョンを選択します。

6. 必要に応じて、アカウントで使用するレプリケーションのタイプを選択できます。既定のレプリケーションのタイプは Geo 冗長レプリケーションで、最高度の持続性が提供されます。レプリケーションのオプションの詳細については、「[Azure Storage 冗長オプション](http://msdn.microsoft.com/library/azure/dn727290.aspx)」および「[Azure Storage Team Blog \(Azure ストレージ チーム ブログ\)](http://blogs.msdn.com/b/windowsazurestorage/)」を参照してください。

6.  **\[ストレージ アカウントの作成\]** をクリックします。

[REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure の管理ポータル]: http://manage.windowsazure.com
[plus-new]: ./media/storage-create-account-include/plus-new.png
[quick-create-storage]: ./media/storage-create-account-include/quick-storage-2.png

<!--HONumber=52-->
