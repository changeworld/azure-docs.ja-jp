---
title: ActiveDirectoryInteractive を使用した SQL への接続 | Microsoft Docs
description: SqlAuthenticationMethod.ActiveDirectoryInteractive モードを使用して Azure SQL Database に接続するための C# コード例と説明。
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: active directory
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: MirekS
ms.reviewer: GeneMi
ms.openlocfilehash: 3d6eb70b3ce9072dc2c51220af89549022b5dacf
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238270"
---
# <a name="use-activedirectoryinteractive-mode-to-connect-to-azure-sql-database"></a>ActiveDirectoryInteractive モードを使用した Azure SQL Database への接続

この記事では、Microsoft Azure SQL Database に接続するための実行可能な C# コードの例を紹介します。 この C# プログラムでは、Azure AD Multi-Factor Authentication (MFA) をサポートする対話モードの認証を使用します。 たとえば、携帯電話に送信される確認コードを接続試行に含めることができます。

SQL ツールでの MFA のサポートの詳細については、「[Azure Active Directory support in SQL Server Data Tools (SSDT) (SQL Server Data Tools (SSDT) での Azure Active Directory のサポート)](https://docs.microsoft.com/sql/ssdt/azure-active-directory)」をご覧ください。




## <a name="sqlauthenticationmethod-activedirectoryinteractive-enum-value"></a>SqlAuthenticationMethod .ActiveDirectoryInteractive 列挙値

.NET Framework バージョン 4.7.2 以降では、[**SqlAuthenticationMethod**](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) 列挙型に **.ActiveDirectoryInteractive** という新しい値があります。 この列挙値は、C# クライアント プログラムで使用すると、Azure SQL Database に対する認証に、MFA をサポートする Azure AD 対話モードを使用するようシステムに指示します。 プログラムを実行するユーザーには、次のダイアログが表示されます。

1. Azure AD ユーザー名を表示し、その Azure AD ユーザーのパスワードの入力を求めるダイアログ。
    - パスワードが不要な場合、このダイアログは表示されません。 ユーザーのドメインが Azure AD とフェデレーションされている場合、パスワードは不要です。

    Azure AD で設定されたポリシーによってユーザーに MFA が適用される場合は、以下のダイアログが次に表示されます。

2. ユーザーが MFA シナリオを初めて実行するときにのみ、システムによって追加のダイアログが表示されます。 ダイアログでは、テキスト メッセージの送信先の携帯電話番号の入力を求められます。 各メッセージでは、ユーザーが次のダイアログに入力する必要がある*確認コード*が提供されます。

3. システムから携帯電話に送信された MFA 確認コードの入力を求める別のダイアログ。

MFA を要求するように Azure AD を構成する方法については、「[Getting started with Azure Multi-Factor Authentication in the cloud (クラウドでの Azure Multi-Factor Authentication の概要)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud)」をご覧ください。

これらのダイアログのスクリーンショットについては、「[Configure multi-factor authentication for SQL Server Management Studio and Azure AD (SQL Server Management Studio と Azure AD 用に多要素認証を構成する)](sql-database-ssms-mfa-authentication-configure.md)」をご覧ください。

> [!TIP]
> あらゆる種類の .NET Framework API の一般的な検索ページは、便利な **.NET API ブラウザー** ツールへの次のリンクで利用できます。
>
> [https://docs.microsoft.com/dotnet/api/](https://docs.microsoft.com/dotnet/api/)
>
> 省略可能な **?term=** パラメーターに型名を追加すると、検索結果が表示された状態で検索ページが表示されます。
>
> [https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)


## <a name="preparations-for-c-by-using-the-azure-portal"></a>Azure portal を使用した C# の準備

[Azure SQL Database サーバーが既に作成され](sql-database-get-started-portal.md)、使用可能であることを前提としています。


### <a name="a-create-an-app-registration"></a>A. アプリの登録を作成する

Azure AD Authentication を使用するには、C# クライアント プログラムは、接続を試みるときに、*clientId* として GUID を指定する必要があります。 アプリの登録を完了すると GUID が生成され、"**アプリケーション ID**" というラベルで Azure portal に表示されます。 ナビゲーション手順は次のとおりです。

1. Azure portal &gt; **[Azure Active Directory]** &gt; **[アプリの登録]**

    ![アプリの登録](media\active-directory-interactive-connect-azure-sql-db\sshot-create-app-registration-b20.png)

2. **アプリケーション ID** 値が生成され、表示されます。

    ![表示されたアプリ ID](media\active-directory-interactive-connect-azure-sql-db\sshot-application-id-app-regis-mk49.png)

3. **[登録済みのアプリ]** &gt; **[設定]** &gt; **[必要なアクセス許可]** &gt; **[追加]**

    ![登録済みのアプリのアクセス許可の設定](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

4. **[必要なアクセス許可]** &gt; **[API アクセスの追加]** &gt; **[Select an API]\(API の選択\)** &gt; **[Azure SQL Database]**

    ![Azure SQL Database の API へのアクセスの追加](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

5. **[API アクセス]** &gt; **[アクセス許可の選択]** &gt; **[委任されたアクセス許可]**

    ![Azure SQL Database の API へのアクセス許可の委任](media\active-directory-interactive-connect-azure-sql-db\sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)


### <a name="b-set-azure-ad-admin-on-your-sql-database-server"></a>B. SQL Database サーバーの Azure AD 管理者を設定する

各 Azure SQL Database サーバーには、Azure AD の独自の SQL 論理サーバーがあります。 この C# シナリオでは、Azure SQL サーバーの Azure AD 管理者を設定する必要があります。

1. **[SQL Server]** &gt; **[Active Directory 管理者]** &gt; **[管理者の設定]**

    - Azure SQL Database の Azure AD 管理者とユーザーの詳細については、[SQL Database での Azure Active Directory Authentication の構成と管理](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)に関する記事の「**Provision an Azure Active Directory administrator for your Azure SQL Database server (Azure SQL Database サーバーの Azure Active Directory 管理者をプロビジョニングする)**」のスクリーンショットをご覧ください。


### <a name="c-prepare-an-azure-ad-user-to-connect-to-a-specific-database"></a>C. 特定のデータベースに接続する Azure AD ユーザーを準備する

Azure SQL Database サーバーに固有の Azure AD では、特定のデータベースにアクセスするユーザーを追加できます。

詳細については、「[Use Azure Active Directory Authentication for authentication with SQL Database, Managed Instance, or SQL Data Warehouse (SQL Database、マネージド インスタンス、または SQL Data Warehouse での認証に Azure Active Directory Authentication を使用する)](sql-database-aad-authentication.md)」をご覧ください。


### <a name="d-add-a-non-admin-user-to-azure-ad"></a>D. Azure AD に管理者以外のユーザーを追加する

SQL Database サーバーの Azure AD 管理者を使用して、SQL Database サーバーに接続できます。 ただし、Azure AD に管理者以外のユーザーを追加する方が一般的です。 管理者以外のユーザーを接続に使用した場合、Azure AD によってこのユーザーに MFA が適用されると、MFA シーケンスが呼び出されます。




## <a name="azure-active-directory-authentication-library-adal"></a>Azure Active Directory Authentication Library (ADAL)

この C# プログラムでは、**Microsoft.IdentityModel.Clients.ActiveDirectory** 名前空間を利用します。 この名前空間のクラスは、同じ名前のアセンブリに含まれています。

- NuGet を使用して ADAL アセンブリをダウンロードし、インストールします。
    - [https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

- C# プログラムのコンパイルをサポートするために、このアセンブリへの参照を追加します。




## <a name="sqlauthenticationmethod-enum"></a>SqlAuthenticationMethod 列挙型

この C# の例で利用する名前空間の 1 つが **System.Data.SqlClient** です。 特に重要なのは、**SqlAuthenticationMethod** 列挙型です。 この列挙型には次の値があります。

- **SqlAuthenticationMethod.ActiveDirectory\*Interactive**\*:&nbsp; MFA (Multi-Factor Authentication) を実現するために、Azure AD ユーザー名と共に使用します。
    - この記事ではこの値が重視されます。 ユーザー パスワードのダイアログを表示し、このユーザーに MFA を適用する場合は MFA 検証のダイアログを表示することで、対話型エクスペリエンスを実現します。
    - この値は、.NET Framework バージョン 4.7.2 以降で使用できます。

- **SqlAuthenticationMethod.ActiveDirectory\*Integrated**\*:&nbsp; "*フェデレーション*" アカウントに使用します。 フェデレーション アカウントの場合、ユーザー名は Windows ドメインに認識されています。 この方法では、MFA はサポートされていません。

- **SqlAuthenticationMethod.ActiveDirectory\*Password**\*:&nbsp; Azure AD ユーザーとそのユーザーのパスワードを必要とする認証に使用します。 Azure SQL Database によって認証が実行されます。 この方法では、MFA はサポートされていません。




## <a name="prepare-c-parameter-values-from-the-azure-portal"></a>Azure portal から C# パラメーター値を準備する

C# プログラムを正常に実行するには、次の静的フィールドに適切な値を割り当てる必要があります。 これらの静的フィールドは、プログラムのパラメーターと同様に機能します。 各フィールドと架空の値を次に示します。 また、適切な値を取得できる Azure portal 内の場所も示します。


| 静的フィールド名 | 架空の値 | Azure portal 内の場所 |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-favorite-sqldb-svr.database.windows.net" | **[SQL servers]\(SQL サーバー\)** &gt; **[Filter by name]\(名前でフィルター\)** |
| AzureAD_UserID | "user9@abc.onmicrosoft.com" | **[Azure Active Directory]** &gt; **[ユーザー]** &gt; **[新しいゲスト ユーザー]** |
| Initial_DatabaseName | "master" | **[SQL servers]\(SQL サーバー\)** &gt; **[SQL データベース]** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **[Azure Active Directory]** &gt; **[アプリの登録]**<br /> &nbsp; &nbsp; &gt; **[Search by name]\(名前で検索\)** &gt; **[アプリケーション ID]** |
| RedirectUri | 新しい URI ("https://bing.com/") | **[Azure Active Directory]** &gt; **[アプリの登録]**<br /> &nbsp; &nbsp; &gt; **[Search by name]\(名前で検索\)** &gt; *[使用するアプリ登録]* &gt;<br /> &nbsp; &nbsp; **[設定]** &gt; **[RedirectURIs]**<br /><br />この記事では、RedirectUri に任意の有効な値を指定できます。 この値は、ここでは実際には使用されません。 |
| &nbsp; | &nbsp; | &nbsp; |


シナリオによっては、上記の表のすべてのパラメーターの値が必要なわけではない場合もあります。




## <a name="run-ssms-to-verify"></a>SSMS を実行して確認する

C# プログラムを実行する前に、SQL Server Management Studio (SSMS) を実行すると便利です。 SSMS を実行すると、さまざまな構成が正しいかどうかが確認されます。 これにより、C# プログラムのエラーの範囲をソース コードに限定できます。


#### <a name="verify-sql-database-firewall-ip-addresses"></a>SQL Database のファイアウォールの IP アドレスを確認する

後で C# プログラムを実行するときと同じ構成で、同じコンピューターから SSMS を実行します。 最も簡単と思われる**認証**モードを使用できます。 データベース サーバーのファイアウォールが、指定した IP アドレスを受け入れていないことを示している場合は、「[Azure SQL Database server-level and database-level firewall rules (Azure SQL Database のサーバー レベルおよびデータベース レベルのファイアウォール規則)](sql-database-firewall-configure.md)」の説明に従ってこれを修正できます。


#### <a name="verify-multi-factor-authentication-mfa-for-azure-ad"></a>Azure AD の Multi-Factor Authentication (MFA) を確認する

**[認証]** を **[Active Directory - MFA サポートで汎用]** に設定して、SSMS をもう一度実行します。 このオプションを使用するには、SSMS バージョン 17.5 以降が必要です。

詳細については、[SSMS と Azure AD 用の多要素認証の構成](sql-database-ssms-mfa-authentication-configure.md)に関する記事をご覧ください。




## <a name="c-code-example"></a>C# コード例

この C# の例をコンパイルするには、**Microsoft.IdentityModel.Clients.ActiveDirectory** という名前の DLL アセンブリへの参照を追加する必要があります。


#### <a name="reference-documentation"></a>リファレンス ドキュメント

- **System.Data.SqlClient** 名前空間:
    - 検索:&nbsp; [https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient](https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient)
    - 直接:&nbsp; [System.Data.Client](https://docs.microsoft.com/dotnet/api/system.data.sqlclient)

- **Microsoft.IdentityModel.Clients.ActiveDirectory** 名前空間:
    - 検索:&nbsp; [https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory)
    - 直接:&nbsp; [Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory)


#### <a name="c-source-code-in-two-parts"></a>2 つに分割された C# ソース コード

&nbsp;

```csharp

using System;    // C# ,  part 1 of 2.

// Add a reference to assembly:  Microsoft.IdentityModel.Clients.ActiveDirectory.DLL
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<YOUR VALUE HERE>";
        static public string AzureAD_UserID = "<YOUR VALUE HERE>";
        static public string Initial_DatabaseName = "master";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<YOUR VALUE HERE>";
        static public readonly Uri RedirectUri = new Uri("<YOUR VALUE HERE>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program .

```

&nbsp;

#### <a name="second-half-of-the-c-program"></a>C# プログラムの後半

見やすくするために、この C# プログラムは 2 つのコード ブロックに分割されています。 プログラムを実行するには、2 つのコード ブロックをまとめて貼り付けます。

&nbsp;

```csharp

    // C# ,  part 2 of 2 ,  to concatenate below part 1.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2 .
    ///  . 
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  . 
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider .
} // EONamespace.  End of entire program source code.

```

&nbsp;

#### <a name="actual-test-output-from-c"></a>C# の実際のテスト出力

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

&nbsp;


## <a name="next-steps"></a>次の手順

- [Get-AzureRMSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator)

