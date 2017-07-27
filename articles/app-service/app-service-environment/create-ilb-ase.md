---
title: "Azure App Service Environment で内部ロード バランサーを作成して使用する"
description: "インターネットから分離された Azure App Service Environment を使用して作成および使用する方法について説明します。"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 8bc82a297ef8e3f1beac773a6a3b0c4a9b80334f
ms.contentlocale: ja-jp
ms.lasthandoff: 06/26/2017

---
# <a name="create-and-use-an-internal-load-balancer-with-an-app-service-environment"></a>App Service Environment で内部ロード バランサーを作成して使用する #

App Service Environment (ASE) は、ユーザーの Azure Virtual Network (VNet) 内のサブネットに Azure App Service をデプロイしたものです。 ASE をデプロイするには、2 つの方法があります。 

- 外部 IP アドレスの VIP を使用する。_外部 ASE_ と呼ばれます。
- 内部 IP アドレスの VIP を使用する。内部ロード バランサー (ILB) は内部エンドポイントであるため、_ILB ASE_ と呼ばれます。 

この記事では、_ILB ASE_ を作成する方法について説明します。  ASE の概要については、まず [App Service Environment の概要][Intro]に関する記事をご覧ください。外部 ASE を作成する方法については、まず[外部 ASE の作成][MakeExternalASE]に関する記事をご覧ください。

## <a name="overview"></a>概要 ##

ASE は、インターネットにアクセスできるエンドポイント、または Virtual Network の IP アドレスを使用してデプロイできます。 IP アドレスを Virtual Network のアドレスに設定するには、ASE を ILB にデプロイする必要があります。 ASE を ILB にデプロイするときは、次を指定する必要があります。

-   アプリの作成に使用される独自のドメイン
-   HTTPS で使用される証明書
-   ドメインの DNS 管理

これに対して、次を行うことができます。

-   サイト間 VPN または ExpressRoute VPN でアクセスするクラウドでイントラネット アプリケーションを安全にホストできます。
-   パブリック DNS サーバーに示されていないクラウドでアプリケーションをホストできます。
-   インターネットから分離されたバックエンド アプリを作成して、フロント エンド アプリを安全に統合できます。

***無効な機能***

次のように、ILB ASE を使用する際に実行できないことがいくつかあります。

-   IP ベースの SSL の使用
-   特定のアプリへの IP アドレスの割り当て
-   ポータルからのアプリの証明書の購入と使用。 証明機関から証明書を直接取得してアプリで使用することはできますが、Azure Portal では使用できません。

## <a name="create-an-ilb-ase"></a>ILB ASE を作成する ##

ILB ASE を作成する方法は次のとおりです。

1.  Azure Portal で、**[新規]、[Web + モバイル]、[App Service Environment]** の順に選択します。
2.  サブスクリプションを選択します。
3.  リソース グループを選択または作成します。
4.  Virtual Network を選択または作成します。
5.  Virtual Network を選択した場合は、サブネットを作成します。 サブネットのサイズは ASE の将来の拡大を考慮した大きさにする必要があります。 推奨されるサイズは、最大サイズの ASE を処理できる 128 のアドレスを持つ `/25` です。 `/29` 以下のサイズのサブネットは使用できません。  インフラストラクチャは最大で 5 つのアドレスを使用する必要があります。  `/28` の場合は、`/28` サブネットで最大 11 個のインスタンスを使用できます。 将来の App Service プランで既定最大の 100 個のインスタンスを超えて使用する必要があると予想される場合、またはフロントエンド スケーリングでよりすばやく 100 個近くにまで増やす必要がある場合は、256 個のアドレスを持つ /24 を使用してください。
6.  **[Virtual Network/場所]、[Virtual Network Configuration]\(Virtual Network 構成\)** の順に選択し、**[VIP の種類]** を **[外部]** に設定します。
7.  ドメイン名を指定します。 このドメインが、この ASE に作成されるアプリで使用されます。 いくつかの制限が適用されます。 次の値は指定できません。
    - net
    - azurewebsites.net
    - p.azurewebsites.net
    - &lt;asename&gt;.p.azurewebsites.net

    また、この ASE にホストされているアプリにカスタム ドメイン名を使用することを検討している場合、カスタム ドメイン名と ASE で使用されるドメイン名が重複しないようにする必要があります。 ILB ASE のドメイン名が _contoso.com_ 場合、次のようなカスタム ドメイン名をアプリで使用できません。
    - www.contoso.com
    - abcd.def.contoso.com
    - abcd.contoso.com

    ILB ASE 上にデプロイするアプリで使用するカスタム ドメイン名が分かっている場合、ILB ASE のドメイン名にはそれとは競合しないものを ASE の作成時に選択してください。 この例では、_contoso-internal.com_ などのようなドメイン名にします。
8.  **[OK]** をクリックして **[作成]** をクリックします。

    ![][1]

[Virtual Network] ブレード内に、**Virtual Network 構成**オプションがあります。 これを使用して、外部 VIP または内部 VIP を選択できます。 既定値は **[外部]** です。 **[外部]** を選択した場合、ASE はインターネット アクセス可能な VIP を使用します。 **[内部]** を選択した場合、Virtual Network 内の IP アドレスに、ILB を含む ASE が構成されます。

**[内部]** を選択すると、ASE に IP アドレスを追加する機能がなくなり、代わりに ASE のドメインを指定する必要があります。 外部 VIP の ASE では、ASE 名はその ASE で作成されたアプリのドメインで使用されます。

**[VIP の種類]** を **[内部]** に設定すると、ASE 名はその ASE のドメインで使用されません。 ドメインを明示的に指定します。 ドメインが ***contoso.corp.net*** で、その ASE 内に ***timereporting*** という名前のアプリを作成した場合、そのアプリの URL は ***timereporting.contoso.corp.net*** になります。

## <a name="apps-in-an-ilb-ase"></a>ILB ASE 内のアプリ ##

ILB ASE でのアプリの作成は、通常の ASE でのアプリの作成と同じです。

1.  Azure Portal で、**[新規]、[Web + モバイル]、[Web]** の順に選択するか、**[モバイル]** または **[API アプリ]** を選択します。
2.  アプリの名前を入力します。
3.  サブスクリプションを選択します。
4.  リソース グループを選択または作成します。
5.  App Service プランを選択または作成します。 新しい App Service プランを作成するには、ASE を場所として選択し、App Service プランを作成するワーカー プールを選択します。 App Service プランを作成するときに、場所として ASE を選択し、ワーカー プールを選択します。 アプリの名前を指定すると、アプリ名の下のドメインが、ASE のドメインによって置き換えられます。
6.  **[作成]**を選択します。 アプリをダッシュボードに表示する場合は、 **[ダッシュボードにピン留めする]** チェックボックスを選択する必要があります。

    ![][2]

アプリ名の下で、ドメイン名が ASE のドメインを反映するように更新されます。

## <a name="post-ilb-ase-creation-validation"></a>ILB ASE 作成後の検証 ##

ILB ASE は、ILB を含まない ASE とは少し異なります。 既に説明したように、独自の DNS を管理する必要があります。また、HTTPS 接続に独自の証明書も用意する必要があります。

ASE を作成すると、指定したドメインがドメイン名として表示され、**[設定]** メニューに **[ILB 証明書]** という項目が表示されます。 ILB ASE ドメインを指定しない証明書が ASE とともに作成されます。 ASE をその証明書とともに使用すると、ブラウザーによってそれが無効であると示されます。 この証明書により HTTPS のテストは簡単になりますが、証明書が自己署名であるか証明機関 (CA) から取得したものであるかに関係なく、ILB ASE ドメインに紐付けられた独自の証明書をアップロードすることが期待されます。

![][3]

有効な SSL 証明書を取得するには、内部 CA を利用する、外部の発行元から証明書を購入する、自己署名証明書を使用するなどの方法があります。 どのようなソースから SSL 証明書を取得した場合でも、以下の証明書属性を適切に構成する必要があります。

-   _Subject_: この属性は、_\*.your-root-domain-here_ に設定する必要があります。
-   _Subject Alternative Name_: この属性は、_\*.your-root-domain-here_ と _\*.scm.your-root-domain-here_ を両方とも含むように設定する必要があります。 2 つ目のエントリが必要な理由は、各アプリに関連付けられた SCM/Kudu サイトへの SSL 接続が、_your-app-name.scm.your-root-domain-here_ 形式のアドレスを使用して確立されるためです。

