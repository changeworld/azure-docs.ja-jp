---
title: Azure Functions の継続的なデプロイ | Microsoft Docs
description: Azure App Service の継続的なデプロイ機能を使用して、関数を発行します。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: 2dc5fab0966c2ead0276cd8b23ea764bd4f9ef59
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190476"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions の継続的なデプロイ

Azure Functions を使用すると、[ソース管理の統合](functions-deployment-technologies.md#source-control)を通じてコードを継続的にデプロイできます。 これにより、コードの更新が Azure へのデプロイをトリガーするワークフローが実現します。 Azure Functions を初めて使う場合は、最初に「[Azure Functions の概要](functions-overview.md)」をご覧ください。

複数の頻繁に発生する投稿を統合する場合、継続的なデプロイはプロジェクトに最適なオプションとなります。 関数コードの単一の信頼できるソースを維持することも可能になります。 次のソース コードの場所から、Azure Functions での継続的なデプロイを構成できます。

* [Azure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Azure Functions のデプロイの単位は関数アプリです。 これは、関数アプリ内のすべての機能が同時にデプロイされることを意味します。 信頼できるソースが他の場所に設定されているため、継続的なデプロイが有効になった後、Azure portal での関数コードへのアクセスは*読み取り専用*として構成されます。

## <a name="requirements-for-continuous-deployment"></a>継続的なデプロイの要件

継続的なデプロイを成功させるには、ディレクトリ構造が、Azure Functions が期待する次の基本フォルダー構造との互換性を備えている必要があります。

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="credentials"></a>継続的なデプロイを設定する

既存の関数アプリの継続的なデプロイを構成するには、次の手順に従います。 これらの手順は GitHub リポジトリとの統合を示しますが、Azure Repos またはその他のソース コード リポジトリに対しても同様の手順が適用されます。

1. [Azure portal](https://portal.azure.com) の関数アプリで、 **[プラットフォーム機能]**  >  **[管理者用センター]** を選択します。

    ![管理者用センターを開く](./media/functions-continuous-deployment/platform-features.png)

2. **[管理者用センター]** で、 **[GitHub]** 、 **[Authorize]\(承認\)** の順に選択します。 または、GitHub を既に承認した場合は、 **[続行]** を選択します。 

    ![管理者用センター](./media/functions-continuous-deployment/github.png)

3. GitHub で、 **[Authorize AzureAppService]\(AzureAppService を承認する\)** を選択します。 

    ![承認](./media/functions-continuous-deployment/authorize.png)
    
    Azure portal の **[管理者用センター]** で、 **[続行]** を選択します。

4. 次のビルド プロバイダーのいずれかを選択します。

    * **App Service のビルドサービス** - ビルドが不要な場合、または汎用のビルドが必要な場合に最適です。
    * **Azure Pipelines (プレビュー)** - ビルドをより詳細に制御する必要がある場合に最適です。 このプロバイダーは現在プレビュー段階です。

    ![ビルド プロバイダーの選択](./media/functions-continuous-deployment/build.png)

5. 指定したソース管理オプションに固有の情報を構成します。 GitHub に対して、コードがある **[組織]** 、 **[リポジトリ]** 、および **[ブランチ]** を指定する必要があります。 その後、 **[続行]** を選択します。

    ![GitHub の構成](./media/functions-continuous-deployment/github-specifics.png)

6. 最後に、すべての詳細を確認し、 **[完了]** を選択してデプロイ構成を完了します。

    ![まとめ](./media/functions-continuous-deployment/summary.png)

プロセスが完了すると、指定されたソースからのすべてのコードがアプリにデプロイされます。 その時点で、デプロイ ソースの変更により、Azure 内の関数アプリにそれらの変更をデプロイする処理がトリガーされます。

## <a name="deployment-scenarios"></a>デプロイメント シナリオ

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>継続的なデプロイへの既存の関数の移動

[Azure portal](https://portal.azure.com) で既に関数を記述しており、継続的なデプロイに切り替える前にアプリのコンテンツをダウンロードしたい場合は、関数アプリの **[概要]** タブに移動して、 **[Download app content]\(アプリのコンテンツをダウンロード\)** ボタンをクリックする必要があります。

![アプリのコンテンツのダウンロード](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> 継続的インテグレーションを構成した後は、Functions のポータルでソース ファイルを編集することはできなくなります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Functions のベスト プラクティス](functions-best-practices.md)
