---
title: Communication Services (.NET) でマネージド ID を使用する
titleSuffix: An Azure Communication Services quickstart
description: マネージド ID を使用すると、Azure VM、関数アプリ、その他のリソースで実行されているアプリケーションからの Azure Communication Services へのアクセスを承認できます。
services: azure-communication-services
author: stefang931
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 12/04/2020
ms.author: gistefan
ms.reviewer: mikben
ms.openlocfilehash: 7e8d9b56077819fc404d6c2bdc39f9f697224136
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692183"
---
# <a name="use-managed-identities-net"></a>マネージド ID を使用する (.NET)

.NET でマネージド ID を使用して、Azure Communication Services を開始します。 Communication Services 管理および SMS クライアント ライブラリでは、[Azure リソースのマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用した Azure Active Directory (Azure AD) 認証がサポートされています。

このクイックスタートでは、マネージド ID をサポートする Azure 環境から、管理および SMS クライアント ライブラリへのアクセスを承認する方法について説明します。 また、開発環境でコードをテストする方法についても説明します。

## <a name="prerequisites"></a>前提条件

 - アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成する](https://azure.microsoft.com/free)
 - アクティブな Communication Services リソースと接続文字列。 [Communication Services のリソースを作成する](./create-communication-resource.md?pivots=platform-azp&tabs=windows)。

## <a name="setting-up"></a>設定

### <a name="enable-managed-identities-on-a-virtual-machine-or-app-service"></a>仮想マシンまたは App Service でマネージド ID を有効にする

承認対象の Azure リソースでマネージド ID を有効にする必要があります。 Azure リソースのマネージド ID を有効にする方法については、次の記事のいずれかを参照してください。

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager テンプレート](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager クライアント ライブラリ](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [App Services](../../app-service/overview-managed-identity.md)

#### <a name="assign-azure-roles-with-the-azure-portal"></a>Azure portal を使用して Azure ロールを割り当てる

1. Azure Portal に移動します。
1. Azure Communication Service リソースに移動します。
1. [アクセス制御 (IAM)] メニュー > [+ 追加] > [ロールの割り当ての追加] に移動します。
1. [共同作成者] のロールを選択します (サポートされている唯一のロールです)。
1. [ユーザー割り当てマネージド ID] (または [システム割り当てマネージド ID]) を選択し、目的の ID を選択します。 選択内容を保存します。

![マネージド ID のロール](media/managed-identity-assign-role.png)

#### <a name="assign-azure-roles-with-powershell"></a>PowerShell を使用して Azure ロールを割り当てる

PowerShell を利用してロールおよびアクセス許可を割り当てるには、「[Azure PowerShell を使用して Azure ロールの割り当てを追加または削除する](../../../articles/role-based-access-control/role-assignments-powershell.md)」を参照してください。

## <a name="add-managed-identity-to-your-communication-services-solution"></a>Communication Services ソリューションにマネージド ID を追加する

### <a name="install-the-client-library-packages"></a>クライアント ライブラリ パッケージをインストールする

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Configuration
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>クライアント ライブラリ パッケージを使用する

Azure ID と Azure Storage クライアント ライブラリを使用するために、次の `using` ディレクティブをコードに追加します。

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Configuration;
using Azure.Communication.Sms;
```

下の例では [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) が使用されています。 この資格情報は、運用と開発の各環境に適しています。

### <a name="create-an-identity-and-issue-a-token"></a>ID を作成してトークンを発行する

次のコード例は、Azure Active Directory トークンを使用してサービス クライアント オブジェクトを作成し、このクライアントを使用して新しいユーザーのトークンを発行する方法を示しています。

```csharp
     public async Task<Response<CommunicationUserToken>> CreateIdentityAndIssueTokenAsync(Uri resourceEdnpoint) 
     {
          TokenCredential credential = new DefaultAzureCredential();
     
          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();
     
          var tokenResponse = await client.IssueTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-azure-active-directory-tokens"></a>Azure Active Directory トークンを使用して SMS を送信する

次のコード例は、Azure Active Directory トークンを使用してサービス クライアント オブジェクトを作成し、このクライアントを使用して SMS メッセージを送信する方法を示しています。

```csharp

     public async Task SendSmsAsync(Uri resourceEndpoint, PhoneNumber from, PhoneNumber to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
     
          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SendSmsOptions { EnableDeliveryReport = true } // optional
          );
     }
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [認証について学習する](../concepts/authentication.md)

次のことも実行できます。

- [Azure のロールベースのアクセス制御の詳細について学習する](../../../articles/role-based-access-control/index.yml)
- [.NET 用 Azure ID ライブラリの詳細について学習する](/dotnet/api/overview/azure/identity-readme)
- [ユーザー アクセス トークンを作成する](../quickstarts/access-tokens.md)
- [SMS メッセージの送信](../quickstarts/telephony-sms/send.md)
- [SMS に関する詳細](../concepts/telephony-sms/concepts.md)