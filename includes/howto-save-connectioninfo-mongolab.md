MongoLab URI をコードに貼り付けることはできますが、管理しやすくするため環境内で構成することをお勧めします。このようにすると、URI が変化した場合、コードに手を加えなくても Azure ポータルで変更できます。


1. Azure ポータルで、**[Websites]** を選択します。
1. Web サイトの一覧に表示されている Web サイトの名前をクリックします。  
![WebSiteEntry][entry-website]  
Web サイトのダッシュボードが表示されます。

1. メニュー バーで **[構成]** をクリックします。  
![WebSiteDashboardConfig][focus-mongolab-websitedashboard-config]

1. 下方向へ [接続文字列] セクションまでスクロールします。  
![WebSiteConnectionStrings][focus-mongolab-websiteconnectionstring]

1. **[名前]** に「MONGOLAB_URI」と入力します。
1. **[値]** に、先にコピーした接続文字列を貼り付けます。
1. **[種類]** ボックスの一覧で、既定値の **[SQLAzure]** の代わりに **[カスタム]** を選択します。
1. ツール バーの **[保存]** をクリックします。  
![SaveWebSite][button-website-save]

**注:** Azure では、この変数に **CUSTOMCONNSTR\_** というプレフィックスが追加されます。そのため上のコードでは **CUSTOMCONNSTR\_MONGOLAB_URI** を参照しています。

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png

<!--HONumber=42-->
