---
title: Azure Digital Twins インスタンスを作成する
titleSuffix: Azure Digital Twins
description: Azure Digital Twins サービスのインスタンスを設定する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4cac7a3f663d9ede966b8d6e5753c48629049dcd
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86057485"
---
# <a name="set-up-an-azure-digital-twins-instance"></a>Azure Digital Twins インスタンスを設定する

この記事では、新しい Azure Digital Twins インスタンスを設定するための基本的な手順について説明します。 これには、インスタンスの作成と自分用の [Azure Active Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) のアクセス許可のインスタンスへの割り当てが含まれます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="set-up-an-azure-digital-twins-instance"></a>Azure Digital Twins インスタンスを設定する

次に、この操作手順で使用する新しい Azure リソース グループを作成します。 その後、そのリソース グループ内に **Azure Digital Twins の新しいインスタンスを作成**できるようになります。 

また、インスタンスの名前を指定し、デプロイのリージョンを選択する必要があります。 Azure Digital Twins をサポートしているリージョンを確認するには、「[リージョン別の利用可能な Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)」を参照してください。

>[!NOTE]
> 新しいインスタンスの名前は、リージョン内で一意である必要があります (つまり、選択した名前がそのリージョン内の別の Azure Digital Twins インスタンスで既に使用されている場合は、別の名前を選択する必要があります)。

次のコマンドを使用して、リソース グループとインスタンスを作成します。

```azurecli
az group create --location <region> --name <name-for-your-resource-group>
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

これらのコマンドの結果には作成したリソースに関する情報が出力され、次のようになります。

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="リソース グループと Azure Digital Twins インスタンスが正常に作成されたコマンド ウィンドウ":::

Azure Digital Twins インスタンスの *hostName*、*name*、*resourceGroup* を出力から確認します。 これらはすべて、認証および関連する Azure リソースの設定のため Azure Digital Twins の操作を続行する上で必要になることがある重要な値です。

> [!TIP]
> これらのプロパティは、インスタンスのすべてのプロパティと共に、`az dt show --dt-name <your-Azure-Digital-Twins-instance>` を実行することでいつでも確認できます。

### <a name="assign-azure-active-directory-permissions"></a>Azure Active Directory のアクセス許可の割り当て

Azure Digital Twins は、ロールベースのアクセス制御 (RBAC) に [Azure Active Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) を使用します。 これは、Azure Digital Twins インスタンスにデータ プレーン呼び出しを行う前に、これらのアクセス許可を持つロールを自身に割り当てる必要があることを意味します。

クライアント アプリケーションで Azure Digital Twins を使用するには、クライアント アプリが Azure Digital Twins に対して認証できることを確認する必要もあります。 これを行うには、Azure Active Directory (AAD) アプリ登録を設定します。これについては [方法: クライアント アプリケーションを認証する](how-to-authenticate-client.md)に関するページ) を使用します。

#### <a name="assign-yourself-a-role"></a>自分にロールを割り当てる

Azure サブスクリプションの AAD テナントに関連付けられているメールを使用して、自分用のロールの割り当てを Azure Digital Twins インスタンスに作成します。 

これを可能にするには、Azure サブスクリプションで所有者として分類されている必要があります。 これを確認するには、`az role assignment list --assignee <your-Azure-email>` コマンドを実行し、出力内の *roleDefinitionName* の値が *Owner* であることを確認します。 値が *Contributor* であるか、または *Owner* 以外のものであった場合は、サブスクリプションでアクセス許可を付与する権限のあるサブスクリプション管理者に連絡して、ロールを昇格してもらってください。

サブスクリプションの所有者は、次のコマンドを使用して、ユーザーを Azure Digital Twins インスタンスの所有者ロールに割り当てることができます。

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-AAD-email>" --role "Azure Digital Twins Owner (Preview)"
```

このコマンドを実行すると、作成したロールの割り当てに関する情報が出力されます。

> [!TIP]
> 代わりに *400:BadRequest* エラーが発生した場合は、次のコマンドを実行して、ユーザーの *ObjectID* を取得します。
> ```azurecli
> az ad user show --id <your-AAD-email> --query objectId
> ```
> 次に、メールの代わりに、ユーザーの*オブジェクト ID* 使用して、ロールの割り当てコマンドを繰り返します。

これで、Azure Digital Twins インスタンスの準備が完了しました。

## <a name="next-steps"></a>次のステップ

インスタンスで動作するようクライアント アプリをセットアップして認証する方法を参照してください。
* [クライアント アプリケーションを認証する](how-to-authenticate-client.md)
