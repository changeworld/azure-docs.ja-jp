---
author: baanders
description: Azure 関数を Azure Digital Twins と連動するよう構成する方法を説明するインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 7/14/2021
ms.author: baanders
ms.openlocfilehash: 6aabec311df33c1d08d12b48117d9763ccf1761d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748620"
---
Azure CLI または Azure portal を使用して、関数アプリに対するセキュリティ アクセスを設定できます。 好みに合わせて、次の手順に従ってください。

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [digital-twins-configure-function-app-cli.md](digital-twins-configure-function-app-cli.md)]

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

[Azure portal](https://portal.azure.com/) で次の手順を実行します。

#### <a name="assign-an-access-role"></a>アクセス ロールを割り当てる

[!INCLUDE [digital-twins-permissions-required.md](digital-twins-permissions-required.md)]

システム割り当てマネージド ID によって、コード内に資格情報を格納せずに、Azure リソースをクラウド サービス (Azure Key Vault など) に対して認証させることができます。 システム割り当てマネージド ID を有効にした後は、Azure のロールベースのアクセス制御によって、必要なすべてのアクセス許可を付与できます。 

この種類のマネージド ID のライフサイクルは、このリソースのライフサイクルに関連付けられています。 また、各リソースが持つことができるシステム割り当てマネージド ID は 1 つのみです。

1. [Azure portal](https://portal.azure.com/) で検索ボックスに名前を入力して関数アプリを検索します。 結果からアプリを選択します。 

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/portal-search-for-function-app.png" alt-text="Azure portal のスクリーンショット: 関数アプリ名がポータルの検索バーに入力され、検索結果が強調表示されています。":::

1. 関数アプリのページで、左側のメニューの __[ID]__ を選択して、関数のマネージド ID を操作します。 __[システム割り当て済み]__ ページで、 __[状態]__ が **[オン]** に設定されていることを確認します。 そうでない場合は、ここで設定し、変更を **保存** します。

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/verify-system-managed-identity.png" alt-text="Azure portal のスクリーンショット: 関数アプリの [ID] ページで、[状態] オプションが [オン] に設定されています。" lightbox="../articles/digital-twins/media/includes/azure-functions/verify-system-managed-identity.png":::

1. __[Azure でのロールの割り当て]__ を選択します。

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-1.png" alt-text="Azure portal のスクリーンショット。Azure Function の [ID] ページの [アクセス許可] で、[Azure でのロールの割り当て] ボタンが強調表示されています。" lightbox="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-1.png":::

    __[+ ロールの割り当ての追加 (プレビュー)]__ を選択します。

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-2.png" alt-text="Azure portal のスクリーンショット。[Azure でのロールの割り当て] ページで [ロールの割り当ての追加 (プレビュー)] ボタンが強調表示されています。" lightbox="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-2.png":::

1. __[ロールの割り当ての追加 (プレビュー)]__ ページで、次の値を選択します。

    * **スコープ**: _リソース グループ_
    * **サブスクリプション**:Azure サブスクリプションを選択します。
    * **リソース グループ**: リソース グループを選択します。
    * **ロール**: _Azure Digital Twins データ所有者_

    __[保存]__ を選択して設定を保存します。

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-3.png" alt-text="新しいロールの割り当てを追加する方法を示す Azure portal のスクリーンショット。ダイアログには、スコープ、サブスクリプション、リソース グループ、およびロールの各フィールドが表示されています。":::

#### <a name="configure-application-settings"></a>アプリケーション設定の構成

関数から Azure Digital Twins インスタンスの URL にアクセスできるようにするには、そのための環境変数を設定します。 アプリケーションの設定は環境変数として公開され、これを使用して、Azure Digital Twins インスタンスへのアクセスを許可できます。 環境変数の詳細については、「[お使いの関数アプリの管理](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)」を参照してください。 

環境変数にインスタンスの URL を設定するには、まず、インスタンスのホスト名を検索します。 

1. [Azure portal](https://portal.azure.com) でインスタンスを検索します。 
1. 左側のメニューで __[概要]__ を選択します。 
1. __[ホスト名]__ の値をコピーします。

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/instance-host-name.png" alt-text="Azure portal のスクリーンショット。インスタンスの [概要] ページで、ホスト名の値が強調表示されています。":::

これで、アプリケーションの設定を作成できます。

1. ポータルの検索バーで関数アプリを検索し、見つかった関数アプリを選択します。

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/portal-search-for-function-app.png" alt-text="Azure portal のスクリーンショット。関数アプリの名前がポータルの検索バーで検索され、検索結果が強調表示されています。":::

1. 左側のメニューで __[構成]__ を選択します。 次に、 __[アプリケーションの設定]__ タブで、 __[+ 新しいアプリケーション設定]__ を選択します。

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/application-setting.png" alt-text="Azure portal のスクリーンショット。関数アプリの [構成] タブで、新しいアプリケーション設定を作成するためのボタンが強調表示されています。":::

1. 開いたウィンドウで、コピーしたホスト名の値を使用してアプリケーション設定を作成します。
    * **名前**: `ADT_SERVICE_URL`
    * **値**: `https://<your-Azure-Digital-Twins-host-name>`
    
    __[OK]__ を選択して、アプリケーションの設定を作成します。
    
    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/add-application-setting.png" alt-text="Azure portal のスクリーンショット。[アプリケーション設定の追加/編集] ページで、[名前] および [値] フィールドに値が入力されています。[OK] ボタンが強調表示されています。":::

1. 設定を作成すると、 __[アプリケーションの設定]__ タブに表示されます。**ADT_SERVICE_URL** がリストに表示されていることを確認します。 次に、 __[保存]__ を選択して、新しいアプリケーション設定を保存します。

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/application-setting-save-details.png" alt-text="Azure portal のスクリーンショット。[アプリケーションの設定] タブで、新しい [A D T SERVICE URL] 設定 と [保存] ボタンの両方が強調表示されています。":::

1. アプリケーション設定に加えた変更を有効にするには、アプリケーションを再起動する必要があるため、確認のメッセージが表示されたら、 __[続行]__ を選択してアプリケーションを再起動します。

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/save-application-setting.png" alt-text="Azure portal のスクリーンショット。アプリケーション設定を変更するとアプリケーションが再起動されるというメモが表示されています。":::

---
