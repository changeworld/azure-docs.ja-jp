---
title: 概要 - Azure Arc 対応 Logic Apps
description: Kubernetes が実行できる任意の場所で実行できるシングルテナント Logic Apps ワークフローについて説明します。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, ladolan, reylons, archidda, sopai, azla
ms.topic: overview
ms.date: 05/25/2021
ms.openlocfilehash: 2a8d3187eecb684f7c0c2b89c135745dbcc9d8e9
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129713365"
---
# <a name="what-is-azure-arc-enabled-logic-apps-preview"></a>Azure Arc 対応 Logic Apps とは (プレビュー)

> [!NOTE]
> この機能はプレビュー段階にあり、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」が適用されます。

Azure Arc 対応 Logic Apps を使用すると、Kubernetes が実行できるすべての場所で、シングルテナント ベースのロジック アプリを開発して実行できます。 たとえば、Azure、Azure Kubernetes Service、オンプレミス、さらにその他のクラウド プロバイダーでもロジック アプリ ワークフローを実行できます。 このオファリングでは、次の機能のために、Azure Arc および Azure portal を介した 1 つのウィンドウからの一元的な管理プラットフォームを提供します。

- 統合プラットフォームとして Azure Logic Apps を使用します。
- ホストされている場所に関係なく、ワークフローをすべてのサービスに接続します。
- サービスと並行して、統合ソリューションを直接実行します。
- Visual Studio Code を使用してワークフローを作成および編集します。
- DevOps 用に選択したパイプラインを使用してデプロイします。
- Azure、Azure 以外、複数クラウド、オンプレミス、およびエッジ環境でインフラストラクチャとリソースを制御します。

詳細については、次のドキュメントを確認してください。

- [Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)
- [シングルテナントとその他の Logic Apps 環境の比較](../logic-apps/single-tenant-overview-compare.md)
- [Azure Arc の概要](../azure-arc/overview.md)
- [Azure Kubernetes Service の概要](../aks/intro-kubernetes.md)
- [Azure Arc 対応 Kubernetes とは](../azure-arc/kubernetes/overview.md)
- [Kubernetes とは](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/)

<a name="why-use"></a>

## <a name="why-use-azure-arc-enabled-logic-apps"></a>Azure Arc 対応 Logic Apps を使用する理由

Azure Arc 対応 Logic Apps では、Azure Logic Apps のシングルテナント エクスペリエンスと同じ方法で、ロジック アプリ ワークフローを作成およびデプロイできます。 また、運用および管理する Kubernetes インフラストラクチャでロジック アプリを実行すると、管理のしやすさと柔軟性が向上します。

ロジックアプリの作成、設計、およびデプロイについて、Azure Arc とシングルテナント Logic Apps エクスペリエンスの間にわずかな違いがあります。 Azure Arc 対応 Logic Apps を使用する場合の大きな違いは、ロジック アプリが "*カスタムの場所*" で実行されることです。 この場所は、Azure App Service プラットフォーム拡張機能バンドルをインストールして有効にした Azure Arc 対応 Kubernetes クラスターにマップされます。

たとえば、このクラスターは、Azure Kubernetes Service、ベアメタル Kubernetes、または別のセットアップにすることができます。 拡張機能バンドルを使用すると、Kubernetes クラスターで Azure Logic Apps、Azure Functions、Azure App Service などのプラットフォーム サービスを実行できます。

詳細については、次のドキュメントを確認してください。

- [シングルテナントとその他の Azure Logic Apps 環境の比較](../logic-apps/single-tenant-overview-compare.md)
- [Azure Kubernetes Service の概要](../aks/intro-kubernetes.md)
- [Azure Arc 対応 Kubernetes とは](../azure-arc/kubernetes/overview.md)
- [Azure Arc 対応 Kubernetes 上のカスタムの場所](../azure-arc/kubernetes/conceptual-custom-locations.md)
- [Azure Arc の App Service、Functions、および Logic Apps (プレビュー)](../app-service/overview-arc-integration.md)
- [Azure Arc 対応の Kubernetes クラスターを設定して、App Service、Functions、Logic Apps を実行します (プレビュー)](../app-service/manage-create-arc-environment.md)

<a name="when-to-use"></a>

## <a name="when-to-use-azure-arc-enabled-logic-apps"></a>Azure Arc 対応 Logic Apps を使用する状況

Kubernetes では管理のしやすさと柔軟性が向上しますが、運用上のオーバーヘッドも発生します。 Logic Apps サービスでニーズを満たせることに満足している場合は、このサービスを引き続き使用することをお勧めします。 ただし、次のシナリオの場合は、Azure Arc 対応 Logic Apps の使用を検討してください。

- すべてのアプリとサービスを Kubernetes で既に実行している。 これらのプロセスとコントロールを他のすべての PaaS サービスに拡張する必要がある。

- Logic Apps を統合プラットフォームとして使用する必要がある。 ただし、コンピューティングの制御と柔軟性のためにきめ細かなネットワークを必要としている。 統合サービス環境 (ISE) や App Service Environment (ASE) は使用したくない。

- セキュリティ上の理由から、ロジック アプリの実行場所を制御する必要がある (独自のリージョンや独自のデータセンターなど)。 

- ロジック アプリをマルチクラウド シナリオで実行し、それらが実行されるすべての場所で、すべてのアプリケーションについての唯一の統合プラットフォームとして Logic Apps サービスを使用する必要がある。

<a name="compare"></a>

## <a name="compare-offerings"></a>オファリングを比較する

次の表は、現在の Azure Logic Apps オファリングの機能を概要レベルで比較したものです。

:::row:::
   :::column:::
      **機能**
   :::column-end:::
   :::column:::
      **マルチテナント Logic Apps (従量課金)**
   :::column-end:::
   :::column:::
      **シングルテナント Logic Apps (Standard)**
   :::column-end:::
   :::column:::
      **スタンドアロン コンテナー**
   :::column-end:::
   :::column:::
      **Azure Arc**
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      ローカル開発
   :::column-end:::
   :::column:::
      Visual Studio Code、Visual Studio
   :::column-end:::
   :::column:::
      Visual Studio Code (実行履歴とブレークポイントのデバッグによる概要を含む)
   :::column-end:::
   :::column:::
      Visual Studio Code
   :::column-end:::
   :::column:::
      Visual Studio Code (実行履歴とブレークポイントのデバッグによる概要を含む)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      Hosting
   :::column-end:::
   :::column:::
      Azure でのみ実行
   :::column-end:::
   :::column:::
      Azure でのみ実行
   :::column-end:::
   :::column:::
      コンテナーを実行する任意の場所で実行
   :::column-end:::
   :::column:::
      Azure Arc 対応 Kubernetes クラスターがある任意の場所で実行
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      管理
   :::column-end:::
   :::column:::
      フル マネージド Logic Apps エクスペリエンス
   :::column-end:::
   :::column:::
      フル マネージド Logic Apps エクスペリエンス
   :::column-end:::
   :::column:::
      管理されていない
   :::column-end:::
   :::column:::
      Kubernetes レベルでの運用制御によるマネージド Logic Apps エクスペリエンス
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      監視
   :::column-end:::
   :::column:::
      Azure portal 内で監視 (必要に応じて実行履歴、実行の再送信、Application Insights 機能を含む)
   :::column-end:::
   :::column:::
      Azure portal 内で監視 (必要に応じて実行履歴、実行の再送信、Application Insights 機能を含む)
   :::column-end:::
   :::column:::
      Application Insights またはその他のコンテナー監視ツールでのみ監視
   :::column-end:::
   :::column:::
      Azure portal 内で監視 (必要に応じて実行履歴、実行の再送信、Application Insights 機能を含む)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      Scaling
   :::column-end:::
   :::column:::
      従量課金プランを使用したスケーリングの制御
   :::column-end:::
   :::column:::
      Standard プランを使用したスケーリングの制御
   :::column-end:::
   :::column:::
      使用不可
   :::column-end:::
   :::column:::
      [Kubernetes ベースのイベント ドリブン自動スケーリング (KEDA)](https://keda.sh/) を使用したスケーリングの制御。 キューの長さに基づいてスケール イベントを構成。
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Arc 対応 Logic Apps を使用してワークフローを作成してデプロイする](azure-arc-enabled-logic-apps-create-deploy-workflows.md)
