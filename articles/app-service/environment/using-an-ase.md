---
title: App Service Environment の使用および管理
description: Azure App Service Environment でアプリを作成、発行、スケーリングする方法。 1 つのドキュメントで共通のタスクを見つけます。
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 01/01/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7be1676c8949cd30d5e1fe93a73afd75a5a9b67f
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565670"
---
# <a name="use-an-app-service-environment"></a>App Service Environment の使用 #

App Service Environment (ASE) は、ユーザーの Azure 仮想ネットワーク内のサブネットに Azure App Service をデプロイしたものです。 ASE は次の要素で構成されます。

- **フロントエンド**:フロントエンドは、App Service Environment で HTTP または HTTPS が終了する場所です。
- **ワーカー**:アプリのホストとなるリソースです。
- **データベース**:環境を定義する情報が格納されます。
- **ストレージ**: ユーザーによって発行されたアプリのホストとして使用されます。

アプリ アクセスに対して外部または内部 VIP を使用して ASE をデプロイできます。 外部 VIP を使用したデプロイは、外部 ASE と呼ばれます。 内部 VIP を使用したデプロイは、内部ロード バランサー (ILB) を使用するので、ILB ASE と呼ばれます。 ILB ASE の詳細については、「[App Service Environment で内部ロード バランサーを作成して使用する][MakeILBASE]」を参照してください。

## <a name="create-an-app-in-an-ase"></a>ASE 内にアプリを作成する ##

ASE でアプリを作成する方法は通常の作成方法とほとんど同じですが、いくつか違いがあります。 新しい App Service プラン (ASP) を作成する場合は、次のようになります。

- アプリのデプロイ先の場所として地理的な場所ではなく ASE を選択します。
- ASE で作成する App Service プランはすべて、Isolated の価格レベルでのみ使用できます。

ASE をご利用でない場合は、「[外部 App Service Environment の作成][MakeExternalASE]」の手順に従って作成できます。

ASE 内にアプリを作成するには:

1. **[リソースの作成]**  >  **[Web + モバイル]**  >  **[Web アプリ]** を選択します。

2. アプリの名前を入力します。 ASE 内で既に App Service プランが選択されている場合、アプリのドメイン名に、ASE のドメイン名が反映されます。

    ![アプリ名の選択][1]

1. サブスクリプションを選択します。

1. 新しいリソース グループの名前を指定するか、 **[既存のものを使用]** を選択して、ボックスの一覧からいずれかを選択します。

1. OS を選択します。 

1. ASE で既存の App Service プランを選択するか、次の手順で新しく作成します。

    a. Azure portal の左側のメニューから **[リソースの作成] > [Web アプリ]** を選択します。

    b. サブスクリプションを選択します。
    
    c. リソース グループを選択または作成します。
    
    d. お使いの Web アプリの名前を指定します。
    
    e. [コード] または [Docker コンテナー] を選択します。
    
    f. ランタイム スタックを選択します。
    
    g. [Linux] または [Windows] を選択します。 
    
    h. **[リージョン]** ドロップダウン リストから該当する ASE を選択します。 
    
    i. 新しい App Service プランを選択または作成します。 新しい App Service プランを作成する場合は、適切な **Isolated** SKU サイズを選択します。
    
    ![[分離] 価格レベル][2]

    > [!NOTE]
    > Linux アプリと Windows アプリを同じ App Service プランに追加することはできませんが、同じ App Service 環境に追加することはできます。 
    >

2. **[Review + create]\(確認と作成\)** を選択し、情報が正しい場合は **[作成]** を選択します。

## <a name="how-scale-works"></a>スケールのしくみ ##

すべての App Service アプリは、App Service プランで実行されます。 App Service 環境に App Service プランが存在し、App Service プランにアプリが存在します。 アプリをスケールするときは、App Service プランをスケールすることになります。そうすることによって同じプラン内のすべてのアプリがスケールされます。

