---
title: "Azure App Service Environment の使用"
description: "Azure App Service Environment でアプリを作成、発行、スケールする方法"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: c24f716d58f534d1439377234fa1263269961db7
ms.contentlocale: ja-jp
ms.lasthandoff: 06/26/2017

---
# <a name="using-an-app-service-environment"></a>App Service Environment の使用 #

## <a name="overview"></a>概要 ##

App Service Environment (ASE) は、ユーザーの Azure Virtual Network (VNet) 内のサブネットに Azure App Service をデプロイしたものです。 構成は次のとおりです。

- フロント エンド - ASE 内で HTTP/HTTPS が終端する部分です。
- ワーカー - アプリのホストとなるリソースです。
- データベース - 環境を定義する情報が格納されます。
- ストレージ - ユーザーによって発行されたアプリのホストとして使用されます。

> [!NOTE]
> App Service Environment には、ASEv1 と ASEv2 の 2 つのバージョンがあります。 ASEv1 では、使用するリソースを事前に管理する必要があります。 ASEv1 の構成と管理の方法については、[App Service Environment v1 の構成][ConfigureASEv1]に関するページを参照してください。 以降このドキュメントでは、ASEv2 について重点的に説明します。
>
>

ASE は (ASEv1 と ASEv2 のどちらも)、アプリ アクセスに外部 VIP を使用したデプロイと内部 VIP を使用したデプロイが可能です。 外部 VIP を使用したデプロイは一般に外部 ASE と呼ばれています。一方、内部 VIP を使用したデプロイは、内部ロード バランサー (ILB) が使用されることから、ILB ASE と呼ばれています。 ILB ASE の詳細については、[ILB ASE の作成と構成][MakeILBASE]に関するページを参照してください。

## <a name="create-a-web-app-in-an-ase"></a>ASE での Web アプリの作成 ##

ASE には、通常と同じプロセスで Web アプリを作成できますが、ごくわずかな違いがあります。 新しい App Service プランを作成する場合、以下が該当します。

- アプリのデプロイ先の場所として地理的な場所ではなく ASE を選択します。
- ASE で作成する App Service プランはすべて、"分離" 価格レベルに属している必要があります。

ASE をご利用でない場合は、[App Service Environment の作成][MakeExternalASE]に関するページの手順に従って作成できます。

ASE で Web アプリを作成するには、次の手順に従います。

1. **[新規] &gt; [Web + モバイル]** をクリックし、**[Web アプリ]** を選択します。
2. Web アプリの名前を指定します。 ASE 内で既に App Service プランが選択されている場合、アプリのドメイン名に、ASE のドメイン名が反映されます。

    ![][1]

1. サブスクリプションを選択します。
2. 新しいリソース グループの名前を指定するか、**[既存のものを使用]** を選択して、ボックスの一覧からいずれかを選択します。
3. ASE で既存の App Service プランを選択するか、次の手順で新しく作成します。
    1. **[新規作成]** を選択します。
    2. App Service プランの名前を指定します。
    3. **[場所]** ボックスの一覧から該当する ASE を選択します。
    4. **[分離]** 価格レベルを選択します。 **[選択]**をクリックします。
    5. **[OK]** をクリックします。
    
    ![][2]
1. ページの下部にある **[Create]**」を参照してください。

## <a name="how-scale-works"></a>スケールのしくみ ##

すべての App Service アプリは、App Service プランで実行されます。 対象環境に App Service プランが存在し、App Service プランにアプリが存在するコンテナー モデルになっています。 アプリをスケールするときは、App Service プランをスケールすることになります。そうすることによって同じプラン内のすべてのアプリがスケールされます。

ASEv2 では、App Service プランをスケールすると、必要なインフラストラクチャが自動的に追加されます。 この点は ASEv1 と異なります。ASEv1 の場合は、App Service プランを作成したりスケールアウトしたりする前に、必要なインフラストラクチャをあらかじめ追加しておく必要があります。 つまり ASEv2 では、インフラストラクチャが追加されるために、スケール操作までに時間差が生じます。

