

次の手順は、Windows ストア クライアント アプリのテストに適用されるものですが、Azure Mobile Services でサポートされている他のすべてのプラットフォームでこれをテストすることができます。


1. Visual Studio で MainPage.xaml.cs を開き、次の `using` ステートメントをファイルの先頭に追加します。
 
        using System.Net.Http;

2. MainPage.xaml.cs で、ユーザー情報をシリアル化しやすいように、次のクラス定義を名前空間に追加します。

	    public class UserInfo
	    {
	        public String displayName { get; set; }
	        public String streetAddress { get; set; }
	        public String city { get; set; }
	        public String state { get; set; }
	        public String postalCode { get; set; }
	        public String mail { get; set; }
	        public String[] otherMails { get; set; }
            
	        public override string ToString()
	        {
	            return "displayName : " + displayName + "\n" +
	                   "streetAddress : " + streetAddress + "\n" +
	                   "city : " + city + "\n" +
	                   "state : " + state + "\n" +
	                   "postalCode : " + postalCode + "\n" +
	                   "mail : " + mail + "\n" +
	                   "otherMails : " + string.Join(", ", otherMails);
	        }
	    }


3. MainPage.xaml.cs で `AuthenticateAsync` メソッドを更新し、カスタム API を呼び出して AAD からのユーザーに関する情報を返すようにします。

        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
                    // Change 'MobileService' to the name of your MobileServiceClient instance.
                    // Sign-in using Facebook authentication.
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory);

                    UserInfo userInfo = await App.MobileService
						.InvokeApiAsync<UserInfo>("getuserinfo", HttpMethod.Get, null);

                    message = string.Format("User info for the logged in user...\n\n{0}",userInfo.ToString());
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

<!---HONumber=Oct15_HO3-->