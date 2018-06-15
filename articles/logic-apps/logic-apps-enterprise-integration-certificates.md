---
title: Azure Logic Apps の証明書を使用して B2B メッセージをセキュリティで保護する | Microsoft Docs
description: Enterprise Integration Pack を使用して B2B メッセージをセキュリティで保護するために証明書を追加します
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 7ba76a15792fe40b2a628b030f06930641d197a3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299336"
---
# <a name="secure-b2b-messages-with-certificates"></a>証明書を使用して B2B メッセージをセキュリティで保護する

B2B の通信の機密を保持することが必要な場合があります。 エンタープライズ統合アプリ (具体的にはロジック アプリ) の B2B 通信をセキュリティで保護するため、統合アカウントに証明書を追加することができます。 証明書はデジタル ドキュメントであり、電子通信の参加者の ID を検証します。
証明書は次の方法で通信をセキュリティ保護します。

* メッセージの内容を暗号化します
* メッセージにデジタル署名します  

エンタープライズ統合アプリでは、次の証明書を使うことができます。

* パブリック証明書。証明機関 (CA) から購入する必要があります。
* プライベート証明書。自身で発行できます。 これらは自己署名証明書とも呼ばれます。

## <a name="upload-a-public-certificate"></a>パブリック証明書のアップロード

B2B 機能を備えたロジック アプリで "*パブリック証明書*" を使うには、最初に統合アカウントに証明書をアップロードする必要があります。 作成した[契約](logic-apps-enterprise-integration-agreements.md)でプロパティを定義した後は、証明書を使って B2B メッセージをセキュリティ保護できるようになります。

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. Azure のメイン メニューで、**[すべてのサービス]** を選びます。 検索ボックスに「統合」と入力し、"**統合アカウント**" を選びます。

   ![統合アカウントの検索](media/logic-apps-enterprise-integration-certificates/overview-1.png)  

3. **[統合アカウント]** で、証明書を追加する統合アカウントを選びます。

   ![証明書の追加先となる統合アカウントの選択](media/logic-apps-enterprise-integration-certificates/overview-3.png)  

4. **[証明書]** タイルを選びます。  

   ![[証明書] を選択する](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

5. **[証明書]** で **[追加]** を選びます。

   ![[追加] の選択](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

6. **[証明書の追加]** で、証明書の詳細を指定します。
   
   1. 証明書の **[名前]** を入力します。 証明書の種類で **[パブリック]** を選びます。

   2. **[証明書]** ボックスの右側にあるフォルダー アイコンを選びます。 
   アップロードする証明書ファイルを探して選びます。 
   完了したら、**[OK]** を選びます。

      ![パブリック証明書をアップロードする](media/logic-apps-enterprise-integration-certificates/certificate-3.png)

   Azure は、選択内容を検証した後、証明書をアップロードします。

   ![新しい証明書を表示する](media/logic-apps-enterprise-integration-certificates/certificate-4.png) 

## <a name="upload-a-private-certificate"></a>プライベート証明書のアップロード

B2B 機能を備えたロジック アプリで "*プライベート証明書*" を使うには、最初に統合アカウントに証明書をアップロードする必要があります。 また、最初に秘密キーを [Azure Key Vault](../key-vault/key-vault-get-started.md) に追加しておく必要があります。 

作成した[契約](logic-apps-enterprise-integration-agreements.md)でプロパティを定義した後は、証明書を使って B2B メッセージをセキュリティ保護できるようになります。

> [!NOTE]
> プライベート証明書の場合、メッセージの署名と暗号化のため [AS2 契約](logic-apps-enterprise-integration-as2.md)の送信と受信の設定に表示されるように、対応するパブリック証明書を追加する必要があります。

1. [Azure Key Vault に秘密キーを追加](../key-vault/key-vault-get-started.md#add)し、**[キー名]** を指定します。
   
2. Azure Logic Apps が Azure Key Vault で操作を実行することを承認します。 Logic Apps サービス プリンシパルへのアクセスを許可するには、次の例のように、PowerShell コマンド [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) を使います。

   `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. [Azure Portal](https://portal.azure.com) にサインインします。

4. Azure のメイン メニューで、**[すべてのサービス]** を選びます。 検索ボックスに「統合」と入力し、"**統合アカウント**" を選びます。

   ![統合アカウントの検索](media/logic-apps-enterprise-integration-certificates/overview-1.png) 

5. **[統合アカウント]** で、証明書を追加する統合アカウントを選びます。

6. **[証明書]** タイルを選びます。  

   ![[証明書] タイルを選ぶ](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

7. **[証明書]** で **[追加]** を選びます。   

   ![[追加] ボタンを選ぶ](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

8. **[証明書の追加]** で、証明書の詳細を指定します。
   
   1. 証明書の **[名前]** を入力します。 証明書の種類で **[プライベート]** を選びます。

   2. **[証明書]** ボックスの右側にあるフォルダー アイコンを選びます。 
   アップロードする証明書ファイルを探して選びます。 
   また、**[リソース グループ]**、**[Key Vault]**、**[キー名]** を選びます。 
   完了したら、**[OK]** を選びます。

      ![証明書を追加する](media/logic-apps-enterprise-integration-certificates/privatecertificate-1.png)

   Azure は、選択内容を検証した後、証明書をアップロードします。

   ![新しい証明書を表示する](media/logic-apps-enterprise-integration-certificates/privatecertificate-2.png)

## <a name="next-steps"></a>次の手順

* [B2B 契約の作成](logic-apps-enterprise-integration-agreements.md)
