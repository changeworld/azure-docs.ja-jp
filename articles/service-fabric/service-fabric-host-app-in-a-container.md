---
title: "コンテナー内の .NET アプリを Azure Service Fabric にデプロイする | Microsoft Docs"
description: "Docker コンテナー内の Visual Studio で .NET アプリをパッケージ化する方法を説明します。 次に、この新しい &quot;コンテナー&quot; アプリを、Service Fabric クラスターにデプロイします。"
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/19/2017
ms.author: mikhegn
ms.translationtype: Human Translation
ms.sourcegitcommit: d9ae8e8948d82b9695d7d144d458fe8180294084
ms.openlocfilehash: 56788914452c0f3a7072d6b2805866072b9b7fea
ms.contentlocale: ja-jp
ms.lasthandoff: 05/23/2017

---

# <a name="deploy-a-net-app-in-a-container-to-azure-service-fabric"></a>コンテナー内の .NET アプリを Azure Service Fabric にデプロイする

このチュートリアルでは、Visual Studio 2017 Update 3 のプレビューを使用して、Windows コンテナー内に既存の ASP.NET アプリケーションをデプロイする方法を説明します。 次に、Visual Studio Team Services を使用してそのコンテナーを Azure にデプロイし、Service Fabric クラスターでコンテナーをホストする方法について説明します。

## <a name="prerequisites"></a>前提条件

1. Windows 10 でコンテナーを実行できるように、[Docker CE for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) をインストールします。
2. [Windows 10 Containers のクイックスタート][link-container-quickstart]の内容について理解を深めます。
3. このチュートリアルでは、**Fabrikam Fiber CallCenter** というサンプル アプリを使用します。このアプリは[こちら][link-fabrikam-github]からダウンロードできます。
4. [Azure PowerShell][link-azure-powershell-install]
5. [Visual Studio 2017 用の継続的配信ツールの拡張機能][link-visualstudio-cd-extension]
6. [Azure サブスクリプション][link-azure-subscription]と [Visual Studio Team Services アカウント][link-vsts-account]。 このチュートリアルは、すべてのサービスの無料階層を使用して進めることができます。

>[!NOTE]
>お使いのコンピューターで Windows コンテナー イメージを初めて実行する場合は、Docker CE でコンテナーの基本イメージをプルする必要があります。 このチュートリアルで使用されるイメージは 14 GB です。 基本イメージをプルするために、Powershell で次のコマンドを実行します。
>1. docker pull microsoft/mssql-server-windows-developer
>2. docker pull microsoft/aspnet:4.6.2

## <a name="containerize-the-application"></a>アプリケーションのコンテナー格納

コンテナー内のアプリケーションの実行を開始するには、Visual Studio 内のプロジェクトに **Docker サポート**を追加する必要があります。 **Docker サポート**をアプリケーションに追加すると、2 つの処理が行われます。 まず、_docker_ ファイルがプロジェクトに追加されます。 この新しいファイルには、コンテナー イメージのビルド方法が記載されています。 次に、新しい _docker-compose_ プロジェクトがソリューションに追加されます。 新しいプロジェクトには、いくつかの docker-compose ファイルが含まれています。 docker-compose ファイルは、コンテナーの実行方法を説明するために使用できます。

[Visual Studio Container Tools][link-visualstudio-container-tools] の使用に関する詳細

### <a name="add-docker-support"></a>Docker サポートの追加

1. Visual Studio で **FabrikamFiber.CallCenter.sln** ファイルを開きます。

2. **FabrikamFiber.Web** プロジェクトを右クリック > **[追加]** > **[Docker サポート]** の順に選択します。

### <a name="add-support-for-sql"></a>SQL のサポートの追加

このアプリケーションでは、データ プロバイダーとして SQL を使用するため、アプリケーションの実行には SQL Server が必要です。 このチュートリアルでは、ローカル デバッグに、コンテナーで実行される SQL Server を使用します。
デバッグ時に、コンテナー内で SQL Server を実行するには、docker-compose.override.yml ファイル内にある SQL Server コンテナー イメージを参照できます。 

1. **ソリューション エクスプローラー**を開きます。

2. **docker-compose** > **docker-compose.yml** > **docker-compose.override.yml** の順に開きます。

