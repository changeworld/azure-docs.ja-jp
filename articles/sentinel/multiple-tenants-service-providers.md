---
title: Microsoft Azure Sentinel で複数のテナントをマネージド セキュリティ サービス プロバイダーとして管理する | Microsoft Docs
description: Azure Lighthouse を使用して、Microsoft Azure Sentinel で複数のテナントをマネージド セキュリティ サービス プロバイダー (MSSP) としてオンボードおよび管理する方法。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: a4518498edf3d2da14c09d396aff56542d61478f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132713013"
---
# <a name="manage-multiple-tenants-in-microsoft-sentinel-as-an-mssp"></a>Microsoft Azure Sentinel で複数のテナントを MSSP として管理する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

マネージド セキュリティ サービス プロバイダー (MSSP) が [Azure Lighthouse](../lighthouse/overview.md) を使用してセキュリティ運用センター (SOC) サービスを顧客に提供している場合は、顧客のテナントに接続する必要なしに、顧客の Microsoft Azure Sentinel リソースを自社の Azure テナントから直接管理することができます。 

## <a name="prerequisites"></a>前提条件

- [Azure Lighthouse をオンボードする](../lighthouse/how-to/onboard-customer.md)

- この作業を適切に行うために、自社のテナント (MSSP テナント) には、1 つ以上のサブスクリプションで Microsoft Azure Sentinel リソース プロバイダーが登録されている必要があります。 さらに、各顧客のテナントには、リソース プロバイダーが登録されている必要があります。 自社のテナントに Microsoft Azure Sentinel が登録されており、顧客のテナントにも同様に登録されている場合は、作業を開始する準備ができています。 登録を確認するには、次の手順を行います。

    1. Azure portal で **[サブスクリプション]** を選択したら、メニューから関連するサブスクリプションを選択します。

    1. サブスクリプション画面で、ナビゲーション メニューの **[設定]** から **[リソース プロバイダー]** を選択します。

    1. **["*サブスクリプション名*" | リソース プロバイダー]** 画面で、*Microsoft.OperationalInsights* と *Microsoft.SecurityInsights* を検索して選択し、 **[状態]** 列を確認します。 プロバイダーの状態が *[未登録]* である場合は、 **[登録]** を選択します。
    
        :::image type="content" source="media/multiple-tenants-service-providers/check-resource-provider.png" alt-text="リソース プロバイダーを確認する":::

## <a name="how-to-access-microsoft-sentinel-in-managed-tenants"></a>マネージド テナントから Microsoft Azure Sentinel にアクセスする方法

1. **[ディレクトリ + サブスクリプション]** で、委任されたディレクトリ (ディレクトリとテナントは同じです) と、顧客の Microsoft Azure Sentinel ワークスペースが配置されているサブスクリプションを選択します。

    :::image type="content" source="media/multiple-tenants-service-providers/directory-subscription.png" alt-text="テナントとサブスクリプションを選択する":::

1. Microsoft Azure Sentinel を開きます。 選択したサブスクリプションのすべてのワークスペースが表示され、それらを自社のテナント内のワークスペースと同様にシームレスに操作できるようになります。

> [!NOTE]
> Microsoft Azure Sentinel でマネージド ワークスペース内からコネクタをデプロイすることはできません。 コネクタをデプロイするには、コネクタをデプロイするテナントに直接サインインし、必要なアクセス許可を使用してそこで認証を受ける必要があります。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、複数の Microsoft Azure Sentinel テナントをシームレスに管理する方法を学習しました。 Microsoft Azure Sentinel の詳細については、次の記事を参照してください。
- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Microsoft Sentinel を使用した脅威の検出](detect-threats-built-in.md)の概要。
