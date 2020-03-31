---
title: 正常性プローブを AKS ポッドに追加する
description: この記事では、Application Gateway を使用して AKS ポッドに正常性プローブ (readiness および/または liveness) を追加する方法について説明します。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5d0543a3a43d53e462a6406312faddf37d2653c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795595"
---
# <a name="add-health-probes-to-your-service"></a>正常性プローブをサービスに追加する
既定では、イングレス コントローラーでは、公開されているポッドに対して HTTP GET プローブをプロビジョニングします。
プローブ プロパティをカスタマイズするには、[readiness または liveness probe](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) を `deployment`/`pod` の仕様に追加します。

## <a name="with-readinessprobe-or-livenessprobe"></a>`readinessProbe` または `livenessProbe` を使用する
```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aspnetapp
spec:
  replicas: 3
  template:
    metadata:
      labels:
        service: site
    spec:
      containers:
      - name: aspnetapp
        image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          periodSeconds: 3
          timeoutSeconds: 1
```

Kubernetes API リファレンス:
* [コンテナー プローブ](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [HttpGet アクション](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe` と `livenessProbe` は、`httpGet` を利用して構成されている場合に、サポートされます。
> * ポッドに公開されているポート以外でのプローブは、現在サポートされていません。
> * `HttpHeaders`、`InitialDelaySeconds`、`SuccessThreshold` はサポートされていません。

##  <a name="without-readinessprobe-or-livenessprobe"></a>`readinessProbe` または `livenessProbe` を使用しない
上記のプローブが指定されなかった場合、イングレス コントローラーでは、`Path` 注釈に対して指定された `backend-path-prefix` またはサービスの `path` 定義内に指定された `ingress` にサービスが到達できることを想定します。

## <a name="default-values-for-health-probe"></a>正常性プローブの既定値
readiness/liveness probe によって推定できないプロパティにはすべて、既定値が設定されます。

| Application Gateway プローブのプロパティ | Default value |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |