<properties
   pageTitle="Azure Active Directory のアプリケーション マニフェストについて | Microsoft Azure"
   description="Azure Active Directory アプリケーション マニフェストの使用方法を詳しく説明しています。このマニフェストは、Azure AD テナントでのアプリケーションの ID 構成を表しています。また、OAuth 認証、同意エクスペリエンスなどを利用しやすくするために使用されます。"
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/26/2016"
   ms.author="dkershaw;bryanla"/>

# Azure Active Directory のアプリケーション マニフェストについて

Azure Active Directory (AD) と統合するアプリケーションは、Azure AD テナントに登録され、アプリケーションの永続的な ID 構成を提供する必要があります。この構成は実行時に参照されるため、アプリケーションが Azure AD を介して認証と承認を外部委託および仲介するシナリオが可能になります。Azure AD のアプリケーション モデルの詳細については、「[アプリケーションの追加、更新、および削除][ADD-UPD-RMV-APP]」を参照してください。

## アプリケーションの ID 構成の更新

次に示すように、アプリケーションの ID 構成のプロパティを更新する方法には、現実に複数の方法があります。ただし、実行できる操作と難易度がそれぞれ異なります。

- **[Azure クラシック ポータルの][AZURE-CLASSIC-PORTAL] Web ユーザー インターフェイス**を使用すると、アプリケーションの最も一般的なプロパティを更新できます。これは、最も手軽でミスを招きにくいアプリケーションのプロパティの更新方法ですが、次の 2 つの方法とは異なり、すべてのプロパティに完全にアクセスすることはできません。
- Azure クラシック ポータルで公開されないプロパティを更新する必要があるより高度なシナリオに対しては、**アプリケーション マニフェスト**を変更することができます。この記事では、この方法に話題を絞り、次のセクションで詳しく説明していきます。
- また、**[Graph API][GRAPH-API] を使用してアプリケーションを更新するアプリケーションを作成する**こともできますが、それには大きな労力が必要になります。これは、管理ソフトウェアを作成している場合や定期的かつ自動的にアプリケーションのプロパティを更新する必要がある場合に魅力的な選択肢になる可能性があります。

## アプリケーション マニフェストを使用してアプリケーションの ID 構成を更新する
[Azure クラシック ポータル][AZURE-CLASSIC-PORTAL]を使用すると、アプリケーション マニフェストと呼ばれる JSON ファイル表現をダウンロードおよびアップロードして、アプリケーションの ID 構成を管理することができます。ディレクトリには実際のファイルは格納されません。アプリケーション マニフェストは Azure AD Graph API アプリケーション エンティティに対する単なる HTTP GET 操作であり、アップロードはアプリケーション エンティティに対する HTTP PATCH 操作です。

したがって、アプリケーション マニフェストの形式とプロパティを理解するには、Graph API の[アプリケーション エンティティ][APPLICATION-ENTITY]に関するドキュメントを参照する必要があります。アプリケーション マニフェストのアップロードによって実行できる更新操作の例を次に示します。

