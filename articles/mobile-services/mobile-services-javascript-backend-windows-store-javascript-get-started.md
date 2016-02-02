<properties
	pageTitle="Windows ストア JavaScript アプリケーション用 Mobile Services の使用 | Azure Mobile Services"
	description="このチュートリアルでは、JavaScript で Windows ストア用の開発を行う場合に Azure Mobile Services を使用する方法を示します。"
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="javascript"
	ms.topic="get-started-article"
	ms.date="11/06/2015"
	ms.author="glenga"/>

# Mobile Services の使用

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

このチュートリアルでは、Azure Mobile Services を使用して Windows ストア JavaScript アプリにクラウドベースのバックエンド サービスを追加する方法を示します。このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリケーション データを保存する簡単な *To do list* アプリケーションの両方を作成します。作成するモバイル サービスは、サーバー側ビジネス ロジックのために JavaScript を使用します。

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F)を参照してください。
* [Visual Studio 2013 Express for Windows]

## 新しいモバイル サービスを作成する

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 新しい Windows ストア アプリを作成する

モバイル サービスを作成したら、Azure クラシック ポータルの簡単なクイック スタートに従って、モバイル サービスに接続する新しい Windows ストア 8.1 JavaScript アプリを作成できます。

1.  [Azure クラシック ポータル]で、**[Mobile Services]** をクリックし、先ほど作成したモバイル サービスをクリックします。


2. [クイック スタート] タブの **[プラットフォームの選択]** で **[Windows]** を選択し、**[新しい Windows ストア アプリを作成する]** を展開します。

3. まだインストールしていない場合は、[Visual Studio 2013][Visual Studio 2013 Express for Windows] をダウンロードし、ローカル コンピューターまたは仮想マシンにインストールします。

4. **[TodoItem テーブルを作成する]** をクリックして、アプリケーション データを格納するテーブルを作成します。

5. **[アプリケーションをダウンロードして実行する]** で、アプリケーションの言語を選択し、**[ダウンロード]** をクリックします。

  	これにより、モバイル サービスに接続する、*To do list* サンプル アプリケーションのプロジェクトがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

## Windows アプリケーションを実行する

このチュートリアルの最後に、新しいアプリケーションをビルドして実行します。

1. 圧縮されたプロジェクト ファイルの保存場所を参照し、ファイルをコンピューター上に展開して、Visual Studio でソリューション ファイルを開きます。

2. **F5** キーを押してプロジェクトをリビルドし、アプリケーションを開始します。

3. アプリケーションで、*[Insert a TodoItem]* ボックスに意味のあるテキスト (たとえば、「**チュートリアルの完了**」) を入力し、**[Save]** をクリックします。

   	これで、Azure でホストされている新しいモバイル サービスに POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目はモバイル サービスによって返され、データはアプリケーションの 2 番目の列に表示されます。

4. (オプション) アプリを再実行し、アプリケーションの起動後に前の手順で保存したデータがモバイル サービスから読み込まれる点に注目してください。
 
4. [Azure クラシック ポータル]に戻り、**[データ]** タブ、**TodoItems** テーブルの順にクリックします。

   	これで、アプリケーションによってテーブルに挿入されたデータを参照できます。

>[AZURE.NOTE] モバイル サービスにアクセスして default.js ファイルにあるデータを照会および挿入するコードを確認できます。

## 次のステップ
クイックスタートを完了しました。次は、[HTML/JavaScript 向け Mobile Services クライアント](mobile-services-html-how-to-use-client-library.md)の操作について説明します。

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Visual Studio 2013 Express for Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure クラシック ポータル]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0128_2016-->