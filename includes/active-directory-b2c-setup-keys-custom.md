
### <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>カスタム ポリシーで使用する署名キーと暗号化キーを B2C テナントに追加する

1. Azure AD B2C テナント設定の [Identity Experience Framework] ブレードに移動します。
1. [`Policy Keys`] を選択して、テナント内で利用できるキーを表示します。 `B2C_1A_TokenSigningKeyContainer` が存在する場合、このキーはスキップしてください。
1. `TokenSigningKeyContainer` を作成します。  
 * [`+Add`] をクリックします。
 * [オプション]: `Generate`
 * [名前]: `TokenSigningKeyContainer` (B2C_1A_ というプレフィックスが自動的に追加される場合があります)
 * [キーの種類]: `RSA`
 * [日付]: 既定値を使用
 * [キー使用法]: `Signature`
1. [`Create`] をクリックします。
1. `B2C_1A_TokenEncryptionKeyContainer` という名前のキーが存在する場合、このキーはスキップしてください。
1. `TokenEncryptionKeyContainer` を作成します。
 * [オプション]: `Generate`
 * [名前]: `TokenSigningKeyContainer` (B2C_1A_ というプレフィックスが自動的に追加される場合があります)
 * [キーの種類]: `RSA`
 * [日付]: 既定値を使用
 * [キー使用法]: `Encryption`
1. [`Create`] をクリックします。


[!TIP]
以下の手順は任意です。ソーシャル ID プロバイダまたはフェデレーション ID プロバイダーをエンド ユーザーに提供する場合にご参照ください。  カスタム ポリシーを使用した Azure AD B2C と外部 ID プロバイダーの連携について理解するための足掛かりとしては Facebook が好例でしょう。

1. `FacebookSecret` を作成します。  必須ではありませんが、外部の ID プロバイダーとのフェデレーション機能を簡単にテストするために、この手順を実行することをお勧めします。  今後、他の ID プロバイダーを使ったポリシーを開発する際の基礎をしっかりと身に付けることができます。
 * [`+Add`] をクリックします。
 * [オプション]: `Manual`
 * [名前]: `FacebookSecret` (B2C_1A_ というプレフィックスが自動的に追加される場合があります)
 * [シークレット]: developers.facebook.com からの FacebookSecret を入力します。  "*Facebook App ID ではない*" ので注意してください。
 * [キー使用法]: 署名
1. [`Create`] をクリックして作成の確認を行い、名前をメモします。

[!NOTE]
Azure AD B2C の組み込みポリシーを使用する場合、通常、組み込みポリシーとカスタム ポリシーの両方に同じシークレットを使用します。 
