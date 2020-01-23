---
title: Azure Service Bus 保存データの暗号化用に独自のキーを構成する
description: この記事では、Azure Service Bus の保存データを暗号化するための独自のキーを構成する方法について説明します。
services: service-bus-messaging
ms.service: service-bus
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: aschhab
ms.openlocfilehash: 6d20d4031f0ed4d1be4dddf9e33946251d6dd523
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903319"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Azure portal を使用して Azure Service Bus 保存データの暗号化用のカスタマー マネージド キーを構成する
Azure Service Bus Premium では、Azure Storage Service Encryption (Azure SSE) による保存データの暗号化が提供されます。 Service Bus Premium では、データを格納するために Azure Storage が使用されます。既定では、Azure Storage を使用して格納されるすべてのデータは、Microsoft のマネージド キーを使用して暗号化されます。 

## <a name="overview"></a>概要
Azure Service Bus で、Microsoft のマネージド キーまたはカスタマー マネージド キー (Bring Your Own Key – BYOK) を使用した保存データ暗号化のオプションがサポートされるようになりました。 この機能を使用して、Azure Service Bus の保存データ暗号化に使用されるカスタマー マネージド キーへの作成、ローテーション、無効化、およびアクセスの取り消しを実行できます。

BYOK 機能の有効化は、名前空間での 1 回限りのセットアップ プロセスです。

> [!NOTE]
> カスタマー マネージド キーには、サービス側の暗号化に関するいくつかの注意事項があります。 
>   * この機能は [Azure Service Bus Premium](service-bus-premium-messaging.md) レベルでサポートされます。 これは、Standard レベルの Service Bus 名前空間に対して有効にすることはできません。
>   * 暗号化は、新規または空の名前空間に対してのみ有効にすることができます。 名前空間にデータが含まれている場合、暗号化操作は失敗します。

キーの管理およびキーの使用状況の監査には、Azure Key Vault を使用できます。 独自のキーを作成してキー コンテナーに格納することも、Azure Key Vault API を使ってキーを生成することもできます。 Azure Key Vault の詳細については、「 [What is Azure Key Vault? (Azure Key Vault とは)](../key-vault/key-vault-overview.md)

この記事では、Azure portal を使って、カスタマー マネージド キーでキー コンテナーを構成する方法について説明します。 Azure portal を使ってキー コンテナーを作成する方法を学習するには、「[クイック スタート: Azure portal を使用して Azure Key Vault との間でシークレットの設定と取得を行う](../key-vault/quick-create-portal.md)」をご覧ください。

> [!IMPORTANT]
> Azure Service Bus でカスタマー マネージド キーを使うには、キー コンテナーに 2 つの必須プロパティが構成されている必要があります。 これらは次のとおりです。 **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** です。 Azure portal で新しいキー コンテナーを作成すると、これらのプロパティは既定で有効になります。 ただし、既存のキー コンテナーでこれらのプロパティを有効にする必要がある場合は、PowerShell または Azure CLI を使う必要があります。

## <a name="enable-customer-managed-keys"></a>カスタマー マネージド キーを有効にする
Azure portal でカスタマー マネージド キーを有効にするには、次の手順のようにします。

