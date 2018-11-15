---
title: Service Fabric クライアント認証用に Azure Active Directory を設定する | Microsoft Docs
description: Service Fabric クラスターのクライアントを認証するための Azure Active Directory (Azure AD) の設定方法について学習します。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/15/2018
ms.author: aljo
ms.openlocfilehash: 75ba2ee378e9eddfeaeb2346b4d5bb584844afe2
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636677"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>クライアント認証用に Azure Active Directory をセットアップする

Azure で実行されているクラスターの場合、Azure Active Directory (Azure AD) を使用して管理エンドポイントへのアクセスをセキュリティで保護することをお勧めします。  この記事では、Service Fabric クラスターのクライアントを認証するための Azure AD の設定方法について説明します。この操作は、[クラスターを作成する](service-fabric-cluster-creation-via-arm.md)前に行う必要があります。  Azure AD は組織 (テナントと呼ばれます) を有効にしてアプリケーションに対するユーザー アクセスを管理します。 アプリケーションは、Web ベースのサインイン UI を持つアプリケーションと、ネイティブ クライアントのエクスペリエンスを持つアプリケーションに分けられます。 この記事では、既にテナントを作成していることを前提としています。 まだ作成していない場合は、まず「[Azure Active Directory テナントを取得する方法][active-directory-howto-tenant]」をお読みください。

## <a name="create-azure-ad-applications"></a>Azure AD アプリケーションを作成する
Service Fabric クラスターでは、Web ベースの [Service Fabric Explorer][service-fabric-visualizing-your-cluster] や [Visual Studio][service-fabric-manage-application-in-visual-studio] など、いくつかのエントリ ポイントから管理機能にアクセスできます。 このため、クラスターへのアクセスを制御するには、2 つの Azure AD アプリケーション (Web アプリケーションとネイティブ アプリケーション) を作成します。  アプリケーションを作成した後は、ユーザーを読み取り専用ロールおよび管理者ロールに割り当てます。

Azure AD の Service Fabric クラスターでの構成に関する手順の一部を簡略化するため、一連の Windows PowerShell スクリプトが作成されています。

> [!NOTE]
> クラスターを作成する前に、次の手順を完了する必要があります。 スクリプトはクラスター名とエンドポイントを想定しているため、値は作成済みのものではなく、計画する必要があります。

1. コンピューターに[スクリプトをダウンロード][sf-aad-ps-script-download]します。
2. zip ファイルを右クリックし、**[プロパティ]** を選択して **[ブロック解除]** チェックボックスをオンにし、**[適用]** をクリックします。
3. zip ファイルを解凍します。
4. `SetupApplications.ps1` を実行します。パラメーターとして、TenantId、ClusterName、WebApplicationReplyUrl を指定します。 例: 

```PowerShell
.\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
```

> [!NOTE]
> 国内のクラウド (Azure Government、Azure China、Azure Germany) については、`-Location` パラメーターを指定する必要もあります。

TenantId は、PowerShell コマンド `Get-AzureSubscription` を実行することで検索できます。 このコマンドを実行すると、すべてのサブスクリプションの TenantId が表示されます。

ClusterName は、スクリプトによって作成される Azure AD アプリケーションのプレフィックスとして使用されます。 実際のクラスター名と完全に一致している必要はありません。 これは、一緒に使用される Service Fabric クラスターに対して、Azure AD のアーティファクトのマッピングを容易にするためだけに使用します。

WebApplicationReplyUrl は、サインインの完了後に Azure AD がユーザーに返す既定のエンドポイントです。 このエンドポイントをクラスターの Service Fabric Explorer エンドポイントして設定します。既定値は次のとおりです。

https://&lt;cluster_domain&gt;:19080/Explorer

Azure AD テナント用の管理特権を持っているアカウントにサインインすることを求められます。 サインインすると、スクリプトは、Service Fabric クラスターを表す Web アプリケーションとネイティブ アプリケーションを作成します。 [Azure Portal][azure-portal] でテナントのアプリケーションを調べると、次の 2 つの新しいエントリがあることがわかります。

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

次のセクションでクラスターを作成するとき、スクリプトによって、Azure Resource Manager テンプレートが必要とする JSON が出力されます。このため、PowerShell ウィンドウは開いたままにしてください。

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>ユーザーをロールに割り当てる
クラスターを表すアプリケーションを作成したら、Service Fabric によってサポートされるロール (read-only と admin) にユーザーを割り当てます。[Azure Portal][azure-portal] を使って、ロールを割り当てることができます。

1. Azure Portal の右上隅でテナントを選びます。

    ![テナント ボタンを選ぶ][select-tenant-button]
2. 左のタブで **[Azure Active Directory]** を選び、[エンタープライズ アプリケーション] を選びます。
3. [すべてのアプリケーション] を選び、`myTestCluster_Cluster` のような名前を持つ Web アプリケーションを探して選びます。
4. **[ユーザーとグループ]** タブをクリックします。

    ![[ユーザーとグループ] タブ][users-and-groups-tab]