3. `services:` ノードの下に、`db:` という名前の新しいノードを追加します。 このノードは、コンテナー内の SQL Server の実行を宣言します。

   ```yml
     db:
       image: microsoft/mssql-server-windows-developer
       environment:
         sa_password: "Password1"
         ACCEPT_EULA: "Y"
       ports:
         - "1433"
       healthcheck:
         test: [ "CMD", "sqlcmd", "-U", "sa", "-P", "Password1", "-Q", "select 1" ]
         interval: 1s
         retries: 20
   ```

   >[!NOTE]
   >ローカル デバッグには、ホストからアクセスできる範囲であれば好きな SQL Server を使用できます。 ただし、**localdb** は `container -> host` 通信をサポートしていません。

   >[!WARNING]
   >コンテナー内での SQL Server の実行では、コンテナーが停止したときに、永続的データをサポートしません。 運用環境には、この構成を使用しないでください。

4. `fabrikamfiber.web` ノードを変更し、`depends_on:` という名前の新しい子ノードを追加します。 これにより、確実に Web アプリケーション (fabrikamfiber.web) の前に `db` サービス (SQL Server コンテナー) を開始します。

   ```yml
     fabrikamfiber.web:
       depends_on:
         - db
   ```

5. **FabrikamFiber.Web** プロジェクトで **web.config** ファイル内の接続文字列を更新し、コンテナー内の SQL Server を指定します。

   ```xml
   <add name="FabrikamFiber-Express" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
   <add name="FabrikamFiber-DataWarehouse" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
   ```

   >[!NOTE]
   >Web アプリケーションのリリース ビルドを作成するときに異なる SQL Server を使用する場合は、web.release.config ファイルに別の接続文字列を追加します。

6. **F5** キーを押して、コンテナー内のアプリケーションを実行しデバッグします。

   NAT の内部ネットワークにあるコンテナーの IP アドレス (通常は 172.x.x.x) を使用して、アプリケーションの定義済み開始ページが Edge で開きます。 Visual Studio 2017 を使用してコンテナー内のアプリケーションをデバッグする方法の詳細については、[この記事][link-debug-container]をご覧ください。

   ![コンテナー内の Fabrikam の例][image-web-preview]

アプリケーションをコンテナー内にビルドしてパッケージ化する準備ができました。 コンピューター上にコンテナー イメージをビルドすると、コンテナー レジストリにプッシュしたり、ホストにプルして実行したりできるようになります。

このチュートリアルの残りの記事では、Visual Studio Team Services を使用してコンテナーを Azure で実行する Service Fabric にデプロイします。

## <a name="create-a-service-fabric-cluster"></a>Service Fabric クラスターの作成

アプリケーションのデプロイ先となる Service Fabric クラスターが既にある場合は、この手順を省略できます。 それ以外の場合は、手順に従い Service Fabric クラスターを作成してください。

>[!NOTE]
>次の手順では、KeyVault 内の自己署名証明書で保護される単一ノード (単一の仮想マシン) Preview Service Fabric クラスターを作成します。
>単一ノード クラスターは 1 台の仮想マシンを超えて拡張することはできず、Preview クラスターを新しいバージョンにアップグレードすることはできません。
>Azure で Service Fabric クラスターを実行することによって発生するコストを計算するには、[Azure 料金計算ツール][link-azure-pricing-calculator]を使用します。
>Service Fabric クラスターの作成の詳細については、「[Azure Resource Manager を使用して Service Fabric クラスターを作成する][link-servicefabric-create-secure-clusters]」をご覧ください。

1. この GitHub リポジトリから Azure Resource Manager テンプレートとパラメーター ファイルのローカル コピーをダウンロードします。
    * [Service Fabric 用 Azure Resource Manager テンプレート][link-sf-clustertemplate]
       - **azuredeploy.json** -Service Fabric クラスターを定義する Azure Resource Manager テンプレートです。
       - **azuredeploy.parameters.json** - クラスター デプロイをカスタマイズするためのパラメーター ファイルです。
2. パラメーター ファイルで次のパラメーターをカスタマイズします。
  
   | パラメーター       | Description | 推奨値 |
   | --------------- | ----------- | --------------- |
   | clusterLocation | クラスターのデプロイ先の Azure リージョンです。 | *westeurope、eastasia、eastus など* |
   | clusterName     | クラスターの名前です。 | *bobs-sfpreviewcluster など* |
   | adminUserName   | クラスター仮想マシンのローカル管理者アカウントです。 | *任意の有効な Windows Server ユーザー名* |
   | adminPassword   | クラスター仮想マシンのローカル管理者アカウントのパスワードです。 | *任意の有効な Windows Server パスワード* |
   | clusterCodeVersion | 実行する Service Fabric バージョン (255.255.X.255 はプレビュー バージョンです)。 | **255.255.5713.255** |
   | vmInstanceCount | クラスター内の仮想マシンの数 (1 または 3 ～ 99 で指定できます)。 | **1** |

