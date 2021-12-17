---
title: Azure PowerShell を介して Apache Kafka for Confluent Cloud を作成する - Azure パートナー ソリューション
description: この記事では、Azure PowerShell を使用して Apache Kafka for Confluent Cloud のインスタンスを作成する方法について説明します。
ms.service: partner-services
ms.topic: quickstart
ms.date: 11/03/2021
author: tfitzmac
ms.author: tomfitz
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 696e5138d80b1ba799f96fe60da3be0f4f1d2548
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485877"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud---azure-powershell"></a>クイック スタート: Apache Kafka for Confluent Cloud の概要 - Azure PowerShell

このクイックスタートでは、Azure Marketplace と Azure PowerShell を使用して Apache Kafka for Confluent Cloud のインスタンスを作成します。

## <a name="prerequisites"></a>前提条件

- Azure アカウント。 有効な Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成してください。
- Azure サブスクリプションの "_所有者_" ロールまたは "_共同作成者_" ロールが必要です。 Azure と Confluent の統合を設定できるのは、"_所有者_" または "_共同作成者_" のアクセス権を持つユーザーだけです。 作業を始める前に、[適切なアクセス権があることを確認](../../role-based-access-control/check-access.md)してください。

## <a name="find-offer"></a>プランを探す

Azure portal を使用して Apache Kafka for Confluent Cloud アプリケーションを探します。

1. Web ブラウザーで、[Azure portal](https://portal.azure.com/) に移動してサインインします。

1. 最近のセッションで **Marketplace** にアクセスした場合は、表示されているオプションからそのアイコンを選択します。 それ以外の場合は、_Marketplace_ を検索します。

    :::image type="content" source="media/marketplace.png" alt-text="Marketplace アイコン。":::

1. **[Marketplace]** ページには、使用したいプランの種類に応じて選択できるオプションが 2 つあります。 従量課金制プランまたはコミットメント プランにサインアップすることができます。 従量課金制は一般公開されています。 コミットメント プランは、プライベート プランが承認されているユーザーが利用できます。

   - **従量課金制** をご利用の場合は、_Apache Kafka on Confluent Cloud_ を検索します。 Apache Kafka on Confluent Cloud のプランを選択します。

     :::image type="content" source="media/search-pay-as-you-go.png" alt-text="Azure Marketplace プランの検索。":::

   - **コミットメント** をご利用の場合は、 **[View Private offers]\(プライベート プランを表示\)** リンクを選択します。 コミットメントを利用するには、最低支出額にサインアップする必要があります。 このオプションは、このサービスが長期間必要であることがわかっている場合にのみ使用してください。

     :::image type="content" source="media/view-private-offers.png" alt-text="プライベート プランの表示。":::

     _Apache Kafka on Confluent Cloud_ を探します。

     :::image type="content" source="media/select-from-private-offers.png" alt-text="プライベート プランの選択。":::

## <a name="create-resource"></a>リソースを作成する

Apache Kafka on Confluent Cloud のプランを選択したら、アプリケーションを設定する準備は完了です。

まず、Azure PowerShell の環境を準備します。

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

> [!IMPORTANT]
> **Az.Confluent** PowerShell モジュールがプレビュー段階にある間は、`Install-Module` コマンドレットを使用して、これを別途インストールする必要があります。

```azurepowershell
Install-Module -Name Az.Confluent -Scope CurrentUser -Repository PSGallery -Force
```

サインインした後、[New-AzConfluentOrganization](/powershell/module/az.confluent/new-azconfluentorganization) コマンドを使用して、新しい組織のリソースを作成します。

```azurepowershell
$ConfluentOrgParams = @{
    Name = 'myOrganization'
    ResourceGroupName = 'myResourceGroup'
    Location = 'my location'
    OfferDetailId = 'string'
    OfferDetailPlanId = 'string'
    OfferDetailPlanName = 'string'
    OfferDetailPublisherId = 'string'
    OfferDetailTermUnit = 'string'
    UserDetailEmailAddress = 'contoso@microsoft.com'
    UserDetailFirstName = 'string'
    UserDetailLastName = 'string'
    Tag = @{Environment='Dev'}
}

New-AzConfluentOrganization @ConfluentOrgParams
```

> [!NOTE]
> 作成操作が完了する前にコマンドから戻るようにするには、省略可能なパラメーター `-NoWait` を追加します。 Confluent 組織が作成されるまで、操作の実行は続きます。

既存の組織の一覧を表示するには、[Get-AzConfluentOrganization](/powershell/module/az.confluent/get-azconfluentorganization) コマンドレットを使用します。

サブスクリプション内のすべての組織を表示できます。

```azurepowershell
Get-AzConfluentOrganization
```

または、リソース グループ内の組織を表示します。

```azurepowershell
Get-AzConfluentOrganization -ResourceGroupName myResourceGroup
```

特定の組織のプロパティを表示するには、`Get-AzConfluentOrganization` コマンドレットと `Name`および `ResourceGroupName` パラメーターを使用します。

名前で組織を表示できます。

```azurepowershell
Get-AzConfluentOrganization -Name myOrganization -ResourceGroupName myResourceGroup
```

エラーが表示された場合は、「[Apache Kafka for Confluent Cloud ソリューションのトラブルシューティング](troubleshoot.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Confluent Cloud リソースを管理する](manage.md)
