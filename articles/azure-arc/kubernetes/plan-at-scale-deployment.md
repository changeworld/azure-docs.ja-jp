---
title: Azure Arc 対応 Kubernetes を計画およびデプロイする方法
services: azure-arc
ms.service: azure-arc
ms.date: 04/12/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 構成管理のために Azure Arc 対応 Kubernetes に多数のクラスターをオンボードする
ms.openlocfilehash: 4b34cee08db508728f01d262ae4b1ee4ed4754e1
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315195"
---
# <a name="plan-and-deploy-azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes を計画およびデプロイする

IT インフラストラクチャ サービスまたはビジネス アプリケーションのデプロイは、どの企業にとっても難題です。 うれしくない驚きや計画外のコストを回避するには、徹底的に計画を立てて、可能な限り確実に準備しておく必要があります。 このような計画では、タスクを完了するために満たす必要がある設計およびデプロイの条件を特定する必要があります。

デプロイを円滑に続行するには、計画で以下のことを明確に理解しておく必要があります。

* ロールと責任。
* すべての Kubernetes クラスターのインベントリ
* ネットワーク要件を満たす。
* デプロイと継続的な管理の成功を実現するために必要なスキル セットとトレーニング。
* 受け入れの基準と、その成功を追跡する方法。
* デプロイを自動化するために使用するツールまたは方法。
* 遅延や中断を避けるために特定されたリスクと軽減策。
* デプロイ中に中断を回避する方法。
* 重要な問題が発生したときにどのようなエスカレーション パスがあるか。

この記事の目的は、環境内の複数の運用クラスターにわたる Azure Arc 対応 Kubernetes のデプロイを成功させるための準備を確実に行うことです。

## <a name="prerequisites"></a>前提条件

