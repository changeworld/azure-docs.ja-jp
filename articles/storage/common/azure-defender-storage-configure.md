---
title: Microsoft Defender for Storage を構成する
titleSuffix: Azure Storage
description: アカウント アクティビティの異常が検出され、ご利用のアカウントへの害を及ぼす可能性のあるアクセス試行が通知されるよう、Microsoft Defender for Storage を構成します。
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: ozgun
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 17cc82bd448910cc92eec42db889605c48b65fb5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297014"
---
# <a name="configure-microsoft-defender-for-storage"></a>Microsoft Defender for Storage を構成する

Microsoft Defender for Storage では、ストレージ アカウントにアクセスし、悪用しようとする不審な (有害の可能性がある) 行為を検出するインテリジェンスでセキュリティ層を厚くします。 この保護レイヤーにより、セキュリティの専門家でなくても、セキュリティ監視システムを管理しなくても、脅威に対処することができます。

セキュリティ アラートは、アクティビティで異常が発生したときにトリガーされます。 これらのセキュリティ アラートは [Microsoft Defender for Cloud](https://azure.microsoft.com/services/security-center/) と統合されます。さらに、不審なアクティビティの詳細と、脅威の調査や修復方法に関する推奨事項と共に、サブスクリプション管理者にメールで送信されます。

このサービスでは、脅威の検出のために、Blob Storage と Azure Files に対する読み取り要求、書き込み要求、削除要求のリソース ログが取り込まれます。 Microsoft Defender for Cloud からのアラートを調査するには、Storage Analytics Logging を使用して関連するストレージ アクティビティを確認します。 詳細については、「[Azure portal でのストレージ アカウントの監視](./manage-storage-analytics-logs.md#configure-logging)」の「**ログの構成**」を参照してください。

## <a name="availability"></a>可用性

現在、Microsoft Defender for Storage は、Blob Storage、Azure Files、Azure Data Lake Storage Gen2 で利用できます。 Microsoft Defender for Storage をサポートするアカウントの種類には、汎用 v2、ブロック BLOB、BLOB ストレージ アカウントが含まれます。 Microsoft Defender for Storage は、すべてのパブリック クラウドと米国政府のクラウドで利用できますが、他のソブリン クラウドや Azure Government クラウドのリージョンでは使用できません。

Data Lake Storage 用に階層型名前空間が有効になっているアカウントでは、Azure Blob Storage API と Data Lake Storage API の両方を使用するトランザクションがサポートされます。 Azure ファイル共有では、SMB 経由のトランザクションがサポートされます。

30 日間の無料試用など、価格の詳細については、[Microsoft Defender for Cloud の価格ページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。

次の一覧は、Microsoft Defender for Storage の可用性をまとめたものです。

- リリース状態:
  - [Blob Storage](https://azure.microsoft.com/services/storage/blobs/) (一般提供)
<<<<<<< HEAD
  - [Azure Files](../files/storage-files-introduction.md) (一般提供)
  - Azure Data Lake Storage Gen2 (一般提供)
- クラウド:<br>
    ✔ 商用クラウド<br>
    ✔ US Gov<br>
    ✘ China Gov、その他の Gov
=======
  - [Azure Files](../files/storage-files-introduction.md) (一般公開)
  - Azure Data Lake Storage Gen2 (一般公開)
- クラウド: ✔ 商用クラウド<br>
    ✔ Azure Government<br>
    ✘ Azure China 21Vianet
>>>>>>> repo_sync_working_branch

## <a name="set-up-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud を設定する

次のセクションで説明するように、Microsoft Defender for Storage を構成するには、いくつかの方法があります。

### <a name="microsoft-defender-for-cloud"></a>[Microsoft Defender for Cloud](#tab/azure-security-center)

Microsoft Defender for Storage は、Microsoft Defender for Cloud に組み込まれています。 サブスクリプションで Microsoft Defender for Cloud の強化されたセキュリティ機能を有効にすると、お使いのすべてのストレージ アカウントで自動的に Microsoft Defender for Storage が有効になります。 特定のサブスクリプションの個々のストレージ アカウントで Defender for Storage を有効または無効にするには、次のようにします。

1. [Azure portal](https://portal.azure.com) で **Microsoft Defender for Storage** を起動します。
1. Defender for Cloud のメイン メニューで **[環境設定]** を選択します。
1. Microsoft Defender for Storage を有効または無効にするサブスクリプションを選択します。
1. **[Enable all Microsoft Defender plans]\(すべての Microsoft Defender プランを有効にする\)** を選択して、サブスクリプションで Microsoft Defender for Cloud を有効にします。
1. **[Select Microsoft Defender plan by resource type]\(リソースの種類を指定して Microsoft Defender プランを選択する\)** で、 **[ストレージ]** 行を見つけて、 **[プラン]** 列で **[有効]** を選択します。
1. 変更内容を保存します。

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="Microsoft Defender for Storage を有効にする方法を示すスクリーンショット。":::

Microsoft Defender for Storage は、このサブスクリプションのすべてのストレージ アカウントに対して有効になりました。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure ポータル](https://portal.azure.com/)を開きます。
1. ストレージ アカウントに移動します。 **[設定]** で **[高度なセキュリティ]** を選択します。
1. **[Microsoft Defender for Storage を有効にする]** を選択します。

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="Microsoft Defender for Storage のアカウントを有効にする方法を示すスクリーンショット。":::

Microsoft Defender for Storage が、このストレージ アカウントで有効になりました。

### <a name="template"></a>[テンプレート](#tab/template)

Azure Resource Manager テンプレートを使用して、Microsoft Defender for Storage が有効な Azure Storage アカウントをデプロイします。 詳細については、[Advanced Threat Protection でのストレージ アカウント](https://azure.microsoft.com/resources/templates/storage-advanced-threat-protection-create/)に関するページを参照してください。

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

特定のサブスクリプションまたはリソース グループのストレージ アカウント全体で Microsoft Defender for Cloud を有効にするには、Azure Policy を使用します。

1. Azure の **[ポリシー - 定義]** ページを起動します。
1. **[Deploy Microsoft Defender for Storage accounts]\(Microsoft Defender for Storage アカウントをデプロイする\)** ポリシーを検索します。

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="Microsoft Defender for Storage アカウントを有効にするポリシーを適用する":::

1. Azure サブスクリプションまたはリソース グループを選択します。

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="ポリシーのスコープのサブスクリプションまたはリソース グループを選択する":::

1. ポリシーを割り当てます。

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="Microsoft Defender for Storage を有効にするポリシーを割り当てる":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してストレージ アカウントに対して Microsoft Defender for Storage を有効にするには、まず [Az.Security](https://www.powershellgallery.com/packages/Az.Security) モジュールがインストールされていることを確認します。 次に、[Enable-AzSecurityAdvancedThreatProtection](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection) コマンドを呼び出します。 山かっこ内の値は、実際の値に置き換えてください。

```azurepowershell
Enable-AzSecurityAdvancedThreatProtection -ResourceId "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/"
```

PowerShell を使用してストレージ アカウントに対する Microsoft Defender for Storage の設定を確認するには、[Get-AzSecurityAdvancedThreatProtection](/powershell/module/az.security/get-azsecurityadvancedthreatprotection) コマンドを呼び出します。 山かっこ内の値は、実際の値に置き換えてください。

```azurepowershell
Get-AzSecurityAdvancedThreatProtection -ResourceId "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/"
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してストレージ アカウントに対して Microsoft Defender for Storage を有効にするには、[az security atp storage update](/cli/azure/security/atp/storage#az_security_atp_storage_update) コマンドを呼び出します。 山かっこ内の値は、実際の値に置き換えてください。

```azurecli
az security atp storage update \
    --resource-group <resource-group> \
    --storage-account <storage-account> \
    --is-enabled true
```

Azure CLI を使用してストレージ アカウントに対する Microsoft Defender for Storage の設定を確認するには、[az security atp storage show](/cli/azure/security/atp/storage#az_security_atp_storage_show) コマンドを呼び出します。 山かっこ内の値は、実際の値に置き換えてください。

```azurecli
az security atp storage show \
    --resource-group <resource-group> \
    --storage-account <storage-account>
```

---

## <a name="explore-security-anomalies"></a>セキュリティ異常を調べる

ストレージ アクティビティの異常が発生すると、疑わしいセキュリティ イベントに関する情報を含む通知が電子メールで送信されます。 イベントの詳細には、次のものが含まれます。

- 異常の種類
- ストレージ アカウント名
- イベント時間
- ストレージの種類
- 考えられる原因
- 調査手順
- 修復手順

電子メールには、潜在的な脅威の考えられる原因と調査や緩和のための推奨されるアクションについての詳細も含まれます。

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="Microsoft Defender for Storage のアラート メール":::

Microsoft Defender for Cloud の [[セキュリティ アラート] タイル](../../security-center/security-center-managing-and-responding-alerts.md)から、現在のセキュリティ アラートを確認して管理できます。 アラートを選択して、詳細な情報と、現在の脅威を調査し、今後の脅威に対処するためのアクションを表示します。

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="Microsoft Defender for Storage のアラート":::

## <a name="security-alerts"></a>セキュリティのアラート

アラートは、ストレージ アカウントへの通常とは異なる、害を及ぼす可能性のあるアクセス試行やストレージ アカウントの悪用が発生すると、生成されます。 Azure Storage のアラートの一覧については、「[Azure Storage のアラート](../../security-center/alerts-reference.md#alerts-azurestorage)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Microsoft Defender for Storage の概要](../../security-center/defender-for-storage-introduction.md)
- [Microsoft Defender for Cloud](../../security-center/security-center-introduction.md)
- [Azure Storage アカウントにログインする](/rest/api/storageservices/About-Storage-Analytics-Logging)
