---
title: Media Services と Python の基本的なエンコードのクイックスタート
description: このクイックスタートでは、Python と Azure Media Services で基本的なエンコードを行う方法について説明します。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: ''
ms.topic: quickstart
ms.date: 7/2/2021
ms.author: inhenkel
ms.openlocfilehash: a0534bc562d20f96cc1c12a8b4dbe0adfc8f9282
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114284411"
---
# <a name="media-services-basic-encoding-with-python"></a>Python を使用した Media Services の基本的なエンコード

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="introduction"></a>はじめに

このクイックスタートでは、Python と Azure Media Services で基本的なエンコードを行う方法について説明します。 この例では、2020-05-01 Media Service v3 API を使用しています。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
- このクイックスタートで使用するリソース グループを作成します。
- Media Services v3 アカウントを作成します。
- ストレージ アカウント キーを取得します。
- サービス プリンシパルとキーを作成します。

## <a name="get-the-sample"></a>サンプルを入手する

フォークを作成し、[Python サンプル リポジトリ](https://github.com/Azure-Samples/media-services-v3-python)にあるサンプルをクローンします。 このクイックスタートでは、BasicEncoding サンプルを使用します。

## <a name="create-the-env-file"></a>.env ファイルを作成する

アカウントから値を取得して *.env* ファイルを作成します。 つまり、拡張子のみで名前は付けずに保存します。  テンプレートとして *sample.env* を使用し、ローカル クローン内の BasicEncoder フォルダーに *.env* ファイルを保存します。

## <a name="use-python-virtual-environments"></a>Python 仮想環境を使用する
サンプルについては、次の手順に従って Python 仮想環境を作成し、アクティブにすることをお勧めします。

1. VSCode または他のエディターでサンプル フォルダーを開きます
2. 仮想環境を作成する

    ``` bash
      # py -3 uses the global python interpreter. You can also use python -m venv .venv.
      py -3 -m venv .venv
    ```

   このコマンドにより、Python venv モジュールが実行され、.venv という名前のフォルダーに仮想環境が作成されます。

3. 次のコマンドを実行して、仮想環境をアクティブにします。

    ``` bash
      .venv\scripts\activate
    ```

  仮想環境は、特定の Python インタープリターのコピーを分離する、プロジェクト内のフォルダーです。 この環境がアクティブになったら (アクティブ化は Visual Studio Code によって自動的に実行されます)、pip install を実行することで、ライブラリがその環境にのみインストールされます。 次に、Python コードを実行すると、すべてのライブラリの特定バージョンを使用して、環境の正確なコンテキストでコードが実行されます。 また、pip freeze を実行すると、これらのライブラリの正確な一覧が表示されます。 (多くのサンプルでは、必要なライブラリを示す requirements.txt ファイルを作成し、pip install -r requirements.txt を使用します。 コードを Azure にデプロイする場合は、通常、要件ファイルが必要になります)。

## <a name="set-up"></a>設定

[Azure 用のローカル Python 開発環境をセットアップして構成します](/azure/developer/python/configure-local-development-environment)

Python 用の azure-identity ライブラリをインストールします。 このモジュールは、Azure Active Directory の認証に必要です。 詳細については、[Python 用 Azure Identity クライアント ライブラリ](/python/api/overview/azure/identity-readme#environment-variables)に関する記事を参照してください

  ``` bash
  pip install azure-identity
  ```

[Azure Media Services](/python/api/overview/azure/media-services) 用の Python SDK をインストールします

最新バージョンの詳細が含まれる Media Services Python SDK の Pypi ページは、[azure-mgmt-media](https://pypi.org/project/azure-mgmt-media/) にあります

  ``` bash
  pip install azure-mgmt-media
  ```

[Azure Storage SDK for Python](https://pypi.org/project/azure-storage-blob/) をインストールします

  ``` bash
  pip install azure-storage-blob
  ```

必要に応じて、samples フォルダーの "requirements.txt" ファイルを使用して、特定のサンプルのすべての要件をインストールできます

  ``` bash
  pip install -r requirements.txt
  ```

## <a name="try-the-code"></a>コードを試す

以下のコードは完全にコメント化されています。  スクリプト全体を使用するか、一部を抜粋して独自のスクリプトにご使用ください。

このサンプルでは、スクリプトの実行時に一緒に作成されたグループとして識別できるよう名前付けの目的で乱数が生成されます。  乱数は省略可能で、スクリプトのテストが完了したら削除してかまいません。

このサンプルでは、入力資産の SAS URL は使用していません。

[!code-python[Main](../../../media-services-v3-python/BasicEncoding/basic-encoding.py)]

## <a name="delete-resources"></a>リソースを削除する

クイックスタートを完了したら、リソース グループに作成したリソースを削除してください。

## <a name="next-steps"></a>次のステップ

[Media Services Python SDK](/python/api/azure-mgmt-media/) について理解を深めます。

## <a name="resources"></a>リソース

- Azure Media Services [管理 API](/python/api/overview/azure/mediaservices/management) を参照する。
- [Python で Storage API](/azure/developer/python/azure-sdk-example-storage-use?tabs=cmd) を使用する方法を学習する
- [Python 用の Azure Identity クライアント ライブラリ](/python/api/overview/azure/identity-readme#environment-variables)の詳細を学習する
- [Azure Media Services v3](./media-services-overview.md) の詳細を学習する。
- [Azure Python SDK](/azure/developer/python) について学習する
- [Azure Python SDK の使用パターン](/azure/developer/python/azure-sdk-library-usage-patterns)について学習する
- [Azure Python SDK 索引](/azure/developer/python/azure-sdk-library-package-index)で他の Azure Python SDK を検索する
- [Azure Storage Blob Python SDK リファレンス](/python/api/azure-storage-blob/)