5. 新しいページの **[ユーザーの追加]** ボタンをクリックして、ユーザーと割り当てるロールを選び、ページの下部にある **[選択]** ボタンをクリックします。

    ![ロールへのユーザーの割り当てページ][assign-users-to-roles-page]
6. ページの下部にある **[割り当て]** ボタンをクリックします。

    ![割り当ての追加の確認][assign-users-to-roles-confirm]

> [!NOTE]
> Service Fabric でのロールの詳細については、「 [ロール ベースのアクセス制御 (Service Fabric クライアント用)](service-fabric-cluster-security-roles.md)」を参照してください。
>
>

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Azure Active Directory の設定に関するトラブルシューティング
Azure AD の設定時や使用時には問題が発生することがあります。そこで、問題のデバッグに役立つポインターをいくつか紹介します。

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer に、証明書の選択を求めるメッセージが表示される
#### <a name="problem"></a>問題点
Service Fabric Explorer で Azure AD に正常にサインインすると、ブラウザーはホームページに戻りますが、証明書の選択を求めるメッセージが表示されます。

![SFX 証明書ダイアログ][sfx-select-certificate-dialog]

#### <a name="reason"></a>理由
ユーザーに Azure AD クラスター アプリケーション内でロールが割り当てられていません。 このため、Service Fabric クラスターで Azure AD の認証が失敗します。 Service Fabric エクスプローラーは、証明書認証にフォールバックします。

#### <a name="solution"></a>解決策
次の説明に従って Azure AD をセットアップしてユーザー ロールを割り当てます。 また、`SetupApplications.ps1` のように [アプリにアクセスするにはユーザー割り当てが必要] をオンにすることをお勧めします。

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>PowerShell を使用した接続が失敗し、"指定した資格情報が無効です" というエラーが返される
#### <a name="problem"></a>問題点
"AzureActiveDirectory" セキュリティ モードで PowerShell を使用してクラスターに接続すると、Azure AD に正常にサインインした後で接続に失敗し、"指定した資格情報が無効です" というエラーが返されます。

#### <a name="solution"></a>解決策
前の問題の解決策と同じです。

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>サインインするときに Service Fabric Explorer で "AADSTS50011" エラーが返される
#### <a name="problem"></a>問題点
Service Fabric Explorer で Azure AD へのサインインを試行すると、"AADSTS50011: 応答アドレス &lt;url&gt; が、アプリケーションに対して構成された応答アドレス &lt;guid&gt; と一致していません" というエラーが返されます。

![SFX の応答アドレスが一致しない][sfx-reply-address-not-match]

#### <a name="reason"></a>理由
Service Fabric Explorer に相当するクラスター (Web) アプリケーションは Azure AD に対する認証を試み、要求の一部として、戻り先のリダイレクト URL を指定しています。 しかし、その URL が Azure AD アプリケーションの **[応答 URL]** のリストに表示されません。

#### <a name="solution"></a>解決策
AAD ページで [アプリの登録] を選び、クラスター アプリケーションを選んで、**[応答 URL]** ボタンを選びます。 [応答 URL] ページで、Service Fabric Explorer の URL をリストに追加するか、リスト内の項目のいずれかと置き換えます。 完了したら、変更を保存します。

![Web アプリケーションの応答 URL][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>PowerShell で Azure AD 認証を使用してクラスターに接続する
Service Fabric クラスターに接続するには、次の PowerShell コマンド例を使用します。

```PowerShell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

詳細については、[Connect-ServiceFabricCluster コマンドレット](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster)に関するページを参照してください。

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>複数のクラスターで同じ Azure AD テナントを再利用できますか?
はい。 ただし、Service Fabric Explorer の URL をクラスター (Web) アプリケーションに追加することを忘れないでください。 そうしないと、Service Fabric Explorer は動作しません。

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Azure AD が有効になっているときもサーバーの証明書が必要なのはどうしてですか?
FabricClient と FabricGateway では、相互認証が実行されます。 Azure AD 認証中、Azure AD 統合がクライアント ID をサーバーに提供し、サーバー証明書を使用してサーバー ID の確認が行われます。 Service Fabric の証明書について詳しくは、「[X.509 証明書と Service Fabric][x509-certificates-and-service-fabric]」をご覧ください。

## <a name="next-steps"></a>次の手順
Azure Active Directory アプリケーションを設定し、ユーザーのロールを設定したら、[クラスターを構成してデプロイ](service-fabric-cluster-creation-via-arm.md)します。


<!-- Links -->
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[select-tenant-button]: ./media/service-fabric-cluster-creation-setup-aad/select-tenant-button.png
[users-and-groups-tab]: ./media/service-fabric-cluster-creation-setup-aad/users-and-groups-tab.png
[assign-users-to-roles-page]: ./media/service-fabric-cluster-creation-setup-aad/assign-users-to-roles-page.png
[assign-users-to-roles-confirm]: ./media/service-fabric-cluster-creation-setup-aad/assign-users-to-roles-confirm.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-setup-aad/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-setup-aad/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-setup-aad/web-application-reply-url.png
