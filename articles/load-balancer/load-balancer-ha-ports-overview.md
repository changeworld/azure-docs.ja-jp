---
title: "Azure の高可用性ポートの概要 | Microsoft Docs"
description: "内部ロード バランサーでの高可用性ポートの負荷分散について説明します。"
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
ms.openlocfilehash: 7a77e6ecbf59944c62aa4ae014bf5b8a5a7f7f1f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2017
---
# <a name="high-availability-ports-overview"></a>高可用性ポートの概要

内部ロード バランサーを使うときは、Azure Load Balancer Standard がすべてのポートで TCP フローと UDP フローの負荷分散を同時に行うのに役立ちます。 

>[!NOTE]
> Load Balancer Standard で利用できる高可用性 (HA) ポート機能は、現在プレビューの段階です。 プレビュー期間は、一般公開リリースの機能と同じレベルの可用性と信頼性がない場合があります。 詳細については、[Microsoft Azure プレビューのMicrosoft Azure 追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 Load Balancer Standard リソースで HA ポートを使うには、Load Balancer Standard プレビューにサインアップしてください。 Load Balancer Standard プレビューにサインアップする手順については、[こちら](https://aka.ms/lbpreview#preview-sign-up)をご覧ください。

HA ポート規則は負荷分散規則の一種であり、内部 Load Balancer Standard で構成されます。 単一の規則を提供することで、内部 Load Balancer Standard のすべてのポートに到着するすべての TCP および UDP フローが負荷分散されるので、Load Balancer を簡単に使うことができるようになります。 負荷分散の決定は、フローごとに行われます。 この決定は、送信元 IP アドレス、送信元ポート、送信先 IP アドレス、送信先ポート、プロトコルの、5 タプル接続に基づいて行われます。

HA ポート機能は、仮想ネットワーク内のネットワーク仮想アプライアンス (NVA) の高可用性と拡張性のような、重要なシナリオで役に立ちます。 また、多数のポートを負荷分散する必要がある場合にも役立ちます。 

HA ポート機能は、フロントエンド ポートとバックエンド ポートを **0** に、プロトコルを**すべて**に設定すると構成されます。 その後は、内部 Load Balancer リソースによって、ポート番号に関係なく、すべての TCP フローおよび UDP フローが負荷分散されます。

## <a name="why-use-ha-ports"></a>HA ポートを使う理由

### <a name="nva"></a>ネットワーク仮想アプライアンス

NVA を使うことで、さまざまなセキュリティの脅威から Azure ワークロードを保護できます。 こうしたシナリオで使われる NVA は、信頼性と可用性に優れ、必要に応じてスケールアウトする必要があります。

NVA インスタンスを Azure 内部 Load Balancer のバックエンド プールに追加して、HA ポート Load Balancer 規則を構成するだけで、このような目標を実現できます。

NVA HA シナリオで HA ポートを使うと複数のメリットがあります。
- インスタンスごとの正常性プローブによる、正常なインスタンスへの高速フェールオーバー
- *n* 個のアクティブなインスタンスへのスケールアウトによるパフォーマンス向上
- *N* アクティブ シナリオとアクティブ/パッシブのシナリオ
- アプライアンスを監視するために Apache Zookeeper ノードのような複雑なソリューションが不要

次の図は、ハブとスポークによる仮想ネットワークのデプロイを示したものです。 トラフィックは、スポークによってハブの仮想ネットワークに強制的にトンネリングされ、NVA を通過した後、信頼される空間を離れます。 NVA は、HA ポート構成の内部 Load Balancer Standard の背後にあります。 すべてのトラフィックを、適切に処理して転送できます。

![HA モードで NVA がデプロイされているハブとスポークの仮想ネットワークの図](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> NVA を使っている場合は、HA ポートを最大限に活用する方法と、サポートされているシナリオについて、それぞれのプロバイダーにご確認ください。

### <a name="load-balancing-large-numbers-of-ports"></a>多数のポートの負荷分散

多数のポートの負荷分散が必要なアプリケーションにも、HA ポートを使うことができます。 HA ポートで内部 [Load Balancer Standard](https://aka.ms/lbpreview) を使うことにより、これらのシナリオが簡単になります。 ポートごとに 1 つずつ、複数の負荷分散規則を使う代わりに、1 つの負荷分散規則で済みます。

## <a name="region-availability"></a>利用可能なリージョン

HA ポート機能は、[Load Balancer Standard と同じリージョン](https://aka.ms/lbpreview#region-availability)で利用できます。  

## <a name="preview-sign-up"></a>プレビューのサインアップ

Load Balancer Standard の HA ポート機能のプレビューに参加するには、サブスクリプションを登録し、アクセスできるようにします。 Azure CLI 2.0 または PowerShell を使うことができます。

>[!NOTE]
>この機能を使うには、HA ポート機能に加えて、Load Balancer [Standard プレビュー](https://aka.ms/lbpreview#preview-sign-up)にもサインアップする必要があります。 登録には最大 1 時間かかります。

### <a name="sign-up-by-using-azure-cli-20"></a>Azure CLI 2.0 を使用したサインアップ

1. 機能を次のようにプロバイダーに登録します。
    ```cli
    az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
2. この操作は、完了するまで最大 10 分かかることがあります。 操作の状態は、次のコマンドで確認できます。

    ```cli
    az feature show --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
    次に示すように、機能の登録状態が **Registered** になったら、操作は正常に行われました。
   
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
    
### <a name="sign-up-by-using-powershell"></a>PowerShell を使用したサインアップ

1. 機能を次のようにプロバイダーに登録します。
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    
2. この操作は、完了するまで最大 10 分かかることがあります。 操作の状態は、次のコマンドで確認できます。

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    次に示すように、機能の登録状態が **Registered** になったら、操作は正常に行われました。
   
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

HA ポート機能でサポートされる構成または例外は次のとおりです。

- 1 つのフロントエンド IP 構成では、HA ポートについての 1 つの DSR 負荷分散規則、または HA ポートについての 1 つの非 DSR 負荷分散規則のどちらかを使うことができます。 両方を持つことはできません。
- 1 つのネットワーク インターフェイス IP 構成では、HA ポートについての非 DSR 負荷分散規則を 1 つだけ使うことができます。 この ipconfig に対して他の規則を構成することはできません。
- 個々のフロントエンド IP 構成のすべてが一意であれば、1 つのネットワーク インターフェイス IP 構成で、HA ポートについての 1 つまたは複数の DSR ロード バランサー規則を使うことができます。
- すべての負荷分散規則が HA ポート (DSR のみ) の場合、同じバックエンド プールを示す複数の Load Balancer 規則を併用できます。 すべての規則が HA ポート以外 (DSR および DSR 以外) の場合も同様です。 ただし、HA ポート規則と非 HA ポート規則の組み合わせがある場合は、そのような 2 つの負荷分散規則を併用することはできません。
- HA ポート機能は、IPv6 では使うことができません。
- NVA シナリオのフロー対称は、単一の NIC でのみサポートされます。 [ネットワーク仮想アプライアンス](#nva)の説明と図をご覧ください。 



## <a name="next-steps"></a>次のステップ

- [内部 Load Balancer Standard で HA ポートを構成する](load-balancer-configure-ha-ports.md)
- [Load Balancer Standard プレビューについて確認する](https://aka.ms/lbpreview)

