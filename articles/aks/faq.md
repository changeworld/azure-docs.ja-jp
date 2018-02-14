---
title: "Azure Container Service についてよく寄せられる質問"
description: "Azure Container Service についてよく寄せられる質問にお答えします。"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/01/2018
ms.author: nepeters
ms.openlocfilehash: 2b78479c257930669729a7781b3893b3e2064bab
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2018
---
# <a name="frequently-asked-questions-about-azure-container-service-aks"></a>Azure Container Service (AKS) についてよく寄せられる質問

この記事では、Azure Container Service (AKS) についてよく寄せられる質問にお答えします。

## <a name="which-azure-regions-will-have-azure-container-service-aks"></a>Azure Container Service (AKS) は、どの Azure リージョンで提供されますか? 

- カナダ中部 
- カナダ東部 
- 米国中央部 
- 米国東部 
- 東南アジア 
- 西ヨーロッパ 
- 米国西部 2 

## <a name="when-will-additional-regions-be-added"></a>その他のリージョンが追加されるのはいつ頃になりますか? 

その他のリージョンは、需要の増加に応じて追加されます。

## <a name="are-security-updates-applied-to-aks-nodes"></a>AKS ノードにセキュリティ更新プログラムは適用されますか? 

クラスターのノードには OS セキュリティ パッチが夜間スケジュールで適用されますが、再起動は実行されません。 必要に応じてポータルまたは Azure CLI からノードを再起動してください。 クラスターをアップグレードすると、最新の Ubuntu イメージが使用され、すべてのセキュリティ パッチ (再起動も含む) が適用されます。

## <a name="do-you-recommend-customers-use-acs-or-akss"></a>ACS または AKS を使用することを Microsoft は推奨していますか? 

今後 Azure Container Service (AKS) が GA になったら、PoC、開発用、テスト用のクラスターについては AKS に、運用クラスターについては ACS-Kubernetes に構築することを推奨することになります。  

## <a name="when-will-acs-be-deprecated"></a>ACS が非推奨化される時期を教えてください。 

ACS は、AKS が GA になるころに非推奨化されます。 その日付から、クラスターを AKS に移行するための期間として 12 か月が設けられます。 この 12 か月間は、ACS のすべての操作を実行することができます。

## <a name="does-aks-support-node-autoscaling"></a>AKS はノードの自動スケールをサポートしていますか? 

ノードの自動スケールはサポートされていませんが、ロードマップには含まれています。 オープン ソースによる[自動スケールの実装][auto-scaler]をご覧ください。

## <a name="why-are-two-resource-groups-created-with-aks"></a>AKS と一緒にリソース グループが 2 つ作成されるのはなぜでしょうか? 

2 つ目のリソース グループは、AKS のデプロイに関連したすべてのリソースを簡単に削除できるようにすることを目的として自動的に作成されます。

## <a name="is-azure-key-vault-integrated-with-aks"></a>AKS には Azure Key Vault が統合されているのですか? 

いいえ。ただし、統合する計画はあります。 その間は、[Hexadite][hexadite] から提供されているソリューションを利用するなどしてください。 

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent  