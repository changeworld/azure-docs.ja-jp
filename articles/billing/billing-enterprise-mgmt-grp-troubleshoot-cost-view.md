---
title: "エンタープライズ コスト表示のトラブルシューティング - Azure | Microsoft Docs"
description: "Azure Portal で組織のコストを表示するときに発生しうる問題を解決する方法を説明します。"
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/22/2017
ms.author: rithorn
ms.openlocfilehash: 54c7610f1a0d3de2503ef471ca9adc0db423f530
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2018
---
# <a name="troubleshoot-enterprise-cost-views"></a>エンタープライズ コスト表示のトラブルシューティング 

エンタープライズ加入契約では、多くの設定において登録ユーザーがコストを表示できません。  これらの設定を管理するのは、登録管理者、または Microsoft と直接加入契約をしていない場合はパートナーです。  この記事では、設定の概要と、設定が登録に与える影響について説明します。 これらの設定は、[Azure RBAC ロール](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)には依存しません。 


## <a name="enabling-access-to-costs"></a>コストへのアクセスを有効にする

コストの情報を探しているときに、未承認のメッセージまたは "*登録ではコストのビューが無効になっています*" というメッセージが表示されますか。 ![未承認](media/billing-enterprise-mgmt-groups/unauthorized.png)

それには、次のいずれかの理由が考えられます。

1. エンタープライズ パートナーを通して Azure を購入し、そのパートナーがまだ料金を公開していない。 料金を公開するには、パートナーに問い合わせて[エンタープライズ ポータル](https://ea.azure.com)で設定を更新してもらってください。
2. または、EA の直接のお客様でない場合は、次の可能性が考えられます。
    * お客様はアカウント所有者であり、登録管理者が、"AO ビュー料金" 設定を無効にした。  
    * お客様は部門管理者者であり、登録管理者が、"DA ビュー料金" 設定を無効にした。
    * 登録管理者に問い合わせて、アクセスできるように設定してもらってください。 登録管理者は[エンタープライズ ポータル](https://ea.azure.com/manage/enrollment)にアクセスして、次のように設定を更新できます。

![エンタープライズ ポータル設定](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)


## <a name="asset-is-unavailable"></a>アセットを使用できない場合 
サブスクリプションまたは管理グループにアクセスしようとした際に "このアセットは使用できません" というエラー メッセージが表示された場合、この項目を表示するための正しいロールを持っていないということになります。  

![asset-not-found](media/billing-enterprise-mgmt-groups/asset-not-found.png)

アクセスを付与してもらうには、サブスクリプションまたは管理グループの管理者に問い合わせてください。  
* サブスクリプションの場合、[Azure ロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) に関するドキュメントをお読みになり、必要なロールの判断に役立ててください。
