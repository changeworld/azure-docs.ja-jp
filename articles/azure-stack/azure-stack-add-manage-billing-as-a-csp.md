---
title: クラウド サービス プロバイダーとして Azure Stack の使用状況と課金を管理する | Microsoft Docs
description: クラウド プロバイダーとして Azure Stack を登録し、顧客を追加するためのチュートリアル。
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
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 23e3a675e6a464c92d26df220c8126c970f590a0
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2018
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>クラウド サービス プロバイダーとして Azure Stack の使用状況と課金を管理する 

*適用対象: Azure Stack 統合システム*

この記事では、クラウド プロバイダー (CSP) として Azure Stack を登録し、顧客を追加する方法について説明します。

CSP としては、Azure Stack を使用する多様な顧客を抱える可能性があります。 各顧客には Azure の CSP サブスクリプションかあり、CSP は Azure Stack の使用状況を各ユーザーのサブスクリプションに転送する必要があります。

次の図は、共有サービス アカウントを選択し、そのアカウントに Azure アカウントを登録する必要がある手順を示しています。 これが完了すると、最終顧客は利用開始が可能になります。

**CSP として使用状況の追跡を追加する手順**

![クラウド サービス プロバイダーとして使用状況と管理を有効にするためのプロセス。](media\azure-stack-add-manage-billing-as-a-csp\process-add-useage-as-a-csp.png)

## <a name="create-a-csp-or-cspss-subscription"></a>CSP または CSPSS サブスクリプションを作成する

### <a name="cloud-service-provider-subscription-types"></a>クラウド サービス プロバイダーのサブスクリプションの種類

Azure Stack に使用する共有サービス アカウントの種類を選択する必要があります。 マルチテナント Azure Stack の登録に使用できるサブスクリプションの種類は次のとおりです。

 - Cloud Service Provider 
 - Partner Shared Services サブスクリプション 

#### <a name="csp-shared-services"></a>CSP Shared Services

Cloud Service Provider Shared Services (CSPSS) サブスクリプションは、直接 CSP または CSP ディストリビューターが Azure Stack を操作するときに登録のために推奨される選択肢です。

CSPSS サブスクリプションは、共有サービス テナントに関連付けられています。 Azure Stack を登録する場合は、このサブスクリプションの所有者であるアカウントの資格情報を指定する必要があります。 Azure Stack を登録するために使用するアカウントは、デプロイに使用する管理者アカウントとは別のものにすることができます。この 2 つが同じドメインに属している必要は*ありません*。 つまり、既に使用しているテナントを使用してデプロイできます。 たとえば、ContosoCSP.onmicrosoft.com を使用した後、別のテナント (IURContosoCSP.onmicrosoft.com など) を使用して登録することが可能です。日常の Azure Stack 管理を実行する場合は、ContosoCSP.onmicrosoft.com を使用してログインするように注意する必要があります。 登録操作を実行する必要がある場合は、IURContosoCSP.onmicrosoft.com を使用して Azure にサインインします。

CSPSS サブスクリプションおよびサブスクリプションの作成方法については、[Azure Partner Shared Services の追加](https://msdn.microsoft.com/partner-center/shared-services)に関するページを参照してください。

#### <a name="csp-subscriptions"></a>CSP サブスクリプション

Cloud Service Provider (CSP) サブスクリプションは、CSP リセラーまたは最終顧客が Azure Stack を操作するときに登録のために推奨される選択肢です。

## <a name="register-azure-stack"></a>Azure Stack の登録

Azure Stack を登録するには、[Azure Stack の Azure サブスクリプションへの登録](azure-stack-registration.md)に関するページを参照してください。

## <a name="add-end-customer"></a>最終顧客を追加する

新しいテナントがリソースを使用すると、それらの使用状況がその Cloud Service Provider (CSP) サブスクリプションに報告されるように Azure Stack を構成するには、[Azure Stack への使用状況と課金のためのテナントの追加](azure-stack-csp-howto-register-tenants.md)に関するページを参照してください。

## <a name="charge-the-right-subscriptions"></a>正しいサブスクリプションに課金する

Azure Stack は、登録と呼ばれる機能を使用します。 登録とは、特定の Azure Stack に対して課金するためにどの Azure サブスクリプションを使用するかを記述する、Azure に格納されるオブジェクトです。 このセクションでは、登録の重要性について説明します。

登録を使用すると、Azure Stack は次のことが可能になります。
 - Azure Stack の使用状況データを Azure コマースに転送し、Azure サブスクリプションに課金する。
 - マルチテナント Azure Stack デプロイを使用して、各顧客の使用状況を異なるサブスクリプションに報告する。 マルチテナント機能を使用すると、Azure Stack は同じ Azure Stack インスタンス上の異なる組織をサポートできます。

Azure Stack ごとに、1 つの既定のサブスクリプションと、必要なだけの数のテナント サブスクリプションが存在します。 既定のサブスクリプションは、テナント固有のサブスクリプションが存在しない場合に課金される Azure サブスクリプションです。 これは最初に登録する必要があります。 マルチテナント使用状況レポートが機能するには、サブスクリプションは CSP または CSPSS サブスクリプションである必要があります。

その場合、登録は、Azure Stack を使用しようとしているテナントごとの Azure サブスクリプションで更新されます。 テナント サブスクリプションは種類が CSP であり、かつ既定のサブスクリプションを所有するパートナーにロールアップされる必要があります。 つまり、別のパートナーの顧客を登録することはできません。

Azure Stack が使用状況の情報をグローバル Azure に転送すると、Azure 内のサービスはその登録を調べ、各テナントの使用状況を適切なテナント サブスクリプションにマップします。 テナントがまだ登録されていない場合、その使用状況は、発信元の Azure Stack インスタンスの既定のサブスクリプションに転送されます。

テナント サブスクリプションは CSP サブスクリプションであるため、その課金は CSP パートナーに送信され、最終顧客には使用状況の情報が表示されません。



## <a name="next-steps"></a>次の手順

 - CSP プログラムの詳細については、[Cloud Solution Provider プログラム](https://partnercenter.microsoft.com/en-us/partner/programs)に関するページを参照してください。
 - リソース使用状況の情報を Azure Stack から取得する方法の詳細については、[Azure Stack での使用状況と課金](/azure-stack-billing-and-chargeback.md)に関するページを参照してください。
