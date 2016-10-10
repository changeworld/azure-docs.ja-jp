
<properties
	pageTitle="Enterprise Integration Pack での証明書の使用 |Microsoft Azure"
	description="Enterprise Integration Pack と Logic Apps で証明書を使用する方法について説明します"
	services="logic-apps"
	documentationCenter=".net,nodejs,java"
	authors="msftman"
	manager="erikre"
	editor="cgronlun"/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="deonhe"/>

# 証明書と Enterprise Integration Pack についての詳細情報

## Overview
エンタープライズ統合では、証明書を使用して B2B 通信を保護します。エンタープライズ統合アプリでは、次の2 種類の証明書を使用できます。

- パブリック証明書。証明機関 (CA) から購入する必要があります。
- プライベート証明書。自身で発行できます。これらは自己署名証明書とも呼ばれます。


## 証明書の概要
証明書はデジタル ドキュメントであり、電子通信の参加者の ID の確認と、電子通信の保護を行うためのものです。

## 証明書を使用する理由
場合によっては、B2B 通信で機密性を保持する必要があります。エンタープライズ統合では、次の 2 つの方法で証明書を使用してこれらの通信を保護します。

- メッセージの内容を暗号化することによって
- メッセージをデジタル署名することによって

## 証明書のアップロード方法

### パブリック証明書
B2B 機能を備えたロジック アプリで*パブリック証明書*を使用するには、まず統合アカウントにこの証明書をアップロードする必要があります。一方、*自己署名証明書*を使用する場合は、最初に [Azure Key Vault](../key-vault/key-vault-get-started.md "Key Vault についての詳細情報") に証明書をアップロードする必要があります。

証明書をアップロードすると、作成する[契約](./app-service-logic-enterprise-integration-agreements.md)で B2B メッセージのプロパティを定義して、B2B メッセージを保護できるようになります。

次に、Azure Portal へのサインイン後に統合アカウントにパブリック証明書をアップロードする詳細な手順を示します。

1. **[参照]** を選択します。![[参照] の選択](./media/app-service-logic-enterprise-integration-overview/overview-1.png)

2. フィルター検索ボックスに「**integration**」と入力し、結果リストから **[Integration Accounts (統合アカウント)]** を選択します。![[Integration Accounts (統合アカウント)] の選択](./media/app-service-logic-enterprise-integration-overview/overview-2.png)

3. 証明書の追加先となる統合アカウントを選択します。![証明書の追加先となる統合アカウントの選択](./media/app-service-logic-enterprise-integration-overview/overview-3.png)

4.  **[証明書]** タイルを選択します。![[証明書] タイルの選択](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)

5. 表示された **[証明書]** ブレードで **[追加]** をクリックします。![[追加] ボタンのクリック](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)

6. 証明書の**名前**を入力して、証明書の種類を選択します (この例では、証明書の種類として [パブリック] を使用します)。 次に、**[証明書]** ボックスの右側にあるフォルダー アイコンをクリックします。

7. ファイル ピッカーが表示されたら、統合アカウントにアップロードする証明書を見つけて選択します。

8. 証明書を選択して、ファイル ピッカーで **[OK]** をクリックします。これにより、証明書を検証し、統合アカウントにアップロードできます。

8. 最後に、**[証明書の追加]** ブレードに戻って **[OK]** をクリックします。![[OK] ボタンのクリック](./media/app-service-logic-enterprise-integration-certificates/certificate-3.png)

9. 約 1 分で、証明書のアップロードが完了したことを示す通知が表示されます。

10. **[証明書]** タイルを選択します。新しく追加された証明書が表示されます。![新しい証明書の確認](./media/app-service-logic-enterprise-integration-certificates/certificate-4.png)

### プライベート証明書
統合アカウントへのプライベート証明書のアップロードは、次の手順で行うことが出来ます。

1. [Key Vault に秘密キーをアップロードします](../key-vault/key-vault-get-started.md "Key Vault についての詳細情報")。

	> [AZURE.TIP] Key Vault に対して操作を実行するには、Azure App Service の Logic Apps 機能を承認する必要があります。Logic Apps サービス プリンシパルにアクセスを付与するには、次の PowerShell コマンドを使用します: `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`。

2. プライベート証明書を作成します。

3. プライベート証明書を統合アカウントにアップロードします。

上記の手順を完了すると、プライベート証明書を使用して契約を作成できるようになります。

次に、Azure Portal へのサインイン後に統合アカウントにプライベート証明書をアップロードする詳細な手順を示します。

1. **[参照]** を選択します。![統合アカウントへのプライベート証明書のアップロード](./media/app-service-logic-enterprise-integration-overview/overview-1.png)

2. フィルター検索ボックスに「**integration**」と入力し、結果リストから **[Integration Accounts (統合アカウント)]** を選択します。![[Integration Accounts (統合アカウント)] の選択](./media/app-service-logic-enterprise-integration-overview/overview-2.png)

3. 証明書の追加先となる統合アカウントを選択します。![証明書の追加先となる統合アカウントの選択](./media/app-service-logic-enterprise-integration-overview/overview-3.png)

4. **[証明書]** タイルを選択します。![[証明書] タイルの選択](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)

5. 表示された **[証明書]** ブレードで **[追加]** をクリックします。![[追加] ボタンのクリック](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)

6. 証明書の**名前**を入力して、証明書の種類を選択します (この例では、証明書の種類として [プライベート] を使用します)。 次に、**[証明書]** ボックスの右側にあるフォルダー アイコンをクリックします。

7. ファイル ピッカーが表示されたら、統合アカウントにアップロードする証明書を見つけて選択します。

8. 証明書を選択したら、ファイル ピッカーで **[OK]** を選択します。これにより、証明書が検証され、統合アカウントにアップロードされます。

9. 最後に、**[証明書の追加]** ブレードに戻って **[OK]** をクリックします。![証明書の追加](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-1.png)

10. 約 1 分で、証明書のアップロードが完了したことを示す通知が表示されます。

11. **[証明書]** タイルを選択します。新しく追加された証明書が表示されます。![新しい証明書の確認](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-2.png)

証明書をアップロードすると、[契約](./app-service-logic-enterprise-integration-agreements.md)で B2B メッセージのプロパティを定義して、B2B メッセージを保護できるようになります。

## 次のステップ
- [B2B 機能を使用したロジック アプリの作成](./app-service-logic-enterprise-integration-b2b.md)
- [B2B 契約の作成](./app-service-logic-enterprise-integration-agreements.md)
- [Key Vault についての詳細情報](../key-vault/key-vault-get-started.md "Key Vault についての詳細情報")

<!---HONumber=AcomDC_0928_2016-->