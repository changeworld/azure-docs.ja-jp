---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: b602cbfde22cc87b42a32b007c19b626814d1660
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554554"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/dotnet/)。

## <a name="create-azure-communication-resource"></a>Azure Communication のリソースを作成する

Azure Communication Services のリソースを作成するには、まず [Azure portal](https://portal.azure.com) にサインインします。 ページの左上にある **[+ リソースの作成]** を選択します。 

:::image type="content" source="../media/create-a-communication-resource/create-resource-plus-sign.png" alt-text="Azure portal の [リソースの作成] ボタンが強調表示されているスクリーンショット。":::

**[Marketplace を検索]** 入力、または portal の上部の検索バーに「**Communication**」と入力します。

:::image type="content" source="../media/create-a-communication-resource/searchbar-communication-portal.png" alt-text="検索バーでの Communication Services の検索を示すスクリーンショット。":::

結果から **[Communication Services]** を選択し、 **[作成]** を選択します。

:::image type="content" source="../media/create-a-communication-resource/create-communication-portal.png" alt-text="[作成] ボタンが強調表示された [Communication Services] パネルを示すスクリーンショット。":::

これで、Communication Services のリソースを構成できます。 作成プロセスの最初のページで、次の内容を指定するように求められます。

* サブスクリプション
* リソース グループ (新しいリソース グループを作成するか、既存のリソース グループを選択できます)
* Communication Services のリソースの名前
* リソースが関連付けられる地理的な場所

次の手順では、リソースにタグを割り当てることができます。 タグは、Azure リソースを整理するために使用できます。 タグの詳細については、[リソースのタグ付けに関するドキュメント](../../../azure-resource-manager/management/tag-resources.md)を参照してください。

最後に、構成を確認し、リソースを **作成** できます。 デプロイが完了するまでに数分かかることに注意してください。

## <a name="manage-your-communication-services-resource"></a>Communication Services のリソースを管理する

Communication Services のリソースを管理するには、[Azure portal](https://portal.azure.com) にアクセスし、「**Azure Communication Services**」を検索して選択します。

**[Communication Services]** ページで、リソースの名前を選択します。

リソースの **[概要]** ページには、参照、停止、開始、再起動、削除などの基本的な管理のためのオプションが含まれています。 その他の構成オプションについては、リソース ページの左側のメニューをご覧ください。
