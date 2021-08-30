---
title: Azure に Affirmed Private Network Service をデプロイする
description: Azure に Affirmed Private Network Service ソリューションをデプロイする方法について説明します。
author: KumudD
ms.service: private-multi-access-edge-compute-mec
ms.topic: how-to
ms.date: 06/16/2021
ms.author: hollycl
ms.openlocfilehash: d3c027a7d4006a947f520dee8406e40a30f1b304
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2021
ms.locfileid: "112458454"
---
# <a name="deploy-affirmed-private-network-service-on-azure"></a>Azure に Affirmed Private Network Service をデプロイする

この記事では、Microsoft Azure Marketplace から Azure Stack Edge デバイスに Affirmed Private Network Service (APNS) ソリューションをデプロイするプロセスについて概説します。

次の図は、デプロイに必要なリソースを含む、Affirmed Private Network Service のシステム アーキテクチャを示したものです。

![Affirmed Private Network Service のデプロイ](media/deploy-affirmed-private-network-service/deploy-affirmed-private-network-service.png)

## <a name="collect-required-information"></a>必要な情報を収集する

APNS をデプロイするには、次のリソースが必要です。

- 構成済みの Azure Network Function Manager (Azure Stack Edge デバイスのデジタル ツインとしての役割を果たすデバイス オブジェクト)。 

- 完全にデプロイされた Azure Stack Edge と NetFoundry VM。 

- Affirmed Management Systems VM Offer と APNS マネージド アプリケーションのサブスクリプションの承認。 

- アクティブなサブスクリプションが含まれる Azure アカウントと次の要素へのアクセス。  

    - リソース グループの組み込みの **所有者** ロール。 

    - サブスクリプションの組み込み **マネージド アプリケーション共同作成者** ロール。 

    - 参加する仮想ネットワークとサブネット (tcp/443 と tcp/8443 ポートを開放)。 

    - 仮想サブネット上の 5 つの IP アドレス。 

    - Affirmed Release Engineering によって提供される有効な SAS トークン。  

    - デプロイ中にプログラムする管理ユーザー名とパスワード。 
    
## <a name="deploy-apns"></a>APNS をデプロイする

すべての必要なリソースとそれに関連して必要な情報と共に APNS マネージド アプリケーションを自動的にデプロイするには、Microsoft Azure Marketplace から [APNS Managed Application]\(APNS マネージド アプリケーション\) を選択します。 APNS をデプロイすると、必要なリソースがすべて自動的に作成されてマネージド リソース グループに追加されます。

APNS をデプロイするには、次の手順を実行します。
1.  Azure portal を開き、 **[リソースの作成]** を選択します。
2.  検索バーに「*APNS*」と入力し、Enter キーを押します。
3.  **[View Private Offers]\(プライベート プランの表示\)** を選択します。 
    > [!NOTE]
    > **[View Private Offers]\(プライベート プランの表示\)** を選択するまで、APNS マネージド アプリケーションは表示されません。
4.  **[Private Offer]\(プライベート プラン\)** のドロップダウン メニューから **[作成]** を選択し、デプロイするオプションを選択します。
5.  アプリケーションのセットアップとネットワーク設定を完了し、確認して作成します。
6.  **[デプロイ]** を選択します。

## <a name="next-steps"></a>次のステップ

- APNS をデプロイして、Azure Stack Edge デバイスで NetFoundry の設定を構成する具体的な手順については、[Affirmed Private Network Service デプロイ ガイド](https://go.microsoft.com/fwlink/?linkid=2165732)を参照してください。
- プライベート モバイル コア ネットワークのデプロイ、監視、管理にオペレーターが使用するプログラマティックな GUI 駆動のポータルについては、[Affirmed Private Network Service マネージャー ユーザー ガイド](https://go.microsoft.com/fwlink/?linkid=2165932)を参照してください。
- [Azure 上の Affirmed Private Network Service](affirmed-private-network-service-overview.md) について詳細を確認します。
