---
title: 汎用 v2 ストレージ アカウントにアップグレードする - Azure Storage | Microsoft Docs
description: 汎用 v2 ストレージ アカウントにアップグレードします。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/11/2018
ms.author: tamram
ms.openlocfilehash: 6e77c4836531a7efd0b52b9a411ac40ff6a613fa
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224496"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>汎用 v2 ストレージ アカウントにアップグレードする

汎用 v2 ストレージ アカウントは、最新の Azure Storage 機能をサポートし、汎用 v1 および BLOB ストレージ アカウントのすべての機能を組み込んでいます。 ほとんどのストレージ シナリオに汎用 v2 アカウントをお勧めします。 汎用 v2 アカウントは、業界内の他社に引けを取らないトランザクション料金で、Azure Storage に対してギガバイトあたり容量の最低価格を提供しています。

汎用 v1 または BLOB ストレージ アカウントから汎用 v2 ストレージ アカウントにアップグレードする方法は簡単です。 Azure portal、PowerShell、または Azure CLI を使用してアップグレードできます。 アカウントのアップグレード操作を元に戻すことはできません。また、アップグレードによって料金がかかる可能性があります。

## <a name="upgrade-using-the-azure-portal"></a>Azure portal を使用したアップグレード

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ストレージ アカウントに移動します。
3. **[設定]** セクションで **[構成]** をクリックします。
4. **[アカウントの種類]** の **[アップグレード]** をクリックします。
5. **[アップグレードの確認]** で、アカウントの名前を入力します。 
6. ブレードの下部にある **[アップグレード]** をクリックします。

## <a name="upgrade-with-powershell"></a>PowerShell を使用したアップグレード

PowerShell を使用して汎用 v1 アカウントを汎用 v2 アカウントにアップグレードするには、まず、**AzureRm.Storage** モジュールの最新バージョンを使用するために PowerShell を更新します。 PowerShell のインストールについては、「[Azure PowerShell のインストールおよび構成](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)」を参照してください。 

次に、次のコマンドを呼び出してアカウントをアップグレードします。その際、目的のリソース グループおよびストレージの名前に置き換えます。

```powershell
Set-AzureRmStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

## <a name="upgrade-with-azure-cli"></a>Azure CLI を使用したアップグレード

Azure CLI を使用して汎用 v1 アカウントを汎用 v2 アカウントにアップグレードするには、まず Azure CLI の最新バージョンをインストールします。 CLI のインストールについては、[Azure CLI 2.0 のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)に関するページを参照してください。 

次に、次のコマンドを呼び出してアカウントをアップグレードします。その際、目的のリソース グループおよびストレージの名前に置き換えます。

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
``` 

## <a name="specify-an-access-tier-for-blob-data"></a>BLOB データのアクセス層を指定する

汎用 v2 アカウントは、すべての Azure ストレージ サービスとデータ オブジェクトをサポートしていますが、アクセス層は BLOB ストレージのブロック BLOB に対してのみ使用できます。 汎用 v2 ストレージ アカウントにアップグレードするときに、BLOB データのアクセス層を指定できます。 

アクセス層を使用すると、想定される使用パターンに基づいて最も費用対効果の高いストレージを選択できます。 ブロック BLOB は、ホット層、クール層、またはアーカイブ層に格納できます。 アクセス層の詳細については、「[Azure Blob Storage: ホット、クール、およびアーカイブ ストレージ層](../blobs/storage-blob-storage-tiers.md)」を参照してください。

既定では、新しいストレージ アカウントはホット アクセス層に作成され、汎用 v1 ストレージ アカウントはホット アクセス層にアップグレードされます。 アップグレード後にデータに使用するアクセス層を検討している場合は、実際のシナリオを考慮してください。 汎用 v2 アカウントに移行する一般的なユーザー シナリオには、次の 2 つがあります。

* 汎用 v1 ストレージ アカウントを既に持っており、適切なストレージ層の BLOB データを使用して、汎用 v2 ストレージ アカウントへの変更を評価したい。
* 汎用 v2 ストレージ アカウントの使用を決定しているか、既に所有しており、BLOB データにホット ストレージ層とクール ストレージ層のどちらを使用すべきかを評価したい。

いずれの場合も、最初にすべきことは、汎用 v2 ストレージ アカウントに格納されるデータの格納、アクセス、操作にかかるコストを見積もり、そのコストを現在のコストと比較することです。

### <a name="estimate-costs-for-your-current-usage-patterns"></a>現在の使用パターンのコストを見積もる

特定の層に汎用 v2 ストレージ アカウントの BLOB データを格納してアクセスするコストを見積もるには、既存の使用パターンを評価するか、予想される使用パターンを概算します。 一般に、以下のことを調べる必要があります。

* 以下のような BLOB ストレージの使用量 (GB 単位)
    - ストレージ アカウントに格納されているデータの量はどれくらいか。
    - 月単位でデータ ボリュームがどのように変化するか。古いデータが定期的に新しいデータに置き換わるかどうか。
* 以下のような BLOB ストレージ データの主なアクセス パターン。
    - ストレージ アカウントから読み取られているデータの量とストレージ アカウントに書き込まれているデータの量はどれくらいか。 
    - ストレージ アカウントのデータに対して実行されている読み取り操作と書き込み操作の数。

実際のニーズに最適なアクセス層を決定するには、BLOB データが現在使用している容量とそのデータが使用されている容量を確認すると役に立ちます。 

移行前にストレージ アカウントの使用データを収集するには、[Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) を使用してストレージ アカウントを監視します。 Azure Monitor ではログ記録が実行され、Azure Storage などの Azure サービスのメトリック データが提供されます。 

ストレージ アカウントの BLOB の使用データを監視するには、Azure Monitor で容量のメトリックを有効にします。 容量のメトリックでは、アカウントの BLOB が毎日使用しているストレージの容量に関するデータが記録されます。 容量のメトリックは、ストレージ アカウントにデータを格納するコストを見積もるために使用できます。 アカウントの各種類について BLOB ストレージの容量がどのように価格設定されているかについては、「[ブロック BLOB の料金](https://azure.microsoft.com/pricing/details/storage/blobs/)」を参照してください。

BLOB ストレージのデータ アクセス パターンを監視するには、Azure Monitor でトランザクション メトリックを有効にします。 さまざまな Azure Storage 操作をフィルター処理して、それぞれが呼び出される頻度を見積もることができます。 アカウントの各種類のブロック BLOB とアペンド BLOB に対して、トランザクションの各種類にどのように価格設定されているかについては、「[ブロック BLOB の料金](https://azure.microsoft.com/pricing/details/storage/blobs/)」を参照してください。  

Azure Monitor からメトリックを収集する方法については、「[Azure Monitor の Azure Storage メトリック](storage-metrics-in-azure-monitor.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- [ストレージ アカウントの作成](storage-quickstart-create-account.md)
- [Azure Storage アカウントを管理する](storage-account-manage.md)