App Service プランをスケールすると、必要なインフラストラクチャが自動的に追加されます。 インフラストラクチャが追加されるまでの間、スケール操作に時間差が生じます。 複数のスケール操作を順番に実行すると、最初のインフラストラクチャ スケール要求が処理され、他のものはキューに入れられます。 最初のスケール操作が完了すると、他のインフラストラクチャ要求はすべて一緒に動作します。 インフラストラクチャを追加すると、App Service プランが適切に割り当てられます。 新しい App Service プランを作成すること自体が、追加のハードウェアを要求するスケール操作になります。 

マルチテナントの App Service では、スケーリングが即座に行われます。それへの対応としてすぐに利用できるリソースのプールが存在するためです。 ASE にはそのようなバッファーが存在せず、リソースは必要に応じて割り当てられます。

ASE では、App Service プランを最大 100 インスタンスまでスケールアップできます。 ASE には、ASE 内のすべての App Service プランにわたって合計で最大 201 のインスタンスを設定できます。 

## <a name="ip-addresses"></a>IP アドレス ##

App Service には、アプリに専用の IP アドレスを割り当てる機能があります。 アプリに専用の IP を割り当てる機能は、[既存のカスタム SSL 証明書の Azure App Service へのバインド][ConfigureSSL]に関する記事で説明されているように、IP ベースの SSL を構成した後に利用可能になります。 ILB ASE では、IP ベースの SSL に使用する IP アドレスをさらに追加することはできません。

外部 ASE では、マルチテナントの App Service の場合と同じ方法で、アプリに IP ベースの SSL を構成できます。 ASE には、IP アドレスが 30 個を上限として、常に予備のアドレスが 1 つ存在します。 すぐに利用できる IP アドレスを常時確保するために、アドレスを 1 つ使用すると、そのたびに、別のアドレスが追加されます。 別の IP アドレスを割り当てるまでに、どうしても時間差が発生するので、IP アドレスを立て続けに追加することはできません。

## <a name="front-end-scaling"></a>フロント エンドのスケーリング ##

App Service プランをスケールアウトすると、それをサポートする worker が自動的に追加されます。 各 ASE は 2 つのフロント エンドで作成されます。 フロント エンドは、合計 15 の App Service プラン インスタンスごとに 1 つのフロント エンドの割合で自動的にスケールアウトされます。 それぞれ 5 つのインスタンスを持つ App Service プランが 3 つある場合、合計 15 のインスタンスと 3 つのフロント エンドになります。 合計 30 インスタンスまでスケーリングすると、4 つのフロント エンドになる、というような具合です。 

既定で割り当てられるフロント エンドの数は、中程度の負荷に適しています。 この比率は、5 つのインスタンスごとに 1 つのフロント エンドまで低く変更できます。 フロント エンドのサイズを変更することもできます。 既定では、単一コアです。 代わりに、ポータルでフロント エンドのサイズを 2 つまたは 4 つのコア サイズに変更できます。 この比率やフロント エンドのサイズの変更には料金がかかります。 詳細については、「[App Service の価格][Pricing]」を参照してください。 ASE の負荷容量を改善する場合は、まず 2 つのコア フロント エンドにスケーリングしてからスケール率を調整することで、さらに改善できます。 フロント エンドのコア サイズの変更は、ASE のアップグレードが発生するため、通常の業務時間外に実行する必要があります。

フロント エンド リソースは、ASE の HTTP/HTTPS エンドポイントです。 既定のフロント エンド構成では、各フロント エンドのメモリ使用量が常時 60% 程度となります。 フロントエンドをスケーリングする主な理由は CPU で、これは主に HTTPS トラフィックによって決まります。

## <a name="app-access"></a>アプリのアクセス ##

外部 ASE では、アプリの作成に使用されるドメイン サフィックスは *.&lt;asename&gt;.p.azurewebsites.net* です。 実際の ASE の名前が _external-ase_ で、その ASE でホストするアプリの名前が _contoso_ である場合、それには次の URL でアクセスすることになります。

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