3. **PowerShell** を開きます。
4. Azure に**ログイン**します。

   ```powershell
   Login-AzureRmAccount
   ```

5. クラスターをデプロイする**サブスクリプション**を選択します。

   ```powershell
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```

6. Service Fabric によって使用される証明書のパスワードを作成し、**暗号化**します。

   ```powershell
   $pwd = "<your password>" | ConvertTo-SecureString -AsPlainText -Force
   ```

7. 次のコマンドを実行して、**クラスターを作成**します。

   ```powershell
      New-AzureRmServiceFabricCluster
          -TemplateFile C:\Users\me\Desktop\azuredeploy.json `
          -ParameterFile C:\Users\me\Desktop\azuredeploy.parameters.json `
          -CertificateOutputFolder C:\Users\me\Desktop\ `
          -CertificatePassword $pwd `
          -CertificateSubjectName "mycluster.westeurope.cloudapp.azure.com" `
          -ResourceGroupName myclusterRG
   ```

   >[!NOTE]
   >`-CertificateSubjectName` パラメーターは、パラメーター ファイルで指定される clusterName パラメーターおよび、選択した Azure リージョンに紐付けられているドメインと一致する必要があります (例: `clustername.eastus.cloudapp.azure.com`)。
   
    構成が完了すると、Azure で作成したクラスターの情報が出力され、CertificateOutputFolder ディレクトリに証明書がコピーされます。

8. 証明書を**ダブルクリック**して証明書インポート ウィザードを開きます。

9. 既定の設定を使用しますが、**[このキーをエクスポート可能にする]** チェック ボックス (**[秘密キーの保護]** 手順で) を必ずオンにしてください。 Visual Studio では、このチュートリアルの後半で、Azure Container Registry の構成時に、Service Fabric クラスター認証のために証明書をエクスポートする必要があります。

10. ブラウザーで Service Fabric Explorer を開けるようになりました。 URL は、PowerShell コマンドレットの出力の **ManagementEndpoint** で、たとえば、*https://mycluster.westeurope.cloudapp.azure.com:19080* などです。 

>[!NOTE]
>自己署名証明書を使用しているため、Service Fabric Explorer を開くときに、証明書エラーが表示されます。 Edge では、*[詳細]* をクリックし、*[Web ページへ移動]* リンクをクリックする必要があります。 Chrome では、*[詳細設定]* をクリックし、*[続行]* リンクをクリックする必要があります。

>[!NOTE]
>クラスターの作成に失敗した場合、既にデプロイされているリソースを更新するコマンドをいつでも再実行できます。 証明書が失敗したデプロイの一部として作成された場合、新しい証明書が生成されます。 クラスターの作成のトラブルシューティングを行うには、「[Azure Resource Manager を使用して Service Fabric クラスターを作成する][ link-servicefabric-create-secure-clusters]」の記事をご覧ください。

## <a name="getting-the-application-ready-for-the-cloud"></a>クラウド用のアプリケーションの準備

Azure の Service Fabric で実行するためにアプリケーションを準備するには、2 つの手順を実行する必要があります。

1. Service Fabric クラスター内の Web アプリケーションにアクセスできるようにするポートを公開します。
2. このアプリケーションに運用対応 SQL データベースを提供します。

### <a name="1-exposing-the-web-application-in-service-fabric"></a>1.Service Fabric での Web アプリケーションの公開
構成した Service Fabric クラスターでは、クラスターへの着信トラフィックを分散する Azure Load Balancer で既定でポート 80 がオープンされます。 docker-compose.yml ファイルを使用して、このポートでコンテナーを公開できます。

1. **ソリューション エクスプローラー**を開きます。

2. **docker-compose** > **docker-compose.yml** を開きます。

3. `fabrikamfiber.web` ノードを変更し、`ports:` という名前の新しい子ノードと文字列 `- "80:80"` を追加します。 完全な docker-compose.yml ファイルは、次のようになります。

   ```yml
      version: '3'

      services:
        fabrikamfiber.web:
          image: fabrikamfiber.web
          build:
            context: .\FabrikamFiber.Web
            dockerfile: Dockerfile
          ports:
            - "80:80"
   ```

### <a name="2-provide-a-production-ready-sql-database-for-our-application"></a>2.このアプリケーションに運用対応 SQL データベースを提供します。
このアプリケーションをコンテナー化し、ローカル デバッグを有効にしている場合、コンテナーで、SQL Server を実行するようにセットアップします。 このアプローチは、データベース内のデータを永続化する必要がないため、アプリケーションをローカルにデバッグする場合に適切なソリューションです。 ただし、運用環境で実行する場合、データベースでデータを永続化する必要があります。 現在コンテナー内のデータの永続化を保証する方法はありません。そのため、コンテナー内に SQL Server の運用データを格納することはできません。

結果として、サービスで、永続的な SQL Database を必要とする場合は、Azure SQL Database を使用することをお勧めします。 Azure で管理対象の SQL Server をセットアップし、実行するには、[Azure SQL データベース クイック スタート][[link-azure-sql]] の記事を参照してください。

>[!NOTE]
>FabrikamFiber.Web プロジェクトで web.release.config ファイル内の接続文字列を SQL Server に変更することを忘れないでください。
>SQL データベースにアクセスできない場合、このアプリケーションは正常に終了します。 SQL Server を使用しないで、続行し、アプリケーションをデプロイすることもできます。

## <a name="deploy-with-visual-studio"></a>Visual Studio でのデプロイ

Visual Studio Team Services を使用してデプロイをセットアップするには、[Visual Studio 2017 用の継続的配信ツールの拡張機能][link-visualstudio-cd-extension]をインストールする必要があります。 この拡張機能を使用して Visual Studio Team Services を構成することで、Azure へのデプロイと Service Fabric クラスターへのアプリケーションのデプロイが簡単になります。

開始するにはまず、コードがソース管理でホストされている必要があります。 このセクションの残りの記事では、**Git** を使用しています。

1. Visual Studio の右下隅にある **[ソース管理に追加]** > **[Git]** の順にクリックします (好きなオプションから選択してもかまいません)。

   ![ソース管理ボタンをクリックする][image-source-control]

2. _[Team Explorer]_ ウィンドウで、**[Git リポジトリを発行]** をクリックします。

3. VSTS リポジトリ名を選択して **[リポジトリ]** をクリックします。

   ![リポジトリを VSTS に発行する][image-publish-repo]

これでコードが VSTS のソース リポジトリと同期されたため、継続的インテグレーションと継続的配信を構成できるようになります。

1. _ソリューション エクスプ ローラー_で、**ソリューション**を右クリック > **[継続的配信を構成する]** をクリックします。

2. [Azure サブスクリプション] を選択します。

3. **[ホストの種類]** を **[Service Fabric クラスター]** に設定します。

   >[!NOTE]
   >ビルドするコンテナーの種類によっては、Azure のコンテナーでアプリケーションをホストするためのオプションをさらに追加します。 

4. 前のセクションで作成した Service Fabric クラスターに**ターゲット ホスト**を設定します。

5. コンテナーを発行する **[コンテナー レジストリ]** を選択します。

   >[!TIP]
   >**[編集]** でコンテナー レジストリを作成します。
    
6. **[OK]** をクリックします。

   ![Service Fabric の継続的インテグレーションをセットアップする][image-setup-ci]
   
   設定が完了すると、コンテナーが Service Fabric にデプロイされます。 リポジトリに更新をプッシュするたびに、新しいビルドとリリースが実行されます。
   
   >[!NOTE]
   >コンテナー イメージの構築には、約 15 分かかります。
   >Service Fabric クラスターへの最初のデプロイによって、基本の Windows Server Core コンテナー イメージがダウンロードされます。 ダウンロードが完了するまでに、さらに 5 ～ 10 分かかります。

7. クラスターの URL (*http://mycluster.westeurope.cloudapp.azure.com* など) を使用して Fabrikam Call Center アプリケーションを参照します。

Fabrikam Call Center ソリューションをコンテナー化してデプロイしたため、[Azure Portal][link-azure-portal] を開いて Service Fabric でアプリケーションが実行されていることを確認できます。 アプリケーションをテストするには、Web ブラウザーを開いて Service Fabric クラスターの URL にアクセスします。

## <a name="next-steps"></a>次のステップ

- [Visual Studio のコンテナー ツール][link-visualstudio-container-tools]
- [Service Fabric コンテナーの概要][link-servicefabric-containers]
- [Service Fabric アプリケーションの作成][link-servicefabric-createapp]

[link-debug-container]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-container-quickstart]: /virtualization/windowscontainers/quick-start/quick-start-windows-10
[link-visualstudio-container-tools]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/en-us/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/en-us/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /sql-database

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png
