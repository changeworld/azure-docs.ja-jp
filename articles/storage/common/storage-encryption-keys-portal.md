---
title: Azure portal を使用してカスタマー マネージド キーを構成する
titleSuffix: Azure Storage
description: Azure ポータルを使用して、Azure Storage 暗号化用に、Azure Key Vault でカスタマー マネージド キーを構成する方法について学びます。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 192e58b101b824ca0cc0c732e02647838be6dc35
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81456483"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Azure portal を使用して Azure Key Vault でカスタマー マネージド キーを構成する

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

この記事では、[Azure portal](https://portal.azure.com/) を使って、カスタマー マネージド キーで Azure Key Vault を構成する方法について説明します。 Azure portal を使ってキー コンテナーを作成する方法を学習するには、「[クイック スタート: Azure portal を使用して Azure Key Vault との間でシークレットの設定と取得を行う](../../key-vault/secrets/quick-create-portal.md)」をご覧ください。

## <a name="configure-azure-key-vault"></a>Azure Key Vault を構成する

Azure Storage 暗号化でカスタマー マネージド キーを使うには、キー コンテナーに **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** の 2 つのプロパティが構成されている必要があります。 これらのプロパティは既定では有効になっていませんが、新規または既存のキー コンテナーに対して PowerShell または Azure CLI を使用して有効にすることができます。

既存のキー コンテナーでこれらのプロパティを有効にする方法については、次のいずれかの記事の「**論理的な削除を有効にする**」および「**消去保護を有効にする**」を参照してください。

- [PowerShell で Key Vault の論理的な削除を使用する方法](../../key-vault/general/soft-delete-powershell.md)
- [CLI で Key Vault の論理的な削除を使用する方法](../../key-vault/general/soft-delete-cli.md)

Azure Storage の暗号化では、2048 ビットの RSA と RSA-HSM キーのみがサポートされています。 キーの詳細については、「[Azure Key Vault のキー、シークレット、証明書について](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)」の「**Key Vault のキー**」を参照してください。

## <a name="enable-customer-managed-keys"></a>カスタマー マネージド キーを有効にする

Azure portal でカスタマー マネージド キーを有効にするには、次の手順のようにします。

1. ストレージ アカウントに移動します。
1. ストレージ アカウントの **[設定]** ブレードで、 **[暗号化]** をクリックします。 次の図に示すように、 **[カスタマー マネージド キー]** オプションを選択します。

    ![暗号化オプションが表示されているポータルのスクリーンショット](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>キーを指定する

カスタマー マネージド キーを有効にした後は、キーとストレージ アカウントの関連付けを指定することができます。

### <a name="specify-a-key-as-a-uri"></a>URI としてキーを指定する

URI としてキーを指定するには、次の手順のようにします。

1. Azure portal でキーの URI を調べるには、キー コンテナーに移動して、 **[キー]** 設定を選択します。 目的のキーを選択し、キーをクリックしてそのバージョンを表示します。 そのバージョンの設定を表示するには、キーのバージョンを選択します。
1. URI を示している **[キー識別子]** フィールドの値をコピーします。

    ![キー コンテナーのキー URI が表示されているスクリーンショット](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. ストレージ アカウントの **[暗号化]** の設定で、 **[キー URI を入力]** オプションを選択します。
1. コピーした URI を **[キー URI]** フィールドに貼り付けます。

   ![キー URI の入力方法が示されているスクリーンショット](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. キー コンテナーを含むサブスクリプションを指定します。
1. 変更を保存します。

### <a name="specify-a-key-from-a-key-vault"></a>キー コンテナーのキーを指定する

キー コンテナーからキーを指定するには、まず、キーが含まれるキー コンテナーがあることを確認します。 キー コンテナーからキーを指定するには、次の手順のようにします。

1. **[Select from Key Vault]\(キー コンテナーから選択\)** オプションを選択します。
1. 使用するキーを含むキー コンテナーを選択します。
1. キー コンテナーからキーを選択します。

   ![カスタマー マネージド キーのオプションが示されているスクリーンショット](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. 変更を保存します。

## <a name="update-the-key-version"></a>キーのバージョンを更新する

キーの新しいバージョンを作成した場合、その新しいバージョンを使用するには、ストレージ アカウントを更新します。 次の手順に従います。

1. お使いのストレージ アカウントに移動し、 **[暗号化]** の設定を表示します。
1. 新しいキーのバージョンの URI を入力します。 または、キー コンテナーとキーを再び選択して、バージョンを更新してもかまいません。
1. 変更を保存します。

## <a name="use-a-different-key"></a>別のキーを使用する

Azure Storage 暗号化に使用されるキーを変更するには、次の手順を実行します。

1. お使いのストレージ アカウントに移動し、 **[暗号化]** の設定を表示します。
1. 新しいキーの URI を入力します。 キーコンテナーを選択して新しいキーを選択することもできます。
1. 変更を保存します。

## <a name="disable-customer-managed-keys"></a>カスタマー マネージド キーを無効にする

カスタマー マネージド キーを無効にすると、ストレージ アカウントは、Microsoft が管理するキーを使用して再び暗号化されます。 カスタマー マネージド キーを無効にするには、次の手順を実行します。

1. お使いのストレージ アカウントに移動し、 **[暗号化]** の設定を表示します。
1. **[独自のキーを使用する]** 設定の横にあるチェック ボックスをオフにします。

## <a name="next-steps"></a>次のステップ

- [保存データに対する Azure Storage 暗号化](storage-service-encryption.md)
- [Azure Key Vault とは](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