- Web API によって公開されている**アクセス許可のスコープ (oauth2Permissions) を宣言する**。oauth2Permissions 委任アクセス許可スコープを使用してユーザーの偽装を実装する方法については、「[Azure Active Directory とアプリケーションの統合][INTEGRATING-APPLICATIONS-AAD]」の「他のアプリケーションへの Web API の公開」を参照してください。既に説明したように、すべてのアプリケーション エンティティ プロパティについては、[OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION] 型のコレクションである oauth2Permissions プロパティを含む Graph API の[エンティティおよび複合型のリファレンス][APPLICATION-ENTITY]記事にドキュメント化されています。
- **アプリによって公開されるアプリケーション ロール (appRoles) を宣言する**。アプリケーション エンティティの appRoles プロパティは、[AppRole][APPLICATION-ENTITY-APP-ROLE] 型のコレクションです。実装例については、[Azure AD を使用したクラウド アプリケーションでのロール ベースのアクセス制御][RBAC-CLOUD-APPS-AZUREAD]に関する記事を参照してください。
- **既知のクライアント アプリケーション (knownClientApplications) を宣言する**。それらによって、指定されたクライアント アプリケーションの同意をリソースや Web API に論理的に関連付けることができます。
- サインインしているユーザーの**グループ メンバーシップ要求 (groupMembershipClaims) を発行するように Azure AD にリクエストする**。注: さらに、ユーザーのディレクトリ ロール メンバーシップに関する要求を発行するように構成することもできます。実装例については、[AD グループを使用したクラウド アプリケーションでの承認][AAD-GROUPS-FOR-AUTHORIZATION]に関する記事を参照してください。
- **アプリケーションが OAuth 2.0 の暗黙的な許可フロー (oauth2AllowImplicitFlow) をサポートできるようにする**。この種類の許可フローは、埋め込み JavaScript Web ページまたは Single Page Applications (SPA) で使用されます。
- **X509 証明書の秘密キー (keyCredentials) としての使用を可能にする**。実装例については、「[Office 365 でのサービスとデーモンのアプリのビルド][O365-SERVICE-DAEMON-APPS]」と「[Azure リソース マネージャー API の権限付与に関する開発者ガイド][DEV-GUIDE-TO-AUTH-WITH-ARM]」を参照してください。
- アプリケーションの**新しいアプリ ID URI (identifierURIs) を追加する**。アプリ ID URI は、Azure AD テナント内で (または、検証済みカスタム ドメインを使用して修飾される際のマルチ テナント シナリオの場合は、複数の Azure AD テナントで) アプリケーションを一意に識別するために使用します。リソース アプリケーションに対するアクセス許可を要求するとき、またはリソース アプリケーションのアクセス トークンを取得するときに使用します。この要素を更新したときに、対応するサービス プリンシパルの servicePrincipalNames コレクションに同じ更新が加えられます。このコレクションはアプリケーションのホーム テナントに格納されています。

アプリケーション マニフェストは、アプリケーションの登録の状態を追跡するための優れた方法でもあります。アプリケーション マニフェストは JSON 形式で使用できるため、アプリケーションのソース コードと共にファイル表現をソース管理にチェックインできます。

## 詳しい手順
ここでは、アプリケーション マニフェストを使ってアプリケーションの ID 構成を更新するための手順を紹介します。上の例のうちの 1 つに注目して、リソース アプリケーションで新しいアクセス許可スコープを宣言する方法を示します。

1. [Azure クラシック ポータル][AZURE-CLASSIC-PORTAL]に移動し、サービス管理者または共同管理者の特権を持つアカウントでサインインします。

2. 認証されたら、下へスクロールして、左側のナビゲーション パネルで Azure の "Active Directory" 拡張機能 (1) を選択します。次に、アプリケーションが登録されている Azure AD テナント (2) をクリックします。

    ![Select the Azure AD tenant][SELECT-AZURE-AD-TENANT]

3. ディレクトリ ページが表示されたら、ページの上部にある [アプリケーション] \(1) をクリックして、テナントに登録されているアプリケーションの一覧を表示します。次に、更新するアプリケーションを一覧内で見つけてクリックします (2)。

    ![Select the Azure AD tenant][SELECT-AZURE-AD-APP]

4. これで、アプリケーションのメイン ページが選択されました。ここで、ページの下部にある [マニフェストの管理] 機能 (1) に注目してください。このリンクをクリックすると、JSON マニフェスト ファイルをアップロードまたはダウンロードするように求められます。[マニフェストのダウンロード] \(2) をクリックするとダウンロードの確認ページが表示され、[マニフェストのダウンロード] \(3) をクリックして確認するよう求められます。その後、ファイルを開くかローカルに保存するよう求められます (4)。

    ![Manage the manifest, download option][MANAGE-MANIFEST-DOWNLOAD]

    ![Download the manifest][DOWNLOAD-MANIFEST]

5. この例では、ファイルをローカルに保存しています。これにより、エディターでファイルを開いて JSON に変更を加え、もう一度保存することができます。次に示すのは、Visual Studio の JSON エディターで開いた JSON 構造の例です。前に説明したように、[アプリケーション エンティティ][APPLICATION-ENTITY]のスキーマに従っていることに注目してください。

    ![Update the manifest JSON][UPDATE-MANIFEST]

    たとえば、リソース アプリケーション (API) で "Employees.Read.All" という名前の新しいアクセス許可を実装/公開する場合は、次のように、単に新しい 2 番目の要素を oauth2Permissions コレクションに追加します。

        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
        {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
        }
        ],

    エントリは一意である必要があるため、`"id"` プロパティ用に新しいグローバル一意識別子 (GUID) を生成する必要があります。この場合、`"type": "User"` を指定したので、このアクセス許可は、リソース/API アプリケーションが登録されている Azure AD テナントによって認証された任意のアカウントで同意することができ、アカウントの代わりにアクセスする許可がクライアント アプリケーションに付与されます。同意するときと Azure クラシック ポータルでの表示には、説明および表示名の文字列が使用されます。

