---
title: Azure portal を使用してカスタマー マネージド キーを構成する
titleSuffix: Cognitive Services
description: Azure portal を使用して、Azure Key Vault でカスタマー マネージド キーを構成する方法について説明します。 カスタマー マネージド キーを使用すると、アクセス制御の作成、ローテーション、無効化、および取り消しを行うことができます。
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 04a8a8d2520376931f2bb7727c1e751b83f6315f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "81455259"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Azure portal を使用して Azure Key Vault でカスタマー マネージド キーを構成する

カスタマー マネージド キーを格納するには、Azure Key Vault を使用する必要があります。 独自のキーを作成してキー コンテナーに格納することも、Azure Key Vault API を使ってキーを生成することもできます。 Cognitive Services リソースとキー コンテナーは、同じリージョンの同じ Azure Active Directory (Azure AD) テナント内に存在する必要がありますが、サブスクリプションは異なっていてもかまいません。 Azure Key Vault の詳細については、「[Azure Key Vault とは](https://docs.microsoft.com/azure/key-vault/key-vault-overview)」をご覧ください。

この記事では、[Azure portal](https://portal.azure.com/) を使って、カスタマー マネージド キーで Azure Key Vault を構成する方法について説明します。 Azure portal を使ってキー コンテナーを作成する方法を学習するには、「[クイック スタート: Azure portal を使用して Azure Key Vault との間でシークレットの設定と取得を行う](../../key-vault/secrets/quick-create-portal.md)」をご覧ください。

## <a name="configure-azure-key-vault"></a>Azure Key Vault を構成する

カスタマー マネージド キーを使用するには、キー コンテナーで "**論理的な削除**" と "**消去しない**" の 2 つのプロパティが設定されている必要があります。 これらのプロパティは既定では有効になっていませんが、新規または既存のキー コンテナーに対して PowerShell または Azure CLI を使用して有効にすることができます。

> [!IMPORTANT]
> "**論理的な削除**" と "**消去しない**" のプロパティを有効にしないで、キーを削除すると、Cognitive Services リソース内のデータを復旧できなくなります。

既存のキー コンテナーでこれらのプロパティを有効にする方法については、次のいずれかの記事の「**論理的な削除を有効にする**」および「**消去保護を有効にする**」を参照してください。

- [PowerShell で Key Vault の論理的な削除を使用する方法](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)
- [CLI で Key Vault の論理的な削除を使用する方法](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli)

Azure Storage 暗号化では、サイズが 2048 の RSA キーのみがサポートされています。 キーの詳細については、「[Azure Key Vault のキー、シークレット、証明書について](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)」の「**Key Vault のキー**」を参照してください。

## <a name="enable-customer-managed-keys"></a>カスタマー マネージド キーを有効にする

Azure portal でカスタマー マネージド キーを有効にするには、次の手順のようにします。

1. Cognitive Services リソースに移動します。
1. Cognitive Services リソースの **[設定]** ブレードで、 **[暗号化]** をクリックします。 次の図に示すように、 **[カスタマー マネージド キー]** オプションを選択します。

    ![[カスタマー マネージド キー] を選択する方法を示すスクリーンショット](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>キーを指定する

カスタマー マネージド キーを有効にした後で、キーと Cognitive Services リソースの関連付けを指定することができます。

### <a name="specify-a-key-as-a-uri"></a>URI としてキーを指定する

URI としてキーを指定するには、次の手順のようにします。

1. Azure portal でキーの URI を調べるには、キー コンテナーに移動して、 **[キー]** 設定を選択します。 目的のキーを選択し、キーをクリックしてそのバージョンを表示します。 そのバージョンの設定を表示するには、キーのバージョンを選択します。
1. URI を示している **[キー識別子]** フィールドの値をコピーします。

    ![キー コンテナーのキー URI が表示されているスクリーンショット](../media/cognitive-services-encryption/key-uri-portal.png)

1. ストレージ アカウントの **[暗号化]** の設定で、 **[キー URI を入力]** オプションを選択します。
1. コピーした URI を **[キー URI]** フィールドに貼り付けます。

   ![キー URI の入力方法が示されているスクリーンショット](../media/cognitive-services-encryption/ssecmk2.png)

1. キー コンテナーを含むサブスクリプションを指定します。
1. 変更を保存します。

### <a name="specify-a-key-from-a-key-vault"></a>キー コンテナーのキーを指定する

キー コンテナーからキーを指定するには、まず、キーが含まれるキー コンテナーがあることを確認します。 キー コンテナーからキーを指定するには、次の手順のようにします。

1. **[Select from Key Vault]\(キー コンテナーから選択\)** オプションを選択します。
1. 使用するキーを含むキー コンテナーを選択します。
1. キー コンテナーからキーを選択します。

   ![カスタマー マネージド キーのオプションが示されているスクリーンショット](../media/cognitive-services-encryption/ssecmk3.png)

1. 変更を保存します。

## <a name="update-the-key-version"></a>キーのバージョンを更新する

キーの新しいバージョンを作成した場合、その新しいバージョンを使用するには、Cognitive Services リソースを更新します。 次の手順に従います。

1. Cognitive Services リソースに移動し、 **[暗号化]** の設定を表示します。
1. 新しいキーのバージョンの URI を入力します。 または、キー コンテナーとキーを再び選択して、バージョンを更新してもかまいません。
1. 変更を保存します。

## <a name="use-a-different-key"></a>別のキーを使用する

暗号化に使用されるキーを変更するには、次の手順のようにします。

1. Cognitive Services リソースに移動し、 **[暗号化]** の設定を表示します。
1. 新しいキーの URI を入力します。 キーコンテナーを選択して新しいキーを選択することもできます。
1. 変更を保存します。

## <a name="disable-customer-managed-keys"></a>カスタマー マネージド キーを無効にする

カスタマー マネージド キーを無効にすると、Cognitive Services リソースはそれ以降、Microsoft マネージド キーを使用して暗号化されます。 カスタマー マネージド キーを無効にするには、次の手順を実行します。

1. Cognitive Services リソースに移動し、 **[暗号化]** の設定を表示します。
1. **[独自のキーを使用する]** 設定の横にあるチェック ボックスをオフにします。

## <a name="next-steps"></a>次のステップ

* [Azure Key Vault とは](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Cognitive Services カスタマー マネージド キー要求フォーム](https://aka.ms/cogsvc-cmk)
* [Face サービスによる保存データの暗号化](../Face/face-encryption-of-data-at-rest.md)
* [QnA Maker での保存データの暗号化](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [Language Understanding サービスでの保存データの暗号化](../LUIS/luis-encryption-of-data-at-rest.md)
* [Content Moderator による保存データの暗号化](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [Translator による保存データの暗号化](../translator/translator-encryption-of-data-at-rest.md)
* [Personalizer による保存データの暗号化](../personalizer/personalizer-encryption-of-data-at-rest.md)
