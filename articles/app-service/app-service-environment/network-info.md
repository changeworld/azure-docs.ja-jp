---
title: "Azure App Service Environment でのネットワークの考慮事項"
description: "ASE ネットワーク トラフィックと、ASE を使用して NSG と UDR を設定する方法について説明します"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: d19590c23da43e08ea42cd278347e01d87433a58
ms.contentlocale: ja-jp
ms.lasthandoff: 06/26/2017

---
# <a name="networking-considerations-for-an-app-service-environment"></a>App Service Environment のネットワークの考慮事項 #

## <a name="overview"></a>概要 ##

[App Service Environment][Intro] (ASE) は、ユーザーの Azure Virtual Network (VNet) 内のサブネットへの Azure App Service のデプロイです。  ASE には 2 種類のデプロイがあります。

- _外部 ASE_: ASE によってホストされているアプリをインターネット アクセス可能な IP アドレスで公開します。 詳細については、[外部 ASE の作成][MakeExternalASE]に関するページを参照してください。
- _ILB ASE_: ASE によってホストされているアプリを Azure Virtual Network (VNet) 内の IP アドレスで公開します。  内部エンドポイントは内部ロード バランサー (ILB) であるため、ILB ASE と呼ばれています。 詳細については、[ILB ASE の作成と使用][MakeILBASE]に関するページを参照してください。

ASE には現在、2 つのバージョンがあります。 ASE の最初のバージョンは ASEv1 と呼ばれ、新しいバージョンは ASEv2 と呼ばれます。 ASEv1 について詳しくは、「[App Service Environment の概要][ASEv1Intro]」を参照してください。 ASEv1 は、クラシック VNet または Resource Manager VNet にデプロイすることができます。 ASEv2 は、Resource Manager VNet にのみデプロイすることができます。

インターネットにアクセスする ASE からのすべての呼び出しは、ASE に割り当てられた VIP を経由して VNet から出ます。 この VIP のパブリック IP は、インターネットにアクセスする ASE からのすべての呼び出しの送信元 IP になります。  ASE 内のアプリが VNet 内のリソースまたは VPN を超えてリソースの呼び出しを行う場合、送信元 IP は ASE で使用されるサブネットの IP の 1 つになります。  ASE は VNet 内にあるため、追加の構成をしなくても、VNet 内のリソースにもアクセスできます。 VNet がオンプレミス ネットワークに接続されている場合、ASE またはアプリの追加の構成をしなくても、そこのリソースにもアクセスできます。

![][1] 

外部 ASE がある場合は、そのパブリック VIP も ASE アプリが HTTP/S、FTP/S、Web 配置とリモート デバッグ用に解決されるエンドポイントになります。

![][2]

ILB ASE がある場合は、ILB の IP アドレスが HTTP/S、FTP/S、Web 配置とリモート デバッグのためのエンドポイントになります。

通常のアプリのアクセス ポートは次のとおりです。

| 最初の起動時にドメインに参加しているマシンになるように VM をプロビジョニングするには、 | ファイル | To |
|----------|---------|-------------|
|  HTTP/HTTPS  | ユーザーが構成可能 |  80、443 |
|  FTP/FTPS    | ユーザーが構成可能 |  21、990、10001-10020 |
|  Visual Studio リモート デバッグ  |  ユーザーが構成可能 |  4016、4018、4020、4022 |

