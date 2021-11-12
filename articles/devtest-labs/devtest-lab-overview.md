---
title: Azure DevTest Labs とは
description: Azure Virtual Machines を簡単に作成、管理、監視するために DevTest ラボを使用する方法について説明します
ms.topic: overview
ms.date: 10/20/2021
ms.openlocfilehash: f521b1fcd886d56387c9b6c44451d1858f95d62f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229059"
---
# <a name="what-is-azure-devtest-labs"></a>Azure DevTest Labs とは
Azure DevTest Labs は、開発者が、承認を待つことなく仮想マシン (VM) とサービスとしてのプラットフォーム (PaaS) リソースを効率的に自己管理できるようにするサービスです。 DevTest Labs では、事前に構成された基盤または Azure Resource Manager テンプレートから成るラボを作成します。 これらのラボには、環境を作成するのに使用できる必要なツールとソフトウェアがすべて備わっています。

DevTest Labs を使用することで、次のタスクを実行して、アプリケーションの最新バージョンをテストできます。

- 再利用可能なテンプレートとアーティファクトを使用することで、Windows と Linux の環境をすばやく作成します。
- デプロイ パイプラインと DevTest Labs を簡単に統合し、オンデマンドの環境を作成します。
- トレーニングとデモ用に複数のテスト エージェントと事前に準備された環境を作成しロード テストをスケール アップします。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="cost-control-and-governance"></a>コスト管理とガバナンス
DevTest Labs により、以下のタスクを実行できることで、コスト管理が簡単になります。

- ユーザーごとまたはラボごとの VM の数など、[ラボでポリシーを設定](devtest-lab-set-lab-policy.md)します。 
- VM を[自動的にシャットダウンし、起動するポリシー](devtest-lab-set-lab-policy.md)を作成します。
- [予算](devtest-lab-configure-cost-management.md)内に収めるため、ラボ内で起動された VM と PaaS のリソースのコストを追跡します。 必要なアクションを実行できるように、ラボの予測される高額のコストに関する通知を受け取ります。
- 外部のリソースを起動しないように、ラボのコンテキスト内に留まるようにします。

## <a name="quickly-get-to-ready-to-test"></a>すばやくテスト準備完了状態へ
DevTest Labs を使用すると、事前にプロビジョニングされた環境を作成して、アプリケーションを開発およびテストすることができます。 アプリケーションの最後の適切なビルドの[環境を要求](devtest-lab-add-claimable-vm.md)し、作業を開始するだけです。 あるいは、コンテナーを使えば環境の作成が一層迅速かつ効率的になります。

## <a name="create-once-use-everywhere"></a>1 回作成すれば、どこでも使える
チーム内や組織内で PaaS [環境テンプレート](devtest-lab-create-environment-from-arm.md)と[アーティファクト](add-artifact-repository.md)のキャプチャと共有を行い、開発者とテスト向けの環境を簡単に作成できます。これらすべてをソース管理の制御のもとで行えます。

## <a name="worry-free-self-service"></a>心配無用のセルフサービス
DevTest Labs を使用すると、開発者とテスト担当者は、事前に構成された一連のリソースを使用して、すばやく簡単に [IaaS VM](devtest-lab-add-vm.md) および [PaaS リソースを作成](devtest-lab-create-environment-from-arm.md)できます。

## <a name="use-iaas-and-paas-resources"></a>IaaS および PaaS リソースを使用する 
Resource Manager テンプレートを使用して、Azure Service Fabric クラスターや SharePoint ファームなどのリソースをスピン アップします。 テンプレートは[パブリック環境リポジトリ](devtest-lab-configure-use-public-environments.md)から取得するか、[ラボをお使いの Git リポジトリに接続](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories)します。 ラボのコンテキスト内で Azure を探索するために、Resource Manager テンプレートを使用して空のリソース グループ (サンドボックス) をスピン アップすることもできます。

## <a name="integrate-with-your-existing-toolchain"></a>既存のツールチェーンと統合する
事前に作成されたプラグインや API を使用して、好みの[継続的インテグレーション (CI) ツール](devtest-lab-integrate-ci-cd.md)、統合開発環境 (IDE)、自動リリース パイプラインから、Dev/Test 環境を直接作成できます。 包括的なコマンド ライン ツールを使用することもできます。

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- DevTest Labs の詳細については、「[DevTest ラボの概念](devtest-lab-concepts.md)」を参照してください。
- 具体的な手順を示すチュートリアルについては、「[チュートリアル: Azure DevTest Labs を使用してラボを設定する](tutorial-create-custom-lab.md)」を参照してください。
