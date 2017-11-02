---
title: "Azure Web アプリを別のリソース グループに移動する |Microsoft ドキュメント"
description: "Web アプリおよびアプリ サービスを一つのリソース グループから他のリソース グループに移動するシナリオについて記述しています。"
services: app-service
documentationcenter: 
author: ZainRizvi
manager: erikre
editor: 
ms.assetid: 22f97607-072e-4d1f-a46f-8d500420c33c
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: zarizvi
ms.openlocfilehash: 4d5d6540dd0b51a4f172649a1fd9a2343efeceee
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2017
---
# <a name="move-an-azure-web-app-to-another-resource-group"></a>Azure Web アプリを別のリソース グループに移動する
Web アプリまたはその関連リソース (あるいはその両方) を、同じサブスクリプション内の別のリソース グループや、別のサブスクリプション内のリソース グループに移動できます。 これは Azure での標準のリソース管理の一環として行われます。 詳細については、[新しいサブスクリプションまたはリソース グループへの Azure リソースの移動](../azure-resource-manager/resource-group-move-resources.md)に関する記事をご覧ください。

## <a name="limitations-when-moving-within-the-same-subscription"></a>同じサブスクリプション内で移動する場合の制限

Web アプリを_同じサブスクリプション内_で移動する場合には、アップロードした SSL 証明書は移動できません。 ただし、アップロードした SSL 証明書を移動せずに Web アプリを新しいリソース グループに移動することはできます。その場合でも、アプリの SSL 機能は引き続き機能します。 

SSL 証明書を Web アプリと共に移動したい場合は、次の手順に従います。

1.  アップロードした証明書を Web アプリから削除します。
2.  Web アプリを移動します。
3.  移動した Web アプリに証明書をアップロードします。

## <a name="limitations-when-moving-across-subscriptions"></a>サブスクリプション間で移動する場合の制限

Web App を_サブスクリプション間_で移動する場合には、次の制限事項が適用されます。

- 移動先のリソース グループに既存の App Service リソースが含まれていてはいけません。 App Service リソースには次のものがあります。
    - Web Apps
    - App Service プラン
    - アップロードまたはインポートした SSL 証明書
    - App Service Environment
- リソース グループ内のすべての App Service リソースを一緒に移動する必要があります。
- App Service リソースは、最初に作成されたときのリソース グループからのみ移動できます。 App Service リソースが元のリソース グループから移動されている場合は、まず元のリソース グループに戻してから、サブスクリプション間の移動を行うことができます。 
