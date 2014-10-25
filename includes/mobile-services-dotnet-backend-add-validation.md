ユーザーにより送信されたデータの長さを検証することをお勧めします。このセクションでは、モバイル サービスに送信された文字列データの長さを検証し、長すぎる文字列 (この場合は 10 文字を超える) を拒否するコードを追加します。

1.  **[管理者として実行]** オプションを使用して Visual Studio を起動し、「[モバイル サービスの使用][モバイル サービスの使用] または [Get started with data (データの使用)][Get started with data (データの使用)] チュートリアルで使用したモバイル サービス プロジェクトが入ったソリューションを開きます。

2.  ソリューション エクスプローラー ウィンドウで、todo list サービス プロジェクトを展開し、**[Contollers]** フォルダーを展開します。モバイル サービス プロジェクトの一部である TodoItemController.cs ファイルを開きます。

    ![][]

3.  `PostTodoItem` メソッドを、テキスト文字列が 10 文字以下であることを検証する次のメソッドと置き換えます。項目のテキストが 10 文字より長い場合は、説明メッセージをコンテンツとして含む "HTTP ステータス コード 400 – Bad Request" が返されます。

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            if (item.Text.Length > 10)
            {
                return BadRequest("The Item's Text length must not be greater than 10.");
            }
            else
            {
                TodoItem current = await InsertAsync(item);
                return CreatedAtRoute("Tables", new { id = current.Id }, current);
            } 
        }

4.  サービス プロジェクトを右クリックし、**[ビルド]** をクリックしてモバイル サービス プロジェクトをビルドします。エラーが発生しないことを確認します。

    ![][1]

5.  サービス プロジェクトを右クリックし、**[発行]** をクリックします。以前、「[Getting Started (概要)][モバイル サービスの使用]」または「[Get started with data (データの使用)][Get started with data (データの使用)]」チュートリアルで使用した発行の設定を使用して、Microsoft Azure アカウントにモバイル サービスを発行します。

    > [WACOM.NOTE] または、IIS Express でローカルにホストされているサービスを使用してテストすることもできます。詳細については、チュートリアル「[Get started with data (データの使用)][Get started with data (データの使用)]」を参照してください。

    ![][2]

<!-- URLs. -->

  [モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
  [Get started with data (データの使用)]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
  []: ./media/mobile-services-dotnet-backend-add-validation/mobile-services-open-todoitemcontroller.png
  [1]: ./media/mobile-services-dotnet-backend-add-validation/mobile-services-build-dotnet-service.png
  [2]: ./media/mobile-services-dotnet-backend-add-validation/mobile-services-publish-dotnet-service.png
