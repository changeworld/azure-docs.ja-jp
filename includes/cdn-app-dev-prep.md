## 準備

CDN 管理コードを記述する前に、いくつか準備作業を行う必要があります。最初に、このチュートリアルで作成する CDN プロファイルを格納するリソース グループを作成します。次に、Azure Active Directory をセットアップして、アプリケーションの認証を提供します。その後、リソース グループにアクセス許可を適用して、Azure AD テナントの承認されたユーザーのみが CDN プロファイルを操作できるようにします。

### リソース グループの作成

1. [Azure ポータル](https://portal.azure.com)にログインします。

2. 左上の **[新規]** ボタンをクリックし、**[管理]**、**[リソース グループ]** の順にクリックします。
	
	![Creating a new resource group](./media/cdn-app-dev-prep/cdn-new-rg-1.png)

3. リソース グループに *CdnConsoleTutorial* という名前を付けます。サブスクリプションを選択し、近くの場所を選択します。必要に応じて **[ダッシュボードにピン留めする]** チェックボックスをオンにして、リソース グループをポータルのダッシュボードにピン留めします。これにより、後で見つけるのが容易になります。必要な項目を選択したら、**[作成]** をクリックします。

	![Naming the resource group](./media/cdn-app-dev-prep/cdn-new-rg-2.png)

4. ダッシュボードにリソース グループをピン留めしていない場合は、**[参照]**、**[リソース グループ]** の順にクリックして、作成したリソース グループを見つけることができます。このリソース グループをクリックして開きます。**サブスクリプション ID** をメモしておきます。この情報は後で必要になります。

	 ![Naming the resource group](./media/cdn-app-dev-prep/cdn-subscription-id.png)

### Azure AD アプリケーションの作成

Azure Active Directory でのアプリの認証には、個別ユーザーか、サービス プリンシパルかという 2 つのアプローチがあります。サービス プリンシパルは、Windows のサービス アカウントと似ています。CDN プロファイルとやり取りするアクセス許可を特定のユーザーに付与する代わりに、サービス プリンシパルにアクセス許可を付与します。通常、サービス プリンシパルは、自動化された非対話型プロセスに使用されます。このチュートリアルでは対話型コンソール アプリを作成していますが、サービス プリンシパルのアプローチに注目します。

サービス プリンシパルの作成は、Azure Active Directory アプリケーションの作成を含むいくつかの手順で構成されます。これを行うために、[このチュートリアルに従います](../articles/resource-group-create-service-principal-portal.md)。

> [AZURE.IMPORTANT] [リンク先のチュートリアル](../articles/resource-group-create-service-principal-portal.md)のすべての手順を実行してください。説明に従って正確に手順を完了することが*非常に重要*です。**テナント ID**、**テナントのドメイン名** (ドメイン カスタム ドメインが指定されている場合を除き、通常は *.onmicrosoft.com* ドメイン)、**クライアント ID**、**クライアントの認証キー**をメモしておきます。これらの情報は後で必要になります。**クライアント ID** と**クライアントの認証キー**の取り扱いには特に注意してください。これらの資格情報を使用すると、だれでもサービス プリンシパルとして操作を実行できます。
> 	
> 「[マルチテナント アプリケーションを構成する](../articles/resource-group-create-service-principal-portal.md#configure-multi-tenant-application)」の手順で、**[いいえ]** を選択します。
> 
> 「[アプリケーションをロールに割り当てる](../articles/resource-group-create-service-principal-portal.md#assign-application-to-role)」の手順で、前に作成したリソース グループ *CdnConsoleTutorial* を使用します。ただし、**[閲覧者]** ロールではなく、**[CDN Profile Contributor (CDN プロファイルの共同作成者)]** ロールを割り当てます。アプリケーションにリソース グループの **[CDN Profile Contributor (CDN プロファイルの共同作成者)]** ロールを割り当てたら、このチュートリアルに戻ります。

サービス プリンシパルを作成し、**[CDN Profile Contributor (CDN プロファイルの共同作成者)]** ロールを割り当てた後、リソース グループの **[ユーザー]** ブレードの表示は次のようになります。

![Users blade](./media/cdn-app-dev-prep/cdn-service-principal.png)


### 対話型ユーザー認証

サービス プリンシパルの代わりに対話型の個別ユーザー認証を行う場合でも、そのプロセスはサービス プリンシパルを使用する場合とよく似ています。実際に行う手順は同じですが、いくつか細かい点が異なります。

>[AZURE.IMPORTANT] 次の手順は、サービス プリンシパルの代わりに個別ユーザー認証を行う場合にのみ使用してください。

1. アプリケーションを作成するときに、**Web アプリ**ではなく**ネイティブ アプリケーション**の項目を選択します。
	
	![Native application](./media/cdn-app-dev-prep/cdn-native-application.png)
	
2. 次のページで、**リダイレクト URI** の入力を求められます。URI は検証されません。入力した内容を覚えておいてください。この情報は後で必要になります。

3. **クライアント認証キー**を作成する必要はありません。

4. サービス プリンシパルを **[CDN Profile Contributor (CDN プロファイルの共同作成者)]** ロールに割り当てる代わりに、個別ユーザーまたはグループに割り当てます。この例では、*CDN Demo User* を **[CDN Profile Contributor (CDN プロファイルの共同作成者)]** ロールに割り当てています。
	
	![Individual user access](./media/cdn-app-dev-prep/cdn-aad-user.png)

<!---HONumber=AcomDC_0629_2016-->