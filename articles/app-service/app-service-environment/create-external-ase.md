---
title: "外部 Azure App Service Environment を作成する"
description: "アプリまたはスタンドアロンの作成中に Azure App Service Environment を作成する方法について説明します。"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 3f1418b71a7327264e29e3f08ef97b254ee9930d
ms.contentlocale: ja-jp
ms.lasthandoff: 06/26/2017

---
# <a name="create-an-external-app-service-environment"></a>外部 App Service Environment を作成する #

App Service Environment (ASE) は、ユーザーの Azure Virtual Network (VNet) 内のサブネットに Azure App Service をデプロイしたものです。 ASE をデプロイするには、2 つの方法があります。

- 外部 IP アドレスの VIP を使用する。_外部 ASE_ と呼ばれます。
- 内部 IP アドレスの VIP を使用する。内部ロード バランサー (ILB) は内部エンドポイントであるため、_ILB ASE_ と呼ばれます。

この記事では、外部 ASE を作成する方法について説明します。 ASE の概要については、まず「[An Introduction to the App Service Environment][Intro]」 (App Service Environment の概要) をご覧ください。ILB ASE を作成する方法について詳しくは、「[Create and use an ILB ASE][MakeILBASE]」 (ILB ASE を作成して使用する) をご覧ください。

## <a name="before-you-create-your-ase"></a>ASE を作成する前に ##

ASE を作成した後には、次の内容は変更できないのでご注意ください。

- 場所
- サブスクリプション
- リソース グループ
- 使用する Virtual Network
- 使用するサブネット
- サブネットのサイズ

> [!NOTE]
> Virtual Network を選択し、サブネットを指定するときは、将来の拡大を考慮した大きさにしてください。 推奨されるサイズは、128 のアドレスを持つ `/25` です。
>

## <a name="three-ways-to-create-an-ase"></a>ASE を作成する 3 つの方法 ##

ASE を作成する方法は 3 つあります。 次のように ASE を作成できます。

- App Service プランを作成するとき。これにより、ASE と App Service プランが 1 つの手順で作成されます。
- スタンドアロンの ASE 作成 UI から。何も含まれていない ASE を作成します。 これは ASE 作成 UI のより高度なエクスペリエンスであり、ここから内部ロード バランサー (ILB) が含まれる ASE を作成します。
- Resource Manager テンプレートを使用して。 これは高度なユーザーが対象で、[テンプレートからの ASE の作成][MakeASEfromTemplate]に関する記事で説明します。

ILB なしで作成された ASE にはパブリック VIP があります。 つまり、ASE 内のアプリへのすべての HTTP トラフィックはインターネットにアクセス可能な IP アドレスをヒットすることを意味します。 ILB がある ASE は、Virtual Network の IP アドレスにエンドポイントがあります。 これらのアプリはインターネットに直接公開されません。

## <a name="create-an-ase-and-an-app-service-plan-together"></a>ASE と App Service プランを一緒に作成する ##

App Service プランは、アプリのコンテナーです。 App Service でアプリを作成するときは常に、App Service プランを選択または作成する必要があります。 対象環境に App Service プランが存在し、App Service プランにアプリが存在するコンテナー モデルになっています。

App Service プランの作成中に ASE を作成するには:

