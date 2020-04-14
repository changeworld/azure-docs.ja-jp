---
title: Azure Media Services アカウントの作成
description: このチュートリアルでは、Azure Media Services アカウントを作成する手順について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/15/2020
ms.author: juliako
ms.openlocfilehash: 2f1694825319ed8b8682c044e7e2282ed4c43dcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474064"
---
# <a name="create-a-media-services-account"></a>Media Services アカウントを作成する

Azure で暗号化、エンコード、分析、管理、およびメディア コンテンツのストリーミングを開始するには、Media Services アカウントを作成する必要があります。 Media Services アカウントは、1 つまたは複数のストレージ アカウントに関連付ける必要があります。

> [!NOTE]
> Media Services アカウントおよび関連するすべてのストレージ アカウントは、同じ Azure サブスクリプションに存在する必要があります。 待ち時間やデータ エグレス コストが増加することを回避するために、ストレージ アカウントを Media Services アカウントと同じ場所で使用することを強くお勧めします。

この記事では、新しい Azure Media Services アカウントを作成する手順について説明します。 次のタブから選択します。

## <a name="use-the-azure-portal"></a>Azure ポータルの使用

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Azure Portal には、Azure Media Services アカウントをすばやく作成する方法が用意されています。 アカウントを使用して Media Services にアクセスすると、Azure でメディア コンテンツを保存、暗号化、エンコード、管理、およびストリーミングすることができます。

