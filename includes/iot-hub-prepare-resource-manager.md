## リソース マネージャーの要求を認証するための準備

[Azure リソース マネージャー][lnk-authenticate-arm] と Azure Active Directory (AD) を使用して、リソース上で実行するすべての操作を認証する必要があります。これを構成するうえで、PowerShell または Azure CLI を使用するのが最も簡単な方法です。

[Azure PowerShell 1.0][lnk-powershell-install] をインストールしていない場合は、次の PowerShell コマンドを使用してインストールできます。管理者として PowerShell を実行する必要があります。

```
# Install the Azure Resource Manager modules from PowerShell Gallery
Install-Module AzureRM
Install-AzureRM

# Install the Azure Service Management module from PowerShell Gallery
Install-Module Azure
```

次のステップでは、PowerShell を使用して AD アプリケーション用のパスワード認証を設定する方法を説明します。これらのコマンドは標準的な PowerShell セッションで実行できます。

1. 次のコマンドを使用して Azure サブスクリプションにログインします。

    ```
    Login-AzureRmAccount
    ```

2. **TenantId** と **SubscriptionId** を書き留めます。この情報は後で必要になります。

3. 次のコマンドを使用して新しい Azure Active Directory アプリケーションを作成します。プレース ホルダーを以下のとおりに置き換えます。

    - **{Display name}:** **MySampleApp** など、アプリケーションの表示名
    - **{Home page URL}:** **http://mysampleapp/home** など、アプリケーションのホーム ページの URL。この URL は実際のアプリケーションを示している必要はありません。
    - **{Application identifier}:** **http://mysampleapp** などの一意識別子。この URL は実際のアプリケーションを示している必要はありません。
    - **{Password}:** 自分のアプリで認証に使用するパスワード。

    ```
    New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} IdentifierUris {Application identifier} -Password {Password}
    ```
    
4. 作成したアプリケーションの **ApplicationId** を書き留めておきます。この情報は後で必要になります。

5. 次のコマンドを使用して新しいサービス プリンシパルを作成します。**{MyApplicationId}** を前の手順で書き留めた **ApplicationId** と置き換えます。

    ```
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
    
6. 次のコマンドを使用してロール割り当てを設定します。**{MyApplicationId}** を自分の **ApplicationId** と置き換えます。

    ```
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```
    
これで、カスタム C# アプリケーションから認証できるようにする Azure AD アプリケーションの作成が完了しました。このチュートリアルでは後で次の値が必要になります。

- TenantId
- SubscriptionId
- ApplicationId
- パスワード

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: https://azure.microsoft.com/ja-JP/blog/azps-1-0-pre/

<!----HONumber=AcomDC_1203_2015-->