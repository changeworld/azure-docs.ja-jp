---
title: Azure Stack でのお客様への請求と配賦 | Microsoft Docs
description: Azure Stack からリソース使用量情報を取得する方法を説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: brenduns
ms.reviewer: alfredop
ms.openlocfilehash: f055837711b52fc32cb387fb86c623d3502f47ab
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090141"
---
# <a name="usage-and-billing-in-azure-stack"></a>Azure Stack での使用量と請求

この記事では、Azure Stack ユーザーに、リソース使用量に対する請求がどのように行われるのかについて説明します。 分析や配賦のために請求情報にアクセスする方法についても説明します。

Azure Stack では、使用されるリソースの使用量データを収集し、グループ化します。 Azure Stack は、このデータを Azure Commerce に転送します。 Azure Commerce は、Azure の使用量に対して課金する場合と同様に、Azure Stack の使用量に対して課金します。

また、請求アダプターを使用して、使用量データを取得し、独自の請求システムや配賦システムにエクスポートしたり、Microsoft Power BI などのビジネス インテリジェンス ツールにエクスポートできます。


## <a name="usage-pipeline"></a>使用量パイプライン

Azure Stack 内の各リソース プロバイダーは、リソース使用率に従って使用量データを投稿します。 Usage Service は、使用量データを定期的に (時間単位および日単位で) 集計し、使用量データベースに保存します。 Azure Stack のオペレーターとユーザーは、Azure Stack リソース使用量 API を使用して、保存された使用量データにアクセスできます。 

[Azure Stack インスタンスを Azure に登録](azure-stack-register.md)している場合、Azure Commerce に使用量データを送信するように Azure Stack が構成されます。 データが Azure にアップロードされたら、課金ポータルまたは Azure リソース使用量 API を使用してアクセスできます。 Azure に報告される使用量データの詳細については、[使用量データのレポート](azure-stack-usage-reporting.md)に関する記事をご覧ください。  

次の図は、使用量パイプラインの主要なコンポーネントを示しています。 

![使用量パイプライン](media\azure-stack-billing-and-chargeback\usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>確認できる使用量情報とその確認方法

計算、ストレージ、ネットワークなど、Azure Stack のリソースプロバイダーでは、サブスクリプションごとに 1 時間間隔で使用量データが生成されます。 使用量データには、リソース名、使用されたサブスクリプション、使用された量など、使用されたリソースに関する情報が含まれます。 測定 ID リソースについては、[使用量 API に関する FAQ](azure-stack-usage-related-faq.md) のページをご覧ください。

使用量データが収集されると、そのデータが [Azure に報告され](azure-stack-usage-reporting.md)、請求書が生成されます。請求書は、Azure Billing Portal から表示できます。 

> [!NOTE]  
> 使用量データのレポートは、Azure Stack Development Kit および容量モデルのライセンスを持つ Azure Stack 統合システムのユーザーにとっては必須ではありません。 Azure Stack のライセンスの詳細については、[パッケージと価格](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)に関するデータ シートをご覧ください。

Azure 課金ポータルには、課金対象のリソースの使用量データが表示されます。 Azure Stack では、課金対象のリソースのほかに、幅広い一連のリソースについて使用量情報が取得されます。この情報には、Azure Stack 環境で REST API または PowerShell を使ってアクセスできます。 Azure Stack のオペレーターは、すべてのユーザー サブスクリプションの使用量データを取得できます。 個々のユーザーは、自分の使用量の詳細のみを取得できます。 

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>マルチテナント クラウド サービス プロバイダーの使用状況レポート

Azure Stack を使用している多数の顧客を持つマルチテナント クラウド サービス プロバイダー (CSP) は、各顧客の使用状況を個別に報告できます。これにより、プロバイダーはさまざまな Azure サブスクリプションに使用料金を課金できます。 

各顧客の ID は、Azure Active Directory (Azure AD) テナントごとに示されることになります。 Azure Stack では、Azure AD テナントごとに 1 つの CSP サブスクリプションを割り当てることができます。 テナントとそのサブスクリプションを Azure Stack の基本登録に追加できます。 基本登録はすべての Azure Stack に対して行われます。 サブスクリプションがテナントに登録されていなくても、ユーザーは引き続き Azure Stack を使用することができ、使用状況は基本登録に使用されたサブスクリプションに送信されます。 


## <a name="next-steps"></a>次の手順

[Azure Stack を登録する](azure-stack-registration.md)

[Azure Stack の使用量データを Azure に報告する](azure-stack-usage-reporting.md)

[プロバイダーリソース使用量 API](azure-stack-provider-resource-api.md)

[テナント リソース使用量 API](azure-stack-tenant-resource-usage-api.md)

[使用量に関するよくあるご質問](azure-stack-usage-related-faq.md)