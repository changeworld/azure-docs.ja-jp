<properties pageTitle="モバイル サービスのローカル テスト向けの IIS Express の構成" metaKeywords="Azure モバイル サービス, .NET バックエンド, IIS Express" description="テスト目的でローカル モバイル サービス プロジェクトに接続できるように IIS Express を構成する方法について説明します。" authors="glenga" title="ローカル モバイル サービスへの接続を可能にするローカル Web サーバーの構成" />

# ローカル モバイル サービスへの接続を可能にするローカル Web サーバーの構成

Azure モバイル サービスを利用すると、サポートされている .NET 言語の 1 つを使用して Visual Studio で独自のモバイル サービスを作成して、Azure に発行することができます。モバイル サービスで .NET バックエンドを使用する大きな利点の 1 つは、Azure に発行する前に、ローカル コンピューターまたは仮想マシン上でモバイル サービスの実行、テスト、デバッグが可能になることです。

エミュレーター、仮想マシン、または別のコンピューター上で実行されているクライアントを使用してモバイル サービスをローカルにテストするには、コンピューターの IP アドレスとポートに接続できるようにローカル Web サーバーとホスト コンピューターを構成する必要があります。このトピックでは、IIS Express を構成して、ローカルにホストされたモバイル サービスへの接続を可能にする方法を示します。

[WACOM.INCLUDE [mobile-services-how-to-configure-iis-express](../includes/mobile-services-how-to-configure-iis-express.md)] 


