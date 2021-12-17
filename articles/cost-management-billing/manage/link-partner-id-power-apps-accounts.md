---
title: Azure の資格情報を使用してパートナー ID を Power Apps アカウントにリンクする
description: この記事は、Microsoft パートナーが Azure 資格情報を使用し、顧客による Microsoft Power Apps の使用を支援する際に役立ちます。
author: bandersmsft
ms.reviewer: akangaw
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: banders
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c7bc61691f0035c9f1aa0361ae3439cfb1e1485e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604400"
---
# <a name="link-a-partner-id-to-your-power-apps-accounts"></a>パートナー ID を Power Apps アカウントにリンクする

この記事は、Power Apps サービス プロバイダーである Microsoft パートナーが Microsoft Power Apps の顧客にサービスを関連付けるうえで役立ちます。 貴社 (Microsoft パートナー) が顧客向けの Power Apps サービスを管理、構成、サポートするときには、顧客の環境にアクセスします。 Azure の資格情報とパートナー管理者リンク (PAL) を使用して、パートナー ネットワーク ID をサービス提供に使用されるアカウントの資格情報に関連付けることができます。

PAL を使用すると、Microsoft が Power Apps の顧客を持つパートナーを特定し、認識することができます。 Microsoft では、アカウントのアクセス許可 (Power Apps のロール) とスコープ (テナント、リソース グループ、リソース) に基づいて、パートナーの組織が使用に貢献していると判断します。

## <a name="get-access-from-your-customer"></a>顧客からアクセス権を取得する

パートナー ID をリンクする前に、次のオプションのいずれかを使用して、顧客の Power Apps リソースに対するアクセス権を顧客から付与される必要があります。

- **ゲスト ユーザー** - 顧客は、貴社をゲスト ユーザーとして追加し、Power Apps ロールを割り当てることができます。 詳細については、[別のディレクトリからのゲスト ユーザーの追加](../../active-directory/external-identities/what-is-b2b.md)に関する記事を参照してください。
- **ディレクトリ アカウント** - 貴社の顧客は、自身のディレクトリ内に貴社のユーザー アカウントを作成し、任意の Power Apps ロールを割り当てることができます。
- **サービス プリンシパル** - 貴社の顧客は、貴社の組織から顧客のディレクトリ内にアプリまたはスクリプトを追加し、Power Apps ロールを割り当てることができます。 アプリまたはスクリプトの ID は、サービス プリンシパルと呼ばれます。
- **委任された管理者** - 貴社の顧客は、貴社のユーザーが貴社のテナント内から作業できるように、リソース グループを委任することができます。 詳細については、「[パートナー対象: 代理管理者](/power-platform/admin/for-partners-delegated-administrator)」を参照してください。

## <a name="link-customer-to-a-partner-id"></a>顧客をパートナー ID にリンクする

顧客のリソースにアクセスできるようになったら、Azure portal、PowerShell、または Azure CLI を使用して、貴社の Microsoft Partner Network ID (MPN ID) を貴社のユーザー ID またはサービス プリンシパルにリンクします。 パートナー ID は、顧客の各テナントにリンクしてください。

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Azure portal を使用して新しいパートナー ID にリンクする

1. [Azure portal](https://portal.azure.com) にサインインします。
1. Azure portal で [[パートナー ID へリンク]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) に移動します。
1. 貴社の [Microsoft Partner Network](https://partner.microsoft.com/) ID を入力します。 必ず、パートナー センターのプロファイルに表示されている **関連付けられている MPN ID** を使用してください。 これは通常、ユーザーの[パートナー拠点アカウントの MPN ID](/partner-center/account-structure) として知られています。  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/link-partner-id.png" alt-text="[パートナー ID へリンク] ウィンドウを示すスクリーンショット。" lightbox="./media/link-partner-id-power-apps-accounts/link-partner-id.png" :::
1. パートナー ID を別の顧客にリンクするには、ディレクトリを切り替えます。 **[ディレクトリの切り替え]** で適切なディレクトリを選びます。  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/switch-directory.png" alt-text="ディレクトリを切り替えられる [ディレクトリ + サブスクリプション] ウィンドウを示すスクリーンショット。" lightbox="./media/link-partner-id-power-apps-accounts/switch-directory.png" :::

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>PowerShell を使用して新しいパートナー ID にリンクする

[Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) Azure PowerShell モジュールをインストールします。

ユーザー アカウントまたはサービス プリンシパルで顧客のテナントにサインインします。 詳細については、[PowerShell を使用したサインイン](/powershell/azure/authenticate-azureps)に関するページを参照してください。

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

新しいパートナー ID にリンクします。 パートナー ID は、貴社の組織の [Microsoft Partner Network](https://partner.microsoft.com/) ID です。 必ず、パートナー プロファイルに表示されている **関連付けられている MPN ID** を使用してください。

```azurepowershell-interactive
new-AzManagementPartner -PartnerId 12345
```

リンク済みのパートナー ID を取得する

```azurepowershell-interactive
get-AzManagementPartner
```

リンク済みのパートナー ID を更新する

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

リンク済みのパートナー ID を削除する

```azurepowershell-interactive
remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Azure CLI を使用して新しいパートナー ID にリンクする

最初に、Azure CLI 拡張機能をインストールします。

```azurecli-interactive
az extension add --name managementpartner
```

ユーザー アカウントまたはサービス プリンシパルで顧客のテナントにサインインします。 詳細については、[Azure CLI を使用したサインイン](/cli/azure/authenticate-azure-cli)に関するページを参照してください。

```azurecli-interactive
az login --tenant TenantName
```

新しいパートナー ID にリンクします。 パートナー ID は、貴社の組織の [Microsoft Partner Network](https://partner.microsoft.com/) ID です。

```azurecli-interactive
az managementpartner create --partner-id 12345
```

リンク済みのパートナー ID を取得する

```azurecli-interactive
az managementpartner show
```

リンク済みのパートナー ID を更新する

```azurecli-interactive
az managementpartner update --partner-id 12345
```

リンク済みのパートナー ID を削除する

```azurecli-interactive
az managementpartner delete --partner-id 12345
```

### <a name="next-steps"></a>次のステップ

- パートナー ID と Power Apps アカウントのリンクに関する質問と回答については、[Cost Management + Billing に関する FAQ](../cost-management-billing-faq.yml) を参照してください。
- [Microsoft パートナー コミュニティ](https://aka.ms/PALdiscussion)のディスカッションに参加して、更新プログラムを受け取ったり、フィードバックを送ったりします。
- ロー コード アプリケーション開発の高度な専門性のために PAL ベースの Power Apps 関連付けを行う場合は、「[ロー コード アプリケーション開発の高度な専門性に関する FAQ](https://assetsprod.microsoft.com/mpn/faq-low-code-app-development-advanced-specialization.pdf)」を参照してください。
