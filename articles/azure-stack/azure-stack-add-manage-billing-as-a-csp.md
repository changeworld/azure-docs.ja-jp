---
title: クラウド サービス プロバイダーとして Azure Stack の使用状況と課金を管理する | Microsoft Docs
description: クラウド プロバイダー (CSP) として Azure Stack を登録し、課金のために顧客を追加するためのチュートリアル。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 1ba3697b5c683ed2e892396db71328e0ed41fdce
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56266919"
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>クラウド サービス プロバイダーとして Azure Stack の使用状況と課金を管理する

*適用対象: Azure Stack 統合システム*

この記事では、クラウド プロバイダー (CSP) として Azure Stack を登録し、顧客を追加する方法について説明します。

CSP は、Azure Stack を使用して多様な顧客を操作します。 各顧客には Azure に CSP サブスクリプションがあります。 Azure Stack から使用状況を各ユーザー サブスクリプションに転送する必要があります。

次の図は、共有サービス アカウントを選択し、その Azure アカウントを Azure Stack アカウントに登録するために必要な手順を示しています。 登録されると、エンド カスタマーによる利用を開始できます。

[![クラウド サービス プロバイダーとして使用状況と管理を有効にするためのプロセス](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png "クラウド サービス プロバイダーとして使用状況と管理を有効にするためのプロセス") ](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png#lightbox)

## <a name="create-a-csp-or-apss-subscription"></a>CSP または APSS サブスクリプションを作成する

### <a name="cloud-service-provider-subscription-types"></a>クラウド サービス プロバイダーのサブスクリプションの種類

Azure Stack で使用する共有サービス アカウントの種類を選択します。 マルチテナント Azure Stack の登録に使用できるサブスクリプションの種類は次のとおりです。

- Cloud Service Provider
- Partner Shared Services サブスクリプション

#### <a name="azure-partner-shared-services"></a>Azure Partner Shared Services

Azure Partner Shared Services (APSS) サブスクリプションは、直接 CSP または CSP ディストリビューターが Azure Stack を運用するときに登録のために推奨される選択肢です。

APSS サブスクリプションは、共有サービス テナントに関連付けられています。 Azure Stack を登録する場合は、このサブスクリプションの所有者であるアカウントの資格情報を指定します。 Azure Stack を登録するために使用するアカウントは、デプロイに使用する管理者アカウントとは別のものにすることができます。 さらに、2 つのアカウントは同じドメインに属している必要はありません。既に使用しているテナントを使用してデプロイできます。 たとえば、`ContosoCSP.onmicrosoft.com` を使用するときに、別のテナント (例: `IURContosoCSP.onmicrosoft.com`) を使用して登録できます。 毎日の Azure Stack 管理を実行するときに、`ContosoCSP.onmicrosoft.com` を使用してサインインする必要があります。 登録操作を実行する必要がある場合は、`IURContosoCSP.onmicrosoft.com` を使用して Azure にサインインします。

APSS サブスクリプションとその作成方法については、「[Add Azure Partner Shared Services (Azure Partner Shared Services の追加)](https://msdn.microsoft.com/partner-center/shared-services)」を参照してください。

#### <a name="csp-subscriptions"></a>CSP サブスクリプション

Cloud Service Provider (CSP) サブスクリプションは、CSP リセラーまたはエンド カスタマーが Azure Stack を運用するときに登録のために推奨される選択肢です。

## <a name="register-azure-stack"></a>Azure Stack の登録

前のセクションの情報に従って作成した APSS サブスクリプションを使用して、Azure Stack を Azure に登録します。 詳細については、[Azure サブスクリプションを使用した Azure Stack の登録](azure-stack-registration.md)に関するページを参照してください。

## <a name="add-end-customer"></a>最終顧客を追加する

新しいテナントがリソースを使用すると、それらの使用状況がその Cloud Service Provider (CSP) サブスクリプションに報告されるように Azure Stack を構成するには、[Azure Stack での使用状況と課金のためのテナントの追加](azure-stack-csp-howto-register-tenants.md)に関するページを参照してください。

## <a name="charge-the-right-subscriptions"></a>正しいサブスクリプションに課金する

Azure Stack は、登録と呼ばれる機能を使用します。 登録は、Azure に格納されているオブジェクトです。 特定の Azure Stack に課金するために Azure サブスクリプションによって使用される、登録オブジェクト ドキュメント。 このセクションでは、登録の重要性について説明します。

登録を使用すると、Azure Stack は次のことが可能になります。

- Azure Stack の使用状況データを Azure コマースに転送し、Azure サブスクリプションに課金する。
- マルチテナント Azure Stack デプロイを使用して、各顧客の使用状況を異なるサブスクリプションに報告する。 マルチテナント機能を使用すると、Azure Stack は同じ Azure Stack インスタンス上の異なる組織をサポートできます。

Azure Stack ごとに、1 つの既定のサブスクリプションと、複数のテナント サブスクリプションが存在します。 既定のサブスクリプションは、テナント固有のサブスクリプションが存在しない場合に課金される Azure サブスクリプションです。 これは登録される最初のサブスクリプションである必要があります。 マルチテナント使用状況レポートが機能するには、サブスクリプションは CSP または APSS サブスクリプションである必要があります。

その後、登録は、Azure Stack を使用するテナントごとの Azure サブスクリプションで更新されます。 テナント サブスクリプションは種類が CSP であり、かつ既定のサブスクリプションを所有するパートナーにロールアップされる必要があります。 別のパートナーの顧客を登録することはできません。

Azure Stack によって使用状況の情報がグローバル Azure に転送されると、Azure 内のサービスによって登録が調べられ、各テナントの使用状況が適切なテナント サブスクリプションにマップされます。 テナントがまだ登録されていない場合、その使用状況は、発信元の Azure Stack インスタンスの既定のサブスクリプションに転送されます。

テナント サブスクリプションは CSP サブスクリプションであるため、その課金は CSP パートナーに送信され、最終顧客には使用状況の情報が表示されません。

## <a name="next-steps"></a>次の手順

- CSP プログラムについて詳しくは、「[クラウド ソリューション プロバイダー プログラム](https://partner.microsoft.com/solutions/microsoft-cloud-solutions)」をご覧ください。
- Azure Stack からリソース使用量情報を取得する方法ついて詳しくは、「[Azure Stack での使用量と請求](azure-stack-billing-and-chargeback.md)」をご覧ください。
