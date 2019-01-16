---
title: Marketplace のプランを作成するために必要なさまざまなポータルの概要 | Microsoft Docs
description: Marketplace のプランを作成するために必要なさまざまなポータルの概要
services: marketplace-publishing
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: 89ce82b3-c28a-4b0d-b37a-db3112160a4e
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2016
ms.author: hascipio
ROBOTS: NOINDEX
ms.openlocfilehash: d1cc0efa1da90d90bd035eaa495a1336b6ff96c2
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54074191"
---
# <a name="portals-you-will-need"></a>必要なポータル

プランを発行するプロセスを開始する前に、必要な各種ポータルを紹介します。 以下に、デベロッパー センター、Azure 発行ポータル、および Azure portal の概要を、やり取りする順番で示します。                                                                            

## <a name="developer-center"></a>デベロッパー センター
[http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure)

### <a name="description"></a>説明
Microsoft デベロッパー センター アカウントの作成は、1 回限りのタスクです。 作成する前に、デベロッパー センター アカウントを会社が既に所有していないことを確認してください。 このプロセスでは、銀行口座の情報、税情報、および会社の住所情報が収集されます。

> [!NOTE]
> 無料のプランのみを発行する場合 (またはライセンス持ち込みの場合)、税情報と銀行情報は必要ありません。
> 
> 

### <a name="identityaccount-used"></a>使用する ID とアカウント
理想的には、この値は配布リストまたはセキュリティ グループです (たとえば、azurepublishing@*partnercompany*.com)。 配布リストまたはセキュリティ グループは、Microsoft アカウントとして登録する **必要があります** 。

> [!TIP]
> 個人に依存することがないように、配布リストまたはセキュリティ グループを使用することをお勧めします。ただし、個人アカウントも同様に使用できます。
> 
> 

## <a name="publishing-portal"></a>発行ポータル
[https://publish.windowsazure.com](https://publish.windowsazure.com)

### <a name="description"></a>説明

この値は、オファーを処理し、それを発行するポータルです (該当する場合、マーケティング、価格設定、発行、認定など)。

### <a name="identityaccount-used"></a>使用する ID とアカウント
発行ポータルに初めてサインインする際には、前述の配布リストまたはセキュリティ グループを使用する必要があります。 他のユーザーは後で共同管理者として追加できます。 この一覧のように、デベロッパー センターの登録データにマップされます。

## <a name="azure-portal"></a>Azure ポータル
[Azure Portal](https://portal.azure.com)

### <a name="description"></a>説明
この値は、Azure Marketplace でステージングされたオファーと発行されたオファーを表示できるポータルです (VM、ソリューション テンプレート、および Azure Resource Manager ベースの開発者サービスに適用されます)。

### <a name="identityaccount-used"></a>使用する ID とアカウント
発行ポータルからオファーをステージングしている間、サブスクリプション ID をホワイトリストに登録する必要があります。 このポータルにサインインして、ステージングされたオファーをテストする間、同じサブスクリプション (関連付けられたユーザー名とパスワードがあります) を使用する必要があります。

## <a name="see-also"></a>関連項目
* [概要: Azure Marketplace にオファーを発行する方法](marketplace-publishing-getting-started.md)

