MongoLab URI をコードに貼り付けることはできますが、管理しやすくするため環境内で構成することをお勧めします。このようにすると、URI が変化した場合、コードに手を加えなくても Azure ポータルで変更できます。

1.  Azure ポータルで、**[Websites]** を選択します。
2.  Web サイトの一覧に表示されている Web サイトの名前をクリックします。  
    ![WebSiteEntry][WebSiteEntry]  
     Web サイトのダッシュボードが表示されます。

3.  メニュー バーで **[構成]** をクリックします。  
    ![WebSiteDashboardConfig][WebSiteDashboardConfig]

4.  下方向へ [接続文字列] セクションまでスクロールします。  
    ![WebSiteConnectionStrings][WebSiteConnectionStrings]

5.  **[名前]** に「MONGOLAB\_URI」と入力します。
6.  **[値]** に、先にコピーした接続文字列を貼り付けます。
7.  **[種類]** ボックスの一覧で、既定値の **[SQLAzure]** の代わりに **[カスタム]** を選択します。
8.  ツール バーの **[保存]** をクリックします。		
    ![SaveWebSite][SaveWebSite]

**注:** Azure では、この変数に **CUSTOMCONNSTR\_** というプレフィックスが追加されます。そのため上のコードでは **CUSTOMCONNSTR\_MONGOLAB\_URI.** になっています。

  [WebSiteEntry]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
  [WebSiteDashboardConfig]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
  [WebSiteConnectionStrings]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
  [SaveWebSite]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png
