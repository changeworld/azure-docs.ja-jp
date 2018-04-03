---
title: VirtualBox を使用した Docker ホストの構成 | Microsoft Docs
description: Docker マシンと VirtualBox を使用して既定の Docker インスタンスを構成する詳細な手順
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: ''
ms.assetid: 0b1335a2-7720-42a8-8260-4e06fc00c9f6
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 11e238fa901a164df1dfd896e38df828601e650b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="configure-a-docker-host-with-virtualbox"></a>VirtualBox を使用した Docker ホストの構成
## <a name="overview"></a>概要
この記事では、Docker マシンと VirtualBox を使用して既定の Docker インスタンスを構成する方法を説明します。 [Docker for Windows のベータ版](http://beta.docker.com/)を使用している場合、この構成は必要ありません。

## <a name="prerequisites"></a>前提条件
次のツールをインストールする必要があります。

* [Docker Toolbox](https://github.com/docker/toolbox/releases)

## <a name="configuring-the-docker-client-with-windows-powershell"></a>Windows PowerShell を使用した Docker クライアントの構成
Docker クライアントを構成するには、Windows PowerShell を開き、次の手順を実行します。

1. 既定の Docker ホスト インスタンスを作成します。
   
    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
2. 既定のインスタンスが構成され、実行されていることを確認します。 (通常は、"default" という名前のインスタンスが実行されていることがわかります)。
   
    ```PowerShell
    docker-machine ls 
    ```
   
    ![docker-machine ls output][0]
3. 現在のホストを既定として設定し、シェルを構成します。
   
    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```
4. アクティブな Docker コンテナーを表示します。 リストは空にする必要があります。
   
    ```PowerShell
    docker ps
    ```
   
    ![docker ps output][1]

> [!NOTE]
> 開発用コンピューターを再起動するたびに、ローカルの Docker ホストを再起動する必要があります。
> これを行うには、コマンド プロンプトで、次のコマンドを発行します。`docker-machine start default`
> 
> 

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png
