---
title: Azure Kubernetes Service にセルフホステッド ゲートウェイをデプロイする
description: Azure API Management のセルフホステッド ゲートウェイ コンポーネントを Azure Kubernetes Service にデプロイする方法について説明します
author: dlepow
manager: gwallace
ms.service: api-management
ms.topic: article
ms.date: 06/11/2021
ms.author: danlep
ms.openlocfilehash: 624229e947a872f4ea50c0a4e61e210fee9d946f
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129708427"
---
# <a name="deploy-to-azure-kubernetes-service"></a>Azure Kubernetes Service にデプロイする

この記事では、Azure API Management のセルフホステッド ゲートウェイ コンポーネントを [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) にデプロイする手順について説明します。 セルフホステッド ゲートウェイを Kubernetes クラスターにデプロイする場合は、[操作方法に関する記事](how-to-deploy-self-hosted-gateway-kubernetes.md)を参照してください。

> [!NOTE]
> また、セルフホステッド ゲートウェイを [Azure Arc 対応 Kubernetes クラスター](how-to-deploy-self-hosted-gateway-azure-arc.md)に[クラスター拡張機能](../azure-arc/kubernetes/extensions.md)としてデプロイすることもできます。

## <a name="prerequisites"></a>前提条件

- [Azure API Management インスタンスを作成する](get-started-create-service-instance.md)
- [Azure Kubernetes クラスターを作成する](../aks/kubernetes-walkthrough-portal.md)
- [API Management インスタンスにゲートウェイ リソースをプロビジョニングします](api-management-howto-provision-self-hosted-gateway.md)。

## <a name="deploy-the-self-hosted-gateway-to-aks"></a>AKS にセルフホステッド ゲートウェイをデプロイする

1. **[Deployment and infrastructure]\(デプロイとインフラストラクチャ\)** から **[ゲートウェイ]** を選択します。
2. デプロイするセルフホステッド ゲートウェイ リソースを選択します。
3. **[Deployment]/(デプロイ/)** を選択します。
4. 既定の **[有効期限]** および **[秘密鍵]** の値を使用して、 **[トークン]** テキスト ボックスに新しいトークンが自動生成されています。 必要に応じていずれかまたは両方を調整し、 **[生成]** を選択して新しいトークンを作成します。
5. **[デプロイ スクリプト]** で **[Kubernetes]** が選択されていることを確認します。
6. **[デプロイ]** の横にある **\<gateway-name\>.yml** ファイルのリンクを選択してファイルをダウンロードします。
7. 必要に応じて、yml ファイルの `config.service.endpoint`、ポート マッピング、コンテナー名を調整します。
8. シナリオによっては、[サービスの種類](../aks/concepts-network.md#services)の変更が必要になる場合があります。 
    * 既定値は `LoadBalancer` です。これは外部ロード バランサーです。 
    * [内部ロード バランサー](../aks/internal-lb.md)を使用すると、セルフホステッド ゲートウェイへのアクセスを内部ユーザーのみに制限できます。 
    * 下のサンプルでは `NodePort` が使用されています。
1. **[Deploy]/(デプロイ/)** テキストボックスの右端にある **[コピー]** アイコンを選択して、`kubectl` コマンドをクリップボードに保存します。
1. コマンドをターミナル (またはコマンド) ウィンドウに貼り付けます。 コマンドでは、ダウンロードした環境ファイルが現在のディレクトリに存在している必要があります。

   ```console
   kubectl apply -f <gateway-name>.yaml
   ```
   
1. コマンドを実行します。 コマンドは、AKS クラスターに次のことを指示します。
    * Microsoft Container Registry からダウンロードしたセルフホステッド ゲートウェイのイメージを使用して、コンテナーを実行する。 
    * HTTP (8080) および HTTPS (443) ポートを公開するようにコンテナーを構成する。
1. 次のコマンドを実行して、ゲートウェイ ポッドが実行中であることを確認します。 使用するポッド名は別のものになります。

   ```console
   kubectl get pods
   NAME                                   READY     STATUS    RESTARTS   AGE
   contoso-apim-gateway-59f5fb94c-s9stz   1/1       Running   0          1m
   ```

1. 次のコマンドを実行して、ゲートウェイ サービスが実行中であることを確認します。 使用するサービス名と IP アドレスは別のものになります。
    ```console
    kubectl get services
    NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
    contosogateway   NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
    ```
1. Azure portal に戻り、デプロイしたゲートウェイ ノードが正常な状態を報告していることを確認します。

> [!TIP]
> `kubectl logs <gateway-pod-name>` コマンドを使用して、セルフホステッド ゲートウェイ ログのスナップショットを表示します。

## <a name="next-steps"></a>次のステップ

* セルフホステッド ゲートウェイの詳細については、[Azure API Management のセルフホステッド ゲートウェイの概要](self-hosted-gateway-overview.md)に関するページを参照してください。
* [Azure Arc 対応 Kubernetes クラスターに API Management セルフホステッド ゲートウェイをデプロイする方法](how-to-deploy-self-hosted-gateway-azure-arc.md)について学習します。
* [Azure Kubernetes Service](../aks/intro-kubernetes.md) の詳細について学習します。
* [クラウドでログを構成して保持する方法](how-to-configure-cloud-metrics-logs.md)について学習します。
* [ローカルでログを構成して永続化する方法](how-to-configure-local-metrics-logs.md)について学習します。
