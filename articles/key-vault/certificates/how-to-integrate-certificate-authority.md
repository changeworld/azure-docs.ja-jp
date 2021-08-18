---
title: Key Vault と DigiCert 証明機関の統合
description: この記事では、Key Vault と DigiCert 証明機関を統合して、ネットワークに証明書をプロビジョニング、管理、デプロイする方法について説明します。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 06/02/2020
ms.author: sebansal
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9cec61df061c7a504da412851349064273507173
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110678858"
---
# <a name="integrating-key-vault-with-digicert-certificate-authority"></a>Key Vault と DigiCert 証明機関の統合

Azure Key Vault を使用すると、ネットワークのデジタル証明書のプロビジョニング、管理、およびデプロイを簡単に行うことができ、アプリケーションのセキュリティで保護された通信を実現できます。 デジタル証明書は、電子的なトランザクションにおいて身元を証明するための電子資格情報です。 

Azure Key Vault ユーザーは、自分のキー コンテナーから DigiCert 証明書を直接生成できます。 Key Vault と DigiCert 証明機関の間に信頼できるパートナーシップが結ばれます。 このパートナーシップにより、DigiCert によって発行された証明書のエンドツーエンドの証明書ライフサイクル管理が保証されます。

証明書に関する一般的な情報については、[Azure Key Vault の証明書](./about-certificates.md)に関するページを参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、以下が必要です。
* キー コンテナー。 既存のキー コンテナーを使用するか、次のいずれかのクイックスタートの手順に従って新しいキー コンテナーを作成できます。
   - [Azure CLI を使用してキー コンテナーを作成する](../general/quick-create-cli.md)
   - [Azure PowerShell を使用してキー コンテナーを作成する](../general/quick-create-powershell.md)
   - [Azure portal を使用してキー コンテナーを作成する](../general/quick-create-portal.md)
*   アクティブ化された DigiCert CertCentral アカウント。 CertCentral アカウントに[サインアップ](https://www.digicert.com/account/signup/)する。
*   対象のアカウントの管理者レベルのアクセス許可。


### <a name="before-you-begin"></a>開始する前に

DigiCert CertCentral アカウントから次の情報を取得していることを確認します。
-   CertCentral アカウント ID
-   組織 ID
-   API キー

## <a name="add-the-certificate-authority-in-key-vault"></a>キー コンテナーへの証明機関の追加 
DigiCert CertCentral アカウントから上記の情報を収集した後、キー コンテナーの証明機関の一覧に DigiCert を追加できます。

### <a name="azure-portal"></a>Azure portal

1.  DigiCert 証明機関を追加するには、追加先のキー コンテナーに移動します。 
2.  Key Vault のプロパティ ページで、 **[証明書]** を選択します。
3.  **[証明書機関]** タブを選択します。:::image type="content" source="../media/certificates/how-to-integrate-certificate-authority/select-certificate-authorities.png" alt-text="[証明書機関] タブの選択を示すスクリーンショット。":::
4.  **[追加]** を選択します。:::image type="content" source="../media/certificates/how-to-integrate-certificate-authority/add-certificate-authority.png" alt-text="[証明書機関] タブの [追加] ボタンを示すスクリーンショット。":::
5.  **[証明機関の作成]** で、次の値を入力します。
    -   **[名前]** : 識別可能な発行者名。 例: **DigiCertCA**。
    -   **[プロバイダー]** : **DigiCert**。
    -   **[アカウント ID]** : DigiCert CertCentral のアカウント ID。
    -   **[アカウントのパスワード]** : DigiCert CertCentral アカウントで生成した API キー。
    -   **[組織 ID]** : DigiCert CertCentral アカウントの組織 ID。 

1. **［作成］** を選択します
   
DigicertCA が証明機関の一覧に表示されるようになりました。


### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell を使用すると、コマンドまたはスクリプトを使って Azure リソースを作成および管理できます。 Azure によってホストされている Azure Cloud Shell は、ブラウザー内で Azure portal を介して使用できる対話型シェル環境です。

PowerShell をローカルにインストールして使用することを選択する場合、次の手順を完了するには、Azure AZ PowerShell モジュール 1.0.0 以降が必要です。 `$PSVersionTable.PSVersion` を入力して、バージョンを確認します。 アップグレードが必要な場合は、[Azure AZ PowerShell モジュールをインストールする](/powershell/azure/install-az-ps)方法に関するページを参照してください。 PowerShell をローカルで実行する場合は、`Login-AzAccount` も実行して Azure との接続を作成する必要があります。

```azurepowershell-interactive
Login-AzAccount
```

1.  [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

    ```azurepowershell-interactive
    New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
    ```

2. 一意の名前を持つキー コンテナーを作成します。 ここで、`Contoso-Vaultname` はキー コンテナーの名前です。

   - **コンテナー名**: `Contoso-Vaultname`
   - **リソース グループ名**: `ContosoResourceGroup`
   - **場所**: `EastUS`

    ```azurepowershell-interactive
    New-AzKeyVault -Name 'Contoso-Vaultname' -ResourceGroupName 'ContosoResourceGroup' -Location 'EastUS'
   ```

3. DigiCert CertCentral アカウントから次の値の変数を定義します。

   - **アカウント ID** 
   - **組織 ID** 
   - **API キー** 

   ```azurepowershell-interactive
   $accountId = "myDigiCertCertCentralAccountID"
   $org = New-AzKeyVaultCertificateOrganizationDetail -Id OrganizationIDfromDigiCertAccount
   $secureApiKey = ConvertTo-SecureString DigiCertCertCentralAPIKey -AsPlainText –Force
   ```

4. 発行者を設定します。 これにより、DigiCert が証明機関としてキー コンテナーに追加されます。 [パラメーターの詳細を参照してください。](/powershell/module/az.keyvault/Set-AzKeyVaultCertificateIssuer)
   ```azurepowershell-interactive
   Set-AzKeyVaultCertificateIssuer -VaultName "Contoso-Vaultname" -Name "TestIssuer01" -IssuerProvider DigiCert -AccountId $accountId -ApiKey $secureApiKey -OrganizationDetails $org -PassThru
   ```

5. 証明書のポリシーを設定し、キー コンテナー内で直接 DigiCert から証明書を発行します。

   ```azurepowershell-interactive
   $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "TestIssuer01" -ValidityInMonths 12 -RenewAtNumberOfDaysBeforeExpiry 60
   Add-AzKeyVaultCertificate -VaultName "Contoso-Vaultname" -Name "ExampleCertificate" -CertificatePolicy $Policy
   ```

これで、指定したキー コンテナーに DigiCert 証明機関によって証明書が発行されました。

## <a name="troubleshoot"></a>トラブルシューティング

発行された証明書が Azure portal で無効状態になっている場合は、[証明書の操作] を表示して、その証明書の DigiCert エラー メッセージをご確認ください。

:::image type="content" source="../media/certificates/how-to-integrate-certificate-authority/certificate-operation-select.png" alt-text="[証明書の操作] タブを示すスクリーンショット。":::

エラー メッセージ: "Please perform a merge to complete this certificate request \(この証明書要求を完了するには、マージを実行してください\)"。
   
証明機関によって署名された CSR をマージして、要求を完了します。 CSR のマージの詳細については、[CSR を作成、マージする](./create-certificate-signing-request.md)方法に関するページを参照してください。

詳細については、[Key Vault REST API リファレンスの証明書の操作](/rest/api/keyvault)に関する記事をご覧ください。 アクセス許可の設定については、「[コンテナー - 作成または更新](/rest/api/keyvault/vaults/createorupdate)」と「[コンテナー - アクセス ポリシーの更新](/rest/api/keyvault/vaults/updateaccesspolicy)」をご覧ください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

- **Key Vault を使用して DigiCert ワイルドカード証明書を生成できますか?** 
   
  はい。ただし、DigiCert アカウントの構成方法に依存します。
- **DigiCert を使用して OV SSL または EV SSL 証明書を作成するには、どうすればよいですか?**
 
   Key Vault では、OV および EV SSL 証明書の作成がサポートされています。 証明書を作成するときに、 **[ポリシーの詳細構成]** を選択し、証明書の種類を指定します。 サポートされる値: OV SSL、EV SSL
   
   ご自分の DigiCert アカウントで許可している場合は、Key Vault にこの種類の証明書を作成できます。 この種類の証明書では、検証は DigiCert によって実行されます。 検証に失敗した場合は、DigiCert サポート チームにお問い合わせください。 `subjectName` に情報を定義すると、証明書を作成するときに情報を追加できます。

  例: `SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"`。
   
- **統合により DigiCert 証明書を作成する方が、DigiCert から直接取得するよりも時間がかかりますか?**
   
   いいえ。 証明書を作成するとき、検証プロセスに時間がかかる場合があります。 プロセスは、DigiCert によって制御されます。


## <a name="next-steps"></a>次のステップ

- [認証、要求、応答](../general/authentication-requests-and-responses.md)
- [Key Vault 開発者ガイド](../general/developers-guide.md)
