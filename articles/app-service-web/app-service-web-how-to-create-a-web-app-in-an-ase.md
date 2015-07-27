<properties
	pageTitle="App Service 環境内で Web アプリを作成する方法"
	description="App Service 環境内での Web アプリおよび App Service プランの作成フロー"
	services="app-service\web"
	documentationCenter=""
	authors="ccompy"
	manager="stefsch"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="04/27/2015"
	ms.author="ccompy"/>

# App Service 環境内で Web アプリを作成する方法 #

Web アプリの作成方法は、App Service 環境 (ASE) においてもほぼ同じです。App Service 環境の機能に慣れていない場合は、[App Service 環境の概要](app-service-app-service-environment-intro.md)に関するページにあるドキュメントを参照してください。

ASE で Web アプリを作成するには、最初に ASE を作成する必要があります。ASE を作成する方法の詳細については、「[App Service 環境の作成方法](app-service-web-how-to-create-an-app-service-environment.md)」を参照してください。

Web アプリを作成するための最初の手順は、App Service プラン (ASP) を作成または選択することです。ASE で ASP を作成する手順は、通常と同じです。つまり、[新規]、[Web + モバイル]、[Web アプリ] の順に選択して、Web アプリ作成フローを開始します。

![][1]


App Service 環境内で既に作成済みの App Service プランを使用する場合は、目的のプランを選択した後で Web アプリの名前を入力し、[作成] を選択します。これは、通常どおりに Web アプリを作成するのと同じフローです。大きな違いは、Web アプリのアドレスが

[*sitename*].[*App Service 環境の名前*].p.azurewebsites.net

となることです。次のアドレスではありません。

[*sitename*].azurewebsites.net

ここで、Web アプリ名は、Azure App Service 全体で一意である必要があります。たとえば、"thisismywebapp" という名前の Web アプリ名を作成する場合、Azure App Service に "thisismywebapp" という名前の他の Web アプリが現在存在していないことが条件となります。

### App Service プラン ###

App Service プランは、管理された Web アプリのセットです。料金を選択するとき、料金は個々 のアプリではなく App Service プランに適用されます。Web アプリのインスタンスの数をスケールアップするには、ASP のインスタンスをスケールアップします。すると、そのプラン内のすべての Web アプリが影響を受けます。サイトのスロット、VNET 統合などのいくつかの機能には、プラン内での数量制限があります。App Service プランの詳細については、[Azure App Service プランの詳細な概要](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)に関するページにあるドキュメントを参照してください。

新しい App Service プランを作成する場合は、App Service 環境で ASP を作成する場合といくつかの違いがあります。特に、ワーカーの選択肢が異なります。これは、App Service 環境では共有ワーカーがないためです。使用する必要があるワーカーは、管理者によって App Service 環境に割り当てられたワーカーです。これは、新しい ASP を作成するには、App Service 環境内のすべてのASP にわたるインスタンスの総数よりも多くのワーカーを App Service 環境に割り当てる必要があることを意味します。ASP を作成するのに十分なワーカーが App Service 環境にない場合は、App Service 環境の管理者に依頼してワーカーを追加してもらう必要があります。

App Service 環境でホストされる ASP に関するもう 1 つの違いは、料金の選択肢がないことです。App Service 環境を利用する場合、システムによって使用されるコンピューティング リソースについて料金を支払い、その環境内の ASP に対する追加料金はありません。通常、ASP を作成するときに、料金プランを選択します。これに基づいて、支払い料金が決定されます。App Service 環境は、基本的に、コンテンツを作成できるプライベートな場所です。支払いは環境に対するもので、コンテンツをホストする操作を対象にしたものではありません。

### App Service 環境の選択 ###

App Service 環境は基本的にプライベートなデプロイメント場所であるため、最初に、使用する ASE を場所の選択コントロールで選択します。

![][2]

選択すると、UI が更新され、料金プランの選択コントロールがワーカー プールの選択コントロールで置き換えられます。場所には、ASE システムの名前と、システムが配置されているリージョンが示されます。[URL] の下の ASE のドメイン名では、通常使用される .azurewebsites.net が App Service 環境の名前で置き換えられます。

![][3]

### ワーカー プールの選択 ###

Azure App Service 内および App Service 環境外では、通常、3 つのサイズが専用の料金プランで選択できるようになっています。同様に、ASE を所有しているお客様は、ワーカーのプールを 3 つまで定義でき、そのワーカー プールで使用する VM のサイズを指定することができます。ASP の料金プランを選択する代わりに、ワーカー プールと呼ばれるものを選択します。

ワーカー プールの選択用 UI では、ワーカー プールに使用される VM のサイズがその名の下に表示されます。"使用可能" な数は、そのプールで使用できる VM の数を示します。プール全体ではこの数を超える VM が実際に含まれている可能性がありますが、この値は単に使用されていない VM の数を表します。App Service 環境を調整してさらに多くのコンピューティング リソースを追加する必要がある場合は、[App Service 環境の構成](app-service-web-configure-an-app-service-environment.md)に関するページを参照してください。

![][4]

この例では、使用できるワーカー プールが 2 つしかありません。これは、ASE 管理者によってこれらの 2 つのワーカー プールのみに VM が割り当てられているためです。3 つ目のワーカー プールは、VM が割り当てられているときに表示されます。

### Web アプリを作成した後 ###

ASE 内での Web アプリの実行と ASP の管理に関しては、いくつかの点を考慮する必要があります。

既に説明したように、ASE の所有者は、システムのサイズに責任を持つため、目的の ASP をホストするのに十分な容量があることも保証する必要があります。利用可能なワーカーがない場合は、ASP を作成できません。これは、Web アプリをスケールアップする場合も同じです。より多くのインスタンスが必要な場合は、App Service 環境の管理者に依頼してワーカーを追加してもらう必要があります。

Web アプリと ASP を作成した後はこれをスケールアップすることをお勧めします。ASE でアプリのフォールト トレランスを提供するには、常に ASP のインスタンスが 2 つ以上存在している必要があります。ASE 内の ASP のスケーリングは、通常どおり ASP UI を使用して実行します。スケーリングの詳細については、[App Service 環境内で Web アプリをスケーリングする方法](app-service-web-scale-a-web-app-in-an-app-service-environment.md)についてのこちらのドキュメントを参照してください。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment
 

<!---HONumber=July15_HO3-->