有効な SSL 証明書を取得した後は、さらに 2 つの準備手順を実行する必要があります。 SSL 証明書は、.pfx ファイルに変換して保存する必要があります。 .pfx ファイルには、中間証明書とルート証明書をすべて含める必要があり、さらにパスワードで保護する必要もあるという点に注意してください。

PowerShell を使用して独自の証明書を作成する場合は、以下に示すコマンドを使用できます。  *internal.contoso.com* の代わりに、自分の ILB ASE ドメイン名を使用してください。 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "\*.internal-contoso.com","\*.scm.internal-contoso.com"
    
    $certThumbprint = "cert:\localMachine\my\" +$certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText
    
    $fileName = "exportedcert.pfx" 
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password

これらの PowerShell コマンドが生成する証明書は、お使いのブラウザーで使用される信頼チェーン内の CA によって作成されなかったため、引き続きブラウザーによってフラグされます。 お使いのブラウザーで問題が発生しない証明書を取得する唯一の方法は、ブラウザーの信頼チェーン内の商用 CA から調達する方法です。  

![][4]

次のようにして独自の証明書をアップロードし、アクセスをテストします。

1.  ASE の作成後に ASE UI に移動します (**[ASE]、[設定]、[ILB 証明書]** を順に選択)。
2.  証明書 pfx ファイルを選択して ILB 証明書を設定し、パスワードを指定します。 この手順の処理には少し時間がかかります。アップロード処理が進行中というメッセージが表示されます。
3.  ASE の ILB アドレスを取得します (**[ASE]、[プロパティ]、[仮想 IP アドレス]** を順に選択)。
4.  作成後に、ASE に Web アプリを作成します。
5.  その Virtual Network 内にない場合は VM を作成します。

    > [!NOTE] 
    > この VM は ASE と同じサブネットには作成しないでください。 作成すると、セットアップが中断します。
    >
    >

6.  ASE ドメインで使用する DNS を設定します。 DNS のドメインでワイルドカードを使用できます。または、単純なテストを行いたい場合は、VM 上のホスト ファイルを編集して、Web アプリ名を VIP IP アドレスに設定します。 ASE のドメイン名が _.ilbase.com_ で、_mytestapp_ という名前の Web アプリを作成した場合、アドレスは _mytestapp.ilbase.com_ になります。 その後 _mytestapp.ilbase.com_ を設定して ILB アドレスを解決します。 (Windows では、ホスト ファイルは _C:\Windows\System32\drivers\etc\_ にあります。)Web デプロイの発行または高度なコンソールへのアクセスをテストする場合は、_mytestapp.scm.ilbase.com_ のレコードも作成する必要があります。
7.  この VM 上でブラウザーを使用し、***http://mytestapp.ilbase.com*** (または、Web アプリ名とドメインの組み合わせ) にアクセスします。
8.  その VM でブラウザーを使用して、***https://mytestapp.ilbase.com*** に移動します。 自己署名証明書を使用する場合は、セキュリティが確保されないことを受け入れる必要があります。

ILB の IP アドレスの一覧は **[IP アドレス]** の下に表示されます。 外部 VIP で使用される IP アドレスや受信管理トラフィック用の IP アドレスも表示されます。

![][5]

### <a name="functions-and-the-ilb-ase"></a>関数と ILB ASE

ILB ASE で関数を使用するときに、"*現在、関数を取得できません。後でもう一度やり直してください。*" というエラーが表示されることがあります。  これは、関数 UI が HTTPS を介して scm サイトを使用することが原因です。  これは、ブラウザーのルート証明書がない HTTP 証明書を ASE に使用している場合に発生することがあります。  さらに、IE\Edge ブラウザーでは *accept-invalid-cert* 設定がタブ間で共有されません。 このため、次のいずれかを実行できます。

- 証明書を信頼された証明書ストアに追加する。 
- Chrome を使用する。ただし、先に scm サイトにアクセスし、信頼されていない証明書を受け入れてからポータルに移動する必要があります。

