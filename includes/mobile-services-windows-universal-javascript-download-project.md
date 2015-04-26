
このチュートリアルは、Visual Studio 2013 のユニバーサル Windows アプリ プロジェクトである [GetStartedWithMobileServices アプリケーション](http://go.microsoft.com/fwlink/p/?LinkID=510826) に基づいています。このアプリケーションの UI は、追加された項目がメモリにローカルに格納される点を除き、モバイル サービスのクイック スタートで生成したアプリケーションと同じです。 

1. JavaScript バージョンの GetStartedWithMobileServices サンプル アプリケーションを[デベロッパー サンプル コード集のサイト]からダウンロードします。 

3. Visual Studio 2013 で、ダウンロードされたプロジェクトを開き、ソリューション エクスプローラーで js フォルダーを展開し、default.js ファイルを調べます。

   	追加された **TodoItem** オブジェクトはメモリ内の  `WinJS.Binding.List` に格納されます。

4. **F5** キーを押してプロジェクトをリビルドし、アプリケーションを開始します。

5. アプリケーションで、**[Insert a TodoItem]** に任意のテキストを入力し、**[Save]** をクリックします。

   	![](./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup.png) 

   	Notice that the saved text is displayed.

6. Windows Phone 8.1 プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックしてから、**F5** キーを押して Windows Phone ストア アプリを起動します。  

	![](./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup-wp8.png)

7. 手順 3 と 4 を繰り返して、サンプルが同じように動作することを確認します。
<!--HONumber=42-->
