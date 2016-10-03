<properties
   pageTitle="VirtualBox を使用した Docker ホストの構成 | Microsoft Azure"
   description="Docker マシンと VirtualBox を使用して既定の Docker インスタンスを構成する詳細な手順"
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="mlearned" />

# VirtualBox を使用した Docker ホストの構成

## Overview
この記事では、Docker マシンと VirtualBox を使用して既定の Docker インスタンスを構成する方法を説明します。[Docker for Windows のベータ版](http://beta.docker.com/)を使用している場合、この構成は必要ありません。

## 前提条件
次のツールをインストールする必要があります。

- [Docker Toolbox](https://www.docker.com/products/overview#/docker_toolbox)

## Windows PowerShell を使用した Docker クライアントの構成

Docker クライアントを構成するには、Windows PowerShell を開き、次の手順を実行します。

1. 既定の Docker ホスト インスタンスを作成します。

    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
 
1. 既定のインスタンスが構成され、実行されていることを確認します。(通常は、"default" という名前のインスタンスが実行されていることがわかります)。

    ```PowerShell
	docker-machine ls 
    ```
		
	![docker-machine ls output][0]
 
1. 現在のホストを既定として設定し、シェルを構成します。

    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```

1. アクティブな Docker コンテナーを表示します。リストは空にする必要があります。

    ```PowerShell
	docker ps
    ```

	![docker ps output][1]
 
> [AZURE.NOTE] 開発用コンピューターを再起動するたびに、ローカルの Docker ホストを再起動する必要があります。これを行うには、コマンド プロンプトで、次のコマンドを発行します。`docker-machine start default`

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png

<!---HONumber=AcomDC_0921_2016-->