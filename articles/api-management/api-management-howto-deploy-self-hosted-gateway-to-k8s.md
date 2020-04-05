---
title: セルフホステッド Azure API Management ゲートウェイを Kubernetes にデプロイする | Microsoft Docs
description: セルフホステッド Azure API Management ゲートウェイを Kubernetes にデプロイする方法について説明します
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 109316acb73d3c5f00186298c1f8840c516e5790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "73510592"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>セルフホステッド Azure API Management ゲートウェイを Kubernetes にデプロイする

この記事では、セルフホステッド Azure API Management ゲートウェイを Kubernetes クラスターにデプロイする手順について説明します。

> [!NOTE]
> セルフホステッド ゲートウェイ機能はプレビュー段階です。 プレビュー期間中、セルフホステッド ゲートウェイは、Developer レベルと Premium レベルでのみ追加料金なしで利用できます。 Developer レベルは、1 つのセルフホステッド ゲートウェイのデプロイに制限されます。


## <a name="prerequisites"></a>前提条件

- [Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関するクイックスタートを完了します
- Kubernetes クラスターを作成します。 [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) は、開発および評価に適したオプションです。 運用ワークロードの場合、[Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) または外部クラウドの Kubernetes クラスターを使用できます。
- [API Management インスタンスにゲートウェイ リソースをプロビジョニングします](api-management-howto-provision-self-hosted-gateway.md)。

## <a name="deploy-the-gateway-to-kubernetes"></a>Kubernetes にゲートウェイをデプロイする

1. **[設定]** の下から **[ゲートウェイ]** を選択します。
2. デプロイするゲートウェイ リソースを選択します。
3. **[Deployment]/(デプロイ/)** を選択します。
4. 既定の **[有効期限]** および **[秘密鍵]** の値を使用して、 **[トークン]** テキスト ボックスの新しいトークンが自動生成されたことに注意してください。 必要に応じていずれかまたは両方を調整し、 **[生成]** を選択して新しいトークンを作成します。
5. **[デプロイ スクリプト]** で **[Kubernetes]** が選択されていることを確認します。
6. **[Deployment]/(デプロイ/)** の横にある **<gateway-name>.yml** ファイルのリンクを選択してファイルをダウンロードします。
7. 必要に応じて、yml ファイルのポート マッピングとコンテナー名を調整します。
8. **[Deploy]/(デプロイ/)** テキストボックスの右端にある **[コピー]** アイコンを選択して、`kubectl` コマンドをクリップボードに保存します。 
9. コマンドをターミナル (またはコマンド) ウィンドウに貼り付けます。 コマンドでは、ダウンロードした環境ファイルが現在のディレクトリに存在する必要があることに注意してください。
```console
    kubectl apply -f <gateway-name>.yaml
```
10. コマンドを実行します。 このコマンドは Kubernetes クラスターに対して、Microsoft Container Registry からダウンロードしたセルフホステッド ゲートウェイのイメージを使用してコンテナーを実行し、HTTP (8080) ポートと HTTPS (443) ポートを公開するようにコンテナーを構成するように指示します。
11. 次のコマンドを実行して、ゲートウェイ ポッドが実行中であることを確認します。 使用するポッド名は異なることに注意してください。 
```console
kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
local-gateway-55f774f844-bv9wt    1/1     Running   0          1m
```
12. 次のコマンドを実行して、ゲートウェイ サービスが実行中であることを確認します。 使用するサービス名は異なることに注意してください。 
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
localgateway     NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
13. Azure portal に戻り、デプロイしたばかりのゲートウェイ ノードが正常な状態を報告していることを確認します。

![ゲートウェイの状態](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> <code>kubectl logs <gateway-pod-name></code> コマンドを使用して、セルフホステッド ゲートウェイ ログのスナップショットを表示します。

## <a name="next-steps"></a>次のステップ

* セルフホステッド ゲートウェイの詳細については、[Azure API Management のセルフホステッド ゲートウェイの概要](self-hosted-gateway-overview.md)に関する記事を参照してください
* [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes) の詳細を見る


