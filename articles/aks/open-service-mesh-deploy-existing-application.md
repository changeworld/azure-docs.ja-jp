---
title: アプリケーションを Open Service Mesh にオンボードする
description: アプリケーションを Open Service Mesh にオンボードする方法
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 202702623353462bafd77002662a35e7c7b7d2f8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066769"
---
# <a name="onboarding-applications-to-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Open Service Mesh (OSM) Azure Kubernetes Service (AKS) アドオンにアプリケーションをオンボードする

次のガイドでは、kubernetes マイクロサービスを OSM にオンボードする方法について説明します。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルで詳しく説明する手順では、AKS クラスターの OSM AKS アドオンを以前に有効にしたことを前提としています。 そうしていない場合、続行する前に、「[OSM AKS アドオンをデプロイする](./open-service-mesh-deploy-addon-az-cli.md)」の記事を確認してから、次に進みます。 また、AKS クラスターは Kubernetes バージョン `1.19+` 以降であること、Kubernetes RBAC が有効になっていること、クラスターとの `kubectl` 接続が確立されていること (これらの項目のいずれかについてのヘルプが必要な場合は、[AKS クイック スタート](./kubernetes-walkthrough.md)に関するページを参照してください)、および AKS OSM アドオンがインストールされていることが必要です。

次のリソースがインストールされている必要があります。

- Azure CLI バージョン 2.20.0 以降
- OSM アドオン バージョン v0.11.1 以降
- OSM CLI バージョン v0.11.1 以降

## <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>Open Service Mesh (OSM) の制限のないトラフィック モード ポリシーを確認する

OSM の制限のないトラフィック ポリシー モードは、[SMI](https://smi-spec.io/) トラフィック ポリシーの適用がバイパスされるモードです。 このモードの OSM では、サービス メッシュの一部であるサービスが自動的に検出され、これらのサービスと通信できるように各エンボイ プロキシ サイドカーのトラフィック ポリシー規則がプログラムされます。

クラスターの OSM の現在の制限のないトラフィック モードを確認するには、次のコマンドを実行します。

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o jsonpath='{.spec.traffic.enablePermissiveTrafficPolicyMode}{"\n"}'
true
```

**enablePermissiveTrafficPolicyMode** が **true** に構成されている場合は、サービス間の通信を中断することなく、名前空間を安全にオンボードできます。 **enablePermissiveTrafficPolicyMode** が **false** に構成されている場合は、正しい [SMI](https://smi-spec.io/) トラフィック アクセス ポリシー マニフェストがデプロイされていることを確認する必要があります。 また、名前空間にデプロイされている各サービスを表すサービス アカウントを持っていることを確認する必要もあります。 制限の少ないトラフィック モードの詳細については、[制限の少ないトラフィック ポリシー モード](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/)に関する記事を参照してください。

## <a name="onboard-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>Open Service Mesh (OSM) の制限のないトラフィック ポリシーを True に構成してアプリケーションをオンボードする

1. アプリケーションをオンボードする前に、[アプリケーション要件](https://release-v0-11.docs.openservicemesh.io/docs/guides/app_onboarding/prereqs/)ガイドを参照してください。

1. メッシュ内のアプリケーションが Kubernetes API サーバーと通信する必要がある場合、ユーザーは、IP 範囲の除外を使用するか、エグレス ポリシーを作成することにより、これを明示的に許可する必要があります。

1. Kubernetes 名前空間を OSM にオンボードします。

    OSM によって管理されるアプリケーションを含む名前空間をオンボードするには、`osm namespace add` コマンドを実行します。

    ```console
    $ osm namespace add <namespace>
    ```

    既定では、この `osm namespace add` コマンドにより、名前空間内のポッドの自動サイドカー インジェクションが有効になります。

    メッシュへの名前空間の登録の一部としての自動サイドカー インジェクションを無効にするには、`osm namespace add <namespace> --disable-sidecar-injection` を使用します。
    名前空間のオンボードが完了したら、自動サイドカー インジェクションを構成することで、ポッドをメッシュに登録できます。 詳細については、[サイドカー インジェクション](https://release-v0-11.docs.openservicemesh.io/docs/guides/app_onboarding/sidecar_injection/)に関するドキュメントを参照してください。

1.  新しいアプリケーションをデプロイするか、既存のアプリケーションを再デプロイします。

    既定では、オンボード名前空間の新しいデプロイでは、自動サイドカー インジェクションが有効になっています。 これは、マネージド名前空間に新しいポッドが作成されると、OSM が自動的にサイドカー プロキシをポッドに挿入することを意味します。
    ポッドの再作成時に OSM がサイドカー プロキシを自動的に挿入できるように、既存のデプロイを再起動する必要があります。 デプロイによって管理されるポッドは、`kubectl rollout restart deploy` コマンドを使用して再起動できます。

    プロトコル固有のトラフィックをサービス ポートに正しくルーティングするには、使用するアプリケーション プロトコルを構成します。 詳細については、「[アプリケーション プロトコルの選択」](https://release-v0-11.docs.openservicemesh.io/docs/guides/app_onboarding/app_protocol_selection/) を参照してください。


## <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>Open Service Mesh (OSM) の制限のないトラフィック ポリシーを False に構成して既存のデプロイ済みアプリケーションをオンボードする

制限のないトラフィック ポリシーの OSM 構成が `false` に設定されている場合、OSM では、クラスター内でサービス間の通信を行うために、明示的な [SMI](https://smi-spec.io/) トラフィック アクセス ポリシーがデプロイされている必要があります。 OSM は Kubernetes サービス アカウントを使用してメッシュ内のアプリケーション間のアクセス制御ポリシーを実装するので、アプリケーション間のトラフィック フローを承認するために、[SMI](https://smi-spec.io/) トラフィック アクセス ポリシーを適用します。

SMI ポリシーの例については、次の例を参照してください。
    - [demo/deploy-traffic-specs.sh](https://github.com/openservicemesh/osm/blob/release-v0.11/demo/deploy-traffic-specs.sh)
    - [demo/deploy-traffic-split.sh](https://github.com/openservicemesh/osm/blob/release-v0.11/demo/deploy-traffic-split.sh)
    - [demo/deploy-traffic-target.sh](https://github.com/openservicemesh/osm/blob/release-v0.11/demo/deploy-traffic-target.sh)


#### <a name="removing-namespaces"></a>名前空間の削除
`osm namespace remove` コマンドを使用して、OSM メッシュから名前空間を削除できます。

```console
$ osm namespace remove <namespace>
```

> [!NOTE]
>
> - **`osm namespace remove`** コマンドは、名前空間でのサイドカー プロキシ構成への更新の適用を停止するように OSM に指示するだけです。 プロキシ サイドカーは削除 **しません**。 つまり、既存のプロキシ構成は引き続き使用されますが、OSM コントロールプレーンによって更新されなくなります。 すべてのポッドからプロキシを削除する場合は、OSM LCI を使用してメッシュからポッドの名前空間を削除し、対応するポッドまたはデプロイを再デプロイします。
