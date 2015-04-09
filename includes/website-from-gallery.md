Azure Marketplace には、Microsoft、サード パーティ企業、およびオープン ソース ソフトウェア活動によって開発された多種多様な人気の Web アプリが用意されています。Azure Marketplace から作成した Web アプリは、[Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)への接続に使用するブラウザー以外に、他のソフトウェアをインストールする必要はありません。 

このチュートリアルでは、次のことについて説明します。

- Azure Marketplace を通じて新しい web アプリを作成する方法。

- Azure ポータルを使用して Web アプリをデプロイする方法。
 
既定のテンプレートを使用する WordPress ブログを構築します。次の図に、完成したアプリケーションを示します。


![Wordpress ブログ][13]

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure App Service を実際に使ってみるには、「[Try App Service (App Service を使ってみる)](http://go.microsoft.com/fwlink/?LinkId=523751)」 にアクセスしてください。App Service で、有効期限付きのスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## ポータルで Web アプリを作成する

1. Azure ポータルにログインします。

2. Azure Marketplace を開きます。**[Marketplace]** アイコンをクリックするか、ダッシュボードの左下にある **[新規]** アイコンをクリックし、**[Web + モバイル]** を選択し、下部にある **[Azure Marketplace]** をクリックします。
	
	<!--todo:![Create New][5]-->
	
3. Marketplace で、**[Web アプリ]** を選択します。

4. **[スケーラブル WordPress]** アイコンをクリックします。

	<!--todo:![WordPress from list][7]-->
	
5. WordPress アプリの説明を読むには、**[作成]** を選択します。

6. 各部分 (**[Web アプリ]**、**[データベース]**、および **[サブスクリプション]**) をクリックし、必要なフィールドに値を入力するか、値を選択します。
	
- 好みの URL 名を入力します	
- 最も近いリージョンを選択します

	<!--todo:![configure your app][8]-->

7. Web アプリの定義が完了したら、**[作成]** をクリックします。

## WordPress Web アプリの起動と管理
	
1. **[Web アプリ]** ブレードで、新しい Web アプリをクリックしてダッシュボードを開きます。

	<!--todo:![launch dashboard][10]-->

2. **[Essentials]** ページで、**[URL]** の下のリンクをクリックして Web アプリのウェルカム ページを開きます。

	<!--todo:![site URL][11]--> 

3. WordPress をインストールしていない場合は、WordPress に必要となる適切な構成情報を入力し、**[WordPress のインストール]** をクリックして構成を完了し、Web アプリのログイン ページを開きます。

	<!--todo:![login to WordPress][12]-->

4. **[ログイン]** をクリックして、資格情報を入力します。  
	<!--todo:screenshot-->

5. 新しい WordPress Web アプリは次の Web アプリのようになります。    

	<!--todo:![your WordPress site][13]-->






[5]: ./media/website-from-gallery/wordpressgallery-01.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/wordpressgallery-03.png
[8]: ./media/website-from-gallery/wordpressgallery-04.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/wordpressgallery-06.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png

<!--HONumber=49-->