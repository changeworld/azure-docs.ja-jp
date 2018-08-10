---
title: Azure Kubernetes Service での Kubernetes に対する Jenkins の継続的デプロイ
description: Jenkins で継続的デプロイ プロセスを自動化し、Azure Kubernetes Service で Kubernetes 上のコンテナー化されたアプリをデプロイおよびアップグレードする方法
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: a1a6799bc049fea829f8e32d12705e26e3a41dc0
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425760"
---
# <a name="continuous-deployment-with-jenkins-and-azure-kubernetes-service"></a>Jenkins と Azure Kubernetes Service を使った継続的デプロイ

このドキュメントでは、Jenkins と Azure Kubernetes Service (AKS) クラスターの間の基本的な継続的デプロイのワークフローを設定する方法を示します。

ワークフローの例には次の手順が含まれます。

> [!div class="checklist"]
> * Kubernetes クラスターに Azure 投票アプリケーションをデプロイします。
> * Azure 投票アプリケーションのコードを更新し、GitHub リポジトリにプッシュして、継続的デプロイのプロセスを開始します。
> * Jenkins がリポジトリを複製し、更新されたコードで新しいコンテナー イメージをビルドします。
> * このイメージが Azure Container Registry (ACR) にプッシュされます。
> * AKS クラスターで実行されているアプリケーションが、新しいコンテナー イメージで更新されます。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

- Kubernetes、Git、CI/CD、Azure Container Registry (ACR) についての基本的な理解。
- 開発システム上に構成された [Azure Kubernetes Service (AKS) クラスター][aks-quickstart]および [AKS 資格情報][aks-credentials]。
- [Azure Container Registry (ACR) レジストリ][acr-quickstart]、ACR ログイン サーバー名、およびプッシュ アクセスとプル アクセスが可能な [ACR 資格情報][acr-authentication]。
- 開発システムにインストールされた Azure CLI。
- 開発システムにインストールされた Docker。
- GitHub アカウント、[GitHub 個人用アクセス トークン][git-access-token]、および開発システムにインストールされた Git クライアント。

## <a name="prepare-application"></a>アプリケーションを準備する

このドキュメントで使う Azure 投票アプリケーションには、1 つ以上のポッドでホストされる Web インターフェイスと、一時的なデータ ストレージ用の Redis をホストしている 2 つ目のポッドが含まれます。

Jenkins と AKS の統合を構築する前に、Azure 投票アプリケーションを準備して AKS クラスターにデプロイします。 これを、アプリケーションのバージョン 1 と考えます。

次の GitHub リポジトリをフォークします。

```
https://github.com/Azure-Samples/azure-voting-app-redis
```

フォークが作成されたら、それを開発システムに複製します。 このリポジトリを複製するときは、フォークの URL を使っていることを確認してください。

```bash
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

ディレクトリを変更して、複製されたディレクトリから作業するようにします。

```bash
cd azure-voting-app-redis
```

`docker-compose.yaml` ファイルを実行して `azure-vote-front` コンテナー イメージを作成し、アプリケーションを開始します。

```bash
docker-compose up -d
```

完了したら、[docker images][docker-images] コマンドを使って、作成されたイメージを確認します。

3 つのイメージがダウンロードまたは作成されたことに注目してください。 `azure-vote-front` イメージはアプリケーションを含み、`nginx-flask` イメージをベースとして使用します。 `redis` イメージは、Redis インスタンスを起動するために使用されます。

```console
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

[az acr list][az-acr-list] コマンドを使って、ACR ログイン サーバーを取得します。 ご使用の ACR レジストリをホストしているリソース グループで、リソース グループ名を更新することを忘れないでください。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

[docker tag][docker-tag] コマンドを使って、ログイン サーバー名とバージョン番号 `v1` でイメージにタグを付けます。

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

ACR ログイン サーバーの値をご使用の ACR ログイン サーバー名で更新し、`azure-vote-front` のイメージをレジストリにプッシュします。

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-application-to-kubernetes"></a>アプリケーションを Kubernetes にデプロイする

Kubernetes マニフェスト ファイルは Azure Vote リポジトリのルートにあり、このファイルを使ってアプリケーションを Kubernetes クラスターにデプロイできます。

まず、ACR レジストリの場所で **azure-vote-all-in-one-redis.yaml** マニフェスト ファイルを更新します。 テキスト エディターでファイルを開き、`microsoft` を ACR ログイン サーバーの名前に置き換えます。 この値は、マニフェスト ファイルの **47** 行にあります。

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

次に、[kubectl apply][kubectl-apply] コマンドを使ってアプリケーションを実行します。 このコマンドは、マニフェスト ファイルを解析し、定義されている Kubernetes オブジェクトを作成します。

```bash
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

アプリケーションをインターネットに公開するための [Kubernetes サービス][kubernetes-service]が作成されます。 このプロセスには数分かかることがあります。

進行状況を監視するには、[kubectl get service][kubectl-get] コマンドと `--watch` 引数を使います。

```bash
kubectl get service azure-vote-front --watch
```

最初に、*azure-vote-front* サービスの *EXTERNAL-IP* が "*保留中*" として表示されます。

```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

