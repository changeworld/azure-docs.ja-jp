---
title: Application Gateway で Cookie ベースのアフィニティを有効にする
description: この記事では、アプリケーション ゲートウェイで Cookie ベースのアフィニティを有効にする方法について説明します。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: adacd805e736524fd7956c4bbc0ad402980b4cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795992"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>アプリケーション ゲートウェイで Cookie ベースのアフィニティを有効にする
[Azure Application Gateway のドキュメント](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings)に示されているように、Application Gateway では Cookie ベースのアフィニティがサポートされています。これは、あるユーザー セッションからの後続のトラフィックを同じサーバーに送信して処理できるということです。

## <a name="example"></a>例
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```
