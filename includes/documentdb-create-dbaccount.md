1.	[Microsoft Azure プレビュー ポータル](https://portal.azure.com/)にサインインします。
2.	ジャンプ バーで **[新規]** をクリックし、**[データ + ストレージ]** を選択して、**[DocumentDB]** をクリックします。 

	![Screen shot of the Azure Preview portal, highlighting the New button, Data + storage in the Create blade, and DocumentDB in the Data + storage blade][1]

	<!-- Alternatively, from the Startboard, you can browse the Azure Marketplace, select **Data + storage**, choose **DocumentDB**, and then click **Create**.  --><!-- ![Screen shot of the Azure Preview portal, showing the Marketplace blade with the DocumentDB tile highlighted, and the DocumentDB blade with the Create button highlighted][2]    -->
   

3. **[新しい DocumentDB]** ブレードで、DocumentDB アカウントに対して必要な構成を指定します。
 
	![Screen shot of the New DocumentDB blade][3]


	- **[ID]** ボックスに、DocumentDB アカウントを識別する名前を入力します。この値は、URI 内のホスト名になります。**ID** に含めることができるのは英小文字、数字、および "-" のみで、文字数は 3 ～ 50 文字にする必要があります。選択したエンドポイント名に *documents.azure.com* が追加され、これが DocumentDB アカウント エンドポイントになります。

	- DocumentDB では標準アカウント レベルのみがサポートされるため、**[アカウント レベル]** レンズはロックされています。詳細については、「[DocumentDB 料金](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409)」を参照してください。

	- **[リソース グループ]** で、DocumentDB アカウントのリソース グループを選択または作成します。既定では、新しいリソース グループが作成されます。ただし、DocumentDB アカウントを追加する既存のリソース グループを選択することもできます。詳細については、[リソース グループを使用した Azure リソースの管理](resource-group-portal.md)に関するページを参照してください。

	- **[サブスクリプション]** で、DocumentDB アカウントに使用する Azure サブスクリプションを選択します。アカウントのサブスクリプションが 1 つである場合は、そのアカウントが自動的に選択されます。
 
	- **[場所]** を指定して、DocumentDB アカウントのホストの場所を指定します。

4.	新しい DocumentDB アカウントのオプションを構成したら、**[作成]** をクリックします。DocumentDB アカウントが作成されるまで数分かかる場合があります。状態を確認するには、スタート画面で進行状況を監視してください。![Screen shot of the Creating tile on the Startboard][4]
  
	または、通知ハブから進行状況を監視できます。

	![Screen shot of the Notifications hub, showing that the DocumentDB account is being created][5]

	![Screen shot of the Notifications hub, showing that the DocumentDB account was created successfully and deployed to a resource group][6]

5.	DocumentDB アカウントが作成されたら、既定の設定で使用するための準備が整います。DocumentDB アカウントの既定の一貫性は Session に設定されることに注意してください。既定の一貫性の設定は、[Azure プレビュー ポータル](https://portal.azure.com/#gallery/Microsoft.DocumentDB)を使用して調整できます。

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

<!---HONumber=62-->