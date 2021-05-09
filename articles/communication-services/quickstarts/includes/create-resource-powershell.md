---
ms.openlocfilehash: 340635f273c20c9d8b42fde0c07a7aa97d9f348a
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2021
ms.locfileid: "108293247"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/dotnet/)。
- [Azure Az PowerShell モジュール](/powershell/azure/)をインストールする

## <a name="create-azure-communication-resource"></a>Azure Communication のリソースを作成する

Azure Communication Services のリソースを作成するには、[Azure CLI にサインイン](/cli/azure/authenticate-azure-cli)します。 そのためにはターミナルから ```Connect-AzAccount``` コマンドを使用し、資格情報を入力します。 リソースを作成するには、次のコマンドを実行します。

```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global
```

特定のサブスクリプションを選択したい場合は、```--subscription``` フラグを指定してサブスクリプション ID を指定することもできます。
```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global -SubscriptionId SubscriptionID
```

以下のオプションを使用して、Communication Services のリソースを構成できます。

* リソース グループ
* Communication Services のリソースの名前
* リソースが関連付けられる地理的な場所

次の手順では、リソースにタグを割り当てることができます。 タグは、Azure リソースを整理するために使用できます。 タグの詳細については、[リソースのタグ付けに関するドキュメント](../../../azure-resource-manager/management/tag-resources.md)を参照してください。

## <a name="manage-your-communication-services-resource"></a>Communication Services のリソースを管理する

Communication Services のリソースにタグを追加するには、以下のコマンドを実行します。 また、特定のサブスクリプションを対象にすることもできます。

```PowerShell
PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"}

PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"} -SubscriptionId SubscriptionID
```

特定のサブスクリプションにある Azure Communication Services リソースをすべて一覧表示するには、次のコマンドを使用します。

```PowerShell
PS C:\> Get-AzCommunicationService -SubscriptionId SubscriptionID
```

特定のリソースの情報をすべて一覧表示するには、次のコマンドを使用します。

```PowerShell
PS C:\> Get-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1
```