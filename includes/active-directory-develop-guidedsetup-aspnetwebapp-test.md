## <a name="test-your-code"></a>コードのテスト

Visual Studio でお使いのアプリケーションをテストするには、**F5** キーを押してプロジェクトを実行します。 ブラウザーで http://<span></span>localhost:{ポート} の場所を開くと、**[Microsoft アカウントでサインイン]** ボタンが表示されます。 ボタンを選択して、サインイン プロセスを開始します。

テストを実行する準備が整ったら、Microsoft Azure Active Directory (Azure AD) アカウント (職場または学校のアカウント) または個人用の Microsoft アカウント (<span>live.</span>com または <span>outlook.</span>com) を使用してサインインします。

![Microsoft アカウントでのサインイン](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Microsoft アカウントへのサインイン](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>アプリケーションの結果を表示する
サインインした後、ユーザーは Web サイトのホーム ページにリダイレクトされます。 ホーム ページは、Microsoft アプリケーション登録ポータルのアプリケーション登録情報で指定した HTTPS の URL です。 ホーム ページには、ようこそメッセージ "Hello \<ユーザー>"、サインアウトのためのリンク、およびユーザーの要求を表示するリンクが含まれます。 ユーザーの要求へのリンクは、先ほど作成した **Authorize** コントローラーを参照します。

### <a name="browse-to-see-the-users-claims"></a>ユーザーの要求を参照して表示する
ユーザーの要求を表示するには、リンクを選択して、承認されたユーザーのみが使用できるコントローラー ビューを参照します。

#### <a name="view-the-claims-results"></a>要求の結果を表示する
コントローラー ビューを参照した後は、ユーザーの基本プロパティを示す次の表を確認する必要があります。

|プロパティ |値 |[説明] |
|---|---|---|
|**名前** |ユーザーのフルネーム | ユーザーの姓と名。
|**ユーザー名** |user<span>@domain.com</span> | ユーザーの識別に使用されているユーザー名。
|**[件名]** |件名 |Web でユーザーを一意に特定する文字列。|
|**テナント ID** |Guid | ユーザーの Azure AD 組織を一意に表す **guid**。|

さらに、認証要求内にあるすべての要求を示した表を確認する必要があります。 詳細については、[Azure AD ID トークンにある要求の一覧](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)をご覧ください。


### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Authorize 属性を持つメソッドへのアクセスをテストする (省略可能)
ユーザーの要求の **Authorize** コントローラーに匿名ユーザーとしてアクセスのテストを行うには、次の手順を実行します。
1. ユーザーのサインアウトのリンクを選択し、サインアウトのプロセスを完了します。
2. お使いのブラウザーで「http://<span></span>localhost:{ポート}/authenticated」を入力し、**Authorize** 属性で保護されているコントローラーにアクセスします。

#### <a name="expected-results-after-access-to-a-protected-controller"></a>保護されているコントローラーへアクセスした後に期待される結果
保護されているコントローラー ビューを使用するために、認証を求めるメッセージが表示されます。

## <a name="additional-information"></a>追加情報

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Web サイト全体を保護する
Web サイト全体を保護するために、**Global.asax** ファイルで、**AuthorizeAttribute** 属性を **Application_Start** メソッドの **GlobalFilters** フィルターに追加します。

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-sign-in-access-to-your-application"></a>アプリケーションへのサインイン アクセスを制限する
既定では、outlook.com、live.com などの個人用アカウントおよび他のアカウントで、アプリケーションにサインインできます。 また、Azure AD で統合組織の職場または学校のアカウントでも、既定でサインインできます。

アプリケーションへのユーザーのサインイン アクセスを制限するには、複数のオプションを使用できます。

#### <a name="restrict-access-to-a-single-organization"></a>単一の組織へのアクセスを制限する
単一の Azure AD 組織に属するユーザー アカウントのみに対して、アプリケーションへのサインイン アクセスを制限できます。
1. **web.config** ファイルで、**Tenant** パラメーターの値を変更します。 **Common** から**contoso.onmicrosoft.com** などの組織のテナント名に値を変更します。
2. **OWIN Startup** クラスの **ValidateIssuer** 引数を **true** に設定します。

#### <a name="restrict-access-to-a-list-of-organizations"></a>組織の一覧へのアクセスを制限する
許可されている組織の一覧にある Azure AD 組織に属するユーザー アカウントのみに対して、サインイン アクセスを制限できます。
1. **web.config** ファイルの **OWIN Startup** クラスで、**ValidateIssuer** 引数を **true** に設定します。
2. **ValidIssuers** パラメーターの値に許可されている組織の一覧を設定します。

#### <a name="use-a-custom-method-to-validate-issuers"></a>カスタム メソッドを使用して発行者を検証する
**IssuerValidator** パラメーターを使用して、カスタム メソッドを実装して発行者を検証できます。 このパラメーターの使用方法の詳細については、MSDN の [TokenValidationParameters クラス](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx)に関するページをご覧ください。

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
