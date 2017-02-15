---
title: "Kubernetes を使用した Azure Container Service クラスターのデプロイ | Microsoft Docs"
description: "Kubernetes を使用して Azure Container Service クラスターをデプロイします"
services: container-service
documentationcenter: 
author: anhowe
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2016
ms.author: anhowe
translationtype: Human Translation
ms.sourcegitcommit: 0989c85e4b7c73a8b7b335254e4af90ef34ddc01
ms.openlocfilehash: 8c10da9d2363f0938d821b59cc4fd58b53f9ed17


---

# <a name="microsoft-azure-container-service-engine---kubernetes-walkthrough"></a>Microsoft Azure Container Service エンジン - Kubernetes チュートリアル

## <a name="deployment"></a>デプロイ

単純な Kubernetes クラスターをデプロイする手順は次のとおりです。

1. [SSH キーを生成する](https://github.com/Azure/azure-quickstart-templates/blob/master/101-acs-dcos/docs/SSHKeyManagement.md#ssh-key-generation)
3. [サービス プリンシパルを生成する](https://github.com/Azure/acs-engine/blob/master/docs/serviceprincipal.md)
4. [README の [Deploy to Azure (Azure へのデプロイ)]](https://github.com/Azure/azure-quickstart-templates/blob/master/101-acs-kubernetes/README.md) ボタンをクリックし、フィールドに入力する

## <a name="walkthrough"></a>チュートリアル

Kubernetes クラスターの作成が完了したら、次を含むリソース グループが生成されます。

1. SSH (ポート 22) または kubectl (ポート 443) でアクセスできる 1 つのマスター

2. 可用性セット内の一連のノード。  ノードはマスター経由でアクセスできます。  この方法の例については、[エージェント転送](https://github.com/Azure/azure-quickstart-templates/blob/master/101-acs-dcos/docs/SSHKeyManagement.md#key-management-and-agent-forwarding-with-windows-pageant)に関するページを参照してください。

次の図は、1 つのマスターと 2 つのエージェントがあるコンテナー サービス クラスターのアーキテクチャを示します。

![Azure 上の Kubernetes クラスターの図](media/container-service-kubernetes-walkthrough/kubernetes.png)

上の図には、次の要素があります。

1. **マスター コンポーネント** - マスターでは、Kubernetes スケジューラ、API サーバー、コントローラー マネージャーが実行されます。  ポート 443 は、kubectl CLI によるリモート管理用に公開されます。
2. **ノード** - Kubernetes ノードは可用性セット内で実行されます。  Azure ロード バランサーは、公開されているサービスに応じてクラスターに動的に追加されます。 
3. **共通コンポーネント** - すべての VM では、kubelet、Docker、プロキシが実行されます。
4. **ネットワーク** - すべての VM には、10.240.0.0/16 ネットワーク内の IP アドレスが割り当てられます。  各 VM には、ポッドごとの IP を有効にするポッド CIDR の /24 サブネットが割り当てられます。  各 VM で実行されているプロキシは、サービス ネットワーク 10.0.0.0/16 を実装します。

すべての VM は同一のプライベート VNET 内にあり、完全な相互アクセスが可能です。

## <a name="create-your-first-kubernetes-service"></a>初めての Kubernetes サービスの作成

このチュートリアルを完了すると、次の方法がわかります。
 * SSH 経由で Kubernetes クラスターにアクセスする。
 * 簡単な Docker アプリケーションをデプロイして公開する。
 * Kube 構成ファイルの場所と Kubernetes クラスターにリモートでアクセスする方法。
 * `kubectl exec` を使用してコンテナーでコマンドを実行する。 
 * 最後に Kubernetes ダッシュボードにアクセスする。

1. テンプレートのデプロイが成功した後、マスター FQDN (完全修飾ドメイン名) を書き留めます。
   1. Powershell または CLI を使用している場合、出力パラメーターは "masterFQDN" という名前の OutputsString セクションにあります。
   2. ポータルを使用している場合、ContainerService リソースの [概要] ブレードを参照して "マスター FQDN" をコピーします。
     
   ![Docker スケーリングの画像](media/container-service-kubernetes-walkthrough/portal-kubernetes-outputs.png)

2. 手順 1. で取得したマスター FQDN に SSH 接続します。

3. ノードと実行中のポッドを確認します。
  1. ノードの一覧を表示するには、「`kubectl get nodes`」と入力します。  ノードの詳細が必要な場合は、`kubectl get nodes -o yaml`となるように `-o yaml` を追加します。
  2. 実行中のポッドの一覧を表示するには、「`kubectl get pods --all-namespaces`」と入力します。

4. 「`kubectl run nginx --image nginx`」と入力して、最初の Docker イメージを開始します。  これで、いずれかのノードにあるポッドで nginx Docker コンテナーが開始されます。

5. 「`kubectl get pods -o yaml`」と入力して、nginx デプロイの詳細を表示します。 ホスト IP とポッド IP を確認できます。  ポッド IP は、ホスト上のポッド CIDR から割り当てられます。  ポッド IP に curl を実行して nginx 出力を表示します。例:  `curl 10.244.1.4`

  ![ポッド IP に対する curl の実行の画像](media/container-service-kubernetes-walkthrough/kubernetes-nginx1.png)

6. 次の手順では、nginx デプロイを Kubernetes サービスとしてプライベート サービス ネットワーク 10.0.0.0/16 に公開します。
  1. コマンド `kubectl expose deployment nginx --port=80` を使用してサービスを公開します。
  2. `kubectl get service` を使用してサービス IP を取得します。
  3. そのサービス IP に対して curl を実行します。例:  `curl 10.0.105.199`

  ![サービス IP に対する curl の実行の画像](media/container-service-kubernetes-walkthrough/kubernetes-nginx2.png)

7. 最後の手順では、サービスを公開します。  これは、サービスの種類を `ClusterIP` から `LoadBalancer` に変更することで実行できます。
  1. `kubectl edit svc/nginx` で、サービスを編集します。
  2. `type` を `ClusterIP` から `LoadBalancer` に変更して保存します。  これにより、Kubernetes がパブリック IP を持つ Azure ロード バランサーを作成します。
  3. 変更には約 2 ～ 3 分かかります。  "保留中" から外部 IP へのサービスの変更を確認するには、「`watch 'kubectl get svc'`」と入力します。

  ![保留中から外部 IP への切り替え確認の画像](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

  4. 外部 IP を確認したら、ブラウザーでそれを参照できます。

  ![nginx の参照の画像](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  

8. このチュートリアルの次の手順では、Kubernetes クラスターをリモートで管理する方法を説明します。  まず Kubectl をコンピューターにダウンロードしてパスに追加します。
  * [Windows Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.4.5/bin/windows/amd64/kubectl.exe)
  * [OSX Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.4.5/bin/darwin/amd64/kubectl)
  * [Linux](https://storage.googleapis.com/kubernetes-release/release/v1.4.5/bin/linux/amd64/kubectl)

9. Kubernetes マスターのホーム ディレクトリ ~/.kube/config には、リモート アクセス用の kube 構成ファイルが含まれています。  このファイルをコンピューターにダウンロードして、KUBECONFIG 環境変数を設定し、kubectl を実行してクラスターに接続できることを確認します。
  * Windows では [putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) の pscp を使用。  証明書を [pageant](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#key-management-and-agent-forwarding-with-windows-pageant) を通じて公開していることを確認します。
  ```
  # MASTERFQDN is obtained in step1
  pscp azureuser@MASTERFQDN:.kube/config .
  SET KUBECONFIG=%CD%\config
  kubectl get nodes
  ```
  * OS X または Linux の場合。
  ```
  # MASTERFQDN is obtained in step1
  scp azureuser@MASTERFQDN:.kube/config .
  export KUBECONFIG=`pwd`/config
  kubectl get nodes
  ```
10. 次の手順では、リモート Docker コンテナーでコマンドをリモートで実行する方法を説明します。
  1. `kubectl get pods` を実行して、nginx ポッドの名前を表示します。
  2. ポッドの名前を使用すると、ポッドに対してリモート コマンドを実行できます。  例:  `kubectl exec nginx-701339712-retbj date`
  3. リモート bash セッションの実行を試します。 例:  `kubectl exec nginx-701339712-retbj -it bash`.  次のスクリーン ショットには、これらのコマンドが表示されています。

  ![ポッド IP に対する curl の実行の画像](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)

11. このチュートリアルの最後の手順は、ダッシュボードの表示です。
  1. `kubectl proxy` を実行して、プロキシに直接接続します。
  2. ブラウザーで[ダッシュボード](http://127.0.0.1:8001/api/v1/proxy/namespaces/kube-system/services/kubernetes-dashboard/#/workload?namespace=_all)を表示します。
  3. ポッドとサービスを細かく確認します。
  ![Kubernetes ダッシュボードの画像](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

# <a name="learning-more"></a>詳細情報

Kubernetes について詳しく知りたい場合は、次のリンクを参照してください。

1. [Azure Kubernetes ドキュメント](https://azure.microsoft.com/en-us/documentation/services/container-service/)

## <a name="kubernetes-community-documentation"></a>Kubernetes コミュニティのドキュメント

1. [Kubernetes ブートキャンプ](https://kubernetesbootcamp.github.io/kubernetes-bootcamp/index.html) - コンテナー化されたアプリケーションをデプロイ、スケール、更新、デバッグする方法について説明されています。
2. [Kubernetes ユーザーガイド](http://kubernetes.io/docs/user-guide/) - 既存の Kubernetes クラスターでプログラムを実行するための情報が記載されています。
3. [Kubernetes の例](https://github.com/kubernetes/kubernetes/tree/master/examples) - Kubernetes を使って実際のアプリケーションを実行する方法の例が多数記載されています。



<!--HONumber=Nov16_HO3-->