マルチテナントの App Service では、通常スケーリングが即座に実行されます。スケールアウトへの対応としてすぐに利用できるリソースのプールが存在するためです。 ASE にはそのようなバッファーが存在せず、リソースは必要に応じて割り当てられます。

ASE では、最大 100 インスタンスまでスケールアップすることができます。 この 100 インスタンスは、すべて 1 つの App Service プランに含めることも、複数の App Service プランに分散させることもできます。

## <a name="ip-addresses"></a>IP アドレス ##

App Service には、アプリに専用の IP アドレスを割り当てる機能があります。 [既存のカスタム SSL 証明書の Azure Web Apps へのバインド][ConfigureSSL]に関する記事で説明されているように、この機能は IP ベースの SSL を構成することによって利用できます。 ただし ASE では重要な例外があります。ILB ASE では、IP ベースの SSL に使用する IP アドレスを自分で別途追加することができません。

ASEv1 では、使用する IP アドレスを事前にリソースとして割り当てておく必要があります。 ASEv2 では、マルチテナントの App Service の場合と同様、単にアプリの IP アドレスを使用します。 ASEv2 には、IP アドレスが 30 個を上限として、常に予備のアドレスが 1 つ存在します。 すぐに利用できる IP アドレスを常時確保するために、アドレスを 1 つ使用すると、そのたびに、別のアドレスが追加されます。 別の IP アドレスを割り当てるまでに、どうしても時間差が発生するので、IP アドレスを立て続けに追加することはできません。

## <a name="front-end-scaling"></a>フロント エンド スケーリング ##

ASEv2 では、App Service プランをスケールアウトすると、そのために必要なワーカーが自動的に追加されます。 フロント エンドは、ASE を 1 つ作成するごとに 2 つ割り当てられますが、それとは別に自動的にスケールアウトされる分として、App Service プラン内の 15 インスタンスにつき 1 つのフロント エンドが増やされます。 つまり 15 個のインスタンスが存在する場合、フロント エンド数は 3 つになります。 同様に、30 インスタンスまでスケールした場合は、フロント エンド数は 4 つです。

ほとんどのシナリオでは、これで十分対応できます。しかし、もっと早いペースでスケールアウトする必要がある場合は、この比率を変更することができます。その場合、App Service プラン内の 5 インスタンスにつきフロント エンドを 1 つ増やすのが最も早いペースとなります。 この比率の変更には料金が発生します。[Azure App Service の価格][Pricing]に関するページを参照してください。

フロント エンド リソースは、ASE の HTTP/HTTPS エンドポイントです。 既定のフロント エンド構成では、各フロント エンドのメモリ使用量が常時 60% 程度となります。 顧客のワークロードがフロント エンドで実行されることはありません。 スケールに関して重要となるフロント エンドの要素は CPU で、これは主に HTTPS トラフィックによって決まります。

## <a name="app-access"></a>アプリのアクセス ##

外部 ASE では、アプリの作成時に使用されるドメインがマルチテナントの App Service とは異なり、ドメインに ASE の名前が含まれます。 外部 ASE の作成について詳しくは、[App Service Environment の作成][MakeExternalASE]に関するページを参照してください。 外部 ASE のドメイン名は、たとえば *.&lt;asename&gt;.p.azurewebsites.net* という形式になります。 つまり実際の ASE の名前が _external-ase_ で、その ASE でホストするアプリの名前が _contoso_ である場合、そのアプリには次の URL でアクセスすることになります。

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

*contoso.scm.external-ase.p.azurewebsites.net* は、Kudu コンソールにアクセスするとき、または Web デプロイでアプリを発行するときに使用する URL です。 Kudu コンソールについては、[Azure App Service の Kudu コンソール][Kudu]に関するページを参照してください。 Kudu コンソールを使用すると、デバッグやファイルのアップロード、ファイルの編集など、さまざまな作業を Web UI で行うことができます。

