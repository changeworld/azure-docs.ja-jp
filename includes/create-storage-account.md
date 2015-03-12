## <a name="create-account"> </a>Azure ストレージ アカウントの作成

Azure ストレージを使用するには、ストレージ アカウントが必要です。ストレージ アカウントを 
作成するには、以下の手順を実行します(また、
Azure サービス管理クライアント ライブラリまたはサービス管理 [REST API] を作成することもできます。

1.  [Azure の管理ポータル]にログインします。

2.  ナビゲーション ウィンドウの下部にある **[+新規]** をクリックします。

	![+new][plus-new]

3.  **[データ サービス]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。

	![Quick create dialog][quick-create-storage]

4.  [URL] ボックスに、ストレージ アカウントの URI に使用するサブドメイン名を
    入力します。文字数は 3 ～ 24 文字とし、アルファベット小文字と数字を
    使用できます。この値は、対応するサブスクリプションの BLOB リソース、キュー リソース、
    またはテーブル リソースのアドレス指定に使用される URI 内で
    使用します。

5.  ストレージの配置先となるリージョンまたはアフィニティ グループを
    選択します。Azure アプリケーションからストレージを使用する場合は、
    アプリケーションのデプロイ先と同じリージョンを
    選択してください。

6. 必要に応じて、アカウントで使用するレプリケーションのタイプを選択できます。既定のレプリケーションのタイプは Geo 冗長レプリケーションで、最高度の持続性が提供されます。レプリケーションのオプションの詳細については、「[Azure Storage 冗長オプション](http://msdn.microsoft.com/library/azure/dn727290.aspx)」と「[Azure Storage Team Blog (Azure ストレージ チーム ブログ)](http://blogs.msdn.com/b/windowsazurestorage/)」を参照してください。

6.  **[ストレージ アカウントの作成]** をクリックします。

[REST API の使用]: http://msdn.microsoft.com/library/windowsazure/hh264518.aspx
[Azure 管理ポータル]: http://manage.windowsazure.com
[plus-new]: ./media/create-storage-account/plus-new.png
[quick-create-storage]: ./media/create-storage-account/quick-storage-2.png
<!--HONumber=42-->
