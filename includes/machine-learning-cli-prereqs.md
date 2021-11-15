---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2021
ms.author: larryfr
ms.openlocfilehash: 8a1f8d3fff304e6abbff97308c06fc9fffc84c6c
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131553451"
---
この記事の手順に従う前に、次の前提条件が満たされていることをご確認ください。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://azure.microsoft.com/free/) をお試しください。

* [Azure CLI](/cli/azure/) と Azure CLI の `ml` 拡張機能。 詳細については、[CLI (v2) (プレビュー) のインストール、セットアップ、使用](/azure/machine-learning/how-to-configure-cli)に関するページを参照してください。

    > [!IMPORTANT]
    > この記事の CLI の例では、Bash (または互換性のある) シェルを使用していることを前提としています。 たとえば、Linux システムや [Linux 用 Windows サブシステム](/windows/wsl/about)などです。 
 
* Azure Machine Learning ワークスペース。 お持ちでない場合は、[CLI (v2) のインストール、セットアップ、使用 (プレビュー)](/azure/machine-learning/how-to-configure-cli) に関する記事の手順を使用して作成します。