*EXTERNAL-IP* アドレスが "*保留中*" から "*IP アドレス*" に変わったら、`control+c` を使用して kubectl ウォッチ プロセスを停止します。

```
azure-vote-front   10.0.34.242   13.90.150.118   80:30676/TCP   2m
```

アプリケーションを表示するには、外部 IP アドレスを参照します。

![Azure 上の Kubernetes クラスターの図](media/aks-jenkins/azure-vote-safari.png)

## <a name="deploy-jenkins-to-vm"></a>Jenkins を VM にデプロイする

仮想マシンのデプロイ、ネットワーク アクセスの構成、Jenkins の基本的なインストールを行うためのスクリプトが、事前に作成されています。 さらに、このスクリプトは、Kubernetes 構成ファイルを開発システムから Jenkins システムにコピーします。 このファイルは、Jenkins と AKS クラスターの間の認証に使われます。

次のコマンドを実行し、スクリプトをダウンロードして実行します。 次の URL は、スクリプトの内容の確認にも使うことができます。

> [!WARNING]
> このサンプル スクリプトの目的は、Azure VM で実行される Jenkins 環境の迅速なプロビジョニングを示すことです。 それは、Azure カスタム スクリプト拡張機能を使用して VM を構成した後、必要な資格情報を表示します。 *~/.kube/config* が Jenkins VM にコピーされます。

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

スクリプトは完了すると、Jenkins サーバーのアドレスと、Jenkins のロックを解除するためのキーを出力します。 URL を参照し、キーを入力した後、画面の指示に従って Jenkins の構成を完了します。