## <a name="dns-configuration"></a>DNS の構成 ##

外部 VIP を使用する場合、DNS は Azure によって管理されます。 ASE に作成されるすべてのアプリは、Azure DNS (パブリック DNS) に自動的に追加されます。 ILB ASE では、独自の DNS を管理する必要があります。 _contoso.net_ など特定のサブドメインについて、次のような ILB アドレスを示す DNS A レコードを DNS に作成する必要があります。

- *.contoso.net
- *.scm.contoso.net

ILB ASE ドメインがこの ASE 以外の複数の用途で使用される場合、DNS をアプリ名ベースごとに管理する必要があることがあります。 これは、作成時に新しいアプリ名をそれぞれ DNS に追加する必要があるため、より煩雑です。 この理由から、専用ドメインを使用することをお勧めします。

## <a name="publishing-with-an-ilb-ase"></a>ILB ASE で発行する ##

作成されるすべてのアプリにはエンドポイントが 2 つあります。 ILB ASE には、*&lt;アプリ名>.&lt;ILB ASE ドメイン>* のほか、*&lt;アプリ名>.scm.&lt;ILB ASE ドメイン>* もあります。 

SCM サイト名をクリックすると、Azure Portal 内から Kudu コンソールに移動します。これは、**高度なポータル**と呼ばれます。 Kudu コンソールでは、環境変数の表示、ディスクの確認、コンソールの使用など、数多くのことを行うことができます。 詳しくは、[Azure App Service の Kudu コンソール][Kudu]に関するビデオをご覧ください。 

マルチテナント App Service と外部 ASE では、Azure Portal と Kudu コンソールの間にシングル サインオンが存在します。 ただし、ILB ASE の場合は代わりに発行証明書を使用して Kudu コンソールにサインインする必要があります。

インターネット ベースの CI システム (Github、VSTS など) は ILB ASE では動作しません。発行エンドポイントにインターネットでアクセスすることができないためです。 プル モデルを使った CI システム (Dropbox など) で代用する必要があります。

ILB ASE 内のアプリには、その ILB ASE の作成時に使われたドメインが、発行エンドポイントとして使用されます。 これはアプリの発行プロファイルのほか、アプリのポータル ブレード (**[概要]** > **[要点]**、**[プロパティ]** など) で確認することができます。 サブドメインが *contoso.net* でアプリ名が *mytest* の ILB ASE では、FTP を使用して *mytest.contoso.net* にアクセスし、*mytest.scm.contoso.net* に対して Web デプロイを実行します。

## <a name="coupling-an-ilb-ase-with-a-waf-device"></a>ILB ASE を WAF デバイスと結合する ##

Azure App Service にはシステムを保護するための多数のセキュリティ対策が施されており、アプリがハッキングされているかどうかを判定するのに役立ちます。 Web アプリケーションの保護を確保する最も優れた方法は、ホスト プラットフォームを結合することです。たとえば、Azure App Service を Web Application Firewall (WAF) と結合します。 ILB ASE にはネットワークから分離されたアプリケーション エンドポイントが存在するため、このような用途に最適です。  

ILB ASE と WAF デバイスを構成する方法について詳しくは、[App Service Environment と Web Application Firewall の構成][ASEWAF]に関する記事をご覧ください。 記事では、Barracuda 仮想アプライアンスを ASE と使用する方法について示します。 Azure Application Gateway を使用する方法もあります。 App Gateway は OWASP コア ルールを使用して、その背後に置かれたすべてのアプリケーションのセキュリティを確保します。 Azure App Gateway について詳しくは、[Azure Web Application Firewall の概要][AppGW]に関する記事をご覧ください。

### <a name="getting-started"></a>使用の開始 ###

App Service 環境に関するすべての記事と作業方法は [App Service Environment の README][ASEReadme] をご覧ください。

App Service 環境の使用を開始するには、「[Introduction to App Service Environments][Intro]」 (App Service Environment の概要) をご覧ください。

Azure App Service プラットフォームの詳細については、 [Azure App Service](http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/)に関するページを参照してください。
 
<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[3]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate.png
[4]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate2.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md

