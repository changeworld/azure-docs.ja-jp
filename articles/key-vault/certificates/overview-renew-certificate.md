---
title: Azure Key Vault の証明書の更新について
description: Azure Key Vault の証明書の更新について
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: c6999b67a5c0a0f4ca7cb943ae8de3afd8b6a11e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87094717"
---
# <a name="about-azure-key-vault-certificate-renewal"></a>Azure Key Vault の証明書の更新について

Azure Key Vault を使用すると、ネットワークのデジタル証明書のプロビジョニング、管理、およびデプロイを簡単に行うことができ、アプリケーションのセキュリティで保護された通信を実現できます。 証明書の一般的な情報については、[Azure Key Vault 証明書](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates)に関するページを参照してください

証明書の有効期間を短くするか、または証明書のローテーションの頻度を増やすと、敵対者による損害の範囲が抑えられます。

キー コンテナーでの証明書の作成には、3 つのカテゴリがあります。 このガイドは、証明書の更新を実現する方法を理解するのに役立ちます。
-   統合された CA を使用して作成された証明書 (DigiCert または GlobalSign)
-   統合されていない CA を使用して作成された証明書
-   自己署名証明書

## <a name="renewal-of-integrated-ca-certificate"></a>統合された CA 証明書の更新 
うれしいことに、 Azure Key Vaults を使用すると、Microsoft の信頼された CA (DigiCert および GlobalSign) によって発行された証明書のエンドツーエンドの保守が行われます。 [信頼された CA とキー コンテナーを統合する](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority)方法について説明します。

## <a name="renewal-of-non-integrated-ca-certificate"></a>統合されていない CA 証明書の更新 
Azure Key Vault のユーザーには、任意の CA から証明書をインポートできるという利点が与えられます。これにより、そのユーザーは複数の Azure リソースと統合し、展開を容易に行うことができます。 ご利用の証明書の期限切れを追跡できなくなることや、さらに悪いことに、ご利用の証明書が既に期限切れになっていると検出されることを心配している場合、最新の状態を維持するのに Key Vault が役に立ちます。 統合されていない CA 証明書の場合、キー コンテナーを使用することで、そのユーザーは有効期限が近づいたときの電子メール通知を設定することができます。 これらの通知は、複数のユーザーに対して設定することもできます。

ここで、証明書を更新するには、Azure Key Vault 証明書がバージョン管理されたオブジェクトであることを理解しておくことが重要です。 現在のバージョンの有効期限が近づいている場合は、新しいバージョンを作成する必要があります。 概念的には、それぞれの新しいバージョンは、キーと、そのキーを ID に関連付ける BLOB とで構成される完全に新しい証明書となります。 非パートナーの CA を使用する場合、キー コンテナーによってキーと値のペアが生成され、CSR が返されます。

**Azure portal で行う手順:**
1.  更新する証明書を開きます。
2.  [証明書] 画面上で **[+ 新しいバージョン]** ボタンを選択します。
3.  **[証明書の操作]** を選択します。
4.  **[CSR のダウンロード]** を選択します。 これにより、ご利用のローカル ドライブ上に csr ファイルがダウンロードされます。
5.  選択した CA に CSR を発行して要求に署名する
6.  署名した要求を戻し、同じ [証明書の操作] 画面で **[Merge CSR]\(CSR のマージ\)** を選択します。

> [!NOTE]
> 署名した CSR を、作成したのと同じ CSR 要求にマージすることが重要です。そうしないと、キーが一致しません。

手順は、新しい証明書の作成方法と似ています。詳細については、[こちら]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal)を参照してください。

## <a name="renewal-of-self-signed-certificate"></a>自己署名証明書の更新

再び朗報です。 自己署名証明書の更新もユーザーに代わって Azure Key Vault が自動的に行います。 発行ポリシーの変更方法および証明書の有効期間のアクション属性の更新方法の詳細については、[こちら](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate)を参照してください。

### <a name="troubleshoot"></a>トラブルシューティング
発行された証明書が Azure portal で "無効" 状態になっている場合は、[証明書の操作] に進み、その証明書のエラー メッセージを確認してください。

### <a name="see-also"></a>参照
*   [Key Vault と DigiCert 証明機関の統合](how-to-integrate-certificate-authority.md)
*   [チュートリアル: Key Vault における証明書の自動ローテーションを構成する](tutorial-rotate-certificates.md)