現時点では、[Azure portal](https://portal.azure.com/) を使って次のことを実行できます。

* Media Services v3 の[ライブ イベント](live-events-outputs-concept.md)を管理する 
* v3 の[アセット](assets-concept.md)を表示する (管理ではない) 
* [API のアクセスに関する情報を取得する](access-api-portal.md)。 

他のすべての管理タスク ([変換とジョブ](transforms-jobs-concept.md)や[コンテンツ保護](content-protection-overview.md)など) については、[REST API](https://aka.ms/ams-v3-rest-ref)、[CLI](https://aka.ms/ams-v3-cli-ref)、またはサポートされているいずれかの [SDK](media-services-apis-overview.md#sdks) を使用します。

この記事では、Azure portal を使用して Media Services アカウントを作成する方法について説明します。

### <a name="create-a-media-services-account"></a>Media Services アカウントを作成する

1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. **[+リソースの作成]**  >  **[メディア]**  >  **[Media Services]** の順にクリックします。
1. **[Media Services アカウントを作成する]** セクションに必要な値を入力します。
    
    | 名前 | 説明 |
    | ---|---|
    |**アカウント名**|新しい Media Services アカウントの名前を入力します。 Media Services アカウント名に使用できる文字は、小文字または数字のみで、空白を含めることはできません。長さは 3 文字から 24 文字です。|
    |**サブスクリプション**|複数のサブスクリプションがある場合は、アクセス権のある Azure サブスクリプションの一覧から 1 つを選択します。|
    |**リソース グループ**|新規または既存のリソースを選択します。 リソース グループとは、ライフサイクル、アクセス許可、ポリシーを共有するリソースの集まりです。 [こちら](../../azure-resource-manager/management/overview.md#resource-groups)をご覧ください。|
    |**場所**|Media Services アカウントのメディアとメタデータのレコードを保存する地理的リージョンを選択します。 このリージョンでメディアの処理とストリーミングが行われます。 ドロップダウン リストのボックスには、利用可能な Media Services リージョンのみが表示されます。 |
    |**ストレージ アカウント**|Media Services アカウントのメディア コンテンツの BLOB ストレージとなるストレージ アカウントを選択します。 Media Services アカウントと同じリージョンにある既存のストレージ アカウントを選択することも、新しいストレージ アカウントを作成することもできます。 新しいストレージ アカウントは同じリージョンに作成されます。 ストレージ アカウントの命名規則は、Media Services アカウントと同じです。<br/><br/>1 つの**プライマリ** ストレージ アカウントを持つ必要があります。Media Services アカウントに関連付けられた任意の数の **セカンダリ** ストレージ アカウントを持つことができます。 Azure portal を使用して、セカンダリ ストレージ アカウントを追加できます。 詳細については、[Azure Storage アカウントの Azure Media Services アカウント](storage-account-concept.md)に関するページを参照してください。<br/><br/>Media Services アカウントおよび関連するすべてのストレージ アカウントは、同じ Azure サブスクリプションに存在する必要があります。 待ち時間やデータ エグレス コストが増加することを回避するために、ストレージ アカウントを Media Services アカウントと同じ場所で使用することを強くお勧めします。|
    
1. **[ダッシュボードにピン留めする]** チェック ボックスをオンにして、アカウントのデプロイの進行状況を確認します。
1. フォームの下部にある **[作成]** をクリックします。

    Media Services アカウントの作成時に、**既定**のストリーミング エンドポイントが**停止**状態でアカウントに追加されます。 コンテンツのストリーミングを開始し、[ダイナミック パッケージ](dynamic-packaging-overview.md)と[動的暗号化](content-protection-overview.md)を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが**実行中**状態である必要があります。 

## <a name="use-the-azure-cli"></a>Azure CLI の使用

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="set-the-azure-subscription"></a>Azure サブスクリプションを設定する

次のコマンドで、Media Services アカウントで使用する Azure サブスクリプション ID を指定します。 [[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) に移動することで、アクセス権があるサブスクリプションの一覧を表示できます。

```azurecli
az account set --subscription mySubscriptionId
```

### <a name="create-a-resource-group"></a>リソース グループを作成する

リソース グループを作成するには、次のコマンドを使用します。 Azure リソース グループは、Azure Media Services のアカウントや関連するストレージ アカウントなど、各種リソースのデプロイと管理の対象となる論理上のコンテナーです。

`amsResourceGroup` を実際の値に置き換えることができます。

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

Media Services アカウントの作成では、Azure Storage アカウント リソースの名前を指定する必要があります。 指定されたストレージ アカウントは、Media Services アカウントに関連付けられます。 Media Services で使用されるストレージ アカウントの使用方法について詳しくは、「[ストレージ アカウント](storage-account-concept.md)」をご覧ください。

1 つの**プライマリ** ストレージ アカウントを持つ必要があります。Media Services アカウントに関連付けられた任意の数の **セカンダリ** ストレージ アカウントを持つことができます。 Media Services は、**汎用 v2** (GPv2) アカウントまたは**汎用 v1** (GPv1) アカウントをサポートします。 BLOB のみのアカウントを**プライマリ**として使用することはできません。 ストレージ アカウントについて詳しくは、「[Azure Storage アカウントの種類](../../storage/common/storage-account-options.md)」をご覧ください。 

この例では、General Purpose v2、Standard LRS アカウントを作成します。 ストレージ アカウントで実験する場合は、`--sku Standard_LRS` を使用します。 ただし、運用環境用の SKU を選択する場合は、ビジネス継続性のために地理的レプリケーションを提供する `--sku Standard_RAGRS` を検討してください。 詳細については、[ストレージ アカウント](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest)に関するページを参照してください。
 
Media Services アカウントに関連付けるストレージ アカウントは、以下のコマンドで作成します。 以下のスクリプトの `storageaccountforams` は、実際の値に置き換えてください。 `amsResourceGroup` は、前の手順で、リソース グループに指定した値と一致させる必要があります。 ストレージ アカウント名は 24 文字未満にする必要があります。

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Media Services アカウントを作成する

新しい Media Services アカウントは、以下の Azure CLI コマンドで作成します。 次の値を置き換えることができます: `amsaccount` と `storageaccountforams` (ストレージ アカウントに指定した値に一致している必要があります)、`amsResourceGroup` (リソース グループに指定した値に一致している必要があります)。

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```

### <a name="see-also"></a>関連項目

* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [Media Services アカウントにセカンダリ ストレージを接続する](https://docs.microsoft.com/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>次のステップ

[ファイルのストリーミング](stream-files-dotnet-quickstart.md)
