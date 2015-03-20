1.	[Azure プレビュー ポータル](https://portal.azure.com/)にサインインします。
2.	ジャンプ バーで **[New]** をクリックし、**[Data + storage]** を選択して **[DocumentDB]** をクリックします。 

	![Screen shot of the Azure Preview portal, highlighting the **New** button, **Data + storage** in the Create blade, and **DocumentDB** in the Data + storage blade][1]   

	または、スタートボードから Azure Marketplace を参照し、**[データ + 分析]**、**[DocumentDB]** と選択して **[作成]** をクリックします。  
	
	![Screen shot of the Azure Preview portal, showing the Marketplace blade with the DocumentDB tile highlighted, and the DocumentDB blade with the Create button highlighted][2]   
   

3. **[新しい DocumentDB (プレビュー)]** ブレードで、DocumentDB アカウントに対して必要な構成を指定します。 
 
	![Screen shot of the New DocumentDB (Preview) blade][3] 


	- **[ID]** ボックスに、DocumentDB を識別する名前を入力します。この値は、URI 内のホスト名になります。ID に含めることができるのは英小文字、数字、および '-' 文字のみで、文字数は 3 ～ 50 文字にする必要があります。選択したエンドポイント名に documents.azure.com が追加され、これが DocumentDB アカウント エンドポイントになります。

	- DocumentDB プレビューで単一の標準料金レベルがサポートされているため、**[料金レベル]** レンズはロックされています。詳細については、「[DocumentDB の料金](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409)」を参照してください。

	- **[省略可能な構成]**レンズは、DocumentDB アカウントに割り当てられる初期の容量を指定するときに使用されます。DocumentDB では、容量単位を利用して、占有データベース ストレージとスループットが各容量単位に含まれる、DocumentDB アカウントを拡張できます。既定では、1 つの容量単位がプロビジョニングされます。DocumentDB アカウントで利用可能な容量単位の数は、[プレビュー管理ポータル](https://portal.azure.com/#gallery/Microsoft.DocumentDB)でいつでも調整できます。DocumentDB アカウントの容量とスループットの詳細については、「[DocumentDB の容量とパフォーマンスの管理][documentdb-manage]」をご覧ください。

	- **[リソース グループ]** で、DocumentDB アカウントのリソース グループを選択または作成します。既定では、新しいリソース グループが作成されます。ただし、DocumentDB アカウントを追加する既存のリソース グループを選択することもできます。詳細については、「[リソース グループを使用した Azure リソースの管理](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups/)」を参照してください。

	- **[サブスクリプション]** で、DocumentDB アカウントに使用する Azure サブスクリプションを選択します。アカウントのサブスクリプションが 1 つである場合は、そのアカウントが自動的に選択されます。*
 
	- **[場所]** を使用して、DocumentDB アカウントがホストされる場所を指定します。   

3.	新しい DocumentDB アカウントのオプションを構成したら、**[作成]** をクリックします。DocumentDB アカウントが作成されるまで数分かかる場合があります。状態を確認するには、スタートボードで進行状況を監視してください。  
	![Screen shot of the Creating tile on the Startboard][4]  
  
	または、通知ハブから進行状況を監視できます。  

	![Screen shot of the Notifications hub, showing that the DocumentDB account is being created][5]  

	![Screen shot of the Notifications hub, showing that the DocumentDB account was created successfully and deplyed to a resource group][6]

4.	DocumentDB アカウントが作成されたら、既定の設定で使用するための準備が整います。

	*DocumentDB アカウントの既定の一貫性は Session に設定されることに注意してください。既定の一貫性の設定は、[プレビュー管理ポータル](https://portal.azure.com/#gallery/Microsoft.DocumentDB)を使用して調整できます。*  
	![Screen shot of the Resource Group blade][7]  


<!--Image references-->
[1]: ./media/documentdb-create-dbaccount/ca1.png
[2]: ./media/documentdb-create-dbaccount/ca2.png
[3]: ./media/documentdb-create-dbaccount/ca3.png
[4]: ./media/documentdb-create-dbaccount/ca4.png
[5]: ./media/documentdb-create-dbaccount/ca5.png
[6]: ./media/documentdb-create-dbaccount/ca6.png
[7]: ./media/documentdb-create-dbaccount/ca7.png

[方法:DocumentDB アカウントを作成する]: #Howto
[次のステップ]: #NextSteps
[documentdb-manage]:../documentdb-manage/
<!--HONumber=47-->
