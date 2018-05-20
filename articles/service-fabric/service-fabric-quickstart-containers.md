---
title: Azure Service Fabric の Windows コンテナー アプリケーションを作成する | Microsoft Docs
description: このチュートリアルでは、Azure Service Fabric で初めての Windows コンテナー アプリケーションを作成します。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/30/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: d78dbc9a32e804e37eb76047edcc050482df5761
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-deploy-a-service-fabric-windows-container-application-on-azure"></a>クイック スタート: Service Fabric の Windows コンテナー アプリケーションを Azure にデプロイする
Azure Service Fabric は、スケーラブルで信頼性に優れたマイクロサービスとコンテナーのデプロイと管理を行うための分散システム プラットフォームです。 

既存のアプリケーションを Service Fabric クラスター上の Windows コンテナー内で実行する場合は、アプリケーションに変更を加える必要はありません。 このクイックスタートでは、あらかじめ用意されている Docker コンテナー イメージを Service Fabric アプリケーションにデプロイする方法を紹介します。 最後まで読み進めていけば、Windows Server 2016 Nano Server と IIS コンテナーを稼働状態にすることができます。 このクイックスタートでは、Windows コンテナーのデプロイについて説明しています。Linux コンテナーをデプロイする場合は、[こちらのクイックスタート](service-fabric-quickstart-containers-linux.md)を参照してください。

![IIS の既定の Web ページ][iis-default]

このクイック スタートでは、次の方法について説明します。

* Docker イメージ コンテナーをパッケージ化する
* 通信を構成する
* Service Fabric アプリケーションのビルドとパッケージ化
* コンテナー アプリケーションを Azure にデプロイする

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション ([無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成できます)。
* 次のものを実行している開発コンピューター。
  * Visual Studio 2015 または Visual Studio 2017。
  * [Service Fabric SDK およびツール](service-fabric-get-started.md)。

## <a name="package-a-docker-image-container-with-visual-studio"></a>Visual Studio で Docker イメージ コンテナーをパッケージ化する
Service Fabric SDK およびツールには、コンテナーを Service Fabric クラスターにデプロイするときに役立つサービスのテンプレートが用意されています。

"管理者" として Visual Studio を起動します。  **[ファイル]** > **[新規作成]** > **[プロジェクト]** の順に選択します。

**[Service Fabric アプリケーション]** を選択し、"MyFirstContainer" という名前を付けて、**[OK]** をクリックします。

**[ホスト コンテナーとアプリケーション]** テンプレートから **[コンテナー]** を選択します。

**[イメージ名]** に、「microsoft/iis:nanoserver」と入力します。これが [Windows Server Nano Server と IIS の基本イメージ](https://hub.docker.com/r/microsoft/iis/)になります。 

ポート 80 で受信するサービスへの要求が、コンテナーのポート 80 にマッピングされるように、コンテナーとホストとの間のポート マッピングを構成します。  **[Container Port] (コンテナー ポート)** を "80" に設定し、**[ホスト ポート]** を "80" に設定します。  

サービスに "MyContainerService" という名前を付けて **[OK]** をクリックします。

![[New service] (新しいサービス) ダイアログ][new-service]

## <a name="create-a-cluster"></a>クラスターの作成
Azure のクラスターにアプリケーションをデプロイする場合、パーティ クラスターに参加できます。 パーティ クラスターは、Azure でホストされる無料の期間限定の Service Fabric クラスターであり、Service Fabric チームによって実行されます。このクラスターには、だれでもアプリケーションをデプロイして、プラットフォームについて学習することができます。  このクラスターでは、ノード間のセキュリティおよびクライアントとノードの間のセキュリティに単一の自己署名証明書が使用されます。 パーティ クラスターでは、コンテナーがサポートされます。 独自のクラスターをセットアップして使用する場合は、コンテナーをサポートする SKU (Windows Server 2016 Datacenter with Containers など) でクラスターを実行する必要があります。

サインインし、[Windows クラスターに参加](http://aka.ms/tryservicefabric)します。 **[PFX]** リンクをクリックして、PFX 証明書をコンピューターにダウンロードします。 **[How to connect to a secure Party cluster?]\(セキュリティで保護されたパーティ クラスターに接続する方法\)** リンクをクリックして、証明書のパスワードをコピーします。 証明書、証明書のパスワード、**[接続のエンドポイント]** の値は、次の手順で使用します。

![PFX と接続エンドポイント](./media/service-fabric-quickstart-containers/party-cluster-cert.png)

> [!Note]
> 1 時間あたりに使用可能なパーティ クラスターの数には制限があります。 パーティ クラスターへのサインアップ時にエラーが発生する場合は、少し待ってからやり直してください。または、[.NET アプリのデプロイ](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-app-to-party-cluster#deploy-the-sample-application)のチュートリアルに記載されている手順に従って、Azure サブスクリプションに Service Fabric クラスターを作成し、アプリケーションをデプロイすることもできます。 Visual Studio で作成されたクラスターは、コンテナーをサポートします。 クラスターにアプリケーションをデプロイして確認したら、このクイック スタートの「[Service Fabric のアプリケーション マニフェストとサービス マニフェストの完全な例](#complete-example-service-fabric-application-and-service-manifests)」に進むことができます。 
>

Windows コンピューターで、*CurrentUser\My* 証明書ストアに PFX をインストールします。

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString 873689604 -AsPlainText -Force)


  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
``` 

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>Visual Studio を使用してアプリケーションを Azure にデプロイする
これでアプリケーションの準備ができたので、Visual Studio から直接クラスターにデプロイできます。

ソリューション エクスプローラーで **[MyFirstContainer]** を右クリックして、**[発行]** を選択します。 [発行] ダイアログが表示されます。

パーティ クラスター ページの**接続のエンドポイント**を **[接続のエンドポイント]** フィールドにコピーします。 たとえば、「`zwin7fh14scd.westus.cloudapp.azure.com:19000`」のように入力します。 

**[発行]** をクリックします。

クラスター内の各アプリケーションには、一意の名前が必要です。  パーティ クラスターはパブリックの共有環境ですが、既存のアプリケーションと競合している可能性があります。  名前の競合が発生している場合は、Visual Studio プロジェクトの名前を変更し、もう一度デプロイします。

ブラウザーを開き、パーティ クラスター ページで指定した**接続エンドポイント**に移動します。 URL には、必要に応じて先頭にスキーム ID (`http://`) を、末尾にポート (`:80`) を追加してください。 たとえば、「http://zwin7fh14scd.westus.cloudapp.azure.com:80」のように入力します。 IIS の既定の Web ページが表示されます。![IIS の既定の Web ページ][iis-default]

## <a name="next-steps"></a>次の手順
このクイック スタートでは、次の方法について説明しました。

* Docker イメージ コンテナーをパッケージ化する
* 通信を構成する
* Service Fabric アプリケーションのビルドとパッケージ化
* コンテナー アプリケーションを Azure にデプロイする

Service Fabric で Windows コンテナーを操作する方法の詳細については、Windows コンテナー アプリのチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Windows コンテナー アプリの作成](./service-fabric-host-app-in-a-container.md)

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
[new-service]: ./media/service-fabric-quickstart-containers/NewService.png
