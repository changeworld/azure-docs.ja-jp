---
title: Azure Service Fabric Mesh アプリケーションのシークレットの格納と使用
description: Service Fabric Mesh では、Azure リソースとしてシークレットがサポートされています。 ここでは、Service Fabric Mesh アプリケーションを使用してシークレットを格納および管理する方法について説明します。
author: erikadoyle
ms.author: edoyle
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: da4eaa34840f14714616b85e24fd62cf65602b84
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277636"
---
# <a name="service-fabric-mesh-application-secrets"></a>Service Fabric Mesh アプリケーションのシークレット
Service Fabric Mesh では、Azure リソースとしてシークレットがサポートされています。 Service Fabric Mesh のシークレットには、格納や送信を安全に行う必要がある、機密性の高いテキスト情報を指定できます (ストレージ接続文字列やパスワードなど)。

![Mesh シークレットの概要][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Mesh シークレットのリソース
Mesh アプリケーションのシークレットは、次の要素で構成されます。
* **シークレット** リソース: シークレットのテキストを格納するコンテナーです。 **シークレット** リソース内に含まれている機密情報は、安全に格納、送信されます。
* 1 つ以上の**シークレット/値**リソース: **シークレット** リソース コンテナーに格納されます。 各**シークレット/値**リソースは、バージョン番号によって区別されます。

## <a name="next-steps"></a>次のステップ 
Service Fabric Mesh のシークレットについて詳しくは、以下の記事をご覧ください。
- [Azure Service Fabric Mesh アプリケーションのシークレットを管理する](service-fabric-mesh-howto-manage-secrets.md)
- [Service Fabric リソース モデルの概要](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
