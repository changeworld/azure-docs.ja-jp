---
title: ユーザーが管理する Azure Key Vault キーを Azure Storage Service Encryption に使用する | Microsoft Docs
description: Azure Storage Service Encryption 機能を使用すると、データを格納するときにサービス側で Azure Blob Storage を暗号化し、データを取得するときに暗号化を解除することができます。この機能には、ユーザーが管理するキーを使用することができます。
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/07/2018
ms.author: lakasa
ms.openlocfilehash: 1360d8bb0911c424747209c69b830fc1ee461798
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>ユーザーが管理する Azure Key Vault キーを Storage Service Encryption に使用する

Microsoft Azure はお客様がそのデータを保護し、組織のセキュリティとコンプライアンスの必達目標を満たせるよう支援するために取り組んでいます。 Azure Storage でデータを保護する方法の 1 つとして、Storage Service Encryption (SSE) があります。SSE では、ストレージに書き込むときにデータを暗号化し、取得するときにデータの暗号化を解除することができます。 暗号化と解読は自動的かつ透過的に行われ、現在最も強力なブロック暗号の 1 つである 256 ビットの [AES 暗号化](https://wikipedia.org/wiki/Advanced_Encryption_Standard)が使用されます。

SSE には Microsoft が管理する暗号化キーのほか、ユーザー独自の暗号化キーを使うことができます。 この記事では、独自の暗号化キーを使用する方法について説明します。 Microsoft が管理するキーまたは SSE 全般について詳しくは、「[Storage Service Encryption for Data at Rest (保存データに対する Storage Service Encryption) ](storage-service-encryption.md)」をご覧ください。

BLOB およびファイル ストレージ用の SSE は、Azure Key Vault に統合されます。そのため、キー コンテナーを使用して暗号化キーを管理できます。 独自の暗号化キーを作成してキー コンテナーに格納したり、Azure Key Vault の API を使って暗号化キーを生成したりすることができます。 Azure Key Vault では、キーを管理および制御したり、キーの使用状況を監査したりすることもできます。

なぜ独自のキーを作成するのでしょうか? カスタム キーを作成すると、柔軟性が向上し、アクセス制御を作成、回転、無効化、および定義できるようになります。 また、データ保護に使用される暗号化キーを監査できるようにもなります。

## <a name="get-started-with-customer-managed-keys"></a>ユーザーが管理するキーを使用する

ユーザーが管理するキーを SSE に使用するには、新しいキー コンテナーとキーを作成するか、既にあるキー コンテナーとキーを使用します。 ストレージ アカウントとキー コンテナーは同じリージョンに存在していることが必要です。ただし、サブスクリプションは異なっていてもかまいません。 

### <a name="step-1-create-a-storage-account"></a>ステップ 1: ストレージ アカウントを作成する

まだお持ちでない場合は、最初にストレージ アカウントを作成します。 詳細については、「[Create a new storage account](storage-quickstart-create-account.md)」(ストレージ アカウントの新規作成) を参照してください。

### <a name="step-2-enable-sse-for-blob-and-file-storage"></a>ステップ 2: BLOB およびファイル ストレージに対して SSE を有効にする

ユーザーが管理するキーを使用して SSE を有効にするには、2 つのキー保護機能である [論理的な削除] と [Do Not Purge]\(消去しない\) も有効にする必要があります。 これらの設定により、キーを誤って削除したり、意図的に削除したりできなくなります。 キーの最大リテンション期間は 90 日に設定されています。この期間は、悪意のあるアクターやランサムウェア攻撃からユーザーが保護されます。

ユーザーが管理する SSE 用のキーをプログラムを使用して有効にする場合は、[Azure Storage Resource Provider REST API](https://docs.microsoft.com/rest/api/storagerp)、[.NET 用 Storage Resource Provider クライアント ライブラリ](https://docs.microsoft.com/dotnet/api)、[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)、または [Azure CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli) を使用します。

ユーザーが管理するキーを SSE に使用するには、ストレージ アカウント ID をストレージ アカウントに割り当てる必要があります。 ID を設定するには、以下の PowerShell コマンドを実行します。

```powershell
Set-AzureRmStorageAccount -ResourceGroupName \$resourceGroup -Name \$accountName -AssignIdentity
```

[論理的な削除] および [Do Not Purge]\(消去しない\) を有効にするには、以下の PowerShell コマンドを実行します。

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enableSoftDelete -Value 'True'

Set-AzureRmResource -resourceid $resource.ResourceId -Properties
$resource.Properties

($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enablePurgeProtection -Value 'True'

Set-AzureRmResource -resourceid $resource.ResourceId -Properties
$resource.Properties
```

### <a name="step-3-enable-encryption-with-customer-managed-keys"></a>ステップ 3: ユーザーが管理するキーによる暗号化を有効にする

既定では、Microsoft によって管理されたキーが SSE に使用されます。 ユーザーが管理するキーを使用する SSE をストレージ アカウントに対して有効にするには、[Azure Portal](https://portal.azure.com/) を使用します。 ストレージ アカウントの **[設定]** ブレードで、**[暗号化]** をクリックします。 以下の図に示すように、**[Use your own key]\(独自のキーの使用\)** オプションを選択します。

![暗号化オプションが表示されたポータルのスクリーンショット](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

### <a name="step-4-select-your-key"></a>ステップ 4: キーを選択する

URI としてキーを指定することも、キー コンテナーのキーを選択してキーを指定することもできます。

#### <a name="specify-a-key-as-a-uri"></a>URI としてキーを指定する

URI からキーを指定するには、以下のステップを実行します。

1. **[キー URI を入力]** オプションを選択します。  
2. **[キー URI]** フィールドで、URI を指定します。

    ![[キー URI を入力] 暗号化オプションが表示されたポータルのスクリーンショット](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)


#### <a name="specify-a-key-from-a-key-vault"></a>キー コンテナーのキーを指定する 

キー コンテナーのキーを指定するには、以下のステップを実行します。

1. **[Select from Key Vault]\(キー コンテナーから選択\)** オプションを選択します。  
2. 使用するキーを含むキー コンテナーを選択します。
3. キー コンテナーからキーを選択します。

    ![[独自のキーを使用する] 暗号化オプションが表示されたポータルのスクリーンショット](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

キー コンテナーへのアクセス権がストレージ アカウントにない場合は、以下の画像に示す Azure PowerShell コマンドを実行して、アクセス権を付与します。

![キー コンテナーへのアクセスが拒否されたことを示すポータルのスクリーンショット](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

アクセス権の付与は、Azure Portal で行うこともできます。Azure Portal の [Azure Key Vault] に移動して、ストレージ アカウントにアクセス権を付与します。


次の PowerShell コマンドを使用して、上記のキーを既存のストレージ アカウントに関連付けることができます。
```powershell
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount"
$keyVault = Get-AzureRmKeyVault -VaultName "mykeyvault"
$key = Get-AzureKeyVaultKey -VaultName $keyVault.VaultName -Name "keytoencrypt"
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVault.VaultName -ObjectId $storageAccount.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
Set-AzureRmStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName -AccountName $storageAccount.StorageAccountName -EnableEncryptionService "Blob" -KeyvaultEncryption -KeyName $key.Name -KeyVersion $key.Version -KeyVaultUri $keyVault.VaultUri
```


### <a name="step-5-copy-data-to-storage-account"></a>ステップ 5: ストレージ アカウントにデータをコピーする

新しいストレージ アカウントにデータを暗号化した状態で転送する方法については、[「Storage Service Encryption for Data at Rest (保存データに対する Storage Service Encryption)」の「Getting Started (概要)」の手順 3](storage-service-encryption.md#step-3-copy-data-to-storage-account) を参照してください。

### <a name="step-6-query-the-status-of-the-encrypted-data"></a>ステップ 6: 暗号化されたデータの状態を照会する

暗号化データの状態を照会する方法については、[「Storage Service Encryption for Data at Rest (保存データに対する Storage Service Encryption)」の「Getting Started (概要)」の手順 4](storage-service-encryption.md#step-4-query-the-status-of-the-encrypted-data) を参照してください。

## <a name="faq-for-sse-with-customer-managed-keys"></a>ユーザーが管理するキーを使用する SSE に関してよくあるご質問

**Q: Premium Storage を使用しています。ユーザーが管理するキーを SSE に使うことはできますか?**

A: はい。Standard Storage と Premium Storage のどちらでも、Microsoft が管理するキーとユーザーが管理するキーを SSE にご利用いただけます。

**Q: ユーザーが管理するキーを使用する SSE が有効な新しいストレージ アカウントを Azure PowerShell と Azure CLI で作成できますか?**

A: はい。

**Q: ユーザーが管理するキーを SSE に使用する場合、Azure Storage の料金はどれくらい増えますか?**

A: Azure Key Vault の使用に関連した料金が発生します。 詳細については、「[Key Vault の価格](https://azure.microsoft.com/pricing/details/key-vault/)」を参照してください。 SSE の追加料金はありません。これは、すべてのストレージ アカウントに当てはまります。

**Q: 暗号化キーへのアクセスを取り消すことはできますか?**

A: はい。いつでもアクセスを取り消すことができます。 キーへのアクセスは、いくつかの方法で取り消すことができます。 詳細については、[Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) に関するページと [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault) に関するページを参照してください。 アクセスを取り消すと、Azure Storage がアカウント暗号化キーにアクセスできなくなるため、ストレージ アカウント内の全 BLOB へのアクセスが事実上ブロックされます。

**Q: ストレージ アカウントとキーを別々のリージョンに作成してもかまいませんか。**

A: いいえ。ストレージ アカウントと Azure Key Vault/キーは、同じリージョンに存在する必要があります。

**Q: ストレージ アカウントを作成しているときに、ユーザーが管理するキーを SSE で使用可能にすることはできますか?**

A: いいえ。 最初にストレージ アカウントを作成する場合、SSE で使用可能になるのは、Microsoft が管理するキーのみです。 ユーザーが管理するキーを使用するには、ストレージ アカウントのプロパティを更新する必要があります。 REST またはいずれかのストレージ クライアント ライブラリを使用してプログラムでストレージ アカウントを更新するか、ストレージ アカウントの作成後にそのプロパティを Azure Portal で更新してください。

**Q: ユーザーが管理するキーを SSE で使用しているときに、暗号化を無効にすることはできますか?**

A: いいえ、暗号化を無効にすることはできません。 既定では、すべてのサービス (BLOB、ファイル、テーブル、キュー ストレージ) で暗号化が有効です。 必要に応じて、Microsoft が管理するキーからユーザーが管理するキーへと、使用するキーを切り替えることができます (その逆も可能です)。

**Q: 新しいストレージ アカウントを作成すると、SSE は既定で有効になりますか?**

A: 既定では、すべてのストレージ アカウントとすべてのサービス (BLOB、ファイル、テーブル、キュー ストレージ) で SSE が有効になっています。

**Q: 自分のストレージ アカウントで、ユーザーが管理するキーを使用して SSE を有効にすることができません。**

A: そのアカウントは、Azure Resource Manager ストレージ アカウントですか? ユーザーが管理するキーでは、クラシック ストレージ アカウントはサポートされていません。 また、ユーザーが管理するキーを使用する SSE は、Resource Manager ストレージ アカウントでのみ有効にすることができます。

**Q: [論理的な削除] および [Do Not Purge]\(消去しない\) とは、何ですか?ユーザーが管理するキーで SSE を使用するには、この設定を有効にする必要はありますか。**

A: ユーザーが管理するキーで SSE を使用するには、[論理的な削除] と [Do Not Purge]\(消去しない\) を有効にする必要があります。 これらの設定により、キーが誤って削除されたり、意図的に削除されたりしなくなくなります。 キーの最大リテンション期間は 90 日に設定されています。この期間は、悪意のあるアクターやランサムウェア攻撃からユーザーが保護されます。 この設定を無効にすることはできません。

**Q: ユーザーが管理するキーを使用する SSE は、特定のリージョンでしか使用できないのでしょうか?**

A: ユーザーが管理するキーを使用する SSE は、すべてのリージョンで BLOB ストレージとファイル ストレージに対して使用できます。

**Q: 問題やフィードバックの連絡先はどこですか?**

A: Storage Service Encryption に関する問題は、 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) にご連絡ください。

## <a name="next-steps"></a>次の手順

-   安全なアプリケーション開発を支援する包括的なセキュリティ機能の詳細については、[Storage セキュリティ ガイド](storage-security-guide.md)に関するページをご覧ください。

-   Azure Key Vault の概要については、「[Azure Key Vault とは](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)」を参照してください。

-   Azure Key Vault の概要については、[Azure Key Vault の概要](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)に関するページを参照してください。
