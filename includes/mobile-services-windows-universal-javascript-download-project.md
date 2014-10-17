このチュートリアルは、Visual Studio 2013 のユニバーサル Windows アプリ プロジェクトの [GetStartedWithMobileServices アプリケーション][GetStartedWithMobileServices アプリケーション]に基づいています。このアプリケーションの UI は、追加された項目がメモリにローカルに格納される点を除き、モバイル サービスのクイック スタートで生成したアプリケーションと同じです。

1.  JavaScript バージョンの GetStartedWithMobileServices サンプル アプリケーションをデベロッパー サンプル コード集のサイトからダウンロードします。

2.  Visual Studio 2013 で、ダウンロードされたプロジェクトを開き、ソリューション エクスプローラーで js フォルダーを展開し、default.js ファイルを調べます。

    追加された **TodoItem** オブジェクトはメモリ内の `WinJS.Binding.List` に格納されます。

3.  **F5** キーを押してプロジェクトをリビルドし、アプリケーションを開始します。

4.  アプリケーションで、**[Insert a TodoItem]** に任意のテキストを入力し、**[Save]** をクリックします。

    ![][]

    保存されたテキストが表示されます。

5.  Windows Phone 8.1 プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックしてから、**F5** キーを押して Windows Phone ストア アプリを起動します。

    ![][1]

6.  手順 3 と 4 を繰り返して、サンプルが同じように動作することを確認します。

  [GetStartedWithMobileServices アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkID=510826
  []: ./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup.png
  [1]: ./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup-wp8.png
