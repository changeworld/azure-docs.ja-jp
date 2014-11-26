ギャラリーには、Microsoft、サード パーティ企業、およびオープン ソース ソフトウェア活動によって開発された多種多様な人気の Web アプリケーションが用意されています。ギャラリーから作成した Web アプリケーションは、Azure の管理ポータルへの接続に使用するブラウザー以外に、他のソフトウェアをインストールする必要はありません。

このチュートリアルでは、次のことについて説明します。

-   ギャラリーから新しいサイトを作成する方法。

-   Azure ポータルを使用してサイトをデプロイする方法。

既定のテンプレートを使用する WordPress ブログを構築します。次の図に、完成したアプリケーションを示します。

![Wordpress blog][Wordpress blog]

<div class="dev-callout"><strong>注</strong>
<p>このチュートリアルを完了するには、Azure アカウントが必要です。数分で無料の試用アカウントを作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/develop/php/tutorials/create-a-windows-azure-account/" target="_blank">Azure アカウントの作成に関するページ</a>を参照してください。</p>
</div>

## ポータルでの Web サイトの作成

1.  [Azure の管理ポータル][Azure の管理ポータル]にログインします。

2.  ダッシュボードの左下にある **[新規]** アイコンをクリックします。

    ![Create New][Create New]

3.  **[Web サイト]** アイコンをクリックし、**[ギャラリーから]** をクリックします。

    ![Create From Gallery][Create From Gallery]

4.  一覧から [WordPress] アイコンを見つけてクリックし、**[次へ]** をクリックします。

    ![WordPress from list][WordPress from list]

5.  **[アプリケーションの構成]** ページで、すべてのフィールドの値を入力または選択します。

-   好みの URL 名を入力します
-   **[データベース]** フィールドの **[新しい MySQL データベースを作成します]** は選択された状態にしておきます。
-   最も近いリージョンを選択します

    ![configure your app][configure your app]

1.  その後、**[次へ]** をクリックします。

2.  **[新しいデータベースを作成する]** ページでは、新しい MySQL データベースの名前を指定するか、既定の名前を使用できます。ホスティング場所としては最も近いリージョンを選択します。画面の下部にあるボックスを選択し、ホストされている MySQL データベースの ClearDB の使用条件に同意します。次に、チェックをクリックして、サイトの作成を完了します。

    ![create database][create database]

**[完了]** をクリックすると、Azure によって構築操作とデプロイ操作が開始されます。Web サイトの構築とデプロイが実行されている間、操作の状態が [Web サイト] ページの下部に表示されます。すべての操作が完了すると、サイトの展開に成功したという状態メッセージが表示されます。

## WordPress サイトの起動と管理

1.  **[Web サイト]** ページで新しいサイトをクリックして、サイトのダッシュボードを開きます。

    ![launch dashboard][launch dashboard]

2.  **[ダッシュボード]** 管理ページで、下にスクロールして **[サイトの URL]** の下の左側にあるリンクをクリックし、サイトのウェルカム ページを開きます。

    ![site URL][site URL]

3.  WordPress に必要となる適切な構成情報を入力し、**[WordPress のインストール]** をクリックして構成を完了し、Web サイトのログイン ページを開きます。

    ![login to WordPress][login to WordPress]

4.  **[ようこそ]** ページで指定したユーザー名とパスワードを入力して、新しい WordPress Web サイトにログインします。

5.  新しい WordPress サイトは次のサイトのようになります。

    ![your WordPress site][Wordpress blog]

  [Wordpress blog]: ./media/website-from-gallery/wordpressgallery-09.png
  [Azure の管理ポータル]: http://manage.windowsazure.com
  [Create New]: ./media/website-from-gallery/wordpressgallery-01.png
  [Create From Gallery]: ./media/website-from-gallery/wordpressgallery-02.png
  [WordPress from list]: ./media/website-from-gallery/wordpressgallery-03.png
  [configure your app]: ./media/website-from-gallery/wordpressgallery-04.png
  [create database]: ./media/website-from-gallery/wordpressgallery-05.png
  [launch dashboard]: ./media/website-from-gallery/wordpressgallery-06.png
  [site URL]: ./media/website-from-gallery/wordpressgallery-07.png
  [login to WordPress]: ./media/website-from-gallery/wordpressgallery-08.png
