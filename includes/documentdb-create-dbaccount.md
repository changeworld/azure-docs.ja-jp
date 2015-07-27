1.	オンラインの [Microsoft Azure プレビュー ポータル](https://portal.azure.com/)にサインインします。
2.	ジャンプバーで **[新規]**、**[データ + ストレージ]**、**[DocumentDB]** の順にクリックします。 

	![[作成] ブレードの [新規] ボタンと [データ + ストレージ] 、および [データ + ストレージ] ブレードの Azure DocumentDB が強調表示された Azure プレビュー ポータルのスクリーン ショット][1]

	<!-- Alternatively, from the Startboard, you can browse the Azure Marketplace, select **Data + storage**, choose **DocumentDB**, and then click **Create**.  --><!-- ![Screen shot of the Azure preview portal, showing the Marketplace blade with the DocumentDB tile highlighted, and the DocumentDB blade with the Create database button highlighted][2]    -->
   

3. **[新しい DocumentDB アカウント]** ブレードで、DocumentDB アカウントに対して必要な構成を指定します。
 
	![新しい [DocumentDB] ブレードのスクリーン ショット][3]


	- **[ID]** ボックスに、DocumentDB アカウントを識別する名前を入力します。**ID** が検証されると、緑色のチェック マークが **[ID]** ボックスに表示されます。この **ID** の値は、URI 内のホスト名になります。**ID** に含めることができるのは英小文字、数字、および "-" のみで、文字数は 3 ～ 50 文字にする必要があります。選択したエンドポイント名に *documents.azure.com* が追加され、これが DocumentDB アカウント エンドポイントになります。

	- DocumentDB では標準アカウント レベルのみがサポートされるため、**[アカウント レベル]** レンズはロックされています。詳細については、「[DocumentDB の料金](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409)」を参照してください。

	- **[リソース グループ]** で、DocumentDB アカウントのリソース グループを選択または作成します。既定では、新しいリソース グループが作成されます。ただし、DocumentDB アカウントを追加する既存のリソース グループを選択することもできます。詳細については、[リソース グループを使用した Azure リソースの管理](resource-group-portal.md)に関するページを参照してください。

	- **[サブスクリプション]** で、DocumentDB アカウントに使用する Azure サブスクリプションを選択します。アカウントのサブスクリプションが 1 つである場合は、既定ではそのアカウントが選択されます。
 
	- **[場所]** を使用して、DocumentDB アカウントのホストの場所を指定します。

4.	新しい DocumentDB アカウントのオプションを構成したら、**[作成]** をクリックします。DocumentDB アカウントが作成されるまで数分かかる場合があります。状態を確認するには、スタート画面で進行状況を監視してください。![スタート画面の [作成中] タイルのスクリーン ショット (オンライン データベース クリエーター)][4]
  
	または、通知ハブから進行状況を監視できます。

	![(迅速なデータベースの作成) 作成中の DocumentDB アカウントを示す [通知] ハブのスクリーン ショット (オンライン データベース クリエーターの通知)][5]

	![DocumentDB アカウントが正常に作成され、リソース グループにデプロイされたことを示す通知ハブのスクリーンショット][6]

5.	DocumentDB アカウントが作成されたら、オンライン ポータルの既定の設定で使用するための準備が整います。DocumentDB アカウントの既定の一貫性は **Session** に設定されることに注意してください。既定の一貫性の設定は、**[DocumentDB アカウント]** ブレードの **[既定の一貫性]** タイルをクリックして変更できます。

    ![Screen shot of the Resource Group blade][7]

<!--Image references-->
[1]: media/documentdb-create-dbaccount/ca1.png
[2]: media/documentdb-create-dbaccount/ca2.png
[3]: media/documentdb-create-dbaccount/ca3.png
[4]: media/documentdb-create-dbaccount/ca4.png
[5]: media/documentdb-create-dbaccount/ca5.png
[6]: media/documentdb-create-dbaccount/ca6.png
[7]: media/documentdb-create-dbaccount/ca7.png

[How to: Create a DocumentDB account]: #Howto
[Next steps]: #NextSteps
[documentdb-manage]: ../articles/documentdb/documentdb-manage.md

<!---HONumber=July15_HO3-->