外部 ASE の作成方法について詳しくは、[App Service Environment の作成][MakeExternalASE]に関する記事を参照してください

ILB ASE では、アプリの作成に使用されるドメイン サフィックスは *.&lt;asename&gt;.appserviceenvironment.net* です。 実際の ASE の名前が _ilb-ase_ で、その ASE でホストするアプリの名前が _contoso_ である場合、それには次の URL でアクセスすることになります。

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

ILB ASE の作成方法について詳しくは、「[App Service Environment で内部ロード バランサーを作成して使用する][MakeILBASE]」を参照してください。 

scm URL は、Kudu コンソールにアクセスしたり、Web デプロイを使用してアプリを発行したりするために使用されます。 Kudu コンソールの詳細については、「[Azure App Service の Kudu コンソール][Kudu]」を参照してください。 Kudu コンソールを使用すると、デバッグやファイルのアップロード、ファイルの編集など、さまざまな作業を Web UI で行うことができます。

## <a name="publishing"></a>発行 ##

マルチテナントの App Service と同様、ASE では次の発行方法を利用できます。

- Web デプロイ。
- FTP。
- 継続的インテグレーション。
- Kudu コンソールでのドラッグ アンド ドロップ。
- IDE (Visual Studio、Eclipse、Intellij IDEA など)。

外部 ASE では、これらの発行オプションの動作はすべて同じです。 詳細については、[Azure App Service へのデプロイ][AppDeploy]に関するページを参照してください。 

発行方法に大きな違いがあるのは ILB ASE です。 ILB ASE では、発行エンドポイントは ILB を介してしか利用できません。 ILB は、仮想ネットワークの ASE サブネット内のプライベート IP 上に存在します。 ILB へのネットワーク アクセスができなければ、その ASE にアプリを発行することができません。 「[App Service Environment で内部ロード バランサーを作成して使用する][MakeILBASE]」で述べられているように、システム内にアプリ用の DNS を構成する必要があります。 SCM のエンドポイントも対象となります。 これらが適切に定義されてないと発行できません。 ご利用の IDE から直接発行するためには、ILB へのネットワーク アクセスが IDE にも必要です。

既定では、インターネットベースの CI システム (GitHub、Azure DevOps など) は ILB ASE では動作しません。発行エンドポイントにインターネットでアクセスすることができないためです。 ILB ASE を含む VNet にセルフホステッド リリース エージェントをインストールすることで、Azure DevOps から ILB ASE への発行を有効にすることができます。 プル モデルを使用した CI システム (Dropbox など) を使用することもできます。

ILB ASE 内のアプリには、その ILB ASE の作成時に使用されたドメインが、発行エンドポイントとして使用されます。 これはアプリの発行プロファイルのほか、アプリのポータル ブレード ( **[概要]**  >  **[要点]** 、 **[プロパティ]** など) で確認することができます。 

## <a name="storage"></a>ストレージ

ASE には、ASE 内のすべてのアプリ用に 1 TB のストレージがあります。 Isolated SKU App Service プランには、既定で 250 GB の制限があります。 5 つ以上の App Service プランがある場合は、ASE の上限である 1 TB を超えないように注意する必要があります。 1 つの App Service プランで 250 GB の制限を超える必要がある場合は、サポートに問い合わせて、App Service プランの制限を最大 1 TB に調整してください。 プランの制限を調整しても、ASE 内のすべての App Service プランで 1 TB の制限が適用されます。 

## <a name="logging"></a>ログ記録 ##

ASE を Azure Monitor と統合して、ASE に関するログを Storage、イベント ハブ、または Log Analytics に送信できます。 現在ログに記録される項目は次のとおりです。

