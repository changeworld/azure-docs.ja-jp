<properties
   pageTitle="リモート Docker ホストへの ASP.NET コンテナーのデプロイ | Microsoft Azure"
   description="Visual Studio Tools for Docker を使用して、ASP.NET 5 Web アプリを Azure Docker ホスト マシンで実行されている Docker コンテナーに発行する方法を説明します。"   
   services="visual-studio-online"
   documentationCenter=".net"
   authors="tomarcher"
   manager="douge"
   editor=""/>

<tags
   ms.service="visual-studio-online"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/26/2016"
   ms.author="tarcher"/>

# リモート Docker ホストへの ASP.NET コンテナーのデプロイ

## 概要
Docker は軽量のコンテナー エンジンで、アプリケーションとサービスをホストするために使用できる仮想マシンにいくつかの点で似ています。Visual Studio は、Ubuntu、CoreOS、および Windows での Docker をサポートしています。このチュートリアルでは、[Visual Studio 2015 Tools for Docker](http://aka.ms/DockerToolsForVS) 拡張機能を使用して、Azure 上の Docker ホストに ASP.NET 5 アプリを発行する手順について説明します。

## 1\.前提条件
このチュートリアルを完了するには、以下が必要です。

- 「[Azure で docker マシンを使用する方法](./virtual-machines/virtual-machines-linux-classic-docker-machine.md)」の説明に従って、Azure Docker ホスト VM を作成する
- [Visual Studio 2015 のインストール](https://www.visualstudio.com/ja-JP/downloads/download-visual-studio-vs.aspx)
- [Visual Studio 2015 Tools for Docker - Preview](http://aka.ms/DockerToolsForVS) のインストール

## 2\.ASP.NET 5 Web アプリの作成
次の手順では、このチュートリアルで使用する基本的な ASP.NET 5 アプリの作成について説明します。

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## 3\.Docker サポートの追加

[AZURE.INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## 4\.リモート Docker ホストのポイント

1.  Visual Studio **ソリューション エクスプローラー**で、**Properties** フォルダーを見つけて展開します。
1.  *Docker.props* ファイルを開きます。

    ![Docker.props ファイルを開く][0]

1.  **DockerMachineName** の値を、リモート Docker ホストの名前に変更します。リモート Docker ホストの名前がわからない場合は、Windows PowerShell プロンプトで ```docker-machine ls``` を実行します。目的のホストの **Name** 列の下にリストされている値を使用します。

    ![Docker マシン名を変更する][1]

1.  Visual Studio を再起動します。

## 5\.Azure Docker ホストのエンドポイントの構成
Visual Studio から Azure にアプリケーションをデプロイする前に、Docker ホスト仮想マシンにエンドポイント 80 を追加して、後でブラウザーからアプリケーションを表示できるようにします。これは、従来の Azure ポータルまたは Windows PowerShell を使用して行うことができます。

- **従来の Azure ポータルを使用して Azure Docker ホストのエンドポイントを構成する**

    1.  [従来の Azure ポータル](https://manage.windowsazure.com/)に移動します。 
    
    1.  **[仮想マシン]** をタップします。
    
    1.  Docker ホスト仮想マシンを選択します。
    
    1.  **[エンドポイント]** タブをタップします。
    
    1.  ページの下部にある **[追加]** をタップします。
    
    1.  指示に従って、ポート 80 を公開します。これは、既定でデプロイ スクリプトで使用されます。

- **Windows PowerShell を使用して Azure Docker ホストのエンドポイントを構成する**

    1. Windows PowerShell を開きます。
    1. Windows PowerShell プロンプトで次のコマンドを入力します (環境に合わせて、山かっこ内の値を変更)。  

        ```PowerShell
        C:\PS>Get-AzureVM -ServiceName "<your_cloud_service_name>" -Name "<your_vm_name>" | Add-AzureEndpoint -Name "<endpoint_name>" -Protocol "tcp" -PublicPort 80 -LocalPort 80 | Update-AzureVM
        ```

## 6\.アプリケーションのビルドと実行
リモート ホストにデプロイする場合、編集と更新デプロイで使用されるボリューム マッピング機能は動作しません。そのため、ボリューム マッピング構成を使用しないようにアプリケーションをビルドする場合には、*リリース構成*を使用する必要があります。アプリケーションを実行するには、次の手順に従います。

1.  Visual Studio ツール バーで、**[リリース]** 構成を選択します。

1.  起動ターゲットを **[Docker]** に変更します。

1.  **[Docker]** アイコンをタップし、アプリケーションをビルドして実行します。

![アプリを起動する][2]

次のような結果が表示されます。

![アプリを表示する][3]

[0]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png

<!---HONumber=AcomDC_0330_2016-->