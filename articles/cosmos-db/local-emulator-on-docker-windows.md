---
title: Docker for Windows でのエミュレーターの実行
itleSuffix: Running the Azure Cosmos DB emulator on Docker for Windows
description: Docker for Windows 上で Azure Cosmos DB Emulator を実行および使用する方法について説明します。 エミュレーターを使用すると、Azure サブスクリプションを作成せずに無料で、アプリケーションの開発とテストをローカルで行うことができます。
ms.service: cosmos-db
ms.topic: how-to
author: StefArroyo
ms.author: esarroyo
ms.date: 04/20/2021
ms.openlocfilehash: a083efb897c0ce3c0628134da0ea3d29720ac0e7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054887"
---
# <a name="use-the-emulator-on-docker-for-windows"></a><a id="run-on-windows-docker"></a>Docker for Windows 上でエミュレーターを使用する
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB Emulator は、Windows Docker コンテナー上で実行できます。 `Dockerfile` と詳細については、[GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) を参照してください。 現在、このエミュレーターは Docker for Oracle Linux 上では機能しません。 Docker for Windows 上でエミュレーターを実行するには、次の手順を実行します。

1. [Docker for Windows](https://www.docker.com/docker-windows) をインストールしたら、ツール バーの Docker アイコンを右クリックし、 **[Switch to Windows containers]\(Windows コンテナーに切り替える\)** を選択して Windows コンテナーに切り替えます。

1. 次に、普段利用しているシェルから次のコマンドを実行し、Docker Hub からエミュレーター イメージをプルします。

   ```bash
   docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```

1. イメージを開始するには、コマンド ラインまたは PowerShell 環境に応じて次のコマンドを実行します。

   # <a name="command-line"></a>[コマンド ライン](#tab/cli)

   ```bash

   md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```
   Windows ベースの Docker イメージは、一般にすべての Windows ホスト OS と互換性があるとは限りません。 たとえば、既定の Azure Cosmos DB Emulator イメージは Windows 10 および Windows Server 2016 とのみ互換性があります。 Windows Server 2019 と互換性があるイメージが必要な場合は、代わりに次のコマンドを実行します。

   ```bash
   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%hostDirectory%,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/winsrv2019/azure-cosmos-emulator:latest
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell

   md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

   ```

   応答は次のようになります。

   ```bash
   Starting emulator
   Emulator Endpoint: https://172.20.229.193:8081/
   Primary Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   Exporting SSL Certificate
   You can import the SSL certificate from an administrator command prompt on the host by running:
   cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
   powershell .\importcert.ps1
   --------------------------------------------------------------------------------------------------
   Starting interactive shell
   ```
   ---

   > [!NOTE]
   > `docker run` コマンドを実行するときに、ポートの競合エラーが表示された場合 (つまり、指定したポートが既に使用されている場合) は、ポート番号を変更してカスタム ポートを渡します。 たとえば、"-p 8081:8081" パラメーターを "-p 443:8081" に変更することができます

1. 次に、応答からエミュレーター エンドポイントと主キーを使用して、TLS/SSL 証明書をホストにインポートします。 TLS/SSL 証明書をインポートするには、管理者コマンド プロンプトから次の手順を実行します。

   # <a name="command-line"></a>[コマンド ライン](#tab/cli)

   ```bash
   cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
   powershell .\importcert.ps1
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell
   cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
   .\importcert.ps1
   ```
   ---

1. エミュレーターの起動後に対話型シェルを閉じると、エミュレーターのコンテナーがシャットダウンされます。 データ エクスプローラーを開くには、ブラウザーで次の URL に移動します。 上記の応答メッセージにエミュレーター エンドポイントが表示されます。

   `https://<emulator endpoint provided in response>/_explorer/index.html`

Linux Docker コンテナーで実行されている .NET クライアント アプリケーションがあり、ホスト マシン上で Azure Cosmos DB Emulator を実行している場合は、次のセクションの手順に従って、Linux Docker コンテナーに証明書をインポートしてください。

## <a name="regenerate-the-emulator-certificates"></a>エミュレーターの証明書を再生成する

Docker コンテナーでエミュレーターを実行する場合、エミュレーターに関連付けられた証明書は、対応するコンテナーを停止して再起動するたびに再生成されます。 そのため、各コンテナーの起動後に証明書を再インポートする必要があります。 この制限を回避するには、Docker Compose ファイルを使用して、Docker コンテナーを特定の IP アドレスとコンテナー イメージにバインドします。

たとえば、Docker Compose ファイル内で次の構成を使用できます。必要に応じて形式を変更してください。 

```yml
version: '2.4' # Do not upgrade to 3.x yet, unless you plan to use swarm/docker stack: https://github.com/docker/compose/issues/4513

networks:
  default:
    external: false
    ipam:
      driver: default
      config:
        - subnet: "172.16.238.0/24"

services:

  # First create a directory that will hold the emulator traces and certificate to be imported
  # set hostDirectory=C:\emulator\bind-mount
  # mkdir %hostDirectory%

  cosmosdb:
    container_name: "azurecosmosemulator"
    hostname: "azurecosmosemulator"
    image: 'mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator'
    platform: windows
    tty: true
    mem_limit: 3GB
    ports:
        - '8081:8081'
        - '8900:8900'
        - '8901:8901'
        - '8902:8902'
        - '10250:10250'
        - '10251:10251'
        - '10252:10252'
        - '10253:10253'
        - '10254:10254'
        - '10255:10255'
        - '10256:10256'
        - '10350:10350'
    networks:
      default:
        ipv4_address: 172.16.238.246
    volumes:
        - '${hostDirectory}:C:\CosmosDB.Emulator\bind-mount'
```

## <a name="next-steps"></a>次のステップ

この記事では、無料のローカル開発のためにローカル エミュレーターを使用する方法について学習しました。 次の記事に進むことができます。

* [Java、Python、および Node.js アプリで使用する Azure Cosmos DB エミュレーター証明書のエクスポート](local-emulator-export-ssl-certificates.md)
* [コマンド ライン パラメーターと PowerShell コマンドを使用してエミュレーターを制御する](emulator-command-line-parameters.md)
* [エミュレーターに関する問題をデバッグする](troubleshoot-local-emulator.md)
