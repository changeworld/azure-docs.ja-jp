---
title: App Service Environment を作成する
description: App Service Environment の作成方法について説明します。
author: madsd
ms.topic: article
ms.date: 11/15/2021
ms.author: madsd
ms.openlocfilehash: ae243842f5201b7d8f2bfa3adcb42c5146c885d2
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525027"
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

## <a name="deployment-considerations"></a>デプロイに関する考慮事項

ASE をデプロイする前に考慮する必要がある重要な点が 2 つあります。

- VIP の種類
- 展開の種類

内部と外部の 2 種類の VIP があります。 内部 VIP を使用すると、ASE 上のアプリに ASE サブネット内のアドレスで到達できます。アプリがパブリック DNS に登録されることはありません。 ポータルでの作成中に、ASE 用の Azure プライベート DNS ゾーンを作成するオプションがあります。 外部 VIP を使用すると、アプリはインターネットに接続するパブリック アドレスに配置され、パブリック DNS に登録されます。 

デプロイには、次の 3 種類があります。

- 単一ゾーン
- ゾーン冗長
- ホスト グループ

単一ゾーン ASE は、ASEv3 が使用可能なすべてのリージョンで使用できます。 単一ゾーン ASE を使用する場合、App Service プラン インスタンスの最低料金として、Windows Isolated v2 の 1 インスタンス分の料金が発生します。 インスタンスが 1 つ以上になるとすぐに、この料金はなくなります。 これは追加の料金ではありません。

ゾーン冗長 ASE では、アプリは同じリージョンの 3 つのゾーンに分散されます。 ゾーン冗長 ASE は、可用性ゾーンをサポートするリージョンによって主に制限される ASE 対応リージョンのサブセットで使用できます。 ゾーン冗長 ASE がある場合、App Service プランの最小サイズは 3 インスタンスです。 これにより、可用性ゾーンごとに 1 つのインスタンスが確保されます。 App Service プランは、一度に 1 つ以上のインスタンスにスケールアップできます。 スケーリングが 3 単位である必要はありませんが、インスタンスの総数が 3 の倍数である場合にのみ、アプリはすべての可用性ゾーンに均等に分散されます。 ゾーン冗長 ASE はインフラストラクチャが 3 倍になり、ゾーン冗長コンポーネントで作成されているため、なんらかの理由で 3 つのゾーンのうち 2 つがダウンした場合でも、ワークロードを引き続き使用できます。 システム ニーズの増加により、ゾーン冗長 ASE の最低料金は 9 インスタンスとなっています。 ASEv3 の App Service プラン インスタンスの総数が 9 個未満の場合、差額は Windows I1v2 として請求されます。 インスタンスが 9 個以上あっても、ゾーン冗長 ASE を使用するための追加料金は発生しません。 ゾーン冗長の詳細については、[リージョンと可用性ゾーン](./overview-zone-redundancy.md)に関する記事を参照してください。

ホスト グループ デプロイでは、アプリは専用ホスト グループにデプロイされます。 専用ホスト グループはゾーン冗長ではありません。 専用ホスト グループ デプロイでは、ASE を専用ハードウェアにデプロイできます。 専用ホスト グループで ASE を使用する場合、最低インスタンス料金はありませんが、ASE をプロビジョニングするときにホスト グループの料金を支払う必要があります。 さらに、プランを作成してスケールアウトするときに、割引された App Service プラン料金を支払います。App Service プランとインフラストラクチャ ロールの両方で使用される、専用ホスト デプロイで使用可能なコアの数には限りがあります。 ASE の専用ホスト デプロイでは、ASE で通常使用できるインスタンスの総数の 200 に達することはできません。 使用可能なインスタンスの総数は、App Service プラン インスタンスの総数とインフラストラクチャ ロールの負荷ベースの数の合計に関連しています。

## <a name="creating-an-ase-in-the-portal"></a>ポータルでの ASE の作成

1. ASE を作成するには、マーケットプレースで **App Service Environment v3** を検索します。

2. 基本:サブスクリプションを選択し、リソース グループを選択または作成して、ASE の名前を入力します。  仮想 IP の種類を選びます。 [Internal]\(内部\) を選んだ場合、受信 ASE アドレスは ASE サブネット内部のアドレスになります。 [External]\(外部\) を選んだ場合、受信 ASE アドレスは、インターネットに公開されるパブリック IP アドレスになります。 ASE 名は、ASE のドメインのサフィックスにも使用されます。 ASE 名が *contoso* であり、内部 VIP ASE を使用している場合、ドメインのサフィックスは *contoso.appserviceenvironment.net* になります。 ASE 名が *contoso* であり、外部 VIP ASE を使用している場合、ドメインのサフィックスは *contoso.p.azurewebsites.net* になります。 

    ![App Service Environment の作成の [基本] タブ](./media/creation/creation-basics.png)

3. [Hosting]\(ホスティング\): Host Group のデプロイについて *[Enabbled]\(有効\)* か *[Disabled]\(無効\)* を選びます。 ホスト グループ デプロイは、専用ハードウェア デプロイを選択するために使用されます。 [Enabled]/(有効/) を選んだ場合は、ASE を専用のハードウェアにデプロイします。 専用のハードウェアにデプロイする場合、ASE 作成中は専用ホスト全体に対する料金の請求を受けます。その後は App Service プランのインスタンスに対する請求だけになって料金が下がります。

    ![App Service Environment のホスティングの選択項目](./media/creation/creation-hosting.png)

4. [Networking]\(ネットワーク\): Virtual Network を選択または作成し、サブネットを選択または作成します。 内部 VIP ASE を作成する場合は、ドメイン サフィックスが ASE を指すように Azure DNS プライベート ゾーンを構成できます。 「[App Service Environment の使用][UsingASE]」の DNS のセクションで、DNS を手動で設定する詳しい方法を説明しています。

    ![App Service Environment のネットワークの選択項目](./media/creation/creation-networking.png)

5. 確認と作成: 構成が正しいことを確認し、[作成] を選択します。 ASE の作成には 2 時間近くかかる場合があります。 

ASE の作成が完了したら、アプリを作成するときにそれを場所として選択できます。 新しい ASE でアプリを作成する方法、ASE の管理方法の詳しい説明は「[App Service Environment の使用][UsingASE]」を読んでください

## <a name="dedicated-hosts"></a>専用ホスト

ASE は、通常、マルチテナント ハイパーバイザーでプロビジョニングされた VM にデプロイされます。 ハードウェアなどの専用システム上にデプロイする必要がある場合は、ASE を専用ホストにプロビジョニングできます。 専用ホストは、冗長性を確保するために対になっています。 専用ホストベースの ASE デプロイは、通常とは異なる方法で価格が決まります。 専用ホストに料金が発生し、App Service プラン インスタンスごとに別の料金が発生します。 ホスト グループへのデプロイはゾーン冗長ではありません。 専用ホストにデプロイするには、[Hosting]\(ホスティング\) タブで、ホストグループのデプロイを **[enable]\(有効化\)** に設定します。

<!--Links-->
[Intro]: ./overview.md
[UsingASE]: ./using.md