これは外部 ASE または ILB ASE の場合に当てはまります。 外部 ASE の場合は、これらはパブリック VIP でヒットするポートです。 ILB ASE の場合は、ILB でこれらのポートにヒットします。 ポート 443 をロックダウンすると、ポータルで公開されている一部の機能に影響する可能性があることに注意してください。 詳細については、「[ポータルの依存関係](#portaldep)」を参照してください。

## <a name="ase-dependencies"></a>ASE の依存関係 ##

ASE の着信アクセスの依存関係は次のとおりです。

| 最初の起動時にドメインに参加しているマシンになるように VM をプロビジョニングするには、 | ファイル | To |
|-----|------|----|
| 管理 | インターネット | ASE サブネット: 454、455 |
|  ASE 内部通信 | ASE サブネット: すべてのポート | ASE サブネット: すべてのポート
|  Azure Load Balancer の着信を許可 | Azure Load Balancer | 任意

着信トラフィックは、システムの監視に加え、ASE のコマンドと制御を提供します。 このトラフィックの送信元 IP は定数ではありません。 これは、ポート 454 および 455 ですべての IP からのアクセスを許可するようにネットワーク セキュリティを構成する必要があることを意味します。

発信アクセスについては、ASE は複数の外部システムに依存します。 これらのシステムの依存関係は、DNS 名で定義され、IP アドレスの固定セットにはマップされません。 つまり、ASE には ASE サブネットからすべての外部 IP へさまざまなポートを介した発信アクセスが必要です。 ASE には、次の発信の依存関係があります。

| 最初の起動時にドメインに参加しているマシンになるように VM をプロビジョニングするには、 | ファイル | To |
|-----|------|----|
| Azure Storage (Azure Storage) | ASE サブネット | table.core.windows.net、blob.core.windows.net、queue.core.windows.net、file.core.windows.net: 80、443、445 (445 は ASEv1 にのみ必要) |
| SQL Database | ASE サブネット | database.windows.net: 1433、11000-11999、14000-14999 (詳細については、[Sql Database V12 ポートの使用](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md)に関するページを参照)|
| Azure の管理 | ASE サブネット | management.core.windows.net、management.azure.com: 443 
| SSL 証明書の検証 |  ASE サブネット            |  ocsp.msocsp.com、mscrl.microsoft.com、crl.microsoft.com: 443
| Azure Active Directory        | ASE サブネット            |  インターネット: 443
| App Service の管理        | ASE サブネット            |  インターネット: 443
| Azure DNS                     | ASE サブネット            | インターネット: 53
| ASE 内部通信    | ASE サブネット: すべてのポート | ASE サブネット: すべてのポート

ASE がこれらの依存関係へのアクセスを失うと、ASE は停止します。 それがしばらく続くと、ASE は中断されます。

**顧客 DNS**

顧客が定義した DNS サーバーによって VNet が構成されている場合、テナント ワークロードはそれを使用します。 ASE は、管理目的で Azure DNS と引き続き通信する必要があります。 

VNet が VPN の反対側で顧客の DNS によって構成されている場合、ASE を含むサブネットから DNS サーバーに到達できる必要があります。

<a name="portaldep"></a>
## <a name="portal-dependencies"></a>ポータルの依存関係 ##

ASE が持つ機能の依存関係に加え、ポータルのエクスペリエンスに関連するいくつかの追加項目があります。 Azure Portal の一部の機能は、_SCM サイト_への直接アクセスに依存しています。 Azure App Service 内のすべてのアプリには、それぞれ 2 つの URL があります。 1 つ目の URL はアプリにアクセスするためのものです。 2 つ目の URL は SCM サイト (_Kudu コンソール_とも呼ばれます) にアクセスするためのものです。 SCM サイトを使用する機能の一部には、次のものがあります。

-   Web ジョブ
-   関数
-   Logstream
-   Kudu
-   拡張機能
-   プロセス エクスプローラー
-   コンソール

ILB ASE を使用している場合、SCM サイトは VNet の外側からインターネットでアクセスできないため、これは非常に困難になります。 このため、ILB ASE でアプリがホストされている場合には、SCM サイトのアクセスに依存するこれらの機能は、Azure Portal では淡色表示されています。

SCM サイトに依存するこれらの機能の多くは、Kudu コンソールでも直接使用できます。 ポータルを使用せずに、直接接続できます。 アプリが ILB ASE でホストされている場合は、自身の公開資格情報を使用してログインする必要があります。 ILB ASE でホストされているアプリの SCM サイトにアクセスする URL には、次の形式があります。 

```
<appname>.scm.<domain name the ILB ASE was created with> 
```

そのため、ILB ASE がドメイン名 *contoso.net* で、アプリ名が *testapp* の場合、アプリには *testapp.contoso.net* で到達でき、それに付随する SCM サイトには *testapp.scm.contoso.net* で到達できます。

## <a name="ase-ip-addresses"></a>ASE IP アドレス ##

ASE では数多くの IP アドレスに対応しています。 次に例を示します。

- 公開着信 IP アドレス: 外部 ASE でのアプリのトラフィックと外部 ASE と ILB ASE の両方で管理トラフィックに使用。
- 発信パブリック IP: VNet から離れる ASE からの発信接続の "発信元" IP として使用 (下にルーティングしない VPN)。
- ILB IP アドレス: ILB ASE を使用している場合。
- IP ベースの SSL アドレスが割り当てられたアプリ: 外部 ASE で IP ベースの SSL アドレスが構成されている場合にのみ可能。

これらすべての IP アドレスは、ASE UI から Azure Portal の ASEv2 で簡単に確認できます。 ILB ASE がある場合は、ILB の IP がリストされます。

![][3]

**IP アドレスが割り当てられたアプリ**

外部 ASE では、個々のアプリに IP アドレスを割り当てることができます。 これは ILB ASE では行えません。 アプリに独自の IP アドレスを設定する詳細については、「[既存のカスタム SSL 証明書の Azure Web Apps へのバインド](../../app-service-web/app-service-web-tutorial-custom-ssl.md)」を参照してください。

アプリに独自の IP ベースの SSL アドレスがある場合、ASE はその IP アドレスにマップするために 2 つのポートを予約します。 1 つは HTTP トラフィック用のポートで、もう 1 つは HTTPS 用のポートです。 これらのポートは、ASE UI の [IP アドレス] セクションに表示されます。 トラフィックで VIP からこれらのポートに到達できる必要があります。そうでない場合、アプリにアクセスできなくなります。 これは、ネットワーク セキュリティ グループを構成する際に留意すべき重要なことです。

## <a name="network-security-groups"></a>ネットワーク セキュリティ グループ ##

[ネットワーク セキュリティ グループ][NSGs] (NSG) は、VNet 内でネットワーク アクセスを制御する機能を提供します。 ポータルを使用するときに、最も低い優先順位にすべてを拒否する暗黙の拒否ルールがあるため、自分が構築するものが許可ルールになります。

ASE では、ASE 自体をホストするために使用する VM にアクセスすることはできません。 これらは、Microsoft 管理対象サブスクリプションにあります。 ASE 上のアプリへのアクセスを制限する場合は、ASE サブネットに NSG を設定する必要があります。 その際には、ASE の依存関係に特に注意する必要があります。 任意の依存関係をブロックすると、ASE が停止します。

NSG は、Azure Portal または PowerShell を使用して構成できます。 わかりやすくするため、ここでは Azure Portal を使用した情報を示します。 ポータルで NSG を **[ネットワーク]** の下の最上位のリソースとして作成して管理します。

着信と発信の要件を考慮すると、NSG は次のようなものになるはずです。 この例では、VNet アドレス範囲は _192.168.250.0/16_ で、ASE があるサブネットは _192.168.251.128/25_ になっています。

この例の先頭には、ASE が機能するための最初の 2 つの着信要件があります。 これらは ASE 管理を有効にし、ASE に自身との通信を許可します。 他のエントリは、すべて構成可能なテナントで、ASE でホストされているアプリケーションへのネットワーク アクセスを制御できます。   

![][4]

VNet 内の IP が ASE サブネットと対話できるようにする既定のルールに加え、ロード バランサー (別名パブリック VIP) を ASE と通信できるようにする既定のルールもあります。  既定のルールを確認するには、*[追加]* アイコンの隣の *[既定の規則]* をクリックします。 NSG ルールを表示した後、他のすべてのルールを拒否すると、VIP と ASE 間のトラフィックが妨げられます。 VNet 内からのトラフィックを防止する場合は、任意の宛先とポート範囲が \* の AzureLoadBalancer と同じソースを持つ着信を許可する独自のルールを追加する必要があります。  NSG ルールは ASE サブネットだけに適用されているため、宛先で特定する必要はありません。

アプリに IP アドレスを割り当てた場合は、それに使用されるポートを開いたままにしておく必要もあります。 使用されるポートは、**[App Service Environment]** > **[IP アドレス]** の UI で確認できます。  

次に示されている送信ルールに表示されている最後の項目を除くすべてのものが必要です。 これらは、このドキュメントで前述した ASE 依存関係へのネットワーク アクセスを有効にします。 これらのいずれかをブロックすると、ASE が停止します。 リストの最後の項目は、ASE が VNet 内の他のリソースと通信できるようにします。

![][5]

NSG を定義したら、それらを ASE があるサブネットに割り当てる必要があります。 ASE VNet またはサブネットを覚えていない場合は、ASE 管理ポータルから確認できます。 NSG をサブネットに割り当てるには、サブネット UI に移動して、NSG を選択します。

## <a name="routes"></a>ルート ##

ルートが問題となるのは、通常、ExpressRoute を使用して VNet を構成するときです。 Azure VNet には、次の 3 種類の ルートがあります。

-   システム ルート
-   BGP のルート
-   ユーザー定義ルート (UDR)

BGP ルートは、システム ルートをオーバーライドします。 UDR は、BGP ルートをオーバーライドします。 Azure Virtual Network のルートの詳細については、「[ユーザー定義のルートと IP 転送][UDRs]」を参照してください。

ASE がシステムを管理するために使用する SQL Database には、ホワイトリストがあり、ASE パブリック VIP から発信元への通信が必要です。 着信管理要求への応答が ExpressRoute 送信されると、応答アドレスは TCP 通信を中断する宛先とは異なります。

これはつまり、VNet が ExpressRoute で構成されている間に ASE を機能させておくために、最も簡単な方法は次のことだということです。

-   _0.0.0.0/0_ をアドバタイズするように ExpressRoute を構成します。これは既定でオンプレミスのすべての発信トラフィックを強制的にトンネリングします。
-   UDR を作成し、それをアドレス プレフィックスに _0.0.0.0/0_ と次ホップの種類に_インターネット_を指定して、App Service Environment が含まれるサブネットに適用します。

これら 2 つの変更を行うと、ASE サブネットから発信されるインターネット宛てのトラフィックは、ExpressRoute に強制的に送信されず、ASE は動作できます。 

> [!IMPORTANT]
> UDR に定義されているルートは、ExpressRoute 構成でアドバタイズされたどのルートよりも優先されるように、詳細にする必要があります。 前の例では、0.0.0.0/0 の広域なアドレス範囲を使用しているので、より詳細なアドレス範囲を使用するルート アドバタイズで誤って上書きされる可能性があります。
>

App Service Environment は、パブリック ピアリング パスからプライベート ピアリング パスにルートをクロスアドバタイズした ExpressRoute 構成ではサポートされません。 パブリック ピアリングが構成された ExpressRoute 構成は、大規模な Microsoft Azure の IP アドレス範囲について Microsoft からルート アドバタイズを受信します。 これらのアドレス範囲がプライベート ピアリング パスでクロスアドバタイズされている場合、App Service Environment のサブネットからのすべての送信ネットワーク パケットは、顧客のオンプレミス ネットワーク インフラストラクチャに強制的にトンネリングされます。 このネットワーク フローは App Service Environment では現在サポートされていません。 この問題を解決するには、パブリック ピアリング パスからプライベート ピアリング パスへのルートのクロスアドバタイズを停止します。

説明に従って UDR を作成するには、次の手順を実行します。

1.  Azure Portal > **[ネットワーク]** > **[ルート テーブル]** の順に移動します。
2.  VNet と同じ領域に、新しいルート テーブルを作成します。
3.  ルート テーブル UI で、**[ルート]** > **[追加]** を選択します。
4.  **[次ホップの種類]** を **[インターネット]** に、**[アドレス プレフィックス]** を _[0.0.0.0/0]_ に設定し、**[保存]** をクリックします。

次のような結果が表示されます。

![][6]

新しいルート テーブルを作成したら、ASE を含むサブネットに移動します。 ポータルで取得するリストからルート テーブルを選択します。 変更を保存すると、NSG とルートがサブネットとともに表示されます。

![][7]

### <a name="deploying-into-existing-azure-virtual-networks-that-are-integrated-with-expressroute"></a>ExpressRoute と統合されている既存の Azure Virtual Network にデプロイする ###

ExpressRoute と既に統合されている VNet に ASE をデプロイする場合は、ASE をデプロイするサブネットを事前に構成してから、Resource Manager テンプレートを使用してデプロイする必要があります。 VNet で ExpressRoute が既に構成されている ASE を作成するには、次の手順を実行します。

- ASE をホストするサブネットを作成します。

    > [!NOTE]
    > サブネットには ASE 以外のものを入れることはできません。アドレス空間は、後から変更できないため、将来の成長に対応できるように、十分な大きさを選択します。 推奨されるサイズは、128 のアドレスを持つ `/25` です。
- 前述のように UDR (ルート テーブル) を作成し、サブネットでそれを設定します。
- [ARM テンプレートを使用して ASE を作成する][MakeASEfromTemplate]説明に従って、Resource Manager テンプレートを使用して、ASE を作成します。

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png

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

