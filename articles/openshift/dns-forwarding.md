---
title: Azure Red Hat OpenShift 4 の DNS 転送を構成する
description: Azure Red Hat OpenShift 4 の DNS 転送を構成する
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 6784c69c049945f3bd4977ecd647d57ec59bf9f1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232634"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>Azure Red Hat OpenShift 4 クラスターで DNS 転送を構成する

Azure Red Hat OpenShift クラスターで DNS 転送を構成するには、DNS オペレーターを変更する必要があります。 この変更によって、クラスター内で実行されているアプリケーション ポッドはクラスター外のプライベート DNS サーバーでホストされている名前を解決できます。 OpenShift 4.3 向けの手順は[こちら](https://docs.openshift.com/container-platform/4.3/networking/dns-operator.html)に記載されています。

たとえば、DNS サーバー 192.168.100.10 によって解決される *.example.com に対する DNS 要求をすべて転送する場合、次を実行してオペレーター構成を編集できます。
 
```bash
oc edit dns.operator/default
```
 
これによってエディターが起動します。`spec: {}` は次で置換できます。
 
 ```yaml
spec:
  servers:
  - forwardPlugin:
      upstreams:
      - 192.168.100.10
    name: example-dns
    zones:
    - example.com
```

ファイルを保存し、エディターを終了します。

## <a name="next-steps"></a>次のステップ
OpenShift 4.3 の DNS 転送に関する詳細は[こちら](https://docs.openshift.com/container-platform/4.3/networking/dns-operator.html)でご覧ください。