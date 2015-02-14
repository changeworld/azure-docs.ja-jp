

次の手順は、Windows ストア クライアント アプリのテストに適用されるものですが、Azure Mobile Services でサポートされている他のすべてのプラットフォームでこれをテストすることができます。 


1. Visual Studio で MainPage.xaml.cs を開き、次の  `using` ステートメントをファイルの最上部に追加します。
 
        using System.Net.Http;

2. MainPage.xaml.cs で、ユーザー情報をシリアル化しやすいように、次のクラス定義を名前空間に追加します。

        public class UserInfo
        {
            public String displayName { get; set; }
            public String streetAddress { get; set; }
            public String city { get; set; }
            public String state { get; set; }
            public String postalCode { get; set; }
        }


3. IMainPage.xaml.cs で、 `AuthenticateAsync` メソッドを更新し、カスタム API を呼び出して、AAD からのユーザーに関する追加情報を返すようにします。 

        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory);
                    UserInfo userInfo = await App.MobileService.InvokeApiAsync<UserInfo>("getuserinfo", 
                        HttpMethod.Get, null);
                    message = string.Format("{0}, you are now logged in.\n\nYour address is...\n\n{1}\n{2}, {3} {4}", 
                        userInfo.displayName, userInfo.streetAddress, userInfo.city, userInfo.state, 
                        userInfo.postalCode);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }


4. 変更内容を保存して、構文エラーがないことを確認するためにサービスを構築します。  
<!--HONumber=42-->
