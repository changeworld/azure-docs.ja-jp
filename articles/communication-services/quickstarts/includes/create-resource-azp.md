---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 051f903c0b203045886b80f72d868adc1fa85f73
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886100"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/dotnet/)。

## <a name="create-azure-communication-resource"></a>Azure Communication のリソースを作成する

Azure Communication Services のリソースを作成するには、まず [Azure portal](https://portal.azure.com) にサインインします。 ページの左上にある **[+ リソースの作成]** を選択します。 

:::image type="content" source="../media/create-a-communication-resource/create-resource-plus-sign.png" alt-text="Azure portal の [リソースの作成] ボタンが強調表示されているスクリーンショット。":::

**[Marketplace を検索]** 入力、または portal の上部の検索バーに「**Communication**」と入力します。

:::image type="content" source="../media/create-a-communication-resource/searchbar-communication-portal.png" alt-text="検索バーでの Communication Services の検索を示すスクリーンショット。":::

結果から **[Communication Services]** を選択し、 **[追加]** を選択します。

:::image type="content" source="../media/create-a-communication-resource/add-communication-portal.png" alt-text="[追加] ボタンが強調表示された Azure パネルを示すスクリーンショット。":::

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