6. マニフェストの更新が終わったら、Azure クラシック ポータルの Azure AD アプリケーション ページに戻ります。もう一度 [マニフェストの管理] \(1) をクリックし、次に [マニフェストのアップロード] \(2) を選択します。ダウンロードの場合と同様に、2 つ目のダイアログ ボックスが表示され、JSON ファイルの場所を指定するよう求められます。[ファイルの参照] \(3) をクリックします。次に、[アップロードするファイルの選択] ダイアログ ボックスで JSON ファイル (4) を選択し、[開く] を押します。ダイアログ ボックスが閉じたら、"OK" のチェック マーク (5) を選択します。これで、マニフェストがアップロードされます。

    ![Manage the manifest, upload option][MANAGE-MANIFEST-UPLOAD]

    ![Upload the manifest JSON][UPLOAD-MANIFEST]

    ![Upload the manifest JSON - confirmation][UPLOAD-MANIFEST-CONFIRM]

これでマニフェストが保存されたので、登録されているクライアント アプリケーションに、上で追加した新しいアクセス許可へのアクセスを許可することができますが、今度はクライアント アプリケーションのマニフェストを編集する代わりに、Azure クラシック ポータルの Web UI を使用することができます。

1. まず、新しい API へのアクセスを追加するクライアント アプリケーションの [構成] ページに移動し、[アプリケーションの追加] ボタンをクリックします。
2. テナントの登録済みのリソース アプリケーション (API) の一覧が表示されます。リソース アプリケーションの名前の横にあるプラス (+) 記号をクリックして選択します。  
3. 次に、右下にあるチェック マークをクリックします。 
4. クライアントの構成ページの [アプリケーションの追加] セクションに戻ると、一覧に新しいリソース アプリケーションが表示されます。その行の右側の [委任されたアクセス許可] セクションにポインターを置くと、ドロップダウン リストが表示されます。一覧をクリックし、クライアントの要求されているアクセス許可の一覧に追加するために、新しいアクセス許可を選択します。注: この新しいアクセス許可は、クライアント アプリケーションの ID 構成の "requiredResourceAccess" コレクション プロパティに格納されます。

![他のアプリケーションに対するアクセス許可][PERMS-TO-OTHER-APPS]

![他のアプリケーションに対するアクセス許可][PERMS-SELECT-APP]

![他のアプリケーションに対するアクセス許可][PERMS-SELECT-PERMS]

これで終了です。アプリケーションは、新しい ID 構成を使用して実行されるようになりました。

## 次のステップ
下部の DISQUS コメント セクションを使って、ご意見をお寄せください。Microsoft のコンテンツの改善にご協力ください。

<!--Image references-->
[DOWNLOAD-MANIFEST]: ./media/active-directory-application-manifest/download-manifest.png
[MANAGE-MANIFEST-DOWNLOAD]: ./media/active-directory-application-manifest/manage-manifest-download.png
[MANAGE-MANIFEST-UPLOAD]: ./media/active-directory-application-manifest/manage-manifest-upload.png
[PERMS-SELECT-APP]: ./media/active-directory-application-manifest/portal-perms-select-app.png
[PERMS-SELECT-PERMS]: ./media/active-directory-application-manifest/portal-perms-select-perms.png
[PERMS-TO-OTHER-APPS]: ./media/active-directory-application-manifest/portal-perms-to-other-apps.png
[SELECT-AZURE-AD-APP]: ./media/active-directory-application-manifest/select-azure-ad-application.png
[SELECT-AZURE-AD-TENANT]: ./media/active-directory-application-manifest/select-azure-ad-tenant.png
[UPDATE-MANIFEST]: ./media/active-directory-application-manifest/update-manifest.png
[UPLOAD-MANIFEST]: ./media/active-directory-application-manifest/upload-manifest.png
[UPLOAD-MANIFEST-CONFIRM]: ./media/active-directory-application-manifest/upload-manifest-confirm.png

<!--article references -->
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#ApplicationEntity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#AppRoleType
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionType
[AZURE-CLASSIC-PORTAL]: https://manage.windowsazure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

<!---HONumber=AcomDC_0601_2016-->