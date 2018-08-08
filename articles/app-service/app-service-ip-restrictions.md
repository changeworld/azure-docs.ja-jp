---
title: Azure App Service の IP 制限 | Microsoft Docs
description: Azure App Service で IP 制限を使用する方法
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 7/30/2018
ms.author: ccompy
ms.openlocfilehash: fb26d91ae772c4da1055da80366d6e8c6b80a6ac
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364310"
---
# <a name="azure-app-service-static-ip-restrictions"></a>Azure App Service 静的 IP 制限 #

IP 制限を使用すると、アプリへのアクセスを許可されている IP アドレスの優先度順の許可/拒否リストを定義できます。 許可リストには、IPv4 アドレスと IPv6 アドレスを含めることができます。 1 つまたは複数のエントリがある場合、リストの最後にあるものはすべて暗黙的に拒否されます。 

IP 制限機能は、App Service でホストされているすべてのワーク ロードに対して動作します。これには、Web アプリ、API アプリ、Linux アプリ、Linux コンテナー アプリ、および関数が含まれます。 

アプリに要求が行われると、発信元 IP アドレスが IP 制限リストと比較されて評価されます。 アドレスがリスト内の規則に基づいてアクセスを許可されない場合、サービスは [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 状態コードで応答します。

IP 制限機能は、コードが実行される worker ホストの上流にある App Service フロントエンド ロールに実装されています。 そのため、IP 制限は、事実上のネットワーク ACL です。  

![IP 制限のフロー](media/app-service-ip-restrictions/ip-restrictions-flow.png)

ポータルの IP 制限機能は、一時、IIS の ipSecurity 機能の上にあるレイヤーでした。 現在の IP 制限機能は異なります。 アプリケーションの web.config 内で ipSecurity を構成することはできますが、トラフィックが IIS に到達する前に、フロントエンドベースの IP 制限規則が適用されます。

## <a name="adding-and-editing-ip-restriction-rules-in-the-portal"></a>ポータルでの IP 制限規則の追加および編集 ##

IP 制限の規則をアプリに追加するには、メニューを使って **[ネットワーク]**>**[IP 制限]** の順にクリックし、**[IP 制限の構成]** をクリックします。

![App Service のネットワーク オプション](media/app-service-ip-restrictions/ip-restrictions.png)  

[IP 制限] UI では、アプリに対して定義されている IP 制限規則の一覧を確認できます。

![IP 制限を一覧表示する](media/app-service-ip-restrictions/ip-restrictions-browse.png)

規則がこの画像のように構成されている場合、アプリは 131.107.159.0/24 からのトラフィックのみを受け入れ、それ以外の IP アドレスからのトラフィックを拒否します。

**[[+] 追加]** をクリックすると、新しい IP 制限の規則を追加できます。 規則は、追加するとすぐに有効になります。 規則は、最も小さい数字から始まり大きい数字に向かって、優先順位順に適用されます。 追加した規則が 1 つであっても、暗黙の "すべて拒否" 規則があります。 

![IP 制限規則を追加する](media/app-service-ip-restrictions/ip-restrictions-add.png)

IP アドレスの表記には、IPv4 アドレスと IPv6 アドレスのどちらの場合も CIDR 表記を使用する必要があります。 正確なアドレスを指定するには、1.2.3.4/32 のようなアドレスを使用できます。この場合、最初の 4 オクテットが IP アドレスを表し、/32 がマスクです。 すべてのアドレスを表す IPv4 CIDR 表記は 0.0.0.0/0 です。 CIDR 表記の詳細については、「[Classless Inter-Domain Routing (クラスレス ドメイン間ルーティング)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)」を参照してください。  

任意の行をクリックして、既存の IP 制限規則を編集できます。 優先順位の変更を含め、編集内容はすぐに有効になります。

![IP 制限規則を編集する](media/app-service-ip-restrictions/ip-restrictions-edit.png)

規則を削除するには、規則の **[...]** をクリックし、**[削除]** をクリックします。 

![IP 制限規則を削除する](media/app-service-ip-restrictions/ip-restrictions-delete.png)

## <a name="programmatic-manipulation-of-ip-restriction-rules"></a>IP 制限規則のプログラムによる操作 ##

現在のところ、新しい IP 制限機能向けの CLI または PowerShell はありませんが、Resource Manager を使用したアプリ構成で PUT 操作を使用して値を手動で設定できます。 たとえば、resources.azure.com を使用して ipSecurityRestrictions ブロックを編集して、必要な JSON を追加することができます。 

Resource Manager におけるこの情報の場所は次のとおりです。

management.azure.com/subscriptions/**<サブスクリプション ID>**/resourceGroups/**<リソース グループ>**/providers/Microsoft.Web/sites/**<Web アプリ名>**/config/web?api-version=2018-02-01

前の例の JSON 構文を次に示します。

    "ipSecurityRestrictions": [
      {
        "ipAddress": "131.107.159.0/24",
        "action": "Allow",
        "tag": "Default",
        "priority": 100,
        "name": "allowed access"
      }
    ],
