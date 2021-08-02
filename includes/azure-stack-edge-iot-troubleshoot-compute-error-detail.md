---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 06/09/2021
ms.openlocfilehash: 86ccf634a9c33d3e8cfb8efd97e94f322fd5127f
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111987851"
---
### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>Compute modules have Unknown status and can't be used (コンピューティング モジュールの状態が不明であるため、使用できません)

#### <a name="error-description"></a>エラーの説明

デバイス上のすべてのモジュールは状態が不明と示されているため、使用できません。 不明の状態は再起動しても保持されます。<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>推奨されている解決方法

IoT Edge サービスを削除してから、モジュールを再デプロイしてください。 詳細については、「[IoT Edge サービスの削除](../articles/databox-online/azure-stack-edge-gpu-manage-compute.md#remove-iot-edge-service)」を参照してください。


### <a name="modules-show-as-running-but-are-not-working"></a>モジュールは実行中と表示されているが動作していない

#### <a name="error-description"></a>エラーの説明

モジュールのランタイムの状態は実行中と表示されていますが、予期された結果は表示されません。 

この状態の原因はモジュール ルート構成が機能していないためか、または `edgehub` が想定どおりにメッセージをルーティングしていない可能性があります。 `edgehub` ログを確認できます。 IoT Hub サービスに接続できないなどのエラーが表示される場合、最も一般的な理由は接続の問題です。 接続の問題は、IoT Hub サービスが既定の通信用ポートとして使用している AMPQ ポートがブロックされているか、Web プロキシ サーバーでこれらのメッセージがブロックされていることが原因である可能性があります。

#### <a name="suggested-solution"></a>推奨されている解決方法

次の手順を実行します。
1. このエラーを解決するには、デバイスの IoT Hub リソースにアクセスし、Edge デバイスを選択します。 
1. **[モジュールの設定] > [ランタイムの設定]** にアクセスします。 
1. `Upstream protocol` 環境変数を追加し、それに値 `AMQPWS` を割り当てます。 この場合に構成されるメッセージは、ポート 443 を介して Websocket 経由で送信されます。

### <a name="modules-show-as-running-but-do-not-have-an-ip-assigned"></a>モジュールは実行中と表示されているが IP が割り当てられていない

#### <a name="error-description"></a>エラーの説明

モジュールのランタイムの状態は実行中と表示されていますが、コンテナー化されたアプリに IP アドレスが割り当てられていません。 

この状態が起きる原因は、Kubernetes 外部サービス IP に指定した IP の範囲が十分でないことです。 デプロイした各コンテナーまたは VM が確実に対象として含まれるように、この範囲を拡張してください。

#### <a name="suggested-solution"></a>推奨されている解決方法

ご利用のデバイスのローカル Web UI で、次の手順を実行します。
1. **[コンピューティング]** ページにアクセスします。 コンピューティング ネットワークを有効にしたポートを選択します。 
1. **[Kubernetes external service IPs]\(Kubernetes 外部サービス IP\)** に IP の連続した静的な範囲を入力します。 `edgehub` サービスに 1 つの IP が必要です。 また、IoT Edge モジュールごと、およびデプロイする VM ごとに 1 つの IP が必要です。 
1. **[適用]** を選択します。 変更された IP 範囲は直ちに有効になります。

詳細については、「[コンテナーの外部サービス IP を変更する](../articles/databox-online/azure-stack-edge-gpu-manage-compute.md#change-external-service-ips-for-containers)」を参照してください。

### <a name="configure-static-ips-for-iot-edge-modules"></a>IoT Edge モジュールの静的 IP を構成する

#### <a name="problem-description"></a>問題の説明

Kubernetes では、Azure Stack Edge Pro GPU デバイス上の各 IoT Edge モジュールに動的 IP が割り当てられます。 モジュールの静的 IP を構成するにはメソッドが必要です。

#### <a name="suggested-solution"></a>推奨されている解決方法

下に示すように、K8s-experimental セクションを使用して、IoT Edge モジュールの固定 IP アドレスを指定できます。 

```yaml
{
  "k8s-experimental": {
    "serviceOptions" : {
      "loadBalancerIP" : "100.23.201.78",
      "type" : "LoadBalancer"
    }
  }
}
```
### <a name="expose-kubernetes-service-as-cluster-ip-service-for-internal-communication"></a>Kubernetes サービスを内部通信用のクラスター IP サービスとして公開する

#### <a name="problem-description"></a>問題の説明

既定では、IoT サービスの種類は **ロード バランサー** であり、外部に接続する IP アドレスがこのサービスに割り当てられます。 アプリケーションがクラスター内の他のポッドにアクセスするために Kubernetes クラスター内の Kubernetes ポッドを必要とする場合は、サービスをロード バランサー サービスでなく、クラスター IP サービスとして構成する必要がある場合があります。 詳細については、「[Azure Stack Edge Pro GPU デバイスでの Kubernetes ネットワーク](../articles/databox-online/azure-stack-edge-gpu-kubernetes-networking.md)」を参照してください。

#### <a name="suggested-solution"></a>推奨されている解決方法

K8s-experimental セクションを通じて作成オプションを使用できます。 次のサービス オプションはポートのバインドで機能します。

```yaml
{
"k8s-experimental": {
  "serviceOptions" : {
    "type" : "ClusterIP"
    }
  }
}
```