* 既存の Kubernetes クラスター。 お持ちでない場合は、次のいずれかのオプションを使用してクラスターを作成できます。
    - [Docker 内の Kubernetes (KIND)](https://kind.sigs.k8s.io/)
    - [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) または [Windows](https://docs.docker.com/docker-for-windows/#kubernetes) 用の Docker を使用して Kubernetes クラスターを作成する
    - [Cluster API](https://cluster-api.sigs.k8s.io/user/quick-start.html) を使用したセルフマネージド Kubernetes クラスター

* お使いのマシンが、オンプレミスのネットワークまたはその他のクラウド環境から、直接またはプロキシ サーバーを介して Azure 内のリソースに接続できること。 詳細については、[ネットワークの前提条件](quickstart-connect-cluster.md#meet-network-requirements)に関するページを参照してください。

* Azure Arc に接続するクラスターを指し示す `kubeconfig` ファイル。
* Azure Arc 対応 Kubernetes リソース (`Microsoft.Kubernetes/connectedClusters` という種類) を作成するユーザーまたはサービス プリンシパルに対する "読み取り" および "書き込み" アクセス許可。

## <a name="pilot"></a>パイロット

すべての運用クラスターへのデプロイを行う前に、まずこのデプロイ プロセスを評価してから、環境内でそれを広く採用します。 パイロットの場合は、ビジネスを遂行する会社の機能にとって重要でないクラスターの代表的なサンプリングを特定します。 パイロットを実行し、その影響を評価するのに十分な時間を確保することをお勧めします (Microsoft では約 30 日をお勧めしています)。

パイロットの範囲と詳細を説明する正式な計画を立てます。 作業を開始するのに以下のサンプル計画が役立つはずです。

* **目標** - パイロットが必要であるという決定に至った、ビジネス上および技術上の決定要因について説明します。
* **選択条件** - パイロットによって、ソリューションのどのような側面を実証するかを選択するために使用する条件を指定します。
* **スコープ** - ソリューションのコンポーネント、予想されるスケジュール、パイロットの期間、およびターゲットとするクラスターの数が含まれます。
* **成功条件とメトリック** - パイロットの成功条件と、成功のレベルを判断するために使用される具体的な基準を定義します。
* **トレーニング計画** - パイロット中に、Azure とそのサービスの使用経験がないシステム エンジニアや管理者などをトレーニングするための計画について説明します。
* **移行計画** - パイロットから運用への移行をガイドするために使用される戦略と条件について説明します。
* **ロールバック** - パイロットをデプロイ前の状態にロールバックする手順について説明します。
* **リスク** - パイロットの実施と、運用のデプロイに関連する、特定されたすべてのリスクを一覧で示します。

## <a name="phase-1-build-a-foundation"></a>フェーズ 1: 基盤を構築する

このフェーズでは、システム エンジニアまたは管理者が、リソース グループ、タグ、ロールの割り当ての作成などのコア アクティビティを実行し、Azure Arc 対応 Kubernetes リソースを作成して操作できるようにします。

|タスク |Detail |Duration |
|-----|-------|---------|
| [リソース グループの作成](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Azure Arc 対応 Kubernetes リソースのみを含み、これらのリソースの管理と監視を一元化する専用リソース グループ。 | 1 時間 |
| マシンの整理に役立つ[タグ](../../azure-resource-manager/management/tag-resources.md)を適用する。 | IT に合わせた[タグ付け戦略](/azure/cloud-adoption-framework/decision-guides/resource-tagging/)を評価し、開発します。 これは、Azure Arc 対応 Kubernetes リソースの管理の複雑さを軽減し、管理上の意思決定を簡略化するのに役立つ場合があります。 | 1 日 |
| GitOps の[構成](tutorial-use-gitops-connected-cluster.md)を特定する | クラスターにデプロイするアプリケーションまたはベースライン構成 (`PodSecurityPolicy`、`NetworkPolicy` など) を特定します | 1 日 |
| [Azure Policy](../../governance/policy/overview.md) ガバナンス計画を作成する | Azure Policy を使用して、サブスクリプションまたはリソース グループのスコープで Azure Arc 対応 Kubernetes クラスターのガバナンスを実装する方法を決定します。 | 1 日 |
| [ロールベースのアクセス制御](../../role-based-access-control/overview.md) (RBAC) を構成する | クラスターに対する読み取り/書き込み/すべてのアクセス許可を持つユーザーを特定するアクセス計画を作成します | 1 日 |

## <a name="phase-2-deploy-azure-arc-enabled-kubernetes"></a>フェーズ 2: Azure Arc 対応 Kubernetes をデプロイする

このフェーズでは、Kubernetes クラスターを Azure に接続します。

|タスク |Detail |Duration |
|-----|-------|---------|
| [最初の Kubernetes クラスターを Azure Arc に接続する](quickstart-connect-cluster.md) | Azure Arc への最初のクラスターの接続作業の一環として、Azure CLI、Helm、および Azure CLI 用の `connectedk8s` 拡張機能などの必要なツールをすべて備えたオンボード環境を設定します。 | 約 15 分 |
| [サービス プリンシパルを作成する](create-onboarding-service-principal.md) | Azure CLI または PowerShell を使用して、非対話形式で Kubernetes クラスターを接続するためのサービス プリンシパルを作成します。 | 1 時間 |


## <a name="phase-3-manage-and-operate"></a>フェーズ 3: 管理して運用する

このフェーズでは、アプリケーションとベースライン構成を Kubernetes クラスターにデプロイします。

|タスク |Detail |Duration |
|-----|-------|---------|
|クラスターで[構成を作成する](tutorial-use-gitops-connected-cluster.md) | Azure Arc 対応 Kubernetes リソースにアプリケーションをデプロイするための構成を作成します。 | 約 15 分 |
|構成の大規模な適用に [Azure Policy を使用する](use-azure-policy.md) | ポリシーの割り当てを作成し、サブスクリプションまたはリソース グループのスコープの下にあるすべてのクラスターにわたるベースライン構成のデプロイを自動化します。 | 約 15 分 |
| [Azure Arc エージェントをアップグレードする](agent-upgrade.md) | クラスター上のエージェントの自動アップグレードを無効にしている場合は、エージェントを最新バージョンに手動で更新し、必ず最新のセキュリティとバグの修正プログラムを適用するようにします。 | 約 15 分 |

## <a name="next-steps"></a>次のステップ

* クイックスタートを利用して、[Kubernetes クラスターを Azure Arc に接続する](./quickstart-connect-cluster.md)。
* Azure Arc 対応 Kubernetes クラスターで[構成を作成する](./tutorial-use-gitops-connected-cluster.md)。
* [Azure Policy を使用して構成を大規模に適用する](./use-azure-policy.md)。