---
title: ワークフローで B2B メッセージをセキュリティで保護するための証明書を追加する
description: Azure Logic Apps と Enterprise Integration Pack を使用し、ワークフロー内の B2B メッセージをセキュリティで保護するための証明書を統合アカウントに追加します。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/23/2021
ms.openlocfilehash: b68bc9f1f77c6ad5b60cd5b2ea9c4b644acb4c5b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361053"
---
# <a name="add-certificates-to-integration-accounts-for-securing-messages-in-workflows-with-azure-logic-apps"></a>Azure Logic Apps を使用し、ワークフロー内のメッセージをセキュリティで保護するための証明書を統合アカウントに追加する

ロジック アプリの企業間 (B2B) ワークフローで機密メッセージを交換する必要がある場合は、証明書を使用することでこの通信に関するセキュリティを強化できます。 証明書は、次の方法で通信をセキュリティで保護するのに役立つデジタル ドキュメントです。

* 電子通信で参加者の ID を確認する。

* メッセージの内容を暗号化する。

* メッセージにデジタル署名する。

ワークフローで使用できる証明書の種類は次のとおりです。

* [パブリック証明書](https://en.wikipedia.org/wiki/Public_key_certificate): これはパブリック インターネットの[証明機関 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) から購入する必要があります。 これらの証明書にキーは必要ありません。

* プライベート証明書または "[*自己署名証明書*"](https://en.wikipedia.org/wiki/Self-signed_certificate): これはお客様自身が作成して発行する必要があります。 ただし、これらの証明書にはプライベート キーが必要です。

ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](logic-apps-overview.md)」を参照してください。 B2B エンタープライズ統合の詳細については、「[Azure Logic Apps と Enterprise Integration Pack を使用した B2B エンタープライズ統合ワークフロー](logic-apps-enterprise-integration-overview.md)」をご確認ください。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをまだお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

