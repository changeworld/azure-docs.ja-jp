---
title: Azurite を使用して自動テストを実行する
titleSuffix: Azure Storage
description: Azurite を使用して Azure Blob Storage のプライベート エンドポイントに対して自動テストを書き込む方法について説明します。
services: storage
author: ikivanc
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 03/31/2021
ms.author: ikivanc
ms.openlocfilehash: c4e8a11e0c46cb9a138a1a66060d9fdcc72c192e
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111120"
---
# <a name="run-automated-tests-by-using-azurite"></a>Azurite を使用して自動テストを実行する

Azurite ストレージ エミュレーターを使用して Azure Blob Storage のプライベート エンドポイントに対して自動テストを書き込む方法について説明します。

## <a name="run-tests-on-your-local-machine"></a>ローカル コンピューターでテストを実行する

1. 最新バージョンの [Python](https://www.python.org/) をインストールします。

1. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) をインストールします。

1. [Azurite](../common/storage-use-azurite.md) をインストールして実行します。

   **オプション 1:** npm を使用して Azurite をインストールし、ローカルで実行します。

   ```bash
   # Install Azurite
   npm install -g azurite
   
   # Create an Azurite directory
   mkdir c:/azurite
   
   # Launch Azurite locally
   azurite --silent --location c:\azurite --debug c:\azurite\debug.log
   ```

   **オプション 2:** Docker を使用して Azurite を実行します。

   ```bash
   docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite azurite-blob --blobHost 0.0.0.0
   ```

1. Azure Storage Explorer で、 **[Attach to a local emulator]\(ローカル エミュレーターにアタッチする\)** を選択します。

    :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection.png" alt-text="Azure Storage ソースに接続している Azure Storage Explorer のスクリーンショット。":::

1. Azurite に接続するための **[Display name]\(表示名\)** と **[Blobs port]\(Blob ポート\)** 番号を指定し、Azure Storage Explorer を使用してローカルの Blob Storage を管理します。

   :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection-attach.png" alt-text="ローカル エミュレーターにアタッチする Azure Storage Explorer のスクリーンショット。":::

1. 仮想 Python 環境を作成する

   ```bash
   python -m venv .venv
   ```

1. コンテナーを作成し、環境変数を初期化します。 テストを生成するには、[PyTest](https://docs.pytest.org/) [conftest.py](https://docs.pytest.org/en/2.1.0/plugins.html) ファイルを使用します。 以下に、conftest.py ファイルの例を示します。

   ```python
   from azure.storage.blob import BlobServiceClient
   import os

   def pytest_generate_tests(metafunc):
      os.environ['AZURE_STORAGE_CONNECTION_STRING'] = 'DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;'
      os.environ['STORAGE_CONTAINER'] = 'test-container'

      # Create a container for Azurite for the first run
      blob_service_client = BlobServiceClient.from_connection_string(os.environ.get("AZURE_STORAGE_CONNECTION_STRING"))
      try:
         blob_service_client.create_container(os.environ.get("STORAGE_CONTAINER"))
      except Exception as e:
         print(e)
   ```

   > [!NOTE]
   > `AZURE_STORAGE_CONNECTION_STRING` に表示される値は Azurite の既定値であり、秘密キーではありません。

1. [requirements.txt](https://github.com/Azure-Samples/automated-testing-with-azurite/blob/main/requirements.txt) ファイルに一覧表示されている依存関係をインストールします。

   ```bash
   pip install -r requirements.txt
   ```

1. テストを実行します。

   ```bash
   python -m pytest ./tests
   ```

テストの実行後、Azure Storage Explorer を使用して、Azurite Blob Storage 内のファイルを確認できます。

:::image type="content" source="media/use-azurite-to-run-automated-tests/http-local-blob-storage.png" alt-text="テストによって生成されたファイルを表示している Azure Storage Explorer のスクリーンショット。":::

## <a name="run-tests-on-azure-pipelines"></a>Azure Pipelines でテストを実行する

テストをローカルで実行した後、[Azure Pipelines](/azure/devops/pipelines) でのテストに合格することを確認します。 Azure でホストされたエージェントとして Docker Azurite イメージを使用するか、npm を使用して Azurite をインストールします。 次の Azure Pipelines の例では、npm を使用して Azurite をインストールします。
  
```yaml
trigger:
- master

steps:
- task: UsePythonVersion@0
  displayName: 'Use Python 3.7'
  inputs:
    versionSpec: 3.7

- bash: |
    pip install -r requirements_tests.txt
  displayName: 'Setup requirements for tests'
  
- bash: |
    sudo npm install -g azurite
    sudo mkdir azurite
    sudo azurite --silent --location azurite --debug azurite\debug.log &
  displayName: 'Install and Run Azurite'

- bash: |
    python -m pytest --junit-xml=unit_tests_report.xml --cov=tests --cov-report=html --cov-report=xml ./tests
  displayName: 'Run Tests'

- task: PublishCodeCoverageResults@1
  inputs:
    codeCoverageTool: Cobertura
    summaryFileLocation: '$(System.DefaultWorkingDirectory)/**/coverage.xml'
    reportDirectory: '$(System.DefaultWorkingDirectory)/**/htmlcov'

- task: PublishTestResults@2
  inputs:
    testResultsFormat: 'JUnit'
    testResultsFiles: '**/*_tests_report.xml'
    failTaskOnFailedTests: true
```

Azure Pipelines テストを実行すると、次のような出力が表示されます。

:::image type="content" source="media/use-azurite-to-run-automated-tests/azure-pipeline.png" alt-text="Azure Pipelines のテスト結果のスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

- [ローカルでの Azure Storage の開発に Azurite エミュレーターを使用する](../common/storage-use-azurite.md)
- [サンプル: Azure DevOps Pipeline での Azurite を使用した Blob Storage テストの実行](https://github.com/Azure-Samples/automated-testing-with-azurite)
