---
title: Azure portal から Azure Arc データ コントローラーをデプロイする | 直接接続モード
description: Azure portal から直接接続モードでデータ コントローラーをデプロイする方法について説明します。
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/03/2021
ms.topic: overview
ms.openlocfilehash: 85a7ca3dc50cb95fc545be6b486a300247837450
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714905"
---
#  <a name="create-azure-arc-data-controller-from-azure-portal---direct-connectivity-mode"></a>Azure portal から Azure Arc データ コントローラーを作成する - 直接接続モード

この記事では、Azure portal から Azure Arc データ コントローラーを直接接続モードでデプロイする方法について説明します。 

## <a name="complete-prerequisites"></a>前提条件を満たす

開始する前に、[直接接続モードでデータ コントローラーのデプロイするための前提条件](create-data-controller-direct-prerequisites.md)に関するページに記載されている前提条件を満たしていることを確認してください。

## <a name="deploy-azure-arc-data-controller"></a>Azure Arc データ コントローラーをデプロイする

Azure Arc データ コントローラーの作成フローは、次のいずれかの方法で Azure portal から開始できます。

- Azure portal の検索バーで、「Azure Arc data controllers」を検索し、[+ 作成] を選択します
- Azure Arc 対応 Kubernetes クラスターの [概要] ページから、
  - [設定] で [拡張機能] を選択します。
  - [拡張機能] 概要ページから [追加] を選択し、[Azure Arc data controller]\(Azure Arc データ コントローラー\) を選択します
  - Azure Arc データ コントローラー マーケットプレース ギャラリー から [作成] を選択します
  
これらのアクションのいずれかを実行すると、作成フローの [Azure Arc data controller prerequisites]\(Azure Arc データ コントローラーの前提条件\) ページが表示されます。

- [Azure Arc-enabled Kubernetes cluster (Direct connectivity mode)]\(Azure Arc 対応 Kubernetes クラスター (直接接続モード)\) オプションが選択されていることを確認します。 [Next: Data controller details]\(次へ: データ コントローラーの詳細\) を選択します
- **Data controller details\(データ コントローラーの詳細\)** ページで以下を実行します。
  - Azure Arc データ コントローラーが投射される Azure サブスクリプションとリソース グループを選択します。
  - データ コントローラーの **名前** を入力します。
  - 事前に作成した **カスタムの場所** を選択するか、[新規作成] を選択して新しいカスタムの場所を作成します。 新しいカスタムの場所を作成する場合は、新しいカスタムの場所の名前を入力し、ドロップダウンから Azure Arc 対応 Kubernetes クラスターを選択してから、新しいカスタムの場所に関連付けられる名前空間を入力し、最後に [Create new custom location]\(新しいカスタムの場所の作成\) ウィンドウで [作成] を選択します。 [カスタムの場所](../kubernetes/conceptual-custom-locations.md)に関する詳細を確認してください
  - **Kubernetes configuration (Kubernetes の構成)** - ドロップダウンから、ご利用の Kubernetes ディストリビューションに最も適した Kubernetes 構成テンプレートを選択します。 独自の設定を使用するか、カスタム プロファイルを使用する場合は、ドロップダウンから [カスタム テンプレート] オプションを選択します。 右側に開いたブレードで、Docker 資格情報、リポジトリ情報、イメージ タグ、イメージのプル ポリシー、インフラストラクチャの種類、データのストレージ設定、ログとそのサイズ、サービスの種類、コントローラーと管理プロキシのポートに関する詳細を入力します。 必要なすべての情報を指定したら、[適用] を選択します。 また、ブレードの上部で [Upload a template (JSON)]\(テンプレート (JSON) のアップロード\) 選択して、独自のテンプレート ファイルをアップロードすることもできます。 カスタム設定を使用し、それらの設定のコピーをダウンロードする必要がある場合は、[Download this template (JSON)]\(このテンプレート (JSON) をダウンロードする\) を使用して実行します。 [カスタム構成プロファイル](create-custom-configuration-template.md)に関する詳細を確認してください。
  - 実際の環境に合った適切な **[サービスの種類]** を選択します
  - **Metrics and Logs Dashboard Credentials (メトリックとログ ダッシュボードの資格情報)** - Grafana および Kibana ダッシュボードの資格情報を入力します
  - 必要なすべての情報を指定したら、[次へ: 追加設定] ボタンを選択して、次に進みます。
- **[追加設定]** ページで以下を実行します。
  - **Metrics upload (メトリックのアップロード):** このオプションは、メトリックを自動的に Azure Monitor にアップロードする場合に選択します。これにより、メトリックの集計と分析、アラートの生成、通知の送信、自動化されたアクションのトリガーを行えます。 必要な **監視メトリック発行者** ロールは、拡張機能のマネージド ID に付与されます。 
  - **Logs upload (ログのアップロード):** このオプションは、既存の Log Analytics ワークスペースにログを自動的にアップロードする場合に選択します。 Log Analytics ワークスペース ID と Log Analytics 共有アクセス キーを入力します。 
  - [次へ: タグ] を選択して、次に進みます。
- **[タグ]** ページで、タグの名前と値を入力し、[次へ: 確認および作成] を選択します。
- **[確認および作成]** ページで、デプロイの概要を表示します。 すべての設定が正しく表示されていることを確認し、[作成] を選択して、Azure Arc データ コントローラーのデプロイを開始します。

## <a name="monitor-the-creation-from-azure-portal"></a>Azure portal から作成を監視する

前の手順で [作成] ボタンを選択すると、Azure デプロイの概要ページが開き、Azure Arc データ コントローラーのデプロイの進行状況が表示されます。

## <a name="monitor-the-creation-from-your-kubernetes-cluster"></a>Kubernetes クラスターから作成を監視する

Azure Arc データ コントローラーのデプロイの進行状況は、次のように監視できます。

- クラスターから ```kubectl get crd ``` を実行して CRD が作成されたどうかを確認します  
- クラスターから ```kubectl get ns``` を実行して 名前空間が作成されたどうかを確認します
- ```az customlocation list --resource-group <resourcegroup> -o table``` を実行して、カスタムの場所が作成されたどうかを確認します 
- ```kubectl get pods -ns <namespace>``` を実行して、ポッドのデプロイの状態を確認します

## <a name="next-steps"></a>次のステップ

[Azure Arc 対応 SQL マネージド インスタンスを作成する](create-sql-managed-instance.md)

[Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを作成する](create-postgresql-hyperscale-server-group.md)
