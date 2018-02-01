---
title: "App Services の worker ロールをスケールアウトする - Azure Stack | Microsoft Docs"
description: "Azure Stack App Services の詳細なガイダンス"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: slinehan
editor: anwestg
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: anwestg
ms.openlocfilehash: a9be9011062f07d59842d417bf6761ec81c39275
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>Azure Stack 上の App Service: インフラストラクチャまたは worker ロールを追加する
*適用先: Azure Stack 統合システムと Azure Stack Development Kit*  

ここでは、Azure Stack インフラストラクチャおよび worker ロールでの App Service を拡大縮小する手順について説明します。 任意のサイズのアプリケーションをサポートする追加の worker ロールを作成する手順について説明します。

> [!NOTE]
> Azure Stack 環境の RAM が 96 GB 以下の場合、容量の追加が困難な可能性があります。

既定では、Azure Stack 上の App Service は無料で共有の worker 階層をサポートしています。 他の worker 階層を追加するには、worker ロールを追加する必要があります。

既定の Azure Stack 上の App Service インストールでデプロイされる内容がわからない場合は、「[App Service on Azure Stack overview](azure-stack-app-service-overview.md)」(Azure Stack 上の App Service の概要) を参照してください。

Azure Stack 上の Azure App Service では、仮想マシン スケール セットを使用するすべてのロールをデプロイします。そのため、このワークロードの拡張機能を活用します。 したがって、worker 階層のすべてのスケーリングは、App Service 管理者を通して行われます。

[App Service Resource Provider Admin]\(App Service リソース プロバイダー管理\) 内から worker を直接追加します。

1. Azure Stack 管理ポータルにサービス管理者としてログインします。

2. **[App Services]** を参照します。

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. **[ロール]** をクリックします。 デプロイされているすべての App Service ロールの内訳が表示されます。

4. 拡大縮小する種類の行を右クリックし、**[ScaleSet]** をクリックします。

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. **[拡大縮小]** をクリックし、拡大縮小するインスタンス数を選択し、**[保存]** をクリックします。

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. Azure Stack 上の App Service によって、VM が追加および構成され、必要なすべてのソフトウェアがインストールされ、プロセスの完了時には準備完了とマークされます。 このプロセスには約 80 分かかる可能性があります。

7. **[ロール]** ブレードで worker を表示することで、新しいロールの準備状況を監視できます。

完全にデプロイされ、準備ができると、worker をユーザーが利用できるようになり、それらにワークロードを展開できます。 既定で使用できる複数の価格レベルの例を次に示します。 特定の worker 階層に使用できる worker がない場合、対応する価格レベルを選択するオプションは使用できません。

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> 管理、フロントエンドまたはパブリッシャー ロール追加をスケールアウトするには、対応する VM スケール セットをスケールアウトする必要があります。 これらのロールのスケールアウト機能は、App Service 管理の将来のリリースで追加されます。

管理、フロントエンド、またはパブリッシャー ロールをスケール アウトするには、適切なロールの種類を選択して同じ手順を実行してください。 コントローラーはスケール セットとしてデプロイされていないため、すべての運用デプロイでインストール時に 2 つをデプロイする必要があります。

### <a name="next-steps"></a>次の手順

[デプロイ ソースを構成する](azure-stack-app-service-configure-deployment-sources.md)