```console
Open a browser to http://52.166.118.64:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

## <a name="jenkins-environment-variables"></a>Jenkins の環境変数

Jenkins の環境変数は、Azure Container Registry (ACR) のログイン サーバー名を保持するために使われます。 この変数は、Jenkins の継続的デプロイ ジョブの間に参照されます。

Jenkins 管理ポータルで、**[Manage Jenkins]\(Jenkins の管理\)** > **[Configure System]\(システムの構成\)** の順にクリックします。

**[Global Properties]\(グローバル プロパティ\)** で **[Environment variables]\(環境変数\)** を選び、`ACR_LOGINSERVER` という名前の変数を追加して、ACR ログイン サーバーを値に設定します。

![Jenkins の環境変数](media/aks-jenkins/env-variables.png)

完了したら、Jenkins 構成ページの **[Save]\(保存\)** をクリックします。

## <a name="jenkins-credentials"></a>Jenkins 資格情報

次に、ACR の資格情報を Jenkins 資格情報オブジェクトに格納します。 これらの資格情報は、Jenkins ビルド ジョブの間に参照されます。

Jenkins 管理ポータルに戻り、**[Credentials]\(資格情報\)** > **[Jenkins]** > **[Global credentials (unrestricted)]\(グローバル資格情報 (無制限)\)** > **[Add Credentials]\(資格情報の追加\)** の順にクリックします。

資格情報の種類が **[Username with password]\(ユーザー名とパスワード\)** であることを確認し、次の項目を入力します。

- **[Username]\(ユーザー名\)** - ACR レジストリでの認証に使われるサービス プリンシパルの ID です。
- **[Password]\(パスワード\)** - ACR レジストリでの認証に使われるサービス プリンシパルのクライアント シークレットです。
- **[ID]** - `acr-credentials` などの資格情報の識別子です。

完了すると、資格情報フォームは次の画像のようになります。

![ACR 資格情報](media/aks-jenkins/acr-credentials.png)

**[OK]** をクリックして、Jenkins 管理ポータルに戻ります。

## <a name="create-jenkins-project"></a>Jenkins プロジェクトを作成する

Jenkins 管理者ポータルで、**[New Item]\(新しい項目\)** をクリックします。

プロジェクトの名前を指定し (例: `azure-vote`)、**[Freestyle Project]\(フリースタイル プロジェクト\)** を選んで、**[OK]** をクリックします。

![Jenkins プロジェクト](media/aks-jenkins/jenkins-project.png)

**[General]\(全般\)** で、**[GitHub project]\(GitHub プロジェクト\)** を選び、Azure Vote GitHub プロジェクトのフォークへの URL を入力します。

![GitHub プロジェクト](media/aks-jenkins/github-project.png)

**[Source Code Management]\(ソース コード管理\)** で、**[Git]** を選び、Azure Vote GitHub リポジトリのフォークへの URL を入力します。

資格情報で、**[Add]\(追加\)** > **[Jenkins]** をクリックします。 **[Kind]\(種類\)** で **[Secret text]\(シークレット テキスト\)** を選び、シークレットとして [GitHub 個人用アクセス トークン][git-access-token]を入力します。

完了したら **[Add]\(追加\)** を選びます。

![GitHub の資格情報](media/aks-jenkins/github-creds.png)

**[Build Triggers]\(ビルド トリガー\)** で、**[GitHub hook trigger for GITScm polling]\(GITScm ポーリングの GitHub フック トリガー\)** を選びます。

![Jenkins のビルド トリガー](media/aks-jenkins/build-triggers.png)

**[Build Environment]\(ビルド環境\)** で、**[Use secret texts or files]\(シークレット テキストまたはファイルを使用する\)** を選びます。

![Jenkins のビルド環境](media/aks-jenkins/build-environment.png)

**[Bindings]\(バインド\)** で、**[Add]\(追加\)** > **[Username and password (separated)]\(ユーザー名とパスワード (別)\)** を選びます。

**[Username Variable]\(ユーザー名変数\)** に「`ACR_ID`」と入力し、**[Password Variable]\(パスワード変数\)** に「`ACR_PASSWORD`」と入力します。

![Jenkins のバインド](media/aks-jenkins/bindings.png)

**実行シェル**タイプの**ビルド ステップ**を追加し、次のテキストを使います。 このスクリプトは、新しいコンテナー イメージをビルドし、ACR レジストリにプッシュします。

```bash
# Build new image and push to ACR.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
docker build -t $WEB_IMAGE_NAME ./azure-vote
docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
docker push $WEB_IMAGE_NAME
```

**実行シェル**タイプの**ビルド ステップ**をもう 1 つ追加し、次のテキストを使います。 このスクリプトは、Kubernetes のデプロイを更新します。

```bash
# Update kubernetes deployment with new image.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
```

終了したら、**[Save]\(保存\)** をクリックします。

## <a name="test-the-jenkins-build"></a>Jenkins のビルドをテストする

次に進む前に、Jenkins のビルドをテストします。 これは、ビルド ジョブが正しく構成されていること、適切な Kubernetes 認証ファイルがあること、適切な ACR 資格情報が提供されていることを検証します。

プロジェクトの左側にあるメニューの **[Build Now]\(今すぐビルド\)** をクリックします。

![Jenkins のテスト ビルド](media/aks-jenkins/test-build.png)

このプロセスの間に、GitHub リポジトリが Jenkins ビルド サーバーに複製されます。 新しいコンテナー イメージがビルドされて、ACR レジストリにプッシュされます。 最後に、AKS クラスターで実行されている Azure 投票アプリケーションが新しいイメージを使うように更新されます。 アプリケーション コードは変更されていないため、アプリケーションは変更されません。

プロセスが完了したら、ビルド履歴の**ビルド #1** をクリックし、**[Console Output]\(コンソール出力\)** を選択して、ビルド プロセスのすべての出力を見ることができます。 最後の行でビルドの成功が示されている必要があります。

## <a name="create-github-webhook"></a>GitHub webhook を作成する

次に、すべてのコミットで新しいビルドがトリガーされるように、Jenkins ビルド サーバーにアプリケーション リポジトリをフックします。

1. フォークされた GitHub リポジトリを参照します。
2. **[Settings]\(設定\)** を選択して、左側の **[Integrations & services]\(統合とサービス\)** を選択します。
3. **[Add webhook]\(Webhook の追加\)** を選択します。 *[Payload URL]\(ペイロード URL\)* に、「`http://<publicIp:8080>/github-webhook/`」と入力します。`publicIp` は、Jenkins サーバーの IP アドレスです。 末尾のスラッシュ (/) を含めていることを確認します。 他の規定値 ([Content type]\(コンテンツの種類\) と *push* イベントでトリガー) は既定値のままにします。
4. **[Add webhook]\(Webhook の追加\)** を選択します。

    ![GitHub webhook](media/aks-jenkins/webhook.png)

## <a name="test-cicd-process-end-to-end"></a>CI/CD プロセスをエンド ツー エンドでテストする

開発マシンで、複製されたアプリケーションをコード エディターで開きます。

**/azure-vote/azure-vote** ディレクトリで、**config_file.cfg** という名前のファイルを見つけます。 このファイルの投票の値を、cats と dogs 以外の値に更新します。

更新された **config_file.cfg** ファイルの例を次に示します。

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

完了したら、ファイルを保存し、変更をコミットして、GitHub リポジトリのフォークにこれらをプッシュします。 コミットが完了すると、GitHub の webhook が新しい Jenkins ビルドをトリガーし、コンテナー イメージと AKS のデプロイが更新されます。 Jenkins 管理コンソールでビルド プロセスを監視します。

ビルドが完了したら、もう一度アプリケーション エンドポイントを参照し、変更を観察します。

![更新された Azure Vote](media/aks-jenkins/azure-vote-updated-safari.png)

この時点で、簡単な継続的デプロイ プロセスが完了します。 この例で示した手順と構成を使って、より堅牢な、運用環境対応の継続的ビルドの自動化を構築できます。

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az-acr-list
[acr-authentication]: ../container-registry/container-registry-auth-aks.md
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli