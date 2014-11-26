MongoLab データベースをプロビジョニングすると、MongoLab により Azure に MongoDB の標準接続文字列形式で URI が送信されます。この値は、選択した MongoDB ドライバーをとおして MongoDB 接続を初期化するために使用されます。接続文字列の詳細については、mongodb.org の[接続に関するページ][接続に関するページ]を参照してください。

**この URI は、データベース ユーザー名とパスワードを含んでいます。機密情報として扱い、他人と共有しないでください。**

この URI は、Azure ポータルで次のステップを使用して取得できます。

1.  **[アドオン]** を選択します。	
    ![AddonsButton][AddonsButton]
2.  アドオンの一覧で MongoLab サービスを見つけます。		
    ![MongolabEntry][MongolabEntry]
3.  アドオンの名前をクリックして、アドオン ページに移動します。
4.  **[接続文字列]** をクリックします。	
    ![ConnectionInfoButton][ConnectionInfoButton]	
     MongoLab の URI が表示されます。	
    ![ConnectionInfoScreen][ConnectionInfoScreen]
5.  MONGOLAB\_URI 値の右側のクリップボード ボタンをクリックし、値全体をクリップボードにコピーします。

  [接続に関するページ]: http://www.mongodb.org/display/DOCS/Connections
  [AddonsButton]: ./media/howto-get-connectioninfo-mongolab/button-addons.png
  [MongolabEntry]: ./media/howto-get-connectioninfo-mongolab/entry-mongolabaddon.png
  [ConnectionInfoButton]: ./media/howto-get-connectioninfo-mongolab/button-connectioninfo.png
  [ConnectionInfoScreen]: ./media/howto-get-connectioninfo-mongolab/dialog-mongolab_connectioninfo.png
