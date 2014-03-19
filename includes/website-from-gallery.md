ギャラリーには、Microsoft、サード パーティ企業、およびオープン ソース ソフトウェア活動によって開発された多種多様な人気の Web アプリケーションが用意されています。ギャラリーから作成した Web アプリケーションは、Windows Azure の管理ポータルへの接続に使用するブラウザー以外に、他のソフトウェアをインストールする必要はありません。

このチュートリアルでは、次のことについて説明します。

- ギャラリーから新しいサイトを作成する方法。

- Web Azure のポータルを使用してサイトを展開する方法。
 
既定のテンプレートを使用する WordPress ブログを構築します。次の図に、完成したアプリケーションを示します。


![WordPress ブログ][13]

<div class="dev-callout"><strong>注</strong>
<p>このチュートリアルを完了するには、Windows Azure アカウントが必要です。数分で無料の試用アカウントを作成することができます。詳細については、<a href="http://www.windowsazure.com/en-us/develop/php/tutorials/create-a-windows-azure-account/" target="_blank">Windows Azure アカウントの作成に関するページ</a>を参照してください。</p>
</div>
<br />

## ポータルでの Web サイトの作成

1. [Windows Azure の管理ポータル](http://manage.windowsazure.com)にログインします。

2. ダッシュボードの左下にある **[新規]** アイコンをクリックします。
	
	![新規作成][5]

3. **[Web サイト]** アイコンをクリックし、**[ギャラリーから]** をクリックします。
	
	![ギャラリーから作成][6]

4. 一覧から [WordPress] アイコンを見つけてクリックし、**[次へ]** をクリックします。
	
	![WordPress リスト][7]

5. **[アプリケーションの構成]** ページで、すべてのフィールドの値を入力または選択します。
	
- 好みの URL 名を入力します	
- **[データベース]** フィールドの **[新しい MySQL データベースを作成します]** は選択された状態にしておきます。
- 最も近いリージョンを選択します

	![アプリケーションの構成][8]

6. そして、**[次へ]** をクリックします。

7. **[新しいデータベースを作成する]** ページでは、新しい MySQL データベースの名前を指定するか、既定の名前を使用できます。ホスティング場所としては最も近いリージョンを選択します。画面の下部にあるボックスを選択し、ホストされている MySQL データベースの ClearDB の使用条件に同意します。次に、チェックをクリックして、サイトの作成を完了します。
	
	![データベースの作成][9]

**[完了]** をクリックすると、Windows Azure によって構築操作と展開操作が開始されます。Web サイトの構築と展開が実行されている間、操作の状態が [Web サイト] ページの下部に表示されます。すべての操作が完了すると、サイトの展開に成功したという状態メッセージが表示されます。

## WordPress サイトの起動と管理

1. **[Web サイト]** ページで新しいサイトをクリックして、サイトのダッシュボードを開きます。

	![ダッシュボードの起動][10]

2. **[ダッシュボード]** 管理ページで、下にスクロールして **[サイトの URL]** の下の左側にあるリンクをクリックし、サイトのウェルカム ページを開きます。

	![サイトの URL][11] 

3. WordPress に必要となる適切な構成情報を入力し、**[WordPress のインストール]** をクリックして構成を完了し、Web サイトのログイン ページを開きます。

	![WordPress へのログイン][12]

4. **[ようこそ]** ページで指定したユーザー名とパスワードを入力して、新しい WordPress Web サイトにログインします。

5. 新しい WordPress サイトは次のサイトのようになります。

	![WordPress サイト][13]





[5]: ./media/website-from-gallery/wordpressgallery-01.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/wordpressgallery-03.png
[8]: ./media/website-from-gallery/wordpressgallery-04.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/wordpressgallery-06.png
[11]: ./media/website-from-gallery/wordpressgallery-07.png
[12]: ./media/website-from-gallery/wordpressgallery-08.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png