| 状況 | Message |
|---------|----------|
| ASE is unhealthy (ASE が異常です) | The specified ASE is unhealthy due to an invalid virtual network configuration. (無効な仮想ネットワーク構成が原因で、指定された ASE が異常です。) The ASE will be suspended if the unhealthy state continues. (異常な状態が続くと、ASE は中断されます。) Ensure the guidelines defined here are followed: https://docs.microsoft.com/azure/app-service/environment/network-info (ここで定義されているガイドラインに従っていることを確認してください: https://docs.microsoft.com/azure/app-service/environment/network-info ) |
| ASE subnet is almost out of space (ASE サブネットの領域が不足しています) | The specified ASE is in a subnet that is almost out of space. (指定された ASE は、空き領域がほとんどないサブネット内にあります。) There are {0} remaining addresses. (残りのアドレスは {0} 個です。) Once these addresses are exhausted, the ASE will not be able to scale (これらのアドレスが枯渇すると、ASE はスケーリングできなくなります)  |
| ASE is approaching total instance limit (ASE で、インスタンスの合計数の上限に近づいています) | The specified ASE is approaching the total instance limit of the ASE. (指定された ASE で、ASE のインスタンスの合計数の上限に近づいています) It currently contains {0} App Service Plan instances of a maximum 201 instances. (現在、最大インスタンス数 201 のうち、{0} 個の App Service プラン インスタンスが含まれています。) |
| ASE is unable to reach a dependency (ASE は依存関係に到達できません) | The specified ASE is not able to reach {0}. (指定された ASE は {0} に到達できません。)  Ensure the guidelines defined here are followed: https://docs.microsoft.com/azure/app-service/environment/network-info (ここで定義されているガイドラインに従っていることを確認してください: https://docs.microsoft.com/azure/app-service/environment/network-info ) |
| ASE is suspended (ASE は中断されています) | The specified ASE is suspended. (指定された ASE は中断されています。) The ASE suspension may be due to an account shortfall or an invalid virtual network configuration. (ASE の中断は、アカウントの不足または無効な仮想ネットワーク構成が原因である可能性があります。) Resolve the root cause and resume the ASE to continue serving traffic (根本原因を解決し、ASE を再開してトラフィックの処理を続行してください) |
| ASE upgrade has started (ASE のアップグレードが開始されました) | A platform upgrade to the specified ASE has begun. (指定された ASE へのプラットフォームのアップグレードが開始されました。) Expect delays in scaling operations (スケーリング操作で遅延が発生することが予想されます) |
| ASE upgrade has completed (ASE のアップグレードが完了しました) | A platform upgrade to the specified ASE has finished (指定された ASE へのプラットフォームのアップグレードが完了しました) |
| Scale operations have started (スケール操作が開始されました) | An App Service plan ({0}) has begun scaling. (App Service プラン ({0}) でスケーリングが開始されました。) Desired state: (必要な状態:){1} I{2} workers ({1} I{2} worker) 
| Scale operations have completed (スケール操作が完了しました) | An App Service plan ({0}) has finished scaling. (App Service プラン ({0}) でスケーリングが完了しました。) Current state: (現在の状態:){1} I{2} workers ({1} I{2} worker) |
| Scale operations have failed (スケール操作が失敗しました) | An App Service plan ({0}) has failed to scale. (App Service プラン ({0}) でスケーリングに失敗しました。) Current state: (現在の状態:){1} I{2} workers ({1} I{2} worker) |

ASE でログ記録を有効にするには、次のようにします。 

1. ポータルで [診断設定] に移動します。  
1. [診断設定を追加する] を選択します
1. ログ統合の名前を指定します
1. 目的のログ出力先を確認して構成します。 
1. AppServiceEnvironmentPlatformLogs を確認します

![ASE 診断ログの設定][4]

Log Analytics と統合している場合は、ASE ポータルから [ログ] を選択し、AppServiceEnvironmentPlatformLogs に対するクエリを作成することでログを表示できます。 

## <a name="upgrade-preference"></a>アップグレードの優先順位 ##

