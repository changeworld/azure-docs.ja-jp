---
author: baanders
description: Azure Digital Twins チュートリアルのインクルード ファイル - サンプル プロジェクトの前提条件
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 5a1baf9631f2d30dd14ff16d2d34beda04605c6c
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660523"
---
## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に **[無料のアカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成** してください。

また、開始する前に、ご利用の開発用コンピューターに **[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) バージョン 16.5 以降をインストール** します。 以前のバージョンが既にインストールされている場合は、ご利用のコンピューター上で "*Visual Studio インストーラー*" アプリを開き、画面の指示に従ってインストールを更新できます。

このチュートリアルは、C# で記述されたサンプル プロジェクトによって進められます。 サンプルについては、こちらを参照してください。[Azure Digital Twins のエンドツーエンド サンプル](/samples/azure-samples/digital-twins-samples/digital-twins-samples)。 サンプルのリンクに移動し、タイトルの下にある " *[Browse Code]\(コードの参照\)* " ボタンを選択して、お使いのマシン上で **サンプル プロジェクトを取得** します。 これにより、サンプル用の GitHub リポジトリに移動します。サンプルは、" *[Code]\(コード\)* " ボタンと、" *[Download ZIP]\(ZIP のダウンロード\)* " を選択して、 *.ZIP* としてダウンロードできます。

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="GitHub のデジタル ツインのサンプル用リポジトリのビュー。[Code]\(コード\) ボタンが選択され、[Download ZIP]\(ZIP のダウンロード\) ボタンが強調表示されている小さなダイアログ ボックスが表示されています。" lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

これにより、 *.ZIP* フォルダーが **digital-twins-samples-master.zip** としてお使いのマシンにダウンロードされます。 フォルダーを解凍し、ファイルを抽出します。

### <a name="prepare-an-azure-digital-twins-instance"></a>Azure Digital Twins インスタンスを準備する

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](digital-twins-local-credentials-outer.md)]