ILB ASE では、デプロイ時にドメインを決定します。 ILB ASE の作成について詳しくは、[ILB ASE の作成と使用][MakeILBASE]に関するページを参照してください。 ドメイン名として _ilb-ase.info_ を指定した場合、その ASE 内のアプリには、その指定したドメインをアプリの作成時に使用します。 たとえば _contoso_ という名前のアプリの URL は次のようになります。

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>発行 ##

マルチテナントの App Service と同様、ASE では次の発行方法を利用できます。

- Web デプロイ
- FTP
- 継続的インテグレーション
- Kudu コンソールでのドラッグ アンド ドロップ
- IDE (Visual Studio、Eclipse、Intellij IDEA など)

外部 ASE に関しては、これとまったく同じ発行方法が利用できます。 詳細については、[Azure App Service へのデプロイ][AppDeploy]に関するページを参照してください。 

発行方法に大きな違いがあるのは ILB ASE です。 ILB ASE では、発行エンドポイントは ILB を介してしか利用できません。 ILB は、仮想ネットワークの ASE サブネット内のプライベート IP 上に存在します。 ILB へのネットワーク アクセスができなければ、その ASE にアプリを発行することができません。 [ILB ASE の作成と使用][MakeILBASE]に関するページで述べられているように、システム内にアプリ用の DNS を構成する必要があります。 SCM のエンドポイントも対象となります。 これらが正しく定義されていないと、アプリを発行することはできません。 ご利用の IDE から直接発行するためには、ILB へのネットワーク アクセスが IDE にも必要です。

インターネットベースの CI システム (GitHub、VSTS など) は ILB ASE では動作しません。発行エンドポイントにインターネットでアクセスすることができないためです。 プル モデルを使用した CI システム (Dropbox など) で代用する必要があります。

ILB ASE 内のアプリには、その ILB ASE の作成時に使用されたドメインが、発行エンドポイントとして使用されます。 これはアプリの発行プロファイルのほか、アプリのポータル ブレード (**[概要]** > **[要点]**、**[プロパティ]** など) で確認することができます。 

## <a name="pricing"></a>価格 ##

ASEv2 では、**分離**という、ASEv2 でのみ使用される新しい価格 SKU が用意されています。 ASEv2 でホストされるすべての App Service プランは、"分離" 価格 SKU に属します。 App Service プランの料金に加え、ASE そのものの固定料金がかかります。 この料金が ASE のサイズによって変わることはありません。 

それ以外では、フロント エンドのスケール率の調整またはフロント エンドのサイズの調整に伴う料金が考えられます。 フロント エンドの追加ペースが早まるような形でスケール率を調整した場合、本来システムに自動的に追加されることのないコア数の上乗せについて料金がかかります。 同様に、より大きなサイズをフロント エンドに選択した場合、本来自動的に割り当てられることのないコア数について料金がかかります。 たとえば、スケール率を調整して 10 にした場合、App Service プラン内の 10 インスタンスにつきフロント エンドが 1 つ追加されます。 固定料金で対応されるスケール率は、15 インスタンスにつき 1 フロント エンドです。 スケール率を 10 にした場合、10 個の ASP インスタンスに関して追加される 3 つ目のフロント エンドについて料金が発生しますが、インスタンス数が 15 に到達したら、そのフロントエンドは、自動的に追加された分と見なされるので、その料金の支払いは不要となります。

詳細については、[Azure App Service の価格][Pricing]に関するページを参照してください。

## <a name="deleting-an-ase"></a>ASE の削除 ##

App Service Environment を削除する必要がある場合は、単に [App Service Environment] ブレード上部の **[削除]** アクションを使用します。 この操作を実行すると、App Service Environment の名前を入力し、操作の実行を確定するように求められます。 App Service Environment を削除すると、その内部のすべてのコンテンツも削除されるので注意してください。 

![][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png


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