複数の ASE を使用している場合は、一部の ASE を他のものよりも先にアップグレードする必要が生じることがあります。 ASE HostingEnvironment Resource Manager オブジェクト内で、UpgradePreference の値を設定できます。 UpgradePreference の設定は、テンプレート、ARMClient、または https://resources.azure.com を使用して構成できます。  次の 3 つの値を選択できます。

* None - [None] が既定値で、Azure が ASE を特定のバッチでアップグレードしないことを意味します。
* Early - [Early] は、App Service アップグレードの前半で ASE がアップグレードされることを意味します。
* Late - [Late] は、App Service アップグレードの後半で ASE がアップグレードされることを意味します。

https://resources.azure.com を使用している場合は、次の方法で upgradePreferences の値を設定できます。

1. resources.azure.com にアクセスし、お使いの Azure アカウントでサインインします
1. サブスクリプション\/\[サブスクリプション名\]\/resourceGroups\/\[リソースグループ名\]\/プロバイダー\/Microsoft.Web\/hostingEnvironments\/\[ASE 名\]と移動します
1. 上部にある [読み取り/書き込み] を選択します
1. [編集] を選択します。
1. upgradePreference の値を、3 つの選択肢から任意のものに変更します。
1. [Patch]\(パッチ\) を選択します

![リソース azure com の表示][5]

upgradePreferences 機能は、複数の ASE がある場合に非常に便利です。"Early" アップグレードの ASE が "Late" の ASE よりも先にアップグレードされるためです。 複数の ASE がある場合は、dev/test の ASE を "Early" に、実稼働の ASE を "Late" に設定します。

## <a name="pricing"></a>価格 ##

**Isolated** という価格 SKU は、ASE でのみ使用されます。 ASE でホストされるすべての App Service プランは、Isolated 価格 SKU に属します。 Isolated App Service プランのリージョンは地域によって異なる場合があります。 

App Service プランの料金に加え、ASE そのものの固定料金がかかります。 固定料金は ASE のサイズによって変わることはありません。App Service プラン インスタンス 15 個ごとに追加フロント エンド 1 という既定スケール率で、ASE インフラストラクチャの料金が発生します。  

15 個の App Service プラン インスタンスごとに 1 フロント エンドという既定スケール率が十分でない場合は、フロント エンドが追加される比率またはフロント エンドのサイズを調整できます。  この比率またはサイズを調整した場合は、既定では追加されないフロント エンド コアの料金が発生します。  

たとえば、スケール率を調整して 10 にした場合、App Service プラン内の 10 インスタンスにつきフロント エンドが 1 つ追加されます。 固定料金で対応されるスケール率は、15 インスタンスにつき 1 フロント エンドです。 スケール率が 10 の場合、App Service プランの 10 インスタンスに対して追加される 3 つ目のフロント エンドの料金がかかります。 インスタンス数が 15 になっても、フロント エンドは自動的に追加されるので追加料金は必要ありません。

フロント エンドのサイズを 2 コアに調整して、比率を調整しなかった場合、追加のコアの料金が発生します。  ASE は 2 つのフロント エンドで作成されています。このため、自動スケーリングしきい値を下回っていても、フロント エンドのサイズを 2 コアに拡張した場合は、追加の 2 つのコアの料金を支払うことになります。

詳細については、「[App Service の価格][Pricing]」を参照してください。

## <a name="delete-an-ase"></a>ASE の削除 ##

ASE を削除するには、次の手順に従います。 

1. **[App Service Environment]** ブレードの上部にある **[削除]** を使用します。 

1. ASE の名前を入力して削除を確定します。 ASE を削除すると、その内部のコンテンツもすべて削除されます。 

    ![ASE の削除][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png
[4]: ./media/using_an_app_service_environment/usingase-logsetup.png
[4]: ./media/using_an_app_service_environment/usingase-logs.png
[5]: ./media/using_an_app_service_environment/usingase-upgradepref.png


<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
