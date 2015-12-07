<properties 
	pageTitle="App Services アプリケーションに Azure Active Directory 認証を構成する方法" 
	description="App Services アプリケーションに Azure Active Directory 認証を構成する方法について説明します。" 
	authors="mattchenderson" 
	services="app-service\mobile" 
	documentationCenter="" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/20/2015" 
	ms.author="mahender"/>

# Azure Active Directory ログインを使用するように App Service アプリケーションを構成する方法

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

このトピックでは、認証プロバイダーとして Azure Active Directory を使用するように Azure App Services を構成する方法を示します。

> [AZURE.NOTE]このトピックでは、App Service 認証/承認の使用例を示します。ほとんどのアプリケーションで、これが App Service ゲートウェイに取って代わっています。ゲートウェイを使用している場合は、[別の方法]を参照してください。ゲートウェイを使用する場合との相違点については、そのセクションの注に記載されています。


## <a name="express"></a>高速設定を使用して Azure Active Directory を構成する

13. [Microsoft Azure 管理ポータル]でアプリケーションに移動します。**[設定]**、**[認証/承認]** の順にクリックします。

14. [認証/承認] 機能が有効になっていない場合は、スイッチを **[オン]** に切り替えます。

15. **[Azure Active Directory]** をクリックし、**[管理モード]** の **[高速]** をクリックします。

16. **[OK]** をクリックして、Azure Active Directory にアプリケーションを登録します。これで新しい登録が作成されます。代わりに既存の登録を選択する場合は、**[既存のアプリケーションの選択]** をクリックし、テナント内で以前に作成した登録の名前を検索します。登録をクリックして選択し、**[OK]** をクリックします。Azure Active Directory 設定ブレードで **[OK]** をクリックします。

    ![][0]
	
	App Service は既定では認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。アプリケーション コードでユーザーを承認する必要があります。

17. (省略可能) サイトに対するアクセスを、Azure Active Directory で認証されたユーザーに限定するには、**[要求が認証されていないときに実行するアクション]** を **[Azure Active Directory]** に設定します。この場合、要求はすべて認証される必要があり、認証されていない要求はすべて認証のために Azure Active Directory にリダイレクトされます。

17. **[保存]** をクリックします。

これで、アプリケーションで認証に Azure Active Directory を使用する準備ができました。

## <a name="advanced"> </a>(代替方法) 詳細設定を使用して Azure Active Directory を手動で構成する
構成設定を手動で行うこともできます。これは、使用する AAD テナントが Azure へのサインインに使用するテナントと異なる場合に推奨されるソリューションです。構成を完了するには、まず Azure Active Directory で登録を作成し、登録の一部の詳細を App Service に提供する必要があります。

### <a name="register"> </a>Azure Active Directory にアプリケーションを登録する

1. [プレビュー段階の Microsoft Azure 管理ポータル]にログオンし、アプリケーションに移動します。**[URL]** をコピーします。これを使用して、Azure Active Directory アプリケーションを構成します。

3. [Microsoft Azure 管理ポータル]にサインインして、**Active Directory** に移動します。

    ![][2]

4. ディレクトリを選択し、上部の **[アプリケーション]** タブを選択します。下部の **[追加]** をクリックして、新しいアプリの登録を作成します。

5. **[組織で開発中のアプリケーションを追加]** をクリックします。

6. アプリケーションの追加ウィザードで、アプリケーションの**名前**を入力し、種類として **[Web アプリケーションや Web API]** をクリックします。その後、クリックして続行します。

7. **[サインオン URL]** ボックスに、先ほどコピーしたアプリケーション URL を貼り付けます。**[アプリケーション ID URI]** ボックスにも同じ URL を入力します。その後、クリックして続行します。

8. アプリケーションが追加されたら **[構成]** タブをクリックします。**[シングル サインオン]** の **[応答 URL]** を編集して、アプリケーションの URL の末尾にパス _/.auth/login/aad/callback_ を追加します。たとえば、「`https://contoso.azurewebsites.net/.auth/login/aad/callback`」のように入力します。HTTPS スキームを使用していることを確認します。

    ![][3]
	
	
	> [AZURE.NOTE]App Service の [認証/承認] 機能ではなく、App Service ゲートウェイを使用している場合は、応答 URL でパス _/signin-aad_ を含むゲートウェイ URL を代わりに使用します。


9. **[保存]** をクリックします。その後、アプリケーションの**クライアント ID** をコピーします。後で、これを使用するようにアプリケーションを構成します。

10. 下部のコマンド バーで、**[エンドポイントの表示]** をクリックして、**[フェデレーション メタデータ ドキュメント]** の URL をコピーし、そのドキュメントをダウンロードするか、ブラウザーでそのドキュメントに移動します。

11. ルートの **EntityDescriptor** 要素内に、テナント固有の GUID ("テナント ID") が後に続く `https://sts.windows.net/` の形式の **entityID** 属性があります。この値をコピーします。これは**発行者の URL** として機能します。後で、これを使用するようにアプリケーションを構成します。

### <a name="secrets"> </a>Azure Active Directory の情報をアプリケーションに追加する

> [AZURE.NOTE]App Service ゲートウェイを使用する場合は、このセクションの内容は無視し、ポータル内で目的のゲートウェイに移動します。**[設定]**、**[ID]**、**[Azure Active Directory]** の順に選択します。ClientID に貼り付け、テナント ID を **[許可されているテナント]** リストに追加します。**[保存]** をクリックします。


13. [プレビュー段階の Microsoft Azure 管理ポータル]に戻り、アプリケーションに移動します。**[設定]**、**[認証/承認]** の順にクリックします。

14. [認証/承認] 機能が有効になっていない場合は、スイッチを **[オン]** に切り替えます。

15. **[Azure Active Directory]** をクリックし、**[管理モード]** の **[詳細設定]** をクリックします。以前に取得したクライアント ID と発行者の URL の値を貼り付けます。次に、 **[OK]** をクリックします

    ![][1]
	
	App Service は既定では認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。アプリケーション コードでユーザーを承認する必要があります。

17. (省略可能) サイトに対するアクセスを、Azure Active Directory で認証されたユーザーに限定するには、**[要求が認証されていないときに実行するアクション]** を **[Azure Active Directory]** に設定します。この場合、要求はすべて認証される必要があり、認証されていない要求はすべて認証のために Azure Active Directory にリダイレクトされます。

17. **[保存]** をクリックします。

これで、アプリケーションで認証に Azure Active Directory を使用する準備ができました。

## <a name="related-content"> </a>関連コンテンツ

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[プレビュー段階の Microsoft Azure 管理ポータル]: https://portal.azure.com/
[Microsoft Azure 管理ポータル]: https://manage.windowsazure.com/
[ios-adal]: ../app-service-mobile-xamarin-ios-aad-sso.md
[別の方法]: #advanced

<!---HONumber=AcomDC_1125_2015-->