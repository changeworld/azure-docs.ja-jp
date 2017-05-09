> [!NOTE]
> このエクスペリエンスを強化し、次の手順を廃止する予定です。

### <a name="create-an-administrator-credential-in-the-azure-ad-b2c-tenant"></a>Azure AD B2C テナントにおける管理者の資格情報の作成

次のセクションでは、Azure AD B2C テナントのドメインを使用する資格情報を使用する必要があります。 そのために、そうした資格情報を持つ管理者アカウントを作成する必要があります。 そのためには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) で、Azure AD B2C テナントのコンテキストに切り替え、[Azure AD B2C] ブレードを開きます。 [方法は、こちらで確認してください](..\articles\active-directory-b2c\active-directory-b2c-navigate-to-b2c-context.md)。
1. **[ユーザーとグループ]** を選択します。
1. **[すべてのユーザー]** を選択します。
1. **[+ 新しいユーザー]** をクリックします。
    * **[名前]** = `Admin` に設定します。
    * **[ユーザー名]** = `admin@{tenantName}.onmicrosoft.com` に設定します。`{tenantName}` は Azure AD B2C テナントの名前です。
1. **[ディレクトリ ロール]** で **[グローバル管理者]** を選択し、**[OK]** をクリックします。
1. **[作成]** をクリックして管理者ユーザーを作成します。
1. **[パスワードの表示]** をクリックし、パスワードをコピーします。

### <a name="set-up-the-key-container"></a>キー コンテナーの設定

キー コンテナーを使用して、キーを格納します。 キー コンテナーを設定するには:

1. 新しい PowerShell コマンド プロンプトを開きます。  1 つの方法としては、**Windows ロゴ キー + R キー**を押し、「`powershell`」と入力して、Enter キーを押します。
1. 次の複製をダウンロードして、PowerShell ExploreAdmin ツールを入手します。

    ```powershell
    git clone https://github.com/Azure-Samples/active-directory-b2c-advanced-policies
    ```

1. ExploreAdmin ツールのあるフォルダーに切り替えます。

    ```powershell
    cd active-directory-b2c-advanced-policies\ExploreAdmin
    ```

1. ExploreAdmin ツールを PowerShell にインポートします。

    ```powershell
    Import-Module .\ExploreAdmin.dll
    ```

1. `b2c_1a_TokenSigningKeyContainer` がまだ存在していないことを確認します。  `{tenantName}` をテナントの名前に置き換えます。

    ```powershell
    Get-CpimKeyContainer -TenantId {tenantName}.onmicrosoft.com -StorageReferenceId b2c_1a_TokenSigningKeyContainer -ForceAuthenticationPrompt
    ```

    a. ログイン プロンプトが表示されたら、前のセクションで作成した管理者アカウントを使用します。

    b. 多要素認証の設定を求められたら、電話番号を入力して設定する必要があります。

    c. パスワードの変更を求められたら、変更します。

    d. **エラーが発生します。**  `b2c_1a_TokenSigningKeyContainer` が見つからないというエラーです。  次の手順を既に完了しているためにエラーが発生しない場合は、このセクションの残りの部分をスキップしてください。

1. `b2c_1a_TokenSigningKeyContainer` を作成します。  `{tenantName}` をテナントの名前に置き換えます。

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com  b2c_1a_TokenSigningKeyContainer  b2c_1a_TokenSigningKeyContainer rsa 2048 0 0
    ```

1. `b2c_1a_TokenEncryptionKeyContainer` を作成します。  `{tenantName}` をテナントの名前に置き換えます。

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com b2c_1a_TokenEncryptionKeyContainer b2c_1a_TokenEncryptionKeyContainer rsa 2048 0 0
    ```

1. `b2c_1a_FacebookSecret` を作成します。  `{tenantName}` をテナントの名前に置き換えます。

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com  b2c_1a_FacebookSecret  b2c_1a_FacebookSecret rsa 2048 0 0
    ```