* エンタープライズ統合および B2B ワークフローで使用する成果物 (取引先、契約、証明書など) を定義して保存する[統合アカウント リソース](logic-apps-enterprise-integration-create-integration-account.md)。 このリソースでは、次の要件が満たされている必要があります。

  * ロジック アプリ リソースと同じ Azure サブスクリプションに関連付けられている。

  * ロジック アプリ リソースと同じ場所または Azure リージョンに存在する。

  * [**ロジック アプリ (従量課金)** というリソースの種類](logic-apps-overview.md#resource-type-and-host-environment-differences)を使用する場合、ワークフローで成果物を使用するには、[統合アカウントをロジック アプリ リソースにリンクする](logic-apps-enterprise-integration-create-integration-account.md#link-account)必要があります。

    **ロジック アプリ (従量課金)** のワークフローで使用する証明書を作成、追加するのに、ロジック アプリ リソースはまだ必要ありません。 ただし、それらの証明書をワークフローで使用するときに、ロジック アプリ リソースで、それらの証明書を保存するための、リンクされた統合アカウントが必要になります。

  * [**ロジック アプリ (Standard)** というリソースの種類](logic-apps-overview.md#resource-type-and-host-environment-differences)を使用している場合、統合アカウントにロジック アプリ リソースへのリンクは必要ありませんが、他の成果物 (パートナー、契約、証明書など) を格納することは、[AS2](logic-apps-enterprise-integration-as2.md)、[X12](logic-apps-enterprise-integration-x12.md)、[EDIFACT](logic-apps-enterprise-integration-edifact.md) などの操作を使用することと共に、まだ必要です。 統合アカウントは、ロジック アプリ リソースと同じ Azure サブスクリプションを使用することや、ロジック アプリ リソースと同じ場所に存在することなど、他の要件も満たす必要があります。

    > [!NOTE]
    > 現時点では、[RosettaNet](logic-apps-enterprise-integration-rosettanet.md) の操作をサポートしているのは、**ロジック アプリ (従量課金)** のリソースの種類のみです。 **ロジック アプリ (Standard)** というリソースの種類には、[RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 操作は含まれていません。

* プライベート証明書の場合は、次の前提条件を満たす必要があります。

  * [Azure Key Vault](../key-vault/general/overview.md) に秘密キーを追加し、 **[キー名]** を指定します。 詳細については、[Azure Key Vault への秘密キーの追加](../key-vault/certificates/certificate-scenarios.md#import-a-certificate)に関するページを参照してください。

  * キー コンテナーに対する操作を実行するには、Azure Logic Apps サービスを承認します。 Azure Logic Apps サービス プリンシパルへのアクセスを許可するには、次の例のように、PowerShell コマンド [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) を使います。

    `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`

    [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

  * キー コンテナーに[対応するパブリック証明書を追加](#add-public-certificate)します。 この証明書は、[メッセージの署名と暗号化のために、契約の **送信** と **受信** の設定](logic-apps-enterprise-integration-agreements.md)に表示されます。 たとえば、[Azure Logic Apps における AS2 メッセージ設定のリファレンス](logic-apps-enterprise-integration-as2-message-settings.md)のページを参照してください。

* 統合アカウント内に、少なくとも 2 つの[取引先](logic-apps-enterprise-integration-partners.md)と、[それらのパートナー間の 1 つの契約](logic-apps-enterprise-integration-agreements.md)。 契約には、ホスト パートナーとゲスト パートナーが必要です。 また、契約では、両方のパートナーが、AS2、X12、EDIFACT、または RosettaNet 契約に適した同じまたは互換性のある "*ビジネス ID*" 修飾子を使用する必要があります。

* 必要に応じて、証明書を使用するロジック アプリ リソースとワークフロー。 ワークフローには、ロジック アプリのワークフローを開始するトリガーが必要です。 ロジック アプリ ワークフローを作成したことがない場合は、[初めてのロジック アプリを作成するクイック スタート](quickstart-create-first-logic-app-workflow.md)のページを参照してください。

<a name="add-public-certificate"></a>

## <a name="add-a-public-certificate"></a>パブリック証明書を追加する

ワークフローで "*パブリック証明書*" を使用するには、最初に証明書を統合アカウントに追加する必要があります。

1. [Azure portal](https://portal.azure.com) の検索ボックスに「`integration accounts`」と入力し、 **[統合アカウント]** を選択します。

1. **[統合アカウント]** で、証明書を追加する統合アカウントを選択します。

1. 統合アカウント メニューの **[設定]** で、 **[証明書]** を選択します。

1. **[証明書]** ペインで、 **[追加]** を選択します。

1. **[証明書の追加]** ペインで、証明書に関する次の情報を指定します。

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **名前** | はい | <*証明書名*> | 証明書の名前 (この例では `publicCert`) |
   | **証明書の種類** | はい | **パブリック** | 証明書の種類 |
   | **[MSSQLSERVER のプロトコルのプロパティ]** | はい | <*証明書ファイル名*> | 追加する証明書ファイルを参照するには、 **[証明書]** ボックスの横にあるフォルダー アイコンを選択します。 |
   |||||

   ![Azure portal と統合アカウントを示すスクリーンショット。[追加] が選択され、[証明書の追加] ペインにパブリック証明書の詳細が表示されています。](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

1. 終了したら、 **[OK]** を選択します。

   選択した内容が Azure によって確認されると、証明書がアップロードされます。

   ![Azure portal と統合アカウントを示すスクリーンショット。[証明書] の一覧にパブリック証明書が表示されています。](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png)

<a name="add-public-certificate"></a>

## <a name="add-a-private-certificate"></a>プライベート証明書を追加する

ワークフローで "*プライベート証明書*" を使用するには、最初に証明書を統合アカウントに追加する必要があります。 [前提条件のプライベート証明書](#prerequisites)も満たしていることを確認します。

1. [Azure portal](https://portal.azure.com) の検索ボックスに「`integration accounts`」と入力し、 **[統合アカウント]** を選択します。

1. **[統合アカウント]** で、証明書を追加する統合アカウントを選択します。

1. 統合アカウント メニューの **[設定]** で、 **[証明書]** を選択します。

1. **[証明書]** ペインで、 **[追加]** を選択します。

1. **[証明書の追加]** ペインで、証明書に関する次の情報を指定します。

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **名前** | はい | <*証明書名*> | 証明書の名前 (この例では `privateCert`) |
   | **証明書の種類** | はい | **プライベート** | 証明書の種類 |
   | **[MSSQLSERVER のプロトコルのプロパティ]** | はい | <*証明書ファイル名*> | 追加する証明書ファイルを参照するには、 **[証明書]** ボックスの横にあるフォルダー アイコンを選択します。 自分のプライベート キーを含むキー コンテナーで、そこに追加するファイルはパブリック証明書です。 |
   | **リソース グループ** | はい | <*統合アカウントのリソース グループ*> | 統合アカウントのリソース グループ (この例では `Integration-Account-RG`) |
   | **Key Vault** | はい | <*キー コンテナー名*> | キー コンテナー名 |
   | **キー名** | はい | <*key-name*> | キー名 |
   |||||

   ![Azure portal と統合アカウントを示すスクリーンショット。[追加] が選択され、[証明書の追加] ペインにプライベート証明書の詳細が表示されています。](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

1. 終了したら、 **[OK]** を選択します。

   選択した内容が Azure によって確認されると、証明書がアップロードされます。

   ![Azure portal と統合アカウントを示すスクリーンショット。[証明書] の一覧にプライベート証明書が表示されています。](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png)

## <a name="next-steps"></a>次のステップ

* [AS2 メッセージの交換](logic-apps-enterprise-integration-as2.md)
* [EDIFACT メッセージの交換](logic-apps-enterprise-integration-edifact.md)
* [X12 メッセージの交換](logic-apps-enterprise-integration-x12.md)
* [RosettaNet メッセージの交換](logic-apps-enterprise-integration-rosettanet.md)
