---
title: Application Gateway で Cookie ベースのアフィニティを有効にする
description: この記事では、アプリケーション ゲートウェイで Cookie ベースのアフィニティを有効にする方法について説明します。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: cb1a403888f8dca78d1af7395095aa2bac39935e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73510512"
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
