# Azure で MySQL データベースを作成する方法

このガイドでは、[ClearDB][ClearDB] を使用して [Azure ストア][Azure ストア]から MySQL データベースを作成する方法と、[Azure Website][Azure Website]を作成するときに MySQL データベースをリンク済みリソースとして作成する方法について説明します。[ClearDB][ClearDB] は、サービスとしてのフォルト トレラント データベース プロバイダーであり、Azure データセンターで MySQL データベースを実行および管理し、どのアプリケーションからもデータベースに接続できるようにしています。

## 目次

-   [方法: Azure ストアからの MySQL データベースの作成][方法: Azure ストアからの MySQL データベースの作成]
-   [方法: Azure Website のリンク済みリソースとしての MySQL データベースの作成][方法: Azure Website のリンク済みリソースとしての MySQL データベースの作成]

<div class="dev-callout"> 
<b>注</b> 
<p>MySQL データベースを Web サイト作成プロセスの一部として作成するときは、無料データベースを作成するだけでかまいません。Azure ストアから MySQL データベースを作成すると、無料データベースを作成するか、有料オプションから選択することができます。</p> 
</div>

## <span id="CreateFromStore"></span></a>方法: Azure ストアからの MySQL データベースの作成

[Azure ストア][Azure ストア]から MySQL データベースを作成するには、以下の手順を実行します。

1.  [Azure 管理ポータル][Azure 管理ポータル]にログインします。
2.  ページの下部にある **[+新規]** をクリックし、**[ストア]** を選択します。

    ![ストアからアドオンを選択][ストアからアドオンを選択]

3.  **[ClearDB MySQL データベース]** を選択し、フレームの下部にある矢印をクリックします。

    ![ClearDB MySQL データベースの作成][ClearDB MySQL データベースの作成]

4.  プランを選択してデータベース名を入力し、リージョンを選択してフレームの下部にある矢印をクリックします。

    ![ストアから MySQL データベースを購入][ストアから MySQL データベースを購入]

5.  チェックマークをクリックして購入を完了します。

    ![購入を確認して完了][購入を確認して完了]

6.  データベースが作成されたら、管理ポータルの **[アドオン]** タブから管理できます。

    ![Azure ポータルで MySQL データベースを管理][Azure ポータルで MySQL データベースを管理]

7.  ページの下部にある **[接続文字列]** をクリックして、データベース接続情報を取得できます (上図を参照)。

    ![MySql の接続情報][MySql の接続情報]

## <span id="CreateForWebSite"></span></a>方法: Azure Website のリンク済みリソースとしての MySQL データベースの作成

[Azure Website][Azure Website] を作成するときに MySQL データベースをリンク済みリソースとして作成するには、以下の手順を実行します。

1.  [Azure 管理ポータル][Azure 管理ポータル]にログインします。
2.  ページの下部にある **[新規]** をクリックし、**[コンピューティング]**、**[Web サイト]**、**[データベースと共に作成]** の順に選択します。

    ![データベースを使用する Web サイトの作成][データベースを使用する Web サイトの作成]

3.  Web サイトの **URL** を指定して、サイトの**リージョン**を選択し、**[データベース]** ボックスから **[新しい MySQL データベースを作成する]** を選択します。オプションで、接続文字列の既定の名前を置き換えることができます。ページの下部にある矢印をクリックします。

    ![Web サイトの詳細の指定][Web サイトの詳細の指定]

4.  データベースの**名前**を指定してデータベースの**リージョン**を選択し (これは Web サイトのリージョンと同じにしてください)、ClearDB の法律条項に同意して、フレームの下部にあるチェックマークをクリックします。

    ![MySQL の詳細の指定][MySQL の詳細の指定]

5.  Web サイトが作成されたら、サイトの名前をクリックしてサイトのダッシュボードに移動します。

    ![Web サイトのダッシュボードに移動][Web サイトのダッシュボードに移動]

6.  **[構成]** をクリックします。

    ![構成タブに移動][構成タブに移動]

7.  **[接続文字列]** セクションまで下方向へスクロールし、**[接続文字列の表示]** をクリックします。

    ![接続文字列の表示][接続文字列の表示]

8.  アプリケーションで使用する接続文字列をコピーします。

    ![表示された接続文字列][表示された接続文字列]

> [WACOM.NOTE] 接続文字列には、Web サイト アプリケーションから接続文字列名によりアクセスできます。.NET アプリケーションでは、接続文字列は **connectionStrings** オブジェクトにあります。他のプログラミング言語では、接続文字列には環境変数としてアクセスできます。詳細については、「[Web サイトの構成方法][Web サイトの構成方法]」を参照してください。

  [ClearDB]: http://www.cleardb.com/
  [Azure ストア]: /ja-jp/gallery/store/
  [Azure Website]: /ja-jp/documentation/services/web-sites/
  [方法: Azure ストアからの MySQL データベースの作成]: #CreateFromStore
  [方法: Azure Website のリンク済みリソースとしての MySQL データベースの作成]: #CreateForWebSite
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [ストアからアドオンを選択]: ./media/create-mysql-db/select-store.png
  [ClearDB MySQL データベースの作成]: ./media/create-mysql-db/select-cleardb-mysql.png
  [ストアから MySQL データベースを購入]: ./media/create-mysql-db/purchase-mysql.png
  [購入を確認して完了]: ./media/create-mysql-db/complete-mysql-purchase.png
  [Azure ポータルで MySQL データベースを管理]: ./media/create-mysql-db/manage-mysql-add-on.png
  [MySql の接続情報]: ./media/create-mysql-db/mysql-conn-info.png
  [データベースを使用する Web サイトの作成]: ./media/create-mysql-db/custom_create.png
  [Web サイトの詳細の指定]: ./media/create-mysql-db/provide-website-details.png
  [MySQL の詳細の指定]: ./media/create-mysql-db/provide-mysql-details.png
  [Web サイトのダッシュボードに移動]: ./media/create-mysql-db/go-to-website-dashboard.png
  [構成タブに移動]: ./media/create-mysql-db/go-to-configure-tab.png
  [接続文字列の表示]: ./media/create-mysql-db/show-conn-string.png
  [表示された接続文字列]: ./media/create-mysql-db/shown-conn-string.png
  [Web サイトの構成方法]: ../web-sites-configure/
