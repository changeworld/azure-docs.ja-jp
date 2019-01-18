---
title: Azure Stack での正常性およびアラートの監視 | Microsoft Docs
description: Azure Stack で正常性およびアラートを監視する方法を説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: mabrigg
ms.openlocfilehash: 9a925a01cae75124dc56b0c2bc5cc931a6e04100
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53721616"
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>Azure Stack での正常性およびアラートの監視

*適用先:Azure Stack 統合システムと Azure Stack Development Kit*

Azure Stack には、ユーザーが Azure Stack リージョンの正常性とアラートを表示できるようにするインフラストラクチャ監視機能が含まれています。 この **[Region management]\(リージョン管理\)** タイルは、既定で既定プロバイダー サブスクリプションの管理者ポータルに固定されており、Azure Stack のデプロイされたすべてのリージョンを一覧表示します。 タイルには、アクティブな重大アラートおよび警告アラートの数がリージョンごとに表示されます。 このタイルは、Azure Stack の正常性とアラートの機能へのエントリ ポイントです。

![[Region Management] \(リージョン管理) タイル](media/azure-stack-monitor-health/image1.png)

## <a name="understand-health-in-azure-stack"></a>Azure Stack の正常性について

正常性リソース プロバイダーは、正常性とアラートを管理します。 Azure Stack のインフラストラクチャ コンポーネントは、Azure Stack のデプロイおよび構成時に正常性リソース プロバイダーに登録されます。 この登録により、各コンポーネントの正常性とアラートの表示が有効になります。 Azure Stack の正常性の概念は単純です。 コンポーネントの登録済みインスタンスのアラートが存在する場合、そのコンポーネントの正常性の状態は、アクティブなアラートの最大の重大度、つまり警告または重大を反映します。

## <a name="alert-severity-definition"></a>アラートの重大度の定義

Azure Stack では、アラートは **Warning** (警告) または **Critical** (重大) のどちらかの重大度でのみ生成されます。

- **Warning**  
  オペレーターは、予定された方法で警告アラートに対処できます。 アラートは通常、ユーザーのワークロードには影響を及ぼしません。

- **Critical**  
  オペレーターは、重大アラートには緊急で対応する必要があります。 これらは、現在または今後すぐに、Azure Stack ユーザーに影響を及ぼす問題です。


## <a name="view-and-manage-component-health-state"></a>コンポーネントの正常性状態の表示および管理

コンポーネントの正常性の状態は、管理者ポータルのほか、Rest API および PowerShell で確認できます。

ポータルで正常性の状態を表示するには、**[Region management]** \(リージョン管理) タイルで表示するリージョンをクリックします。 インフラストラクチャ ロールとリソース プロバイダーの正常性の状態を表示できます。

![インフラストラクチャ ロールのリスト](media/azure-stack-monitor-health/image2.png)

リソース プロバイダーまたはインフラストラクチャ ロールをクリックすると、詳細情報を表示できます。

> [!WARNING]  
> インフラストラクチャ ロールとロール インスタンスを順にクリックすると、**[Start]\(起動\)**、**[Restart]\(再起動\)**、または **[Shutdown]\(シャットダウン\)** のオプションが表示されます。 統合システムに更新プログラムを適用する場合は、これらのアクションを使用しないでください。 また、Azure Stack Development Kit 環境では、これらのオプションを使用**しない**でください。 これらのオプションは、インフラストラクチャ ロールあたり複数のロール インスタンスが存在する統合システム環境専用に設計されています。 開発キットでロール インスタンス (特に AzS-Xrp01) を再起動すると、システムが不安定になります。 トラブルシューティングの支援のため、問題を [Azure Stack フォーラム](https://aka.ms/azurestackforum)に投稿してください。
>

## <a name="view-alerts"></a>アラートを表示する

各 Azure Stack リージョンのアクティブなアラートのリストは、**[Region management]** \(リージョン管理) ブレードから直接使用できます。 既定の構成の最初のタイルは **[Alerts]** (アラート) タイルで、リージョンの重大アラートと警告アラートの概要が表示されます。 アラート タイルは、このブレードの他のタイルと同様、ダッシュボードに固定してすばやくアクセスできます。

![警告が表示された [Alerts] \(アラート) タイル](media/azure-stack-monitor-health/image3.png)

**[Alerts]\(アラート\)** タイルの最上部を選択することで、リージョンのアクティブな全アラートのリストに移動します。 タイル内の **[Critical]** \(重大) または **[Warning]** \(警告) 行項目のいずれかを選択した場合、フィルター処理されたアラート リスト ([Critical] \(重大) または [Warning] \(警告)) に移動します。 

**[Alerts]** \(アラート) ブレードは、状態 ([Active] \(アクティブ) または [Closed] \(終了)) と重大度 ([Critical] \(重大) または [Warning] \(警告)) の両方でフィルター処理する機能をサポートしています。 既定のビューには、すべてのアクティブなアラートが表示されます。 終了したアラートはすべて、7 日後にシステムから削除されます。

![重大または警告の状態に基づいてフィルター処理するフィルター ウィンドウ](media/azure-stack-monitor-health/alert-view.png)

また、**[View API]\(APIの表示\)** アクションには、リスト ビューの生成に使用された REST API が表示されます。 このアクションにより、アラートの照会に使用できる REST API 構文をすばやく理解できます。 この API は、自動化、または既存のデータ センターの監視、レポート、およびチケット発行ソリューションとの統合に使用できます。

特定のアラートをクリックすると、アラートの詳細を表示できます。 アラートの詳細は、アラートに関連付けられているすべてのフィールドを表示し、影響を受けるコンポーネントとアラートのソースにすばやく移動できるようにします。 たとえば、インフラストラクチャ ロール インスタンスの 1 つがオフラインになるか、アクセスできない場合、次のアラートが発生します。  

![[Alert Details]\(アラートの詳細\) ブレード](media/azure-stack-monitor-health/alert-detail.png)

## <a name="repair-alerts"></a>アラートの修復

一部のアラートでは、**[修復]** を選択できます。

選択すると、**[修復]** アクションによって、そのアラートに固有の手順が実行されて問題の解決が試みられます。 **[修復]** を選択すると、そのアクションの状態がポータルの通知として表示されます。

![修復が進行中](media/azure-stack-monitor-health/repair-in-progress.png)

同じポータル通知ブレードで、**[修復]** アクションの正常完了またはアクションの失敗がレポートされます。  アラートの修復アクションが失敗した場合は、アラートの詳細から **[修復]** アクションを再実行することができます。 **[修復]** アクションが正常に完了した場合は、再実行**しないでください**。

![修復が正常に完了](media/azure-stack-monitor-health/repair-completed.png)

インフラストラクチャ ロール インスタンスがオンラインに戻ると、このアラートは自動的に閉じます。 根本的な問題が解決されると、多くのアラートは自動的に閉じますが、すべてのアラートがそうなるわけではありません。 [修復] アクションのボタンが用意されているアラートは、Azure Stack によって問題が解決されると自動的に閉じます。  その他すべてのアラートについては、修復の手順を実行した後に、**[アラートを閉じる]** を選択してください。 問題が解決しなかった場合は、Azure Stack で新しいアラートが生成されます。 問題が解決した場合は、アラートが閉じたままとなり、それ以上の手順は不要となります。

## <a name="next-steps"></a>次の手順

[Azure Stack での更新の管理](azure-stack-updates.md)

[Azure Stack でのリージョンの管理](azure-stack-region-management.md)