1. [Azure Portal](https://portal.azure.com/) で、**[新規]、[Web + モバイル]、[Web アプリ]** の順にクリックします。

    ![][1]
1. サブスクリプションを選択します。 サブスクリプションを複数保有している場合、ASE でアプリを作成するには、ASE を作成するときに使用したものと同じサブスクリプションを使用する必要があります。
1. リソース グループを選択または作成します。 *リソース グループ*を使用すると、関連する Azure リソースを 1 つの単位として管理でき、アプリ用に*ロール ベースのアクセス制御* (RBAC) 規則を作成する際に便利です。 詳細については、「[Azure Resource Manager の概要][ARMOverview]」をご覧ください。
1. App Service プランをクリックし、**[新規作成]** を選択します。

    ![][2]
1. **[場所]** ドロップダウンで、AES を作成するリージョンを選択します。 既存の ASE を選択すると、新しい ASE は作成されず、単に選択した ASE に App Service プランが作成されます。
1. **料金プラン**の UI を選択し、**[分離]** 価格 SKU の 1 つを選択します。 **[分離]** SKU カードと ASE 以外の場所を選択すると、新しい ASE がその場所に作成されます。 これを行うことで、価格カード ページで **[選択]** をクリックすると ASE 作成 UI が表示されます。 **[分離]** SKU は、ASE と組み合わせてのみ使用できます。 また、**[分離]** 以外の ASE の他の価格 SKU は使用できません。

    ![][3]
1. ASE の名前を入力します。 ASE の名前は、アプリのアドレス可能な名前に使用されます。 たとえば、ASE の名前が _appsvcenvdemo_ の場合、サブドメイン名は *.appsvcenvdemo.p.azurewebsites.net* になります。 ここで *mytestapp* という名前のアプリを作成した場合、この Web アプリのアドレスは *mytestapp.appsvcenvdemo.p.azurewebsites.net* になります。 ASE の名前に空白文字は使用できません。 名前に大文字を使用した場合、ドメイン名はその名前をすべて小文字で表記したバージョンになります。

    ![][4]
1. **[新規作成]** または **[既存のものを選択]** のいずれかを選択します。 既存の Virtual Network を選択するオプションは、選択したリージョンに Virtual Network がある場合にのみ使用できます。 **[新規作成]** を選択し、Virtual Network の名前を指定すると、選択したリージョンのアドレス領域 `192.168.250.0/23` にその名前の新しい Resource Manager Virtual Network が作成されます。 **[既存のものを選択]** を選択した場合は、次のことを行う必要があります。
    1. 複数ある場合は、Virtual Network のアドレス ブロックを選択します。
    2. 新しいサブネット名を指定します。
    3. サブネットのサイズを選択します。 **リマインダー: ASE の将来の拡大を考慮した大きさにする必要があります。** 推奨されるサイズは、最大サイズの ASE を処理できる 128 のアドレスを持つ `/25` です。 たとえば、`/28` は 16 のアドレスしか使用できないため、推奨されません。 インフラストラクチャは最小で 5 つのアドレスを使用する必要があるため、`/28` サブネットに最大で 11 のアドレスしか残りません。
    4. サブネット IP の範囲を選択します。

**[作成]** を選択すると、ASE の作成プロセスが開始します。 これにより、App Service プランとアプリも作成されます。 ASE、App Service プラン、およびアプリはすべて同じサブスクリプションの元に作成され、同じリソース グループに入ります。 ASE を App Service プランやアプリと別のリソース グループに入れる必要がある場合、または ILB ASE が必要な場合は、スタンドアロンの ASE 作成操作を使用します。

## <a name="create-an-ase-by-itself"></a>ASE を単独で作成する ##

スタンドアロンの ASE 作成フローを実行すると、何も持たない ASE が作成されます。 空の ASE でも、インフラストラクチャに対して月額料金が発生します。 このワークフローを実行する主な理由は、ILB を持つ ASE を作成するか、独自のリソース グループ内に ASE を作成することです。 ASE を作成したら、通常のアプリ作成操作により、および新しい ASE を場所として選択することで、その ASE にアプリを作成できます。

Azure Marketplace で ***App Service Environment*** を検索または [新規]、[Web + モバイル]、[App Service Environment] の順に選択することで、ASE 作成 UI にアクセスします。 スタンドアロンの作成操作を使用して ASE を作成するには:

1. ASE の名前を指定します。 ASE に指定した名前は、ASE で作成されるアプリに使用されます。 ASE の名前が *mynewdemoase* の場合、サブドメイン名は *.mynewdemoase.p.azurewebsites.net* になります。 ここで *mytestapp* という名前のアプリを作成した場合、この Web アプリのアドレスは *mytestapp.mynewdemoase.p.azurewebsites.net* になります。 ASE の名前に空白文字は使用できません。 名前に大文字を使用した場合、ドメイン名はその名前をすべて小文字で表記したバージョンになります。 ILB を使用する場合、ASE の名前はサブドメインでは使用されず、ASE の作成時に明示的に指定されます。

    ![][5]
1.  サブスクリプションを選択します。 ASE で使用するサブスクリプションは、その ASE 内のすべてのアプリを作成するときに使用されるサブスクリプションでもあります。 サブスクリプションが異なる Virtual Network に ASE を配置することはできません。
1.  新しいリソース グループを選択するか指定します。 ASE で使用されるリソース グループは、Virtual Network で使用されるものと同じである必要があります。 既存の Virtual Network を選択すると、ASE のリソース グループの選択は、Virtual Network のリソース グループを反映するように更新されます。

    ![][6]
1. Virtual Network と場所を選択します。 新しい Virtual Network を作成するか、既存の Virtual Network を選択できます。 新しい Virtual Network を選択した場合は、名前と場所を指定できます。 新しい Virtual Network のアドレス範囲は 192.168.250.0/23 になり、**[既定]** という名前のサブネットが 192.168.250.0/24 として定義されます。 Resource Manager Virtual Network のみを選択できます。 選択した **[VIP の種類]** によって、インターネットから ASE に直接アクセスできる (外部) か、内部ロード バランサー (ILB) を使用するかが決まります。 詳しくは、「[Create and Use an Internal Load Balancer with an App Service Environment][MakeILBASE]」 (App Service Environment で内部ロード バランサーを作成して使用する) をご覧ください。 [VIP の種類] として **[外部]** を選択した場合は、システムが IP ベースの SSL 目的で作成する外部 IP アドレスの数を選択できます。 **[内部]** を選択した場合は、ASE で使用するサブドメインを指定する必要があります。 ASE は、パブリック アドレス範囲*と* RFC1918 アドレス空間の*どちらか*を使用する仮想ネットワークにデプロイできます (RFC1918 アドレス空間とは プライベート アドレスのことです)。 パブリック アドレス範囲の仮想ネットワークを使用するには、あらかじめ Virtual Network サブネットを作成しておく必要があります。 既存の Virtual Network を選択した場合は、ASE の作成時に新しいサブネットを作成する必要があります。 **ポータルで事前に作成したサブネットを使用することはできません。Resource Manager テンプレートを使用して ASE を作成する場合は、既存のサブネットを持つ ASE を作成できます。** テンプレートから ASE を作成するには、[テンプレートからの App Service Environment の作成][MakeASEfromTemplate]に関する記事をご覧ください。

## <a name="app-service-environment-v1"></a>App Service Environment v1 ##

最初のバージョンの ASE 機能 (ASEv1) のインスタンスを作成することもできます。 その操作に到達するには、マーケットプレースで **App Service Environment v1** を検索します。 作成操作は、スタンドアロンの ASE 作成エクスペリエンスと同じです。 完了すると、ASEv1 が 2 つのフロントエンドと 2 つのワーカーとともに作成されます。 ASEv1 では、フロントエンドやワーカーを管理する必要はありません。 App Service プランを作成するときに自動的には追加されません。 フロントエンドは HTTP/HTTPS エンドポイントとして動作し、アプリをホストする役割を持つワーカーにトラフィックを送信します。 数量は ASE を作成した後に調整できます。 ASEv1 について詳しくは、[App Service Environment v1 の概要][ASEv1Intro]に関するページをご覧ください。 ASEv1 のスケーリング、管理、および監視について詳しくは、「[App Service Environment の構成][ConfigureASEv1]」をご覧ください。

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png



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

