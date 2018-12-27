---
title: Azure Service Fabric リソース モデルの概要 | Microsoft Docs
description: Service Fabric Mesh アプリケーションの定義を簡易化した方法である Service Fabric リソース モデルについて説明します。
services: service-fabric-mesh
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 6a96995a46cd2ad0a1afe4f4afe49d62409f2b68
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075554"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Service Fabric リソース モデルの概要

Service Fabric リソース モデルには、Service Fabric Mesh アプリケーションを構成するリソースを定義する簡単な方法が示されています。 現在、このモデルでは次の種類のリソースがサポートされています。

- [アプリケーション]
- サービス
- ボリューム
- ネットワーク

各リソースは、リソース ファイルで宣言的に示されています。リソース ファイルは Mesh アプリケーションを示す簡単な YAML または JSON ドキュメントであり、Service Fabric プラットフォームによってプロビジョニングされます。

## <a name="resource-overview"></a>ソースの概要

### <a name="applications-and-services"></a>アプリケーションとサービス

アプリケーション リソースは、Mesh アプリケーションのデプロイ、バージョン管理、有効期間の単位です。 マイクロサービスを表す 1 つまたは複数のサービス リソースで構成されています。 さらに各サービス リソースは 1 つまたは複数のコード パッケージで構成されます。コード パッケージには、以下のようにコード パッケージに関連付けられたコンテナー イメージの実行に必要なすべてが示されています。

- コンテナー名、バージョン、およびレジストリ
- 各コンテナーに必要な CPU およびメモリ リソース
- ネットワーク エンドポイント
- 別のボリューム リソースを参照して、コンテナーでマウントされるボリューム。

サービス リソースの一部として定義されたすべてのコード パッケージは、グループとしてまとめてデプロイおよびアクティブ化されます。 また、サービス リソースには、実行するサービスのインスタンス数も示され、それが依存する他のリソース (ネットワーク リソースなど) を参照します。

Mesh アプリケーションが複数のサービスで構成されている場合、同じノード上で同時に動作することは保証されません。 また、アプリケーションのアップグレード中に、単一のサービスのアップグレードが失敗すると、すべてのサービスが以前のバージョンにロールバックされます。



```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

前述のように、各アプリケーション インスタンスのライフサイクルは個別に管理できます。 たとえば、1 つのアプリケーション インスタンスを他のアプリケーション インスタンスとは関係なくアップグレードすることができます。 通常、アプリケーションに含めるサービス数が多くなるほど、各サービスを個別に管理することが困難になるため、アプリケーション内のサービスはごく少数に抑えます。

### <a name="networks"></a>ネットワーク

ネットワーク リソースは、依存関係として参照している可能性があるアプリケーション リソースまたはサービス リソースとは関係なく、個別にデプロイできるリソースです。 ネットワーク リソースはアプリケーション用のプライベート ネットワークを作成するために使用されます。 異なるアプリケーションの複数のサービスは、同じネットワークに属することができます。

> [!NOTE]
> 現在のプレビューでは、アプリケーションとネットワーク間の 1 対 1 のマッピングのみをサポートしています。

### <a name="volumes"></a>ボリューム

ボリュームは、コンテナー インスタンス内にマウントされ、状態を保持するために使用できるディレクトリです。 ボリューム リソースは、ディレクトリのマウント方法とそのバッキング ストレージを示す宣言的な方法です。

## <a name="programming-models"></a>プログラミング モデル
サービス リソースは、リソースに関連付けられたコード パッケージで参照されているコンテナー イメージのみを実行する必要があります。 コンテナー内の任意のフレームワークを使用して、任意の言語で記述した任意のコードを実行できます。Service Fabric Mesh 固有の API を理解したり使用したりする必要はありません。 

Service Fabric Mesh の外部でもアプリケーション コードは移植性があり、サービスの実装に使用される言語やフレームワークに関係なく、アプリケーションのデプロイは一貫したままです。 アプリケーションが ASP.NET Core、Go、または一連のプロセスとスクリプトのいずれであっても、Service Fabric Mesh リソースのデプロイ モデルは変わりません。 

## <a name="deployment"></a>Deployment

Service Fabric Mesh にデプロイする場合、リソースは、HTTP または Azure CLI を介して Azure Resource Manager テンプレートとして Azure にデプロイされます。 


## <a name="next-steps"></a>次の手順 
Service Fabric Mesh の詳細については、以下の概要ページを参照してください。
- [Service Fabric Mesh の概要](service-fabric-mesh-overview.md)
