---
title: セルフホステッド Azure API Management ゲートウェイを Docker にデプロイする | Microsoft Docs
description: セルフホステッド Azure API Management ゲートウェイを Docker にデプロイする方法について説明します
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: b5acda30f7f4a01e1b7b6ac82652452c3c40e3bf
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768505"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Azure API Management のセルフホステッド ゲートウェイを Docker にデプロイする

この記事では、セルフホステッド Azure API Management ゲートウェイを Docker 環境にデプロイする手順について説明します。

> [!NOTE]
> セルフホステッド ゲートウェイ機能はプレビュー段階です。 プレビュー期間中、セルフホステッド ゲートウェイは、Developer レベルと Premium レベルでのみ追加料金なしで利用できます。 Developer レベルは、1 つのセルフホステッド ゲートウェイのデプロイに制限されます。

## <a name="prerequisites"></a>前提条件

- 次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)
- Docker 環境を作成します。 [デスクトップ向けの Docker](https://www.docker.com/products/docker-desktop) は、開発および評価の目的に適したオプションです。 Docker のすべてのエディション、その機能、および Docker 自体に関する包括的なドキュメントについては、「[Docker Documentation (Docker ドキュメント)](https://docs.docker.com)」を参照してください。
- [API Management インスタンスにゲートウェイ リソースをプロビジョニングします](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> セルフホステッド ゲートウェイは、x86-64 の Linux ベースの Docker コンテナーとしてパッケージ化されます。

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Docker にセルフホステッド ゲートウェイをデプロイする

1. **[設定]** の下から **[ゲートウェイ]** を選択します。
2. デプロイするゲートウェイ リソースを選択します。
3. **[Deployment]/(デプロイ/)** を選択します。
4. 既定の **[有効期限]** および **[秘密鍵]** の値を使用して、 **[トークン]** テキスト ボックスの新しいトークンが自動生成されたことに注意してください。 必要に応じていずれかまたは両方を調整し、 **[生成]** を選択して新しいトークンを作成します。
4. **[デプロイ スクリプト]** 下で **[Docker]** が選択されていることを確認します。
5. **[環境]** の横にある **env.conf** ファイル リンクを選択して、ファイルをダウンロードします。
6. **[実行]** テキスト ボックスの右端にある **[コピー]** アイコンを選択して、Docker コマンドをクリップボードに保存します。
7. コマンドをターミナル (またはコマンド) ウィンドウに貼り付けます。 必要に応じて、ポート マッピングとコンテナー名を調整します。 コマンドでは、ダウンロードした環境ファイルが現在のディレクトリに存在する必要があることに注意してください。
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. コマンドを実行します。 コマンドでは、Microsoft Container Registry からダウンロードしたセルフホステッド ゲートウェイのイメージを使用してコンテナーを実行し、コンテナーの HTTP (8080) ポートと HTTPS (8081) ポートをホスト上のポート 80 と 443 にマップするように、Docker 環境に対して指示します。
9. 次のコマンドを実行して、ゲートウェイ ポッドが実行中であることを確認します。
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:beta   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Azure portal に戻り、デプロイしたばかりのゲートウェイ ノードが正常な状態を報告していることを確認します。

![ゲートウェイの状態](media/api-management-howto-deploy-self-hosted-gateway-to-docker/status.png)

> [!TIP]
> <code>console docker container logs <gateway-name></code> コマンドを使用して、セルフホステッド ゲートウェイ ログのスナップショットを表示します。
>
> <code>docker container logs --help</code> コマンドを使用して、すべてのログ表示オプションを表示します。

## <a name="next-steps"></a>次のステップ

* セルフホステッド ゲートウェイの詳細については、[Azure API Management のセルフホステッド ゲートウェイの概要](self-hosted-gateway-overview.md)に関するページを参照してください。
* [セルフホステッド ゲートウェイのカスタム ドメイン名を構成する](api-management-howto-configure-custom-domain-gateway.md)。
