---
title: About Azure DevTest Labs | Microsoft Docs
description: Azure Virtual Machines を簡単に作成、管理、監視するために DevTest ラボを使用する方法について説明します
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1b9eed3b-c69a-4c49-a36e-f388efea6f39
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: b7cd6bb1fd0377ca1440d9c667453df922aacbd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561457"
---
# <a name="about-azure-devtest-labs"></a>Azure DevTest Labs とは
Azure DevTest Labs を使用すれば、チームの開発者は、承認を待つことなく、仮想マシン (VM) と PaaS リソースを効率的に自己管理できるようになります。

DevTest Labs では、事前に構成された基盤または Azure Resource Manager テンプレートから成るラボを作成します。 これには、環境を作成するのに使用できる必要なツールとソフトウェアがすべて備わっています。 数時間や数日ではなく、数分で、環境を作成できます。

DevTest Labs を使用することで、次のタスクを実行して、アプリケーションの最新バージョンをテストできます。

- 再利用可能なテンプレートとアーティファクトを使用することで、Windows と Linux の環境をすばやくプロビジョニングします。
- デプロイ パイプラインと DevTest ラボを簡単に統合し、オンデマンドの環境をプロビジョニングします。
- 複数のテスト エージェントをプロビジョニングしてロード テストをスケール アップし、トレーニングおよびデモの事前プロビジョニング済み環境を作成します。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>機能
DevTest Labs は、VM を使用する開発者に次の機能を提供します。

- 5 つ未満の簡単な手順に従って VM をすばやく作成します。
- チーム リーダーまたは中央 IT によって構成、許可、承認された VM ベースの精選された一覧から選択します。
- すべてのソフトウェアとツールが既にインストールされている事前作成済みのカスタム イメージから VM を作成します。 
- VM 作成時にインストールされたソフトウェアの最新のビルドと組み合わせた、本質的にはカスタム イメージである数式から VM を作成します。 
- プロビジョニング後に、VM にデプロイされた拡張機能であるアーティファクトをインストールします。
- VM の自動シャットダウンと自動開始のスケジュールを設定します。
- 作成プロセスを経由せずに、事前に作成された VM を要求します。

DevTest Labs では、PaaS 環境を使用する開発者に次の機能を提供します。

- 3 つ未満の簡単な手順に従って、Resource Manager を使用してPaaS 環境をすばやく作成します。
- チーム リーダーまたは中央 IT によって構成および承認された Resource Manager テンプレートの精選された一覧から選択します。
- ラボのコンテキスト内で Azure を探索するために、Resource Manager テンプレートを使用して空のリソース グループ (サンドボックス) を起動します。

DevTest Labs では、次のタスクを実行することによって、中央 IT が無駄を制御し、リソースのコストを最適化し、予算内に収めることもできます。  

- VM の自動シャットダウンと自動開始のスケジュールの設定。
- ユーザーが作成できる VM の数に関するポリシーの設定。
- VM のサイズとユーザーが選択可能なギャラリー イメージに関するポリシーの設定。
- ラボのコストの追跡とターゲットの設定。
- 必要なアクションを実行できるように、ラボの予測される高額のコストに関する通知の取得。

DevTest Labs を使用することで、クラウド内の環境の作成、構成、管理において次の利点が提供されます。

## <a name="cost-control-and-governance"></a>コスト管理とガバナンス
DevTest Labs により、以下のタスクを実行できることで、コスト管理が簡単になります。

- ユーザーごとまたはラボごとの VM の数など、[ラボでポリシーを設定](devtest-lab-get-started-with-lab-policies.md)します。 
- VM を[自動的にシャットダウンし、起動するポリシー](devtest-lab-set-lab-policy.md)を作成します。
- [予算](devtest-lab-configure-cost-management.md)内に収めるため、ラボ内で起動された VM と PaaS のリソースのコストを追跡します。
- 外部のリソースを起動しないように、ラボのコンテキスト内に留まるようにします。

## <a name="quickly-get-to-ready-to-test"></a>すばやくテスト準備完了状態へ
DevTest Labs を使用することで、チームがアプリケーションの開発とテストを行うために必要なものをすべて備えた、事前プロビジョニングされた環境を作成できます。 アプリケーションの最新の良好なビルドがインストールされた[環境を要求](devtest-lab-add-claimable-vm.md)し、作業を開始するだけです。 あるいは、コンテナーを使えば環境の作成が一層迅速かつ効率的になります。

## <a name="create-once-use-everywhere"></a>1 回作成すれば、どこでも使える
チーム内や組織内で PaaS [環境テンプレート](devtest-lab-create-environment-from-arm.md)と[アーティファクト](add-artifact-repository.md)のキャプチャと共有を行い、開発者とテスト向けの環境を簡単に作成できます。これらすべてをソース管理の制御のもとで行えます。

## <a name="worry-free-self-service"></a>心配無用のセルフサービス
DevTest Labs を使用すると、開発者とテスト担当者は、事前に構成された一連のリソースを使用して、すばやく簡単に [IaaS VM](devtest-lab-add-vm.md) および [PaaS リソースを作成](devtest-lab-create-environment-from-arm.md)できます。

## <a name="use-iaas-and-paas-resources"></a>IaaS および PaaS リソースを使用する 
開発者は、Resource Manager テンプレートを使用することで、Azure Service Fabric クラスター、Azure App Service の Web Apps 機能、SharePoint ファームなどの PaaS リソースを起動することもできます。 ラボで PaaS を開始するには、[パブリック環境リポジトリ](devtest-lab-configure-use-public-environments.md)からテンプレートを使用するか、[ラボを独自の Git リポジトリに接続](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories)します。 予算内に収めるために、これらのリソースのコストを追跡することもできます。

## <a name="integrate-with-your-existing-toolchain"></a>既存のツールチェーンと統合する
事前に作成されたプラグインや API を使用して、好みの[継続的インテグレーション (CI) ツール](devtest-lab-integrate-ci-cd-vsts.md)、統合開発環境 (IDE)、自動リリース パイプラインから、開発やテストの環境を直接プロビジョニングできます。 包括的なコマンド ライン ツールを使用することもできます。

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- DevTest Labs の詳細については、「[DevTest ラボの概念](devtest-lab-concepts.md)」を参照してください。
- 詳細な手順については、「[チュートリアル:Azure DevTest Labs を使用してラボを設定する](tutorial-create-custom-lab.md)」を参照してください。