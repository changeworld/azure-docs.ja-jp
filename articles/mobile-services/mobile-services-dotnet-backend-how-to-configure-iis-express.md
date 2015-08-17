<properties 
	pageTitle="Mobile Service のローカル テストのための IIS Express の構成" 
	description="テストのためにローカル モバイル サービス プロジェクトに接続できるように IIS Express を構成する方法について説明します。" 
	authors="ggailey777" 
	manager="dwrede" 
	services="mobile-services" 
	documentationCenter="" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="glenga"/>

# ローカル モバイル サービスへの接続を可能にするローカル Web サーバーの構成 

Azure モバイル サービスを利用すると、サポートされている .NET 言語の 1 つを使用して Visual Studio で独自のモバイル サービスを作成して、Azure に発行することができます。モバイル サービスで .NET バックエンドを使用する大きな利点の 1 つは、Azure に発行する前に、ローカル コンピューターまたは仮想マシン上でモバイル サービスの実行、テスト、デバッグが可能になることです。

エミュレーター、仮想マシン、または別のコンピューター上で実行されているクライアントを使用してモバイル サービスをローカルにテストするには、コンピューターの IP アドレスとポートに接続できるようにローカル Web サーバーとホスト コンピューターを構成する必要があります。このトピックでは、IIS Express を構成して、ローカルにホストされたモバイル サービスへの接続を可能にする方法を示します。

[AZURE.INCLUDE [mobile-services-how-to-configure-iis-express](../../includes/mobile-services-how-to-configure-iis-express.md)]

 

<!---HONumber=August15_HO6-->