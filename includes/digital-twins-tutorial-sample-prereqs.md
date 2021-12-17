---
author: baanders
description: Azure Digital Twins チュートリアルのインクルード ファイル - サンプル プロジェクトの前提条件
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 3a3e32a0ef85ba870cfe7af9639794632549f614
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "111350704"
---
## <a name="prerequisites"></a>前提条件

このチュートリアルの手順を実行するには、まず次の前提条件を満たしておく必要があります。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

### <a name="get-required-resources"></a>必要なリソースを入手する

このチュートリアルを実行するには、ご利用の開発マシンに [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) **バージョン 16.5 以降** をインストールします。 以前のバージョンが既にインストールされている場合は、ご利用のコンピューター上で "*Visual Studio インストーラー*" アプリを開き、画面の指示に従ってインストールを更新できます。

>[!NOTE]
> Visual Studio 2019 のインストールに [Azure 開発ワークロード](/dotnet/azure/configure-visual-studio) が含まれていることを確認します。 このワークロードにより、アプリケーションでは Azure functions を発行し、その他の Azure 開発タスクを実行できます。

このチュートリアルは、[C# で記述された Azure Digital Twins のエンドツーエンドのサンプル プロジェクト](/samples/azure-samples/digital-twins-samples/digital-twins-samples)を使用して進められます。 サンプルのリンクに移動し、タイトルの下にある " *[Browse Code]\(コードの参照\)* " ボタンを選択して、お使いのマシン上で **サンプル プロジェクトを取得** します。 これにより、サンプル用の GitHub リポジトリに移動します。 *[Code]\(コード\)* ボタンと、 *[Download ZIP]\(ZIP のダウンロード\)* を選択することによって、.zip 形式でこれをダウンロードできます。

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="zip としてダウンロードする手順を強調表示した GitHub のデジタル ツインのサンプル リポジトリを示すスクリーンショット。" lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

これにより、.zip フォルダーが **digital-twins-samples-master.zip** としてお使いのマシンにダウンロードされます。 フォルダーを解凍し、ファイルを抽出します。

### <a name="prepare-an-azure-digital-twins-instance"></a>Azure Digital Twins インスタンスを準備する

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]
