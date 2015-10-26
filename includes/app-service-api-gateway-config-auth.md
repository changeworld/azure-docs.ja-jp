4. API アプリのゲートウェイ ブレードに移動します。

	![[ゲートウェイ] をクリック](./media/app-service-api-gateway-config-auth/gateway.png)

7. **[ゲートウェイ]** ブレードで、**[設定]**、**[ID]** の順にクリックします。

	![[設定] のクリック](./media/app-service-api-gateway-config-auth/clicksettingsingateway.png)

	![[ID] をクリック](./media/app-service-api-gateway-config-auth/clickidentity.png)

	**[ID]** ブレードからは、Azure Active Directory や他のさまざまなプロバイダーを使用して認証を構成する別のブレードに移動することができます。

	![[ID] ブレード](./media/app-service-api-gateway-config-auth/identityblade.png)
  
3. 使用する ID プロバイダーを選択し、該当する記事の手順に従って、API アプリをそのプロバイダーで構成します。これらの記事はモバイル アプリ向けに書かれていますが、API アプリでも手順は同じです。一部の手順では、Azure ポータルを使用する必要があります。

 - [Microsoft アカウント](../articles/app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication-preview.md)
 - [Facebook ログイン](../articles/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication-preview.md)
 - [Twitter ログイン](../articles/app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication-preview.md)
 - [Google ログイン](../articles/app-service-mobile/app-service-mobile-how-to-configure-google-authentication-preview.md)
 - [Azure Active Directory](../articles/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication-preview.md)

たとえば、次のスクリーン ショットは、Azure Active Directory の認証を設定した後の Azure ポータルのページと Azure プレビュー ポータルのブレードを示しています。

Azure プレビュー ポータルでは、**[Azure Active Directory]** ブレードに、Azure ポータルの [Azure Active Directory] タブで作成したアプリケーションの**クライアント ID** が表示されます。さらに、**[許可されたテナント]** に、Azure Active Directory テナント ("contoso.onmicrosoft.com" など) が表示されます。

![[Azure Active Directory] ブレード](./media/app-service-api-gateway-config-auth/tdinaadblade.png)

Azure ポータルでは、**[Azure Active Directory]** タブで作成したアプリケーションの **[構成]** タブに、Azure プレビュー ポータルの **[Azure Active Directory]** ブレードの **[サインオン URL]**、**[アプリケーション ID/URI]**、および **[応答 URL]** が表示されます。

![](./media/app-service-api-gateway-config-auth/oldportal1.png)

![](./media/app-service-api-gateway-config-auth/oldportal2.png)

![](./media/app-service-api-gateway-config-auth/oldportal3.png)

![](./media/app-service-api-gateway-config-auth/oldportal4.png)

(この図の応答 URL には、同じ URL が 2 つ表示されています。1 つには `http:`、もう 1 つには `https:` が付いています。)

<!---HONumber=Oct15_HO3-->