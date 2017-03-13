---
title: "Jenkins を使用した Linux Java アプリケーションの継続的なビルドおよびデプロイ | Microsoft Docs"
description: "Jenkins を使用した Linux Java アプリケーションの継続的なビルドおよびデプロイ"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: raunakp
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: sayantancs
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: d3c64e3ed78a5379592b5e4276c83d26f04d2e1c
ms.lasthandoff: 03/08/2017


---
# <a name="build-and-deploy-your-linux-java-application-using-jenkins"></a>Jenkins を使用した Linux Java アプリケーションのビルドおよびデプロイ
Jenkins は、継続的な統合とデプロイのための一般的なツールです。 このドキュメントでは、Jenkins を使用して、Service Fabric アプリケーションをビルドおよびデプロイする方法について説明します。

## <a name="general-prerequisites"></a>一般的な前提条件
1. Git がローカルにインストールされている必要があります。 お使いの OS に応じた適切な Git のバージョンを[ここ](https://git-scm.com/downloads)からインストールできます。 Git を初めて利用する場合は、[ドキュメント](https://git-scm.com/docs)に記載されている手順に従って、Git の利用方法を確認できます。
2. Service Fabric Jenkins プラグインを手元に用意する必要があります。 Service Fabric Jenkins プラグインは、[ここ](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi)からダウンロードします。

## <a name="setting-up-jenkins-inside-a-service-fabric-cluster"></a>Service Fabric クラスター内での Jenkins のセットアップ

### <a name="prerequisites"></a>前提条件
1. Service Fabric Linux クラスターを用意します。 Azure Portal から作成された Service Fabric クラスターには、Docker が既にインストールされています。 ローカル クラスターを実行している場合は、``docker info`` コマンドを使用して、Docker がインストールされているかどうかを確認してください。インストールされていない場合は、次のコマンドを使用して、適宜インストールします。
```sh
sudo apt-get install wget
wget -qO- https://get.docker.io/ | sh
```
2. クラスター ``http://PublicIPorFQDN:19080`` に Service Fabric コンテナー アプリケーションをデプロイします。 次の手順に従うことができます。
```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git -b JenkinsDocker && cd service-fabric-java-getting-started/Services/JenkinsDocker/
azure servicefabric cluster connect http://PublicIPorFQDN:19080 # Azure CLI cluster connect command
bash Scripts/install.sh
```
  これにより、接続したクラスターに Jenkins コンテナーがインストールされます。 Service Fabric Explorer を使用して、コンテナー アプリケーションを監視できます。

### <a name="steps"></a>手順
1. ブラウザーから URL ``http://PublicIPorFQDN:8081`` に移動します。 ログインに必要な最初の管理者パスワードのパスが提供されます。 最初の管理者アカウントでログインしたら、管理者ユーザーとして Jenkins を使用し続けるか、ユーザーを作成して変更することができます。
> [!NOTE]
> クラスターの作成時にアプリケーションのエンドポイント ポートとしてポート 8081 が指定されていることを確認する必要があります。
>

2. ``docker ps -a`` を使用してコンテナーのインスタンス ID を取得します。
3. コンテナーに SSH ログインし、Jenkins ポータルに表示されたパスを貼り付けます。 たとえば、ポータルに表示されたパスが `PATH_TO_INITIAL_ADMIN_PASSWORD` の場合、次を使用できます。  
  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash # This takes you inside docker shell
  cat PATH_TO_INITIAL_ADMIN_PASSWORD
  ```

4. [こちら](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)を使用して、Jenkins を利用できるように GitHub をセットアップします。
    * GitHub から提供されている手順を使用して、SSH キーを生成し、リポジトリをホストする (予定の) GitHub アカウントにその SSH キーを追加します。
    * (ホストではなく) Jenkins docker シェルで上のリンク先に記載されているコマンドを実行します。
    * ホストから Jenkins シェルにログオンするには、次を実行する必要があります。
  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
  ```

## <a name="setting-up-jenkins-outside-a-service-fabric-cluster"></a>Service Fabric クラスター外での Jenkins のセットアップ

### <a name="prerequisites"></a>前提条件
1. Docker がインストールされている必要があります。 インストールされていない場合は、ターミナルで次のコマンドを入力して、docker をインストールできます。
```sh
sudo apt-get install wget
wget -qO- https://get.docker.io/ | sh
```
ターミナルで ``docker info`` を実行すると、docker サービスが実行されていることが出力に表示されます。

### <a name="steps"></a>手順
  1. Service Fabric Jenkins コンテナーを取得します。``docker pull IMAGE_NAME ``
  2. コンテナー イメージを実行します。``docker run -itd -p 8080:8080 IMAGE_NAME``
  3. (インストールした) Jenkins イメージが存在する docker コンテナーの ID を取得します。 ``docker ps –a`` コマンドを使用して、すべての docker コンテナーの一覧を取得できます。
  4. Jenkins アカウントの管理者パスワードを取得します。 これについては、次を実行できます。
    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    ここで、コンテナー ID が 2d24a73b5964 の場合は、2d24 だけを挿入する必要があります。
    * このパスワードは、ポータルから Jenkins ダッシュボードにログインする際に必要になります (``http://<HOST-IP>:8080``)。
    * 初めてログインすると、独自のユーザー アカウントを作成して今後使用するか、管理者アカウントを使い続けることができます。 新しいユーザーを作成した後は、そのユーザーを使い続ける必要があります。
  5. [こちら](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)を使用して、Jenkins を利用できるように GitHub をセットアップします。
      * GitHub から提供されている手順を使用して、SSH キーを生成し、リポジトリをホストする (予定の) GitHub アカウントにその SSH キーを追加します。
      * (ホストではなく) Jenkins docker シェルで上のリンク先に記載されているコマンドを実行します。
      * ホストから Jenkins シェルにログオンするには、次を実行する必要があります。
    ```sh
    docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
    ```

> [!NOTE]
> GitHub からの通知を Jenkins インスタンスが受信できるように、Jenkins コンテナー イメージがホストされているクラスターまたはコンピューターにパブリック IP アドレスが割り当てられていることを確認します。
>

## <a name="install-the-service-fabric-jenkins-plugin-from-portal"></a>ポータルからの Service Fabric Jenkins プラグインのインストール

1. ``http://PublicIPorFQDN:8081`` に移動します
2. Jenkins ダッシュボードで、[``Manage Jenkins``] -> [``Manage Plugins``] -> [``Advanced``] の順に選択します。
ここでは、プラグインをアップロードできます。 [``Choose file``] オプションを選択し、前提条件に従ってダウンロードした serviceFabric.hpi ファイルを選択します。 アップロードを選択すると、Jenkins によってプラグインが自動的にインストールされます。 再起動を求められた場合は、許可してください。

## <a name="creating-and-configuring-a-jenkins-job"></a>Jenkins ジョブの作成と構成

1. ダッシュボードから、``new item`` を作成します。
2. 新しい項目の名前 (例: ``MyJob``) を入力し、フリースタイル プロジェクトを選択して、[OK] をクリックします。
3. ジョブ ページに移動し、[``Configure``] をクリックします。 -
  * 全般セクションで、[``Github project``] に Jenkins CI/CD フローと統合する Service Fabric Java アプリケーションをホストする GitHub プロジェクトの URL を指定します (例: ``https://github.com/sayantancs/SFJenkins``)。
  * [``Source Code Management``] セクションで [``Git``] を選択します。 Jenkins CI/CD フローと統合する Service Fabric Java アプリケーションをホストするリポジトリの URL を指定します (例: ``https://github.com/sayantancs/SFJenkins.git``)。 ここで、ビルドする分岐を指定することもできます (例: ``*/master``)。
4. 次の手順を使用して、Jenkins と対話できるように (リポジトリをホストする) *Github* を構成します。
  - GitHub リポジトリ ページに移動します。 [``Settings``] -> [``Integrations and Services``] に移動します。
  - [``Add Service``] を選択し、「Jenkins」と入力して、[``Jenkins-Github plugin``] を選択します。
  - Jenkins webhook の URL を入力します (既定では、``http://<PublicIPorFQDN>:8081/github-webhook/`` です)。 サービスの追加または更新をクリックします。
  - テスト イベントが Jenkins インスタンスに送信されます。 GitHub で webhook の側に緑色のチェック マークが表示され、プロジェクトがビルドされます。
  - [``Build Triggers``] セクションで、目的のビルド オプションを選択します。ここでは、リポジトリへの何らかのプッシュが発生するたびにビルドがトリガーされるようにします。そのため、対応するオプションは、[``GitHub hook trigger for GITScm polling``] です (以前は、[Build when a change is pushed to GitHub (変更が GitHub にプッシュされたときにビルド)] でした)。
  - [``Build section``] にある [``Add build step``] ドロップダウン リストから、[``Invoke Gradle Script``] オプションを選択します。 表示されたウィジェットで、[``Root build script``] にアプリケーションのパスを指定します。 ウィジェットは、指定されたパスから build.gradle を取得し、適切に動作します。 (Eclipse プラグインまたは Yeoman ジェネレーターを使用して) ``MyActor`` という名前のプロジェクトを作成した場合は、ルート ビルド スクリプトに ``${WORKSPACE}/MyActor`` を含める必要があることに注意してください。 たとえば、このセクションはほぼ次のようになります。

    ![Service Fabric Jenkins のビルド アクション][build-step]
  - [``Post-Build Actions``] ドロップダウン リストで、[``Deploy Service Fabric Project``] を選択します。 ここでは、Jenkins でコンパイルされた Service Fabric アプリケーションをデプロイするクラスターの詳細と、アプリケーションのデプロイに使用される追加のアプリケーションの詳細を指定する必要があります。 次のスクリーンショットは、参照用として利用してください。

    ![Service Fabric Jenkins のビルド アクション][post-build-step]

 >[!Note]
 > このクラスターは、Service Fabric を使用して Jenkins コンテナー イメージをデプロイする場合に、Jenkins コンテナー アプリケーションをホストするクラスターと同じにすることもできます。
 >

### <a name="end-to-end-scenario"></a>エンド ツー エンドのシナリオ
ここまでで GitHub と Jenkins を構成したので、リポジトリ (https://github.com/sayantancs/SFJenkins など) の ``MyActor`` プロジェクトにサンプルの変更を加え、変更内容をリモートの ``master`` 分岐 (または動作するように構成したいずれかの分岐) にプッシュできます。 これにより、構成した Jenkins ジョブ ``MyJob`` がトリガーされます。 基本的な動作としては、GitHub から変更内容をフェッチし、それらをビルドして、ビルド後のアクションで指定したクラスター エンドポイントにアプリケーションをデプロイします。  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png