1. お使いの Service Bus Premium 名前空間に移動します。
2. Service Bus 名前空間の **[設定]** ページで、 **[暗号化]** を選択します。
3. 次の図に示すように、**カスタマー マネージド キーによる保存中の暗号化**を選択します。

    ![カスタマー マネージド キーの有効化](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>キーを使用したキー コンテナーの設定

カスタマー マネージド キーを有効にした後、カスタマー マネージド キーを Azure Service Bus 名前空間に関連付ける必要があります。 Service Bus では Azure Key Vault のみがサポートされます。 前のセクションで**カスタマー マネージド キーによる暗号化**のオプションを有効にした場合は、キーが Azure Key Vault にインポートされている必要があります。 また、キーに対して **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** が構成されている必要があります。 これらの設定は、[PowerShell](../key-vault/key-vault-soft-delete-powershell.md) または [CLI](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection) を使用して構成できます。

1. 新しいキー コンテナーを作成するには、Azure Key Vault の[クイック スタート](../key-vault/key-vault-overview.md)に従ってください。 既存のキーをインポートする方法については、「[キー、シークレット、証明書について](../key-vault/about-keys-secrets-and-certificates.md)」を参照してください。
1. コンテナーの作成時、論理的な削除と消去保護の両方をオンにするには、[az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) コマンドを使用します。

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 既存のコンテナー (論理的な削除が既に有効になっている) に消去保護を追加するには、[az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) コマンドを使用します。

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 次の手順に従ってキーを作成します。
    1. 新しいキーを作成するには、 **[設定]** の下にある **[キー]** メニューから **[生成/インポート]** を選択します。
        
        ![[Generate/Import]\(生成/インポート\) ボタンの選択](./media/configure-customer-managed-key/select-generate-import.png)

    1. **[オプション]** を **[生成]** に設定し、キーの名前を付けます。

        ![キーの作成](./media/configure-customer-managed-key/create-key.png) 

    1. 次に、ドロップダウン リストからこのキーを選択して、暗号化用の Service Bus 名前空間に関連付けることができます。 

        ![Key Vault からのキーの選択](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > 冗長性を確保するために、最大 3 つのキーを追加できます。 いずれかのキーの有効期限が切れた場合、またはアクセスできない場合は、他のキーが暗号化に使用されます。
        
    1. キーの詳細を入力し、 **[選択]** をクリックします。 これにより、カスタマー マネージド キーを使用して、名前空間の保存データを暗号化できるようになります。 


> [!IMPORTANT]
> カスタマー マネージド キー を geo ディザスター リカバリーと共に使用する場合は、以下を確認してください。 
>
> カスタマー マネージド キーを使用して保存時の暗号化を有効にするために、指定された Azure KeyVault の Service Bus のマネージド ID に対して[アクセス ポリシー](../key-vault/key-vault-secure-your-key-vault.md)が設定されます。 これにより、Azure Service Bus 名前空間から Azure Key Vault へのアクセスが制御されます。
>
> これにより:
> 
>   * Service Bus 名前空間に対して [geo ディザスター リカバリー](service-bus-geo-dr.md)が既に有効になっているときに、カスタマー マネージド キーを有効化する場合は、以下を行います。 
>     * ペアリングを解除します。
>     * キー コンテナーに対して、プライマリとセカンダリの名前空間の両方のマネージド ID を対象とする[アクセス ポリシー を設定](../key-vault/managed-identity.md)します。
>     * プライマリ名前空間に暗号化を設定します。
>     * プライマリとセカンダリの名前空間を再びペアリングします。
> 
>   * カスタマー マネージド キーが既に設定されている Service Bus 名前空間で Geo DR を有効にする場合は、以下を実行します。
>     * キー コンテナーに対して、セカンダリ名前空間のマネージド ID を対象とする[アクセス ポリシー を設定](../key-vault/managed-identity.md)します。
>     * プライマリとセカンダリの名前空間をペアリングします。


## <a name="rotate-your-encryption-keys"></a>暗号化キーのローテーション

Azure Key Vault のローテーション メカニズムを使用して、キー コンテナー内のキーをローテーションできます。 詳細については、[キー ローテーションと監査を設定する](../key-vault/key-vault-key-rotation-log-monitoring.md)方法に関するページを参照してください。 アクティブ化と有効期限の日付を設定してキー ローテーションを自動化することもできます。 Service Bus サービスによって新しいキーのバージョンが検出され、それらの使用が自動的に開始されます。

## <a name="revoke-access-to-keys"></a>キーへのアクセスの取り消し

暗号化キーへのアクセスを取り消しても、Service Bus からデータが消去されることはありません。 ただし、Service Bus 名前空間からデータにアクセスすることはできません。 暗号化キーは、アクセス ポリシーを使用して、またはキーを削除することで取り消すことができます。 アクセス ポリシーと、キー コンテナーのセキュリティ保護の詳細については、「[キー コンテナーへのアクセスをセキュリティで保護する](../key-vault/key-vault-secure-your-key-vault.md)」を参照してください。

暗号化キーを取り消すと、暗号化されている名前空間で Service Bus サービスが機能しなくなります。 キーへのアクセスを有効にするか、削除済みのキーを復元すると、Service Bus サービスによってキーが選択され、暗号化された Service Bus 名前空間からデータにアクセスできるようになります。

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。
- [Service Bus の概要](service-bus-messaging-overview.md)
- [Key Vault の概要](../key-vault/key-vault-overview.md)


