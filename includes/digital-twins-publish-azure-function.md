---
author: baanders
description: Visual Studio から Azure 関数を発行するプロセス用のインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: 49f57f39ba59575417f3336d395b1cfa6cf97eb6
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110451109"
---
Azure で関数アプリにプロジェクトを発行するには、ソリューション エクスプローラーを起動します。 プロジェクトを右クリックし、 **[発行]** を選択できます。

> [!IMPORTANT] 
> Azure の関数アプリに発行すると、Azure Digital Twins とは関係なく、サブスクリプションに追加料金が発生します。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="右クリックのソリューション メニューを示す Visual Studio のスクリーンショット。メニューの [発行] が強調表示されています。":::

表示された **[発行]** ページで、既定のターゲット (**Azure**) を選択したままにします。 **[次へ]** を選択します。 

特定のターゲットで **[Azure Function App (Windows)]** を選択し、 **[次へ]** を選択します。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Azure 関数の発行ダイアログを示す Visual Studio のスクリーンショット。[特定のターゲット] ページで選択されているのは [Azure Function App (Windows)] です。":::

**[Functions インスタンス]** タブで、自分のサブスクリプションを選択します。 その後、プラス (+) アイコンを選択して新しい関数を作成します。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Azure 関数の発行ダイアログを示す Visual Studio のスクリーンショット。プラス アイコンが強調表示されています。":::

**[Function App (Windows) - 新規作成]** ウィンドウの次のフィールドに入力します。
* **[名前]** は、Azure Functions アプリをホストするために Azure で使用される従量課金プランの名前です。 この名前は、実際の関数を保持する関数アプリにも適用されます。 一意の値を選択することも、候補として表示された既定値をそのまま使用することもできます。
* **[サブスクリプション]** が、使用するサブスクリプションと一致することを確認します。 
* **[リソース グループ]** が、使用するものであることを確認します。
* **[プランの種類]** の選択は **[従量課金プラン]** のままにします。
* リソース グループの **[場所]** を選択します。
* **[新規]** リンクを選択して、新しい **Azure Storage** リソースを作成します。 リソース グループに合わせて場所を設定します。それ以外は既定値を使用して、 **[OK]** を選択します。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="新しい関数アプリの詳細が入力されている Azure 関数発行ダイアログ ページが示されている Visual Studio のスクリーンショット。":::

**[作成]** を選択します。

アプリ サービスが作成されると、 **[Functions インスタンス]** タブが開きます。 新しい関数アプリは、 **[関数アプリ]** 領域で、自分のリソース グループの下に表示されます。 **[完了]** を選択します。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="[Functions インスタンス] タブが選択されている Azure 関数発行ダイアログが示されている Visual Studio のスクリーンショット。":::

Visual Studio のメイン ウィンドウで開かれている **[発行]** ペインで、すべての情報が正しいことを確認します。 **[発行]** を選択します。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="[発行] ペインを示す Visual Studio のスクリーンショット。[発行] ボタンが強調表示されています。":::

> [!NOTE]
> 次の例のようなポップアップ ウィンドウが表示された場合は、 **[Azure から資格情報の取得を試みる]** を選択し、 **[保存]** を選択します。
> :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="[公開資格情報] ポップアップ ウィンドウが示されている Visual Studio のスクリーンショット。" border="false":::
>
> 次のいずれかの警告が表示された場合は、画面の指示に従って、最新の Azure Functions Runtime バージョンにアップグレードしてください。
> * "Upgrade Functions version on Azure. (Azure の Functions バージョンをアップグレードしてください)"
> * "Your version of the functions runtime does not match the version running in Azure. (お使いの Functions Runtime のバージョンが Azure で実行されているバージョンと一致しません)。"
>
> これらの警告は、以前のバージョンの Visual Studio を使用している場合に表示されることがあります。

これで、関数アプリが Azure に発行されました。
