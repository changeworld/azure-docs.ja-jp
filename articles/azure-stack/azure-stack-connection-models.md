---
title: "Azure Stack 統合システムの接続モデル | Microsoft Docs"
description: "マルチノード Azure Stack のデプロイ計画の決定を確認します。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 0151b64b39699e2a7a804cdc57e368fd786119a7
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Azure Stack 統合システムの接続モデル
Azure Stack 統合システムに関心がある場合は、Azure Stack デプロイに関して、[データセンターの統合についてのいくつかの考慮事項](azure-stack-datacenter-integration.md)を理解し、このシステムがデータセンターにどのように適合するかを確認する必要があります。 さらに、Azure Stack をハイブリッド クラウド環境に統合する方法を正確に決定する必要があります。 この記事では、これらの主な決定 (Azure 接続、ID ストア、および課金モデルの決定を含む) の概要について説明します。

統合システムの購入を決定した場合、相手先ブランド供給 (OEM) ハードウェア ベンダーから計画プロセスの大部分について詳しく案内があります。 実際のデプロイもベンダーが実行します。

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Azure Stack デプロイの接続モデルを選択する
Azure Stack をインターネットに (および Azure に) 接続した状態でデプロイするか、接続していない状態でデプロイするかを選択できます。 Azure Stack と Azure 間のハイブリッド シナリオを含めて、Azure Stack から最大のメリットを得るには、Azure に接続した状態でのデプロイをお勧めします。 この選択では、次の図と表に要約されているように ID ストアとして使用可能なオプション (Azure Active Directory または Active Directory フェデレーション サービス) および課金モデル (従量制ベースの課金または容量ベースの課金) を定義します。 

![Azure Stack デプロイと課金のシナリオ](media/azure-stack-deployment-decisions/azure-stack-scenarios.png)   
  
> [!IMPORTANT]
> これは重要な意思決定のポイントです。 Active Directory フェデレーション サービス (AD FS) または Azure Active Directory (Azure AD) の選択は、デプロイ時に行う必要がある 1 回限りの決定です。 システム全体を再デプロイせずに、後でこれを変更することはできません。  


|オプション|Azure への接続|Azure からの切断|
|-----|-----|-----|
|Azure AD|![サポートされています](media/azure-stack-deployment-decisions/check.png)| |
|AD FS|![サポートされています](media/azure-stack-deployment-decisions/check.png)|![サポートされています](media/azure-stack-deployment-decisions/check.png)|
|消費ベースの課金|![サポートされています](media/azure-stack-deployment-decisions/check.png)| |
|容量ベースの課金|![サポートされています](media/azure-stack-deployment-decisions/check.png)|![サポートされています](media/azure-stack-deployment-decisions/check.png)|
|Azure Stack に更新プログラム パッケージを直接ダウンロード|![サポートされています](media/azure-stack-deployment-decisions/check.png)|  |

Azure Stack デプロイに使用される Azure 接続モデルを決定したら、ID ストアと課金方法に関する、接続に依存した追加の決定を行う必要があります。 

## <a name="next-steps"></a>次の手順

[Azure に接続された Azure Stack デプロイの決定](azure-stack-connected-deployment.md)

[Azure から切断された Azure Stack デプロイの決定](azure-stack-disconnected-deployment.md)
