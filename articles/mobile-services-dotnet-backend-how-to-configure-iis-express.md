<properties pageTitle="Configure IIS Express for local testing of Mobile Service" metaKeywords="Azure Mobile Services, .NET Backend, IIS Express" description="Learn how to configure IIS Express to allow connections to a local mobile service project for testing." authors="glenga" title="Configure the local web server to allow connections to a local mobile service" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# ローカル モバイル サービスへの接続を可能にするローカル Web サーバーの構成

Azure モバイル サービスを利用すると、サポートされている .NET 言語の 1 つを使用して Visual Studio で独自のモバイル サービスを作成して、Azure に発行することができます。モバイル サービスで .NET バックエンドを使用する大きな利点の 1 つは、Azure に発行する前に、ローカル コンピューターまたは仮想マシン上でモバイル サービスの実行、テスト、デバッグが可能になることです。

エミュレーター、仮想マシン、または別のコンピューター上で実行されているクライアントを使用してモバイル サービスをローカルにテストするには、コンピューターの IP アドレスとポートに接続できるようにローカル Web サーバーとホスト コンピューターを構成する必要があります。このトピックでは、IIS Express を構成して、ローカルにホストされたモバイル サービスへの接続を可能にする方法を示します。

[WACOM.INCLUDE [mobile-services-how-to-configure-iis-express](../includes/mobile-services-how-to-configure-iis-express.md)]

