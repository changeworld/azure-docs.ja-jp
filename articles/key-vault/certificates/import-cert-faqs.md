---
title: よく寄せられる質問 - Azure Key Vault 証明書のインポート
description: よく寄せられる質問 - Azure Key Vault 証明書のインポート
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 1063f7189de4bdf1aaca4a6d72c979476433c32f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87094716"
---
# <a name="frequently-asked-questions---azure-key-vault-certificate-import"></a>よく寄せられる質問 - Azure Key Vault 証明書のインポート

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Azure Key Vault に証明書をインポートするには、どうすればよいですか?

証明書のインポート – インポート操作の場合、Azure Key Vault で受け付けられる証明書の形式は、PEM と PFX の 2 種類です。 公開部分のみを含む PEM ファイルもありますが、Azure Key Vault では、要件としてファイル フォルダー上にあり秘密キーを含む PEM または PFX のみが受け付けられます。 [証明書をインポートするチュートリアル](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-import-certificate#import-a-certificate-to-key-vault)に従ってください。

### <a name="after-importing-password-protected-certificate-into-the-key-vault-and-then-downloading-it-i-am-not-able-to-see-the-password-associated-with-the-certificate"></a>パスワードで保護された証明書を Key Vault にインポートした後、それをダウンロードした場合、その証明書に関連付けられたパスワードを確認することはできないのでしょうか?
    
保護された証明書をアップロードして Key Vault に保管する場合、それに関連付けられたパスワードは保存されません。 インポート操作でこれが必要になるのは、1 回だけです。 これは設計による概念ですが、いつでも証明書をシークレットとして取得し、[Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx) を使用して、以前のパスワードを追加して Base64 から PFX に変換することができます。

### <a name="how-can-i-resolve-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-in-key-vault"></a>"パラメーターが正しくありません" というエラーを解決する方法はありますか? Key Vault にインポートするためにサポートされている証明書の形式は何ですか?

証明書をインポートするときは、キーがファイル自体に含まれていることを確認する必要があります。 秘密キーが異なる形式で別にある場合は、キーと証明書を組み合わせる必要があります。 証明機関によっては、証明書が異なる形式で提供されるため、証明書をインポートする前に、.pem または .pfx 形式であること、および使用されているキーが RSA または ECC のいずれかであることを確認します。 [証明書の要件](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#formats-of-import-we-support)と[証明書キーの要件](https://docs.microsoft.com/azure/key-vault/keys/about-keys#cryptographic-protection)をご確認ください。

### <a name="error-when-importing-certificate-via-portal-something-went-wrong-how-can-i-investigate-further"></a>ポータルを使用して証明書をインポートする際にエラー ("問題が発生しました") が発生しました。 詳しく調査するにはどうすればよいですか?
    
さらにわかりやすいエラーを表示するには、[Azure CLI](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import) または [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0) を使用して証明書ファイルをインポートします。

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>解決方法を教えてください。"エラーの種類: アクセスが拒否されたか、ユーザーに証明書をインポートする権限がない"
    
この操作には、証明書/インポートのアクセス許可が必要です。 Key Vault が配置されている場所に移動し、アクセス ポリシーに基づいてユーザーに適切なアクセス許可を付与する必要があります。 [Key Vault] > [アクセス ポリシー] > [アクセス ポリシーの追加] > [証明書のアクセス許可] を選択 (アクセス許可を付与する場合) > [プリンシパル] の順に移動し、ユーザーの電子メールを検索して追加します。 [証明書関連のアクセス ポリシーの詳細については、こちらを参照してください](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#certificate-access-control)。


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>解決方法を教えてください。"エラーの種類: 証明書の作成時に競合が発生する"
    
証明書名は一意である必要があります。 同じ名前の証明書が論理的に削除された状態になっている可能性があります。また、Azure Key Vault 内の[証明書の構成](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate)に従い、証明書に指定しようとしているのと同じ名前の別のキーまたはシークレットが Key Vault に存在する場合はエラーになり、そのキーまたはシークレットを削除するか、証明書に別の名前を使用する必要があります。 [削除された証明書の表示](https://docs.microsoft.com/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate)

### <a name="why-am-i-getting-the-error-type-char-length-is-too-long"></a>"エラーの種類: 文字の長さが長すぎます" が返されるのはなぜですか?
    
* 証明書のサブジェクト名の長さには 200 文字という制限があります
* 証明書のパスワードの長さには 200 文字という制限があります

### <a name="can-i-import-an-expired-certificate-in-azure-key-vault"></a>有効期限が切れた証明書を Azure Key Vault にインポートできますか?
    
いいえ、期限切れの PFX 証明書は Azure Key Vault にインポートされません。

### <a name="how-can-i-convert-my-certificate-to-proper-format"></a>証明書を適切な形式に変換するには、どうすればよいですか?

ご利用の証明機関に対して、必要な形式で証明書を提供するように要求することができます。また、適切な形式に変換するために役立つサード パーティ製のツールもあります。ただし、Microsoft では、証明書を目的の形式で取得する方法について、さらに助言することはできません。

### <a name="can-i-import-certificates-from-non-partner-cas"></a>パートナー以外の CA から証明書をインポートできますか?
はい、証明書は任意の CA からインポートできますが、Key Vault でそれらの証明書を自動的に更新することはできません。 証明書の有効期限に関する通知を受け取るように電子メール通知を設定できます。

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-auto-renew-feature-still-work"></a>パートナーの CA から証明書をインポートした場合、自動更新機能は引き続き機能しますか?
はい。アップロードが完了したら、証明書の発行ポリシーで自動ローテーションを指定してください。 また、変更は、次のサイクルまたは証明書のバージョンまで反映されています。


## <a name="next-steps"></a>次のステップ

- [Azure Key Vault 証明書](/azure/key-vault/certificates/about-certificates)
