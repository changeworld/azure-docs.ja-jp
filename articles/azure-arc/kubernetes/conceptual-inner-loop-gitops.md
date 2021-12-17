---
title: GitOps を採用するチームの内部ループ開発者エクスペリエンス
services: azure-arc
ms.service: azure-arc
ms.date: 06/18/2021
ms.topic: conceptual
author: sudivate
ms.author: sudivate
description: 'ここでは、GitOps を採用するチームの内部ループ開発者エクスペリエンスの概念に関する概要を説明します '
keywords: GitOps、Kubernetes、K8s、Azure、Helm、Arc、AKS、Azure Kubernetes Service、コンテナー、CI、CD、Azure DevOps、内部ループ、開発者エクスペリエンス
ms.openlocfilehash: 3bd60c3236bb187f14b924f4246d8e6fcfed4ed3
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706419"
---
# <a name="inner-loop-developer-experience-for-teams-adopting-gitops"></a>GitOps を採用するチームの内部ループ開発者エクスペリエンス

ここでは、確立された内部ループによって、どのように開発者の生産性を高め、GitOps を採用するチームの内部開発ループから外部ループにシームレスに移行できるかについて説明します。

## <a name="inner-dev-loop-frameworks"></a>内部開発ループ フレームワーク

コンテナーの構築とデプロイによって、内部開発エクスペリエンスに遅延が生じ、チームの生産性に影響が及ぼされる可能性があります。 クラウドネイティブの開発チームは、堅牢な内部開発ループ フレームワークからメリットを得られます。 内部開発ループ フレームワークは、コードの作成、ビルド、デバッグの反復プロセスに役立ちます。

内部開発ループ フレームワークには、次のような機能があります。

 
- コードやコンテナーを作成し、ターゲット クラスターにデプロイする手順など、反復的な手順を自動化します。 
- リモート クラスターとローカル クラスターを簡単に処理し、ハイブリッド セットアップ用のローカル トンネル デバッグをサポートします。
- チームベースの生産性を得られるように、カスタム フローを構成できます。
- マイクロサービスの依存関係を処理できます。 
- ホット リロード、ポート フォワーディング、ログ、ターミナル アクセスなどの機能があります。 



開発チームは、サービスの成熟度と複雑さに応じて、内部開発ループを促進するために使用するクラスター設定を決定します。 

* 完全にローカル
* 完全にリモート
* ハイブリッド 


幸いにも、一覧表示されている機能をサポートする多くのフレームワークがあります。 Microsoft では、ローカル トンネル デバッグ用の Bridge to Kubernetes を提供しており、DevSpace、Scaffold、Tilt など、同様の市場オファリングがあります。

> [!NOTE]
> この市場オファリングである [DevSpace](https://github.com/loft-sh/devspace) を、以前の名称である Microsoft の DevSpace と混同しないでください。現在これは、[Bridge to Kubernetes](https://code.visualstudio.com/docs/containers/bridge-to-kubernetes) と呼ばれています。


## <a name="inner-loop-to-outer-loop-transition"></a>内部ループから外側のループへの移行 

内部ループ開発フレームワークを評価して選択したら、外部ループ移行につながるシームレスな内部ループを構築します。

[GitOps を使用した CI/CD ワークフロー](conceptual-gitops-ci-cd.md)に関する記事の例で説明したように、アプリケーション開発者はアプリケーション リポジトリ内のアプリケーション コードを処理します。 このアプリケーション リポジトリには、大まかなデプロイ Helm や Kustomize テンプレートも含まれています。 CI\CD パイプライン:

* 大まかなテンプレートから詳細なマニフェストを生成し、環境に固有な値を追加します
* 特定の環境の目的の状態を保持する GitOps リポジトリと、詳細なマニフェストを統合する pull request を作成します。 

開発者のローカル側にある構成値を使用して、同様の詳細なマニフェストを内部開発ループ用にローカル側で生成できます。 アプリケーション開発者は、コードの変更を反復処理し、詳細なマニフェストを使用してアプリケーションをデプロイおよびデバッグできます。 詳細なマニフェストの生成は、開発者のローカル構成を使用して、内部ループ ワークフローに統合できます。 大部分の内部ループ フレームワークでは、カスタム プラグインを拡張するか、フックに基づいてスクリプト呼び出しを挿入することで、カスタム フローを構成できます。 

## <a name="example-inner-loop-workflow-built-with-devspace-framework"></a>DevSpace フレームワークを使用して構築された内部ループ ワークフローの例


### <a name="diagram-a-inner-loop-flow"></a>図 A: 内部ループ フロー
:::image type="content" source="media/dev-inner-loop.png" alt-text="devspace を使用した内部ループ フローの図。":::

### <a name="diagram-b-inner-loop-to-outer-loop-transition"></a>図 B: 内部ループから外部ループへの移行
:::image type="content" source="media/inner-loop-to-outer-loop.png" alt-text="内部ループから外部ループへの移行を示す図。" :::


## <a name="example-workflow"></a>ワークフローの例
アプリケーション開発者として、アリスは次のことを行います。
- devspace.yaml を作成して、内部ループを構成します。
- 効率を考えて、内部ループを使用し、アプリケーション コードを作成してテストします。
- 外部ループを使用して、ステージングまたは運用環境にデプロイします。


アリスは、ローカル クラスターまたはリモート クラスターでアプリケーションを更新、実行、デバッグしたいと思っていると想定します。

1. アリスは、.env ファイルに示されている開発環境のローカル構成を更新します。
1. `devspace use context` を実行し、Kubernetes クラスター コンテキストを選択します。
1.  `devspace use namespace <namespace_name>` を実行して、使用する名前空間を選択します。
1.  アリスは、アプリケーション コードに対する変更を反復処理し、`devspace dev` を実行して、ターゲット クラスターにアプリケーションをデプロイおよびデバッグできます。
1. `devspace dev` を実行すると、アリスのローカル構成に基づいて詳細なマニフェストが生成され、アプリケーションがデプロイされます。 これらの詳細なマニフェストは、devspace.yaml の devspace フックを使用して構成されます
1. DevSpace ではファイル同期を使用してコンテナー内に最新の変更をコピーすることでホット リロードが有効になるため、アリスはコード変更を行うたびにコンテナーをリビルドする必要はありません。
1. `devspace dev` を実行すると、フロントエンドへのバックエンド依存関係など、devspace.yaml で構成された依存関係もデプロイされます。 
1. アリスは、devspace.yaml を介して構成された転送を使用してアプリケーションにアクセスすることで、変更内容をテストします。
1. アリスは変更を完了すると、`devspace purge` を実行してデプロイを消去し、新しい pull request を作成して、アプリケーション リポジトリの dev ブランチに変更をマージできます。

> [!NOTE]
> 上記のワークフローのサンプル コードについては、この [GitHub リポジトリ](https://github.com/Azure/arc-cicd-demo-src)を確認してください

## <a name="next-steps"></a>次のステップ

[Azure Arc 対応 Kubernetes を使用して、構成リソースとして](./conceptual-configurations.md)、クラスターと Git リポジトリ間の接続を作成する方法の詳細について説明します