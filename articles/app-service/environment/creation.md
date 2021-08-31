---
title: App Service Environment を作成する
description: App Service Environment の作成方法について説明します。
author: ccompy
ms.assetid: 7690d846-8da3-4692-8647-0bf5adfd862a
ms.topic: article
ms.date: 07/06/2021
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 45d2c817f579cd183337a42e252dd3e606eafefa
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2021
ms.locfileid: "113433216"
---
# <a name="create-an-app-service-environment"></a>App Service Environment を作成する
> [!NOTE]
> この記事では、Isolated v2 App Service プランと合わせて使用する App Service Environment v3 について説明します
> 


[App Service Environment (ASE)][Intro] は、ご使用の Azure 仮想ネットワーク (VNet) に導入される App Service のシングル テナント デプロイです。 ASE をデプロイするには、1 つのサブネットを使用する必要があります。 このサブネットは、ASE 以外には使用できません。 

## <a name="before-you-create-your-ase"></a>ASE を作成する前に

ASE 作成後は、次の変更はできません。

- 場所
- サブスクリプション
- Resource group
- 使用する Azure 仮想ネットワーク (VNet)
- 使用するサブネット
- サブネットのサイズ
- ASE の名前

サブネットには、ASE をスケールするときの最大サイズに対応できるだけの大きさが必要です。 作成後に変更することはできないため、最大スケールのニーズをサポートするために十分な大きさのサブネットを選択します。 推奨されるサイズは、256 のアドレスを持つ /24 です。

## <a name="creating-an-ase-in-the-portal"></a>ポータルでの ASE の作成

1. ASE を作成するには、マーケットプレースで **App Service Environment v3** を検索します。  
2. 基本:サブスクリプションを選択し、リソース グループを選択または作成して、ASE の名前を入力します。  仮想 IP の種類を選びます。 [Internal]\(内部\) を選んだ場合、受信 ASE アドレスは ASE サブネット内部のアドレスになります。 [External]\(外部\) を選んだ場合、受信 ASE アドレスは、インターネットに公開されるパブリック IP アドレスになります。 ASE 名は、ASE のドメインのサフィックスにも使用されます。 ASE 名が *contoso* であり、内部 VIP ASE を使用している場合、ドメインのサフィックスは *contoso.appserviceenvironment.net* になります。 ASE 名が *contoso* であり、外部 VIP ASE を使用している場合、ドメインのサフィックスは *contoso.p.azurewebsites.net* になります。 
![App Service Environment の作成の [基本] タブ](./media/creation/creation-basics.png)
3. [Hosting]\(ホスティング\): Host Group のデプロイについて *[Enabbled]\(有効\)* か *[Disabled]\(無効\)* を選びます。 ホスト グループ デプロイは、専用ハードウェアを選択するために使用されます。 [Enabled]/(有効/) を選んだ場合は、ASE を専用のハードウェアにデプロイします。 専用のハードウェアにデプロイする場合、ASE 作成中は専用ホスト全体に対する料金の請求を受けます。その後は App Service プランのインスタンスに対する請求だけになって料金が下がります。 
![App Service Environment のホスティングの選択項目](./media/creation/creation-hosting.png)
4. [Networking]\(ネットワーク\): Virtual Network を選択または作成し、サブネットを選択または作成します。 内部 VIP ASE を作成する場合は、ドメインのサフィックスを ASE に指定するよう、ASE Azure DNS プライベート ゾーンを設定することができます。 「[App Service Environment の使用][UsingASE]」の DNS のセクションで、DNS を手動で設定する詳しい方法を説明しています。
![App Service Environment のネットワークの選択項目](./media/creation/creation-networking.png)
5. 確認と作成: 構成が正しいことを確認し、[作成] を選択します。 ASE の作成には 2 時間近くかかる場合があります。 

ASE の作成が完了したら、アプリを作成するときにそれを場所として選択できます。 新しい ASE でアプリを作成する方法、ASE の管理方法の詳しい説明は「[App Service Environment の使用][UsingASE]」を読んでください

## <a name="dedicated-hosts"></a>専用ホスト

ASE は、通常、マルチテナント ハイパーバイザーでプロビジョニングされた VM にデプロイされます。 ハードウェアなどの専用システム上にデプロイする必要がある場合は、ASE を専用ホストにプロビジョニングできます。 専用ホストは、冗長性を確保するために対になっています。 専用ホストベースの ASE デプロイは、通常とは異なる方法で価格が決まります。 専用ホストに料金が発生し、App Service プラン インスタンスごとに別の料金が発生します。 ホスト グループへのデプロイはゾーン冗長ではありません。 専用ホストにデプロイするには、[Hosting]\(ホスティング\) タブで、ホストグループのデプロイを **[enable]\(有効化\)** に設定します。

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/alerts/alerts-log.md
