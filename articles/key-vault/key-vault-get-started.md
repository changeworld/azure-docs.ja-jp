---
title: Azure Key Vault の概要 | Microsoft Docs
description: このチュートリアルを使用すると、Azure Key Vault で、強化されたコンテナーを Azure に作成し、暗号化キーやシークレットを Azure に格納して管理できるようになります。
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 36721e1d-38b8-4a15-ba6f-14ed5be4de79
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2018
ms.author: barclayn
ms.openlocfilehash: fcf3c7d47e43adc94d2de933430f16d64ec987a2
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2018
ms.locfileid: "41918792"
---
# <a name="get-started-with-azure-key-vault"></a>Azure Key Vault の概要
この記事では、PowerShell を使用した Azure Key Vault の操作について取り上げると共に、次のアクティビティの手順を紹介します。
- 強化されたコンテナー (資格情報コンテナー) を Azure に作成する方法。
- Key Vault を使用して暗号化キーとシークレットを Azure に保管して管理する方法。
- アプリケーションがそのキーやパスワードを使用する方法。

Azure Key Vault は、ほとんどのリージョンで使用できます。 詳細については、 [Key Vault の価格のページ](https://azure.microsoft.com/pricing/details/key-vault/)を参照してください。

クロスプラットフォーム コマンド ライン インターフェイスの手順については、 [対応するチュートリアル](key-vault-manage-with-cli2.md)を参照してください。

## <a name="requirements"></a>必要条件
この記事の作業を進める前に、次の要件を満たしていることを確認してください。

- **Azure サブスクリプション**。 このサブスクリプションがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。
- **Azure PowerShell** **1.1.0 以降のバージョン**。 Azure PowerShell をインストールして、Azure サブスクリプションに関連付けるには、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)」を参照してください。 Azure PowerShell をインストール済みで、バージョンがわからない場合は、Azure PowerShell コンソールで「 `(Get-Module azure -ListAvailable).Version`」と入力します。 Azure PowerShell バージョン 0.9.1 ～ 0.9.8 がインストールされている場合は、少し変更を加えるだけで、引き続きこのチュートリアルを利用できます。 たとえば、 `Switch-AzureMode AzureResourceManager` コマンドを使用する必要があったり、Azure Key Vault のコマンドの一部が変更されていたりします。 バージョン 0.9.1 ～ 0.9.8 の Key Vault コマンドレットの一覧については、「[Azure Key Vault Cmdlets (Azure Key Vault コマンドレット)](/powershell/module/azurerm.keyvault/#key_vault)」を参照してください。
- **Key Vault を使用するように構成できるアプリケーション**。 サンプル アプリケーションは、[Microsoft ダウンロード センター](http://www.microsoft.com/download/details.aspx?id=45343)から入手できます。 手順については、付属の **Readme** ファイルをご覧ください。

>[!NOTE]
この記事では、PowerShell と Azure を基本的に理解していることを前提としています。 PowerShell の詳細については、「[Windows PowerShell ファースト ステップ ガイド](https://technet.microsoft.com/library/hh857337.aspx)」を参照してください。

このチュートリアルに表示されているコマンドレットの詳しいヘルプを確認には、 **Get-Help** コマンドレットを使用します。

```powershell-interactive
Get-Help <cmdlet-name> -Detailed
```
    
たとえば、**Connect-AzureRmAccount** コマンドレットのヘルプを確認するには、次のように入力します。

```PowerShell
Get-Help Connect-AzureRmAccount -Detailed
```

また、次の記事を読むと、Azure PowerShell での Azure Resource Manager デプロイ モデルを理解することができます。

* [Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)
* [リソース マネージャーでの Azure PowerShell の使用](../powershell-azure-resource-manager.md)

## <a id="connect"></a>サブスクリプションへの接続
Azure PowerShell セッションを開始し、次のコマンドで Azure アカウントにサインインします。  

```PowerShell
Connect-AzureRmAccount
```

>[!NOTE]
 特定の Azure インスタンスを使用している場合は、-Environment パラメーターを使用してください。 例:  
 ```powershell
 Connect-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)
 ```

ポップアップ ブラウザー ウィンドウで、Azure アカウントのユーザー名とパスワードを入力します。 Azure PowerShell は、このアカウントに関連付けられているすべてのサブスクリプションを取得し、既定で最初のサブスクリプションを使用します。

複数のサブスクリプションがあり、特定の 1 つのサブスクリプションを指定して Azure Key Vault を使用する場合は、次のように入力して自分のアカウントのサブスクリプションを表示します。

```powershell
Get-AzureRmSubscription
```

使用するサブスクリプションを指定するには、次のように入力します。

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

Azure PowerShell の詳細については、「 [How to install and configure Azure PowerShell (Azure PowerShell のインストールと構成の方法)](/powershell/azure/overview)」をご覧ください。

## <a id="resource"></a>新しいリソース グループを作成する
Azure リソース マネージャーを使用すると、すべての関連するリソースが 1 つのリソース グループ内に作成されます。 このチュートリアルでは、 **ContosoResourceGroup** という名前の新しいリソース グループを作成します。

```powershell
New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East US'
```

## <a id="vault"></a>Key Vault を作成する
[New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) コマンドレットを使用して、キー コンテナーを作成します。 このコマンドレットには、3 つの必須パラメーター (**リソース グループ名**、**キー コンテナー名**、**地理的な場所**) が含まれています。

たとえば、以下を使用するとします。
- コンテナー名: **ContosoKeyVault**
- リソース グループ名: **ContosoResourceGroup**
- 場所: **米国東部**

この場合、次のように入力します。

```powershell
New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```
![キー コンテナー作成コマンドの実行後の出力](./media/key-vault-get-started/output-after-creating-keyvault.png)

このコマンドレットの出力では、作成したキー コンテナーのプロパティが示されます。 最も重要な 2 つのプロパティは、次のとおりです。

* **Vault Name**: この例では、これは **ContosoKeyVault** です。 この名前を他の Key Vault コマンドレットに使用できます。
* **Vault URI (コンテナー URI)**: この例では、これは https://contosokeyvault.vault.azure.net/ です。 その REST API から資格情報コンテナーを使用するアプリケーションは、この URI を使用する必要があります。

Azure アカウントは、この Key Vault ですべての操作の実行が許可されるようになりました。 まだ、どのユーザーも許可されていません。

> [!NOTE]
> 新しいキー コンテナーを作成しようとすると、"**サブスクリプションが名前空間 'Microsoft.KeyVault' を使用するように登録されていません**" というエラーが表示される場合があります。 このメッセージが表示された場合は、`Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"` を実行してください。 登録が正常に完了したら、New-AzureRmKeyVault コマンドを再実行できます。 詳細については、「[Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider)」を参照してください。
>
>

## <a id="add"></a>キーやシークレットを Key Vault に追加する
Key Vault とキーまたはシークレットと対話操作するために必要な方法はいくつか存在します。

### <a name="azure-key-vault-generates-a-software-protected-key"></a>ソフトウェアで保護されたキーを Azure Key Vault で生成する

Azure Key Vault でソフトウェアで保護されたキーを作成する場合は、[Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) コマンドレットを使用し、次のように入力します。

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'
```
このキーの URI を表示するには、次のように入力します。
```powershell
$key.id
```

作成したキーや、Azure Key Vault にアップロードしたキーは、その URI を使用すると参照できます。 現在のバージョンを取得するには、**https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** を使用してください。**https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** を使用すると、この特定のバージョンが取得されます。  

### <a name="importing-an-existing-pfx-file-into-azure-key-vault"></a>既存の PFX ファイルを Azure Key Vault にインポートする

pfx ファイルに保存されている既存のキーを Azure Key Vault にアップロードする場合は、手順が異なります。 例: 
- ソフトウェアで保護されたキーが .PFX ファイルに既に存在する場合。
- pfx ファイルに softkey.pfx という名前が付けられている場合。 
- そのファイルが C ドライブに保存されている場合。

次のように入力できます。

```powershell
$securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force  // This stores the password 123 in the variable $securepfxpwd
```

次のように入力して、.PFX ファイルからキーをインポートします。このキーは、Key Vault サービスのソフトウェアで保護されます。

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoImportedPFX' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd
```

このキーの URI を表示するには、次のように入力します。

```powershell
$Key.id
```
キーを表示するには、次のように入力します。 

```powershell
Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'
```
ポータルで PFX ファイルのプロパティを表示すると、次のような画像のように表示されます。

![ポータルでの証明書の表示](./media/key-vault-get-started/imported-pfx.png)
### <a name="to-add-a-secret-to-azure-key-vault"></a>Azure Key Vault にシークレットを追加するには

資格情報コンテナーにシークレットを追加する (SQLPassword という名前で値が Pa$$w0rd のパスワードを Azure Key Vault に設定する) には、次のように入力して、まず Pa$$w0rd 値をセキュリティで保護された文字列に変換します。

```powershell    
$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force
```

その後、次を入力します。

```powershell
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue
```


Azure Key Vault に追加したパスワードは、その URI を使用すると参照できます。 常に現在のバージョンを取得するには **https://ContosoVault.vault.azure.net/secrets/SQLPassword** を使用します。この特定のバージョンを取得するには、**https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** を使用します。

このシークレットの URI を表示するには、次のように入力します。

```powershell
$secret.Id
```
自分のシークレットを表示するには、「`Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`」と入力します。または、ポータルでシークレットを表示することもできます。

![secret](./media/key-vault-get-started/secret-value.png)

シークレットに格納されている値をプレーンテキストとして表示するには:
```powershell
(get-azurekeyvaultsecret -vaultName "Contosokeyvault" -name "SQLPassword").SecretValueText
```
Key Vault とキーやシークレットは、アプリケーションを使用できる状態になりました。 これらを使用するには、アプリケーションを承認する必要があります。  

## <a id="register"></a>Azure Active Directory にアプリケーションを登録する
この手順は通常、開発者が別のコンピューター上で行います。 これは Azure Key Vault に固有ではありません。 Azure Active Directory にアプリケーションを登録する手順の詳細については、「[Azure Active Directory とアプリケーションの統合](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)」または「[リソースにアクセスできる Azure Active Directory アプリケーションとサービス プリンシパルをポータルで作成する](../azure-resource-manager/resource-group-create-service-principal-portal.md)」の記事を参照してください。

> [!IMPORTANT]
> チュートリアルを完了するには、この手順で登録するアカウント、資格情報コンテナー、アプリケーションがすべて同じ Azure ディレクトリに格納されている必要があります。


Key Vault を使用するアプリケーションは、Azure Active Directory から取得したトークンを使用して認証する必要があります。 これを行うには、アプリケーションの所有者は、まず Azure Active Directory でアプリケーションを登録する必要があります。 登録の最後に、アプリケーションの所有者は次の値を取得します。

- **アプリケーション ID** 
- **認証キー** (共有シークレットとも呼ばれます) 

アプリケーションは、トークンを取得するために、この 2 つの値を Azure Active Directory に示す必要があります。 これを行うようにアプリケーションを構成する方法は、アプリケーションによって異なります。 [Key Vault のサンプル アプリケーション](https://www.microsoft.com/download/details.aspx?id=45343)の場合は、アプリケーション所有者がこれらの値を app.config ファイルに設定します。


Azure Active Directory にアプリケーションを登録するには:

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側にある **[アプリの登録]** をクリックします。 [アプリの登録] が表示されない場合は、**[その他のサービス]** をクリックすると表示されます。  
>[!NOTE]
キー コンテナーを作成した Azure サブスクリプションが含まれている、同じディレクトリを選択する必要があります。 
3. **[新しいアプリケーションの登録]** をクリックします。
4. **[作成]** ブレードでアプリケーションの名前を入力し、**[Web アプリケーションや Web API]** (既定値) を選択して、Web アプリケーションの**サインオン URL** を指定します。 この時点でこの情報がない場合は、この手順で構成できます (たとえば、http://test1.contoso.com と指定できます)。 これらのサイトが存在するかどうかは関係ありません。 

    ![[新しいアプリケーションの登録]](./media/key-vault-get-started/new-application-registration.png)
    >[!WARNING]
    必ず **[Web アプリケーションや Web API]** を選択してください。そうしないと、設定に **[キー]** オプションが表示されません。

5. **[作成]** ボタンをクリックします。
6. アプリの登録が完了すると、登録済みのアプリの一覧が表示されます。 先ほど登録したアプリを探してクリックします。
7. **[登録済みのアプリケーション]** ブレードをクリックし、**アプリケーション ID** をコピーします。
8. **[すべての設定]** をクリックします。
9. **[設定]** ブレードで **[キー]** をクリックします。
9. **[キーの説明]** ボックスに説明を入力し、期間を選択して、**[保存]** をクリックします。 ページが更新され、キーの値が表示されます。 
10. **[アプリケーション ID]** と **[キー]** の情報は、次の手順でコンテナーのアクセス許可を設定する際に使用します。

## <a id="authorize"></a>キーまたはシークレットを使用してアプリケーションを承認する
コンテナー内のキーまたはシークレットにアクセスするアプリケーションを承認するには、2 とおりの方法があります。

### <a name="using-powershell"></a>PowerShell の使用
PowerShell を使用するには、[Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) コマンドレットを使用します。

たとえば、資格情報コンテナー名が **ContosoKeyVault** で、承認するアプリケーションのクライアント ID が 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed の場合、アプリケーションの暗号化を解除し、資格情報コンテナー内のキーで署名することを承認するには、次のように実行します。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

その同じアプリケーションを認証し、資格情報コンテナーのシークレットの読み取りを許可する場合、次を実行します。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get
```
### <a name="using-the-azure-portal"></a>Azure ポータルの使用
アプリケーションの認可を変更するには、キーまたはシークレットを使用します。
1. Key Vault リソース ブレードから **[アクセス ポリシー]** を選択します。
2. ブレードの上部にある [+ 新規追加] ボタンをクリックします。
3. **[プリンシパルの選択]** をクリックして、先ほど作成したアプリケーションを選択します。
4. **[キーのアクセス許可]** ボックスの一覧から [暗号化解除] と [署名] を選択して、資格情報コンテナー内のキーの暗号化を解除してそのキーで署名することをアプリケーションに承認します。
5. **[シークレットのアクセス許可]** ボックスの一覧から [取得] を選択して、資格情報コンテナー内のシークレットの読み取りをアプリケーションに許可します

## <a id="HSM"></a>ハードウェア セキュリティ モジュール (HSM) を使用する
さらに安心感を高めたい場合には、ハードウェア セキュリティ モジュール (HSM) でキーのインポートや生成を行うことができ、キーは HSM の境界内から出ることはありません。 HSM は、FIPS 140-2 レベル 2 で検証済みです。 この要件が自分に当てはまらない場合は、このセクションをスキップし、 [Key Vault と関連するキーとシークレットを削除する](#delete)に進んでください。

これらの HSM で保護されたキーを作成するには、[HSM で保護されたキーがサポートされている Azure Key Vault Premium サービス レベル](https://azure.microsoft.com/pricing/details/key-vault/)を使用する必要があります。 また、この機能は Azure China では使用できないことに注意してください。

キー コンテナーを作成するときに、**-SKU** パラメーターを追加します。

```powershell
New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US' -SKU 'Premium'
```


このキー コンテナーには、ソフトウェアで保護されたキー (前述のとおり) と HSM で保護されたキーを追加できます。 HSM で保護されたキーを作成するには、 **-Destination** パラメーターを 'HSM' に設定します。

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'
```

次のコマンドを使用して、自分のコンピューターの PFX ファイルからキーをインポートできます。 このコマンドでは、Key Vault サービスでキーを HSM にインポートします。

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'
```

次のコマンドは、“bring your own key" (BYOK) パッケージをインポートします。 このシナリオを使用すると、ローカルの HSM でキーを生成し、これを Key Vault サービスで HSM に転送でき、キーは HSM の境界内から出ることはありません。

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'
```

この BYOK パッケージを生成する方法の詳細な手順については、「 [Azure Key Vault の HSM 保護キーを生成し、転送する方法](key-vault-hsm-protected-keys.md)」を参照してください。

## <a id="delete"></a>Key Vault と関連付けられているキーやシークレットを削除する
キー コンテナーと、それに含まれるキーまたはシークレットが不要になった場合は、[Remove-AzureRmKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault) コマンドレットを使用してキー コンテナーを削除できます。

```powershell
Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'
```

または、Key Vault やそのグループに含まれる他のすべてのリソースを含む、Azure リソース グループ全体を削除できます。

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'
```

## <a id="other"></a>その他の Azure PowerShell コマンドレット
Azure Key Vault の管理に役立つその他のコマンドは次のとおりです。

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: このコマンドは、すべてのキーと選択したプロパティを表形式で取得します。
- `$Keys[0]`: このコマンドは、指定されたキーのプロパティの完全な一覧を表示します。
- `Get-AzureKeyVaultSecret`: このコマンドは、すべてのシークレットの名前と選択したプロパティを表形式で一覧します。
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: 特定のキーを削除する方法の例です。
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: 特定のシークレットを削除する方法の例です。

## <a name="next-steps"></a>次の手順

- Azure Key Vault の概要については、「 [Azure Key Vault とは](key-vault-whatis.md)
- Key Vault の使用方法については、「 [Azure Key Vault のログ記録](key-vault-logging.md)」を参照してください。
- Web アプリケーションでの Azure Key Vault の使用方法に関するフォローアップ チュートリアルについては、「 [Web アプリケーションからの Azure Key Vault の使用](key-vault-use-from-web-application.md)」をご覧ください。
- プログラミング リファレンスについては、「 [Azure Key Vault 開発者ガイド](key-vault-developers-guide.md)」を参照してください。
- Azure Key Vault の Azure PowerShell コマンドレットの最新の一覧については、「[Azure Key Vault Cmdlets (Azure Key Vault コマンドレット)](/powershell/module/azurerm.keyvault/#key_vault)」を参照してください。
