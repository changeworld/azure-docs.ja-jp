---
title: オフライン展開
description: オフライン展開を使用すると、コンテナー イメージを Microsoft Container Registry からではなく、プライベート コンテナー レジストリからプルできます。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e81ccc8ef133cdf7c454a7a50610a1bfdf2be442
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2021
ms.locfileid: "113731272"
---
# <a name="offline-deployment-overview"></a>オフライン展開の概要

通常、Azure Arc データ コントローラー、SQL Managed Instance、PostgreSQL Hyperscale サーバー グループの作成で使用されるコンテナー イメージは Microsoft Container Registry (MCR) から直接プルされます。 場合によっては、展開先の環境で Microsoft Container Registry に接続できないことがあります。  このような状況では、Microsoft Container Registry にアクセス "_できる_" コンピューターを使用してコンテナー イメージをプルしてからそれにタグを付け、Azure Arc 対応データ サービスをデプロイする環境から接続 "_できる_" プライベート コンテナー レジストリにそれらのイメージをプッシュできます。

Azure Arc 対応データ サービスには毎月の更新プログラムが提供され、多数のコンテナー イメージが存在するため、このコンテナー イメージをプルしてタグを付け、プライベート コンテナー レジストリにプッシュするプロセスはスクリプトを使用して実行することをお勧めします。  このスクリプトは、自動化することも、手動で実行することもできます。

[サンプル スクリプト](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/scripts/pull-and-push-arc-data-services-images-to-private-registry.py)が Azure Arc GitHub リポジトリにあります。

> [!NOTE]
> このスクリプトには、Python と [Docker CLI](https://docs.docker.com/install/) のインストールが必要です。

このスクリプトでは、対話形式で次の情報の入力が要求されます。  あるいは、対話型プロンプトなしでスクリプトを実行したい場合は、スクリプトを実行する前に対応する環境変数を設定することもできます。

|Prompt|環境変数|メモ|
|---|---|---|
|ソース コンテナー レジストリを指定してください - `mcr.microsoft.com` を使用する場合は Enter キーを押してください。|SOURCE_DOCKER_REGISTRY|通常、イメージは Microsoft Container Registry からプルしますが、別のレジストリのプライベート プレビューに参加している場合は、プレビュー プログラムの一部として提供された情報を使用できます。|
|ソース コンテナー レジストリ リポジトリを指定してください - `arcdata` を使用する場合は Enter キーを押してください。|SOURCE_DOCKER_REPOSITORY|Microsoft Container Registry からプルしている場合、このリポジトリは `arcdata` になります。|
|ソース コンテナー レジストリのユーザー名を指定してください - 何も使用しない場合は Enter キーを押してください。|SOURCE_DOCKER_USERNAME|コンテナー イメージをログインが必要なソースからプルしている場合にのみ値を指定します。  Microsoft Container Registry にログインは必要ありません。|
|ソース コンテナー レジストリのパスワードを指定してください - 何も使用しない場合は Enter キーを押してください。|SOURCE_DOCKER_PASSWORD|コンテナー イメージをログインが必要なソースからプルしている場合にのみ値を指定します。  Microsoft Container Registry にログインは必要ありません。 これはマスクされたパスワード プロンプトです。  パスワードを入力したり貼り付けたりしても、そのパスワードは表示されません。|
|ソースでのイメージのコンテナー イメージ タグを指定してください - `<current monthly release tag>` を使用する場合は Enter キーを押してください。|SOURCE_DOCKER_TAG|既定のタグ名は、Microsoft Container Registry 上の現在のリリースの月と年を反映して毎月更新されます。|
|ターゲット コンテナー レジストリの DNS 名または IP アドレスを指定してください。|TARGET_DOCKER_REGISTRY|ターゲット レジストリの DNS 名または IP アドレス。  これは、イメージのプッシュ _先_ のレジストリです。|
|ターゲット コンテナー レジストリ リポジトリを指定してください。|TARGET_DOCKER_REPOSITORY|イメージのプッシュ先のターゲット レジストリ上のリポジトリ。|
|ターゲット コンテナー レジストリのユーザー名を指定してください - 何も使用しない場合は Enter キーを押してください。|TARGET_DOCKER_USERNAME|ターゲット コンテナー レジストリにログインするために使用されるユーザー名 (存在する場合)。|
|ターゲット コンテナー レジストリのパスワードを指定してください - 何も使用しない場合は Enter キーを押してください。|TARGET_DOCKER_PASSWORD|ターゲット コンテナー レジストリにログインするために使用されるパスワード (存在する場合)。 これはマスクされたパスワード プロンプトです。  パスワードを入力したり貼り付けたりしても、そのパスワードは表示されません。|
|ターゲットでのイメージのコンテナー イメージ タグを指定してください。|TARGET_DOCKER_TAG|通常は、混乱を避けるために、ソースと同じタグを使用します。|