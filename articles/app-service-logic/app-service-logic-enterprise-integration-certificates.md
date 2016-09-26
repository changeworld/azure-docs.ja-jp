<properties 
	pageTitle="Enterprise Integration Pack での証明書の使用 |Microsoft Azure App Service" 
	description="Enterprise Integration Pack と Logic Apps を使用して証明書を使用する方法について説明します" 
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
証明書はデジタル ドキュメントであり、電子通信の参加者の ID を確認するために使用します。また、電子通信を保護するためにも使用します。

## 証明書を使用する理由
場合によっては、B2B 通信は機密性を保持する必要があります。エンタープライズ統合では、次の 2 つの方法で証明書を使用してこれらの通信を保護します。
- メッセージの内容を暗号化することによって
- メッセージをデジタル署名することによって

## 証明書のアップロード方法

### パブリック証明書
B2B 機能を備えたロジック アプリで**パブリック証明書**を使用するには、最初に統合アカウントにアップロードする必要があります。一方、**自己署名証明書**を使用するには、最初に [key Vault](../key-vault/key-vault-get-started.md "Key Vault についての詳細情報") にアップロードする必要があります。

証明書をアップロードすると、[契約](./app-service-logic-enterprise-integration-agreements.md)でプロパティを定義するときに、B2B メッセージを保護できるようになります。

Azure Portal にログインした後に統合アカウントにパブリック証明書をアップロードする詳細な手順を次に示します。
1. **[参照]** を選択します。 ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. フィルター検索ボックスに「**integration**」と入力し、結果リストから **[Integration Accounts (統合アカウント)]** を選択します。 ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. 証明書の追加先となる**統合アカウント**を選択します。![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  **[証明書]** タイルを選択します。![](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)
5. 開かれた [証明書] ブレードの **[追加]** ボタンを選択します。![](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)
6. 証明書に**名前**を入力し、証明書の種類を選択します (この例では、パブリック証明書の種類を使用)。次に、 **[証明書]** テキスト ボックスの右側にあるフォルダー アイコンを選択します。これにより、統合アカウントにアップロードする証明書ファイルを参照して選択するためのファイル ピッカーが開きます。証明書を選択したら、ファイル ピッカーで **[OK]** を選択します。これにより、証明書が検証され、統合アカウントにアップロードされます。最後に、**[証明書の追加] ブレード**に戻り、**[OK]** ボタンを選択します。![](./media/app-service-logic-enterprise-integration-certificates/certificate-3.png)
7. 1 分以内に、証明書のアップロードが完了したことを示す通知が表示されます。
8. **[証明書]** タイルを選択します。ページが更新され、新しく追加された証明書が表示されます。![](./media/app-service-logic-enterprise-integration-certificates/certificate-4.png)

### プライベート証明書

プライベート証明書を統合アカウントにアップロードすることもできます。そのためには、次の手順を実行します。

1. [key Vault に秘密キーをアップロードします。](../key-vault/key-vault-get-started.md "Key Vault についての詳細情報")

	> [AZURE.TIP] Key Vault に対して操作を実行するには、Logic Apps サービスを承認する必要があります。Logic Apps サービス プリンシパルにアクセスを付与するには、次の PowerShell コマンドを使用します: `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`

2. プライベート証明書を作成します。
3. プライベート証明書を統合アカウントにアップロードします。

上記の手順が完了すると、プライベート証明書を使用して契約を作成することができます。

Azure Portal にログインした後に統合アカウントにプライベート証明書をアップロードする詳細な手順を次に示します。
   
1. **[参照]** を選択します。 ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. フィルター検索ボックスに「**integration**」と入力し、結果リストから **[Integration Accounts (統合アカウント)]** を選択します。 ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. 証明書の追加先となる**統合アカウント**を選択します。![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  **[証明書]** タイルを選択します。![](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)
5. 開かれた [証明書] ブレードの **[追加]** ボタンを選択します。![](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)
6. 証明書に**名前**を入力し、証明書の種類を選択します (この例では、パブリック証明書の種類を使用)。次に、 **[証明書]** テキスト ボックスの右側にあるフォルダー アイコンを選択します。これにより、統合アカウントにアップロードする証明書ファイルを参照して選択するためのファイル ピッカーが開きます。証明書を選択したら、ファイル ピッカーで **[OK]** を選択します。これにより、証明書が検証され、統合アカウントにアップロードされます。最後に、**[証明書の追加] ブレード**に戻り、**[OK]** ボタンを選択します。![](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-1.png)
7. 1 分以内に、証明書のアップロードが完了したことを示す通知が表示されます。
8. **[証明書]** タイルを選択します。新しく追加された証明書が表示されます。![](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-2.png)

証明書をアップロードすると、[契約](./app-service-logic-enterprise-integration-agreements.md)でプロパティを定義するときに、B2B メッセージを保護できるようになります。


## 次のステップ
- [B2B 機能を使用したロジック アプリの作成](./app-service-logic-enterprise-integration-b2b.md)
- [B2B 契約の作成](./app-service-logic-enterprise-integration-agreements.md)
- [key Vault についての詳細情報](../key-vault/key-vault-get-started.md "Key Vault についての詳細情報")

<!---HONumber=AcomDC_0914_2016-->