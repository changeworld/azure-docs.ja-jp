---
title: "Azure の高可用性ポートの概要 | Microsoft Docs"
description: "内部 Load Balancer 上の高可用性ポートの負荷分散について説明します"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: kumud
ms.openlocfilehash: e72fc0d4323f7a2d203fee66311c3fea10ad7a09
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="high-availability-ports-overview-preview"></a>高可用性ポートの概要 (プレビュー)

Azure Load Balancer Standard には、内部ロード バランサーを使用するときに、すべてのポートで TCP フローと UDP フローの負荷分散を同時に行うための新しい機能が導入されています。 

>[!NOTE]
> 高可用性ポート機能は、Load Balancer Standard で利用できますが、現在プレビューの段階です。 プレビュー期間は、一般公開リリースの機能と同じレベルの可用性と信頼性がない場合があります。 詳細については、[Microsoft Azure プレビューのMicrosoft Azure 追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 Load Balancer Standard リソースで HA ポートを使用するには、Load Balancer Standard プレビューにサインアップする必要があります。 Load Balancer Standard プレビューと、サインアップの手順については、[こちら](https://aka.ms/lbpreview#preview-sign-up)を参照してください。

HA ポート ルールは、内部 Load Balancer Standard で構成されている負荷分散規則の一種です。  そのシナリオはシンプルで、1 つの LB ルールによって、内部 Load Balancer Standard フロントエンドのすべてのポートに到着した、すべての TCP および UDP フローの負荷が分散されます。 負荷分散は、ソース IP アドレス、ソース ポート、分散先 IP アドレス、分散先ポート、およびプロトコルの 5 つのタプルに基づいて、フローごとに決まります。

HA ポートにより、高可用性、仮想ネットワーク内のネットワーク仮想アプライアンス (NVA) のスケールなどの重要なシナリオや、多数のポートの負荷を分散する必要があるシナリオに対応することができます。 

HA ポートを構成するには、フロント エンド ポートとバックエンド ポートを **0** に、プロトコルを **All** に設定します。  内部 Load Balancer のリソースにより、ポート番号に関係なく、すべての TCP および UDP フローが負荷分散されるようになりました。

## <a name="why-use-ha-ports"></a>HA ポートを使用する理由

### <a name="nva"></a>ネットワーク仮想アプライアンス

ネットワーク仮想アプライアンス (NVA) により、さまざまなセキュリティの脅威から Azure ワークロードを保護できます。 こうしたシナリオで使用する NVA は、信頼性と可用性に優れ、必要に応じてスケールアウトできなければなりません。

シナリオでこのような目標を実現するには、NVA インスタンスを Azure 内部 Load Balancer のバックエンド プールに追加して、HA ポート Load Balancer ルールを構成するだけです。

NVA HA シナリオで HA ポートを使用するメリットは複数あります。
- インスタンスごとの正常性プローブで、正常なインスタンスに高速フェールオーバー
- n アクティブ インスタンスへのスケールアウトによるパフォーマンス向上
- n アクティブ シナリオとアクティブ/パッシブのシナリオ
- Zookeeper ノードのような複雑なアプライアンス監視ソリューションが不要

次の例は、ハブとスポークの仮想ネットワーク デプロイを示しています。スポークによってトラフィックをハブの仮想ネットワークへのトンネリングを強制し、NVA を経由してから、信頼される空間を離れます。 NVA は、HA ポート構成の内部 Load Balancer Standard の背後にあります。  これに従って、すべてのトラフィックが処理および転送されます。 

![HA ポートの例](./media/load-balancer-ha-ports-overview/nvaha.png)

図 1 - HA モードで NVA がデプロイされているハブとスポークの仮想ネットワーク

ネットワーク仮想アプライアンスを使用している場合は、HA ポートを最大限に活用する方法と、サポートされているシナリオについて、それぞれのプロバイダーにご確認ください。

### <a name="load-balancing-large-numbers-of-ports"></a>多数のポートの負荷分散

多数のポートで負荷分散を行う必要があるアプリケーション シナリオで、HA ポートを使用することもできます。 こうしたシナリオは、HA ポートが指定された内部 [Load Balancer Standard](https://aka.ms/lbpreview) を使用することで簡素化できます。この場合、適用される負荷分散規則は 1 つで、複数の負荷分散規則が、ポートごとに 1 つずつ個別に適用されることはなくなります。

## <a name="region-availability"></a>利用可能なリージョン

HA ポートは、[Load Balancer Standard と同じリージョン](https://aka.ms/lbpreview#region-availability)で利用できます。  

## <a name="preview-sign-up"></a>プレビューのサインアップ

Load Balancer Standard の HA ポート機能のプレビューに参加するには、Azure CLI 2.0 または PowerShell を使用してサブスクリプションを登録し、アクセスできるようにします。  次の 3 つの手順を実行してください。

>[!NOTE]
>この機能を使用するには、HA ポートに加えて、Load Balancer [Standard プレビュー](https://aka.ms/lbpreview#preview-sign-up)にもサインアップする必要があります。 HA ポートまたは Load Balancer Standard プレビューの登録には、最大で 1 時間ほどかかる場合があります。

### <a name="sign-up-using-azure-cli-20"></a>Azure CLI 2.0 を使用したサインアップ

1. 機能をプロバイダーに登録します
    ```cli
    az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
2. この操作は、完了するまで最大 10 分かかることがあります。  操作の状態は、次のコマンドで確認できます。

    ```cli
    az feature show --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
    次に示すように、機能の登録状態が "Registered" になったら、手順 3. に進んでください。
   
    ```json
    {
       "id": "/subscriptions/foo/providers/Microsoft.Features/providers/Microsoft.Network/features/AllowLBPreview",
       "name": "Microsoft.Network/AllowILBAllPortsRule",
       "properties": {
          "state": "Registered"
       },
       "type": "Microsoft.Features/providers/features"
    }
    ```
    
3. サブスクリプションをリソース プロバイダーに再登録して、プレビューのサインアップを完了してください。

    ```cli
    az provider register --namespace Microsoft.Network
    ```
    
### <a name="sign-up-using-powershell"></a>PowerShell を使用したサインアップ

1. 機能をプロバイダーに登録します
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    
2. この操作は、完了するまで最大 10 分かかることがあります。  操作の状態は、次のコマンドで確認できます。

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    次に示すように、機能の登録状態が "Registered" になったら、手順 3. に進んでください。
   
    ```
    FeatureName          ProviderName      RegistrationState
    -----------          ------------      -----------------
    AllowILBAllPortsRule Microsoft.Network Registered
    ```
    
3. サブスクリプションをリソース プロバイダーに再登録して、プレビューのサインアップを完了してください。

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```


## <a name="limitations"></a>制限事項

HA ポートでサポートされる構成または例外は次のとおりです。

- 1 つのフロントエンド IP 構成は、HA ポートが指定された 1 つの DSR Load Balancer ルールを持つことができます。または、HA ポートが指定された 1 つの DSR 以外の Load Balancer ルールを持つことができます。 両方を持つことはできません。
- 1 つのネットワーク インターフェイス IP 構成は、HA ポートが指定された DSR 以外のロード バランサー ルールを 1 つのみ持つことができます。 この ipconfig には、他のルールを構成できません。
- 個々のフロントエンド IP 構成のすべてが一意であれば、1 つのネットワーク インターフェイス IP 構成は、HA ポートが指定された 1 つまたは複数の DSR ロード バランサー ルールを持つことができます。
- すべての負荷分散規則が HA ポート (DSR のみ) の場合、またはすべての規則が HA ポート以外 (DSR および DSR 以外) の場合、同じバックエンド プールを示す複数の Load Balancer ルールが共存できます。 このような 2 つの負荷分散規則は、HA ポートと HA ポート以外のルールの組み合わせがある場合は共存できません。
- HA ポートは、IPv6 では使用できません。
- NVA シナリオのフロー対称は、単一の NIC のみでサポートされます。 [ネットワーク仮想アプライアンス](#nva)の説明と図を参照してください。 



## <a name="next-steps"></a>次のステップ

- [内部 Load Balancer Standard で HA ポートを構成する](load-balancer-configure-ha-ports.md)
- [Load Balancer Standard プレビューについて確認する](https://aka.ms/lbpreview)

