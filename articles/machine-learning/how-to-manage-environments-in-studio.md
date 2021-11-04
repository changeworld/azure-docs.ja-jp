---
title: スタジオで環境を管理する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning スタジオで環境を作成および管理する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: 38dc05a1b2d0baf4058ce3c4d8a90384f7dce8ff
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560893"
---
# <a name="manage-software-environments-in-azure-machine-learning-studio"></a>Azure Machine Learning スタジオでソフトウェア環境を管理する

この記事では、Azure Machine Learning スタジオで Azure Machine Learning の[環境](/python/api/azureml-core/azureml.core.environment.environment)を作成および管理する方法について説明します。 環境を使用して、プロジェクトのソフトウェアの発展する依存関係を追跡および再現します。

この記事の例は、以下の方法を示します。

* キュレートされた環境を参照する。
* 環境を作成して、パッケージの依存関係を指定する。
* 既存の環境の仕様とそのプロパティを編集する。
* 環境をリビルドしてイメージのビルド ログを確認する。

Azure Machine Learning の環境のしくみの概要については、[ML 環境の概要](concept-environments.md)に関するページを参照してください。 [Azure Machine Learning のための開発環境をセットアップする方法](how-to-configure-environment.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)

## <a name="browse-curated-environments"></a>キュレートされた環境を参照する

キュレーションされた環境には、既定でお使いのワークスペースで使用できる Python パッケージのコレクションが含まれています。 これらの環境は、キャッシュされた Docker イメージでバックアップされ、実行の準備コストが下がり、トレーニングと推論シナリオがサポートされます。 

環境をクリックすると、その内容についての詳細な情報が表示されます。 詳細については、「[Azure Machine Learning のキュレーションされた環境](resource-curated-environments.md)」を参照してください。 

## <a name="create-an-environment"></a>環境の作成

環境を作成するには:
1. [Azure Machine Learning Studio](https://ml.azure.com) で、ワークスペースを開きます。
1. 左側にある **[環境]** を選択します。
1. **[カスタム環境]** タブを選択します。 
1. **[作成]** ボタンを選択します。 

次のいずれかを指定して環境を作成します。
* pip 要件[ファイル](https://pip.pypa.io/en/stable/cli/pip_install)
* Conda yaml [ファイル](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)
* Docker [イメージ](https://hub.docker.com/search?q=&type=image)
* [Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

:::image type="content" source="media/how-to-manage-environments-in-studio/create-page.jpg" alt-text="環境の作成ウィザード":::

構成ファイルをカスタマイズしてタグと説明を追加し、プロパティを確認したうえで、エンティティを作成することができます。 

ワークスペースで新しい環境に既存の環境と同じ名前を付けた場合、既存の環境の新しいバージョンが作成されます。

## <a name="view-and-edit-environment-details"></a>環境の詳細を表示して編集する

環境が作成されたら、名前をクリックしてその詳細を表示します。 ドロップダウンメニューを使用して、さまざまなバージョンの環境を選択します。 ここで、環境の Docker レイヤーと Conda レイヤーを通じて、そのメタデータと内容を確認できます。 

鉛筆アイコンをクリックして、タグと説明、および構成ファイルまたはイメージを編集します。 Docker セクションまたは Conda セクションに少しでも変更を加えると、新しいバージョンの環境が作成されることに留意してください。 

:::image type="content" source="media/how-to-manage-environments-in-studio/details-page.jpg" alt-text="[環境の詳細] ページ":::

## <a name="view-image-build-logs"></a>イメージのビルド ログを確認する

詳細ページ内の **[ビルド ログ]** タブをクリックすると、環境のバージョンに対するイメージのビルド ログが表示されます。 

## <a name="rebuild-an-environment"></a>環境をリビルドする

詳細ページで **[リビルド]** ボタンをクリックすると、環境がリビルドされます。 構成ファイル内の固定されていないパッケージ バージョンが、このアクションによって最新のバージョンに更新されます。 
