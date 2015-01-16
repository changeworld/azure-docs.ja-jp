<properties pageTitle="Appcelerator Titanium での Azure Mobile Services の使用" metaKeywords="" description="次のチュートリアルに従って、Appcelerator 開発用の Azure Mobile Services を使用します。 " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="Appcelerator team;mahender" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-appcelerator" ms.devlang="multiple" ms.topic="article" ms.date="11/24/2014" ms.author="Appcelerator team;mahender" />

# <a name="getting-started"> </a>モバイル サービスの使用

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

このチュートリアルでは、Appcelerator で構築したアプリケーションで Azure Mobile Services を活用する方法について説明します。

このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリ データを保存する簡単な _To do list_ アプリの両方を作成します。作成するモバイル サービスは、サーバー側ビジネス ロジックのために JavaScript を使用します。

このチュートリアルを完了するには、[Appcelerator Titanium] が必要です。

iOS 向けに構築する場合は、Xcode 5.1 および iOS 7.1 SDK またはそれ以降のバージョンも必要です。 

Android 向けに構築する場合は、Android 4.3 またはそれ以降の SDK も必要になります。

## <a name="create-new-service"> </a>新しいモバイル サービスを作成する

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## 新しい Appcelerator アプリケーションを作成する

1. Mobile Services のポータルで、使用するモバイル サービスの **[データ]** タブをクリックします。

2. **[テーブルの追加]** をクリックし、**TodoItem** という名前の新しいテーブルを作成します。

3. 新しい Appcelerator [Todolist アプリケーション]をダウンロードし、プロジェクトを解凍します。

4. まだインストールしていない場合は、[Appcelerator Titanium Studio][Appcelerator Titanium] と SDK v3.2.1 以降をダウンロードしてインストールします。プロジェクトを実行するには、Xcode (v 5.0 +) と Android SDK (v 4.3+) の両方、または一方も必要になります。

5. Mobile Services ポータルに戻り、**ダッシュボード**で **[キーの管理]** をクリックし、**アプリケーション キー**をコピーします。

5. アプリケーションの index.js で、`<---App Name---->` および `<------------APP KEY------------>` を、使用するモバイル サービスの値と置き換えます。

## 新しい Appcelerator アプリケーションを実行する ##

このチュートリアルの最後に、新しいアプリケーションをビルドして実行します。

1. Titanium Studio を開き、**[File]、[Import]** の順に移動して、前にダウンロードしたプロジェクトをインポートします。

    ![][0]

2.	次の画面で、**[Existing Projects into Workspace]** を選択し、**[Next]** をクリックします。

    ![][1]

3.	プロジェクト選択画面で、参照オプションを使用して、Azure 管理ポータルからダウンロードした Azure Titanium プロジェクトを見つけます。

    ![][2]

4.	プロジェクト セクションで選択したプロジェクトが表示されます。または、[Copy projects into workspace] オプションをオンにすると、ダウンロードしたプロジェクトがワークスペースにコピーされます。最後に、[Finish] をクリックして、Titanium Studio でプロジェクトを開きます。

    ![][3]

5.	プロジェクトを実行するプラットフォーム (iOS/Android) を選択します。

    ![][4]

6.	[実行] をクリックして、プロジェクトを構築し、iPhone シミュレーターまたは Android エミュレーターを起動します。

7.	Azure 管理ポータルでの設定内容に基づいて選択します。

    ![][5]

8.	次の画面で、正符号 (+) アイコンをクリックし、「このチュートリアルを完了する」など意味のあるテキストを入力して [保存] をクリックします。<br />

    ![][6]

    ![][7]

これにより、Windows Azure でホストされている新しいモバイル サービスに POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目がモバイル サービスによって返され、データが一覧に表示されます。

![][8]


>[WACOM.NOTE] モバイル サービスにアクセスして TodoService.m ファイルにあるデータを照会および挿入するコードを確認できます。

4. 管理ポータルに戻り、**[データ]** タブ、**TodoItems** テーブルの順にクリックします。

   	![][9]

   	これで、アプリケーションによってテーブルに挿入されたデータを参照できます。

   	![][10]


## <a name="next-steps"> </a>次のステップ
クイック スタートはこれで完了です。モバイル サービスで重要になるこれ以外の作業については、以下のトピックを参照してください。 

* [認証の使用]
  <br/>ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用] 
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。



<!-- Images. -->
[0]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image007.png
[1]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image008.png
[2]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image009.png
[3]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image010.png
[4]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image011.png
[5]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image012.png
[6]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image013.png
[7]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image014.png
[8]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image015.png
[9]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/mobile-data-tab.png
[10]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/mobile-data-browse.png

<!-- URLs. -->
[Todolist アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=506859
[Appcelerator Titanium]: http://go.microsoft.com/fwlink/p/?LinkID=509987
[認証の使用]: /ja-jp/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users
[プッシュ通知の使用]: /ja-jp/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push

<!--HONumber=35.1-->
