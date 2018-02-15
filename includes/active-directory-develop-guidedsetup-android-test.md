## <a name="test-your-code"></a>コードのテスト

1. コードをデバイスやエミュレーターにデプロイします。

2. アプリケーションをテストする準備ができたら、Azure Active Directory アカウント (職場または学校アカウント) または Microsoft アカウント (live.com、outlook.com) を使用してサインインします。 

    ![アプリケーションをテストする](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![ユーザー名とパスワードを入力する](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="provide-consent-for-application-access"></a>アプリケーションによるアクセスに同意する
アプリケーションに初めてサインインするときに、次に示すように、アプリケーションがプロファイルにアクセスし、サインインすることを許可することへの同意を求められます。 

![アプリケーションによるアクセスに同意する](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)


### <a name="view-application-results"></a>アプリケーションの結果を表示する
サインインしたら、Microsoft Graph API の呼び出しによって返された結果が表示されます。 Microsoft Graph API **me** エンドポイントの呼び出しによって、[ユーザー プロファイル](https://graph.microsoft.com/v1.0/me)が返されます。 一般的な Microsoft Graph エンドポイントの一覧については、[Microsoft Graph API 開発者向けドキュメント](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries)をご覧ください。

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>スコープと委任されたアクセス許可の詳細

Microsoft Graph API には、ユーザーのプロファイルを読み取るための *user.read* スコープが必要です。 このスコープは、アプリケーション登録ポータルで登録されたすべてのアプリケーションで、既定で自動的に追加されます。 Microsoft Graph の他の API や、バックエンド サーバーのカスタム API には、追加のスコープが必要な場合があります。 Microsoft Graph API には、ユーザーの予定表を表示するための *Calendars.Read* スコープが必要です。 

アプリケーションのコンテキストでユーザーの予定表にアクセスするには、*Calendars.Read* の委任されたアクセス許可をアプリケーション登録情報に追加します。 次に、*Calendars.Read* スコープを `acquireTokenSilent` 呼び出しに追加します。 

>[!NOTE]
>スコープの数を増やすと、ユーザーは追加の同意を求められることがあります。

<!--end-collapse-->

[!INCLUDE [Help and support](active-directory-develop-help-support-include.md)]
