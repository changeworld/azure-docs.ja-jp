<properties 
	pageTitle="App Service 環境内で Web アプリをスケーリングする方法" 
	description="App Service 環境内での Web アプリのスケーリング" 
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
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="ccompy"/>

# App Service 環境内での Web アプリのスケーリング #

大まかに言うと、App Service 環境は基本的に Azure App Service の VNET への個人的なデプロイメントであり、サブスクリプションでのみ管理できます。App Service 環境は VNET 内に配置されるため、新しいネットワーク機能を提供し、さらに Azure App Service 環境で通常提供されるレベル以上の拡張性を提供します。App Service 環境 (ASE) の詳細については、[App Service 環境の概要][WhatisASE]に関するページを参照してください。App Service 環境を作成する方法や App Service 環境に Web アプリを作成する方法の詳細については、[App Service 環境の作成方法][HowtoCreateASE]に関するページと [App Service 環境での Web アプリの作成方法][CreateWebappinASE]に関するページを参照してください。

通常、Web アプリのスケール属性を変更する場合は、App Service プランのレベルで変更が加えられることに注意してください。App Service プランのスケーリングや App Service 環境外部の App Service プランの詳細については、[Azure App Service での Web アプリのスケーリング][ScaleWebapp]に関するページおよび [App Service プランの詳細][Appserviceplans]に関するページを参照してください。

App Service 環境で Web アプリをスケーリングする方法は、通常の Web アプリをスケーリングする方法とよく似ています。Azure App Service では、通常、次の 3 つの項目をスケーリングできます。

- 料金プラン
- (専用インスタンスの) ワーカーのサイズ
- インスタンスの数

ASE では、料金プランを選択または変更する必要はありません。機能に関しては、既に Premium 料金機能レベルにあります。App Service 環境においても、共有ワーカーは存在しません。すべて専用ワーカーです。コンピューティング リソースは固定サイズではなく、ASE 管理者が各ワーカー プールに使用するコンピューティング リソースのサイズを割り当てることができます。つまり、必要に応じて、ワーカー プール 1 に P4 コンピューティング リソースを割り当て、ワーカー プール 2 に P1 コンピューティング リソースを割り当てることができます。サイズの順に従う必要はありません。サイズとその料金設定の詳細については、[Azure App Service の料金][AppServicePricing]に関するページにあるドキュメントを参照してください。結果として、App Service 環境内の Web アプリおよび App Service プランに対しては、次のスケーリング オプションが残されます。

- ワーカー プールの選択
- インスタンスの数

どちらの項目も、App Service プランに対応する UI を使用して変更できます。

![][1]

### インスタンスの数のスケーリング ###

App Service 環境で Web アプリを初めて作成する場合は、フォールト トレランスを実現するために少なくとも 2 つのインスタンスまでスケーリングする必要があります。

ASE に十分な容量がある場合、この操作は非常に単純です。スケールアップするサイトが保持されている App Service プランに移動し、[スケール] を選択します。UI が開きます。ここで、目的の値までインスタンス インジケーターを動かした後、この設定を保存します。

![][2]

App Service プランが属しているワーカー プールで利用可能なコンピューティング リソースの数を超えて App Service プランをスケールアップすることはできません。より多くのコンピューティング リソースが必要な場合は、ASE 管理者に依頼して、必要な量のコンピューティング リソースをワーカー プールに割り当ててもらう必要があります。ASE を再構成する方法については、[App Service 環境の構成方法][HowtoConfigureASE]に関するページを参照してください。
 

### ワーカー プールの選択 ###

ワーカー プールを選択するには、App Service プランの UI を使用します。スケーリングする App Service プランを開き、[ワーカー プール] を選択します。App Service 環境内に構成されているすべてのワーカー プールが表示されます。構成されているワーカー プールが 1 つの場合は、1 つのプールが表示されます。App Service プランが属しているワーカー プールを変更するには、App Service プランの移動先のワーカー プールを選択します。

![][3]

これを実行する前に、App Service プランのための十分な容量があることを確認してください。ワーカー プールの一覧には、ワーカー プールの名前だけでなく、そのワーカー プールで使用可能なワーカーの数も表示されます。App Service プランを格納するための十分なインスタンスがあることを確認します。移動先のワーカー プールにより多くのコンピューティング リソースが必要な場合は、ASE 管理者に依頼してコンピューティング リソースを追加してもらう必要があります。

Web アプリを 1 つのワーカー プールから移動すると、Web アプリが再起動されます。そのため、再起動に要する時間に応じて、アプリで短時間のダウンタイムが発生する可能性があります。

## 使用の開始

App Service 環境の使用を開始するには、[App Service 環境の作成方法][HowtoCreateASE]に関するページを参照してください。

Azure App Service プラットフォームの詳細については、[Azure App Service][AzureAppService] に関するページを参照してください。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/scaleasp.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/scaleinstances.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/scalepool.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ScaleWebapp]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[CreateWebappinASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
 

<!---HONumber=August15_HO6-->