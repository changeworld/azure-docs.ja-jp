---
title: チュートリアル - Key Vault における証明書の自動ローテーション頻度を更新する | Microsoft Docs
description: Azure portal を使用して、Azure Key Vault における証明書の自動ローテーション頻度を更新する方法について紹介するチュートリアル。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 2e6c250a0bcb9d73e7c572dfe8138c31269993e8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82107559"
---
# <a name="tutorial-configuring-certificates-auto-rotation-in-key-vault"></a>チュートリアル:Key Vault における証明書の自動ローテーションを構成する

Azure Key Vault を使用すると、デジタル証明書のプロビジョニング、管理、デプロイを簡単に行うことができます。 その対象となる証明書には、証明機関によって署名されたパブリック SSL/TLS 証明書とプライベート SSL/TLS 証明書のほか、自己署名証明書があります。 Key Vault は、証明機関とのパートナーシップを通じて証明書を要求したり更新したりすることもでき、証明書のライフ サイクル管理のための堅牢なソリューションを実現します。 このチュートリアルでは、証明書の属性 (有効期間、自動ローテーション頻度、CA) を更新します。 Key Vault の詳細については、[概要](../general/overview.md)に関する記事をご覧ください。

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> * Azure portal を使用して証明書を管理する
> * 証明機関プロバイダーのアカウントを追加する
> * 証明書の有効期間を更新する
> * 証明書の自動ローテーション頻度を更新する
> * Azure PowerShell を使用して証明書の属性を更新する


始める前に、[Key Vault の基本的な概念](../general/basic-concepts.md)を確認してください。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-a-vault"></a>コンテナーの作成

操作を実行するために、キー コンテナーを作成するか、既存のキー コンテナーを選択します  ([キー コンテナーを作成する手順](../quick-create-portal.md))。 この例では、**Example-Vault** というコンテナー名を使用します。 

![Key Vault の作成が完了した後の出力](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Key Vault に証明書を作成する

コンテナーに証明書を作成またはインポートします  ([キー コンテナーに証明書を作成する手順](../quick-create-portal.md))。 この例では、**ExampleCertificate** という証明書を操作します。

> [!NOTE]
> Azure Key Vault では、証明書のライフ サイクル属性を、証明書の作成時だけでなく、作成後に更新することもできます。 
## <a name="updating-certificates-life-cycle-attributes"></a>証明書のライフ サイクル属性を更新する

Key Vault には、次の証明書を作成できます。 
- 自己署名証明書
- Key Vault と提携している証明機関 (CA) で作成された証明書
- Key Vault と提携していない証明機関で作成された証明書

次の証明機関は、現在 Key Vault と提携しているプロバイダーです。
- DigiCert - Key Vault は、DigiCert による OV TLS/SSL 証明書を提供します。
- GlobalSign - Key Vault は、GlobalSign による OV TLS/SSL 証明書を提供します。

Azure Key Vault では、証明機関とのパートナーシップを通じて証明書が自動的にローテーションされます。 その確立されたパートナーシップを通じて、Key Vault は証明書を自動的に要求し、更新します。 そのため、**Key Vault と提携していない CA で作成された証明書には、自動ローテーション機能が適用されません**。 

> [!NOTE]
> CA プロバイダーのアカウント管理者は、Key Vault が使用する資格情報を作成し、Key Vault を介して TLS/SSL 証明書を作成、更新、使用します。
![証明機関](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
> 


### <a name="updating-certificates-life-cycle-attributes-at-the-time-of-certificate-creation"></a>証明書の作成時に証明書のライフ サイクル属性を更新する

1. Key Vault のプロパティ ページで、 **[証明書]** を選択します。
2. **[Generate/Import]\(生成/インポート\)** をクリックします。
3. **[証明書の作成]** 画面で、次の値を更新します。
    

    - **有効期間**: 月単位の値を入力します。 セキュリティ プラクティスとして、作成する証明書の有効期間は短くすることをお勧めします。 新しく作成された証明書の有効期間は、既定では 12 か月です。
    - **有効期間のアクション タイプ**: 証明書の自動更新とアラート アクションを選択します。 選択内容に応じて、"有効期間の割合" または "有効期限までの日数" を更新します。 既定では、証明書の自動更新が、その有効期間の 80% に設定されます。<br> ドロップ ダウン メニューからオプションを選択します。

    |  特定のタイミングで自動的に更新する| 特定のタイミングですべての連絡先にメールを送信する |
    |-----------|------|
    |このオプションを選択すると、自動ローテーションがオンになります。 | このオプションを選択すると、自動ローテーションが実行されません。連絡先にアラートが送信されるだけです。|
        


4. **[作成]** をクリックします。

![証明書のライフ サイクル](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="updating-life-cycle-attributes-of-stored-certificate"></a>格納されている証明書のライフ サイクル属性を更新する

1. キー コンテナーを選択します。
2. Key Vault のプロパティ ページで、 **[証明書]** を選択します。
3. 更新する証明書を選択します。 この例では、**ExampleCertificate** という証明書を操作します。
4. 上部のメニュー バーから **[発行ポリシー]** を選択します。

![証明書のプロパティ](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)
5. **[発行ポリシー]** 画面で、次の値を更新します。
- **有効期間**: 月単位の値を更新します。
- **有効期間のアクション タイプ**: 証明書の自動更新とアラート アクションを選択します。 選択内容に応じて、"有効期間の割合" または "有効期限までの日数" を更新します。 

![証明書のプロパティ](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)
6. **[Save]** をクリックします。

> [!IMPORTANT]
> 証明書の有効期間のアクション タイプを変更すると、既存の証明書の変更がすぐに記録されます。


### <a name="updating-certificates-attributes-using-powershell"></a>PowerShell を使用して証明書の属性を更新する

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> 一連の証明書に対する更新ポリシーを変更するには、コンテナー名と証明書名を含んだ File.csv を入力します。 <br/>
>  vault1,Cert1 <br/>
>  vault2,Cert2
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
パラメーターの詳細については、[こちら](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-set-attributes)を参照してください

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Key Vault に関する他のクイック スタートとチュートリアルは、このクイック スタートに基づいています。 後続のクイック スタートおよびチュートリアルを引き続き実行する場合は、これらのリソースをそのまま残しておくことをお勧めします。
不要になったら、リソース グループを削除します。これにより、Key Vault と関連リソースが削除されます。 ポータルを使用してリソース グループを削除するには:

1. ポータル上部にある検索ボックスにリソース グループの名前を入力します。 このクイック スタートで使用されているリソース グループが検索結果に表示されたら、それを選択します。
2. **[リソース グループの削除]** を選択します。
3. **[リソース グループ名を入力してください:]** ボックスにリソース グループの名前を入力し、 **[削除]** を選択します。


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、証明書のライフサイクルを更新しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

[Azure Key Vault での証明書作成の管理](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-scenarios)の詳細をご覧ください。
- [Key Vault の概要](../general/overview.md)を確認してください。