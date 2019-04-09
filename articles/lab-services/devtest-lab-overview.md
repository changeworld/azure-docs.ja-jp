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
ms.openlocfilehash: 599b4325e0eb9aa7f6ccca2626c6d4b14f38149d
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339331"
---
# <a name="about-azure-devtest-labs"></a>Azure DevTest Labs とは
Azure DevTest Labs とは、チームの開発者が、必要なツールへの定期的な承認を待つことなく、開発、テスト、トレーニング、およびデモなどに必要な仮想マシンや PaaS リソースを効率的にセルフ サービスできるようにするサービスです。 

ラボは既に、開発者が環境を作成するために使用できる必要なすべてのツールとソフトウェアを含む事前構成済みのベースや Resource Manager テンプレートで構成されています。 開発者は、数時間や数日ではなく、数分で、環境を作成できます。 

DevTest Labs を使用して、次のタスクを実行して、アプリケーションの最新バージョンをテストできます。

- 再利用可能なテンプレートとアーティファクトを使用して、Windows と Linux の環境をすばやくプロビジョニングします
- デプロイ パイプラインと DevTest Labs を簡単に統合し、オンデマンドの環境をプロビジョニングします
- 複数のテスト エージェントをプロビジョニングしてロード テストをスケール アップし、トレーニングおよびデモの事前プロビジョニング済み環境を作成します。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]
> 
> 

## <a name="capabilities"></a>機能
DevTest Labs は、仮想マシンを使用する開発者に次の機能を提供します。

- 5 つ未満の簡単な手順に従って、仮想マシンをすばやく作成します。
- チーム リーダーまたは中央 IT によって構成、許可、承認された VM ベースの精選された一覧から選択します。
- すべてのソフトウェアとツールがイメージにインストールされている事前作成済みのカスタム イメージからマシンを作成します。 
- 基本的にカスタム イメージである数式と、仮想マシンの作成時にインストールされているソフトウェアの最新のビルドからマシンを作成します。
- プロビジョニング後に、VM に展開された拡張機能である成果物をインストールします。
- マシンで、1 日の最後にシャットダウンし、翌朝に起動させる自動シャットダウンおよび自動開始スケジュールをセットアップします。
- マシン作成のプロセスを実行することなく、事前作成済みの仮想マシンを要求するだけです。 

DevTest Labs では、PaaS 環境を使用する開発者に次の機能を提供します。

- 3 つ未満の簡単な手順に従って、PaaS Azure Resource Manager 環境をすばやく作成します。
- チーム リーダーまたは中央 IT によって構成、許可、承認された Resource Manager テンプレートの精選された一覧から選択します。
- Resource Manager テンプレートを使用して、空のリソース グループ (サンドボックス) を起動し、ラボのコンテキスト内に留まりながら、Azure 全体を探索します。
- DevTest ラボを使用することで、クラウド内の開発者とテスト向けの環境の作成、構成、管理において次の利点が提供されます。

チームの開発者向けのセルフサービス モデルとは別に、サービスでは、次のタスクを実行することによって、中央 IT が無駄を制御し、リソースのコストを最適化し、予算内に収めることができます。 

- 仮想マシンの自動シャットダウンと自動開始スケジュールの設定。
- ユーザーが作成できる仮想マシンの数に関するポリシーの設定。
- 仮想マシンのサイズとユーザーが選択できるギャラリー イメージに関するポリシーの設定。
- ラボのコストの追跡とターゲットの設定。
- 必要なアクションを実行できるように、ラボの予測される高額のコストに関する通知の取得。 

DevTest Labs を使用することで、クラウド内の環境の作成、構成、管理において次の利点が提供されます。

## <a name="cost-control-and-governance"></a>コスト管理とガバナンス
DevTest Labs により、以下のタスクを実行できることで、コスト管理が簡単になります。

- ユーザーあたりの仮想マシン (VM) 数、ラボあたりの VM 数など、ラボに関するポリシーを設定します。 
- VM を自動的にシャット ダウンし、起動するポリシーを作成します。
- ラボ内で起動された仮想マシンと PaaS リソースのコストを追跡できるため、定義済みの予算内に収めることができます。 
- 開発者がラボのコンテキスト内に留まり、最終的に基になるリソース グループやサブスクリプションの、その外部のリソースを起動することがないように支援します。

## <a name="quickly-get-to-ready-to-test"></a>すばやくテスト準備完了状態へ
DevTest ラボを使用することで、チームがアプリケーションの開発とテストを開始するために必要なものがすべて事前にプロビジョニングされている環境を作成できます。 アプリケーションの最新の良好なビルドがインストールされた環境を要求し、作業を開始するだけです。 あるいは、コンテナーを使えば環境の作成が一層迅速かつ効率的になります。

## <a name="create-once-use-everywhere"></a>1 回作成すれば、どこでも使える
チーム内や組織内で PaaS 環境テンプレートとアーティファクトのキャプチャと共有を行い、開発者とテスト向けの環境を簡単に作成できます。これらすべてをソース管理の制御のもとで行えます。

## <a name="worry-free-self-service"></a>心配無用のセルフサービス
DevTest Labs により、開発者とテスト担当者は、専用に事前構成されている一連のリソースを使用して、数回のクリックで仮想マシン (IaaS) および PaaS リソースをすばやく簡単に作成できます。

## <a name="use-iaas-and-paas-resources"></a>IaaS および PaaS リソースを使用する 
DevTest Labs により、開発者は Resource Manager テンプレートを使用して、Web Apps、Service Fabric クラスター、SharePoint ファームなどの PaaS リソースを ラボ内で起動することもできます。 パブリック環境リポジトリから Resource Manager テンプレートを使用するか、またはラボを独自の Git リポジトリに接続し、ラボで PaaS を開始します。 事前定義済みの予算内に収めるために、これらのリソースのコストを追跡することもできます。 

## <a name="integrate-with-your-existing-toolchain"></a>既存のツールチェーンと統合する
事前作成済みのプラグインや Microsoft の API を使用して、好みの継続的インテグレーション (CI) ツール、統合開発環境 (IDE)、自動リリース パイプラインから、Dev/Test 環境のプロビジョニングを直接行うことができます。 Microsoft の包括的なコマンド ライン ツールを使用することもできます。

## <a name="next-steps"></a>次の手順
次の記事を参照してください。 

- DevTest Labs の詳細については、「[DevTest ラボの概念](devtest-lab-concepts.md)」を参照してください。
- 詳細な手順については、「[チュートリアル:Azure DevTest Labs を使用してラボを設定する](tutorial-create-custom-lab.md)」を参照してください。


