---
title: Azure Container Instances を Jenkins ビルド エージェントとして使用する
description: Azure Container Instances を Jenkins ビルド エージェントとして使用する方法を説明します。
services: container-instances
author: neilpeterson
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/20/2018
ms.author: nepeters
ms.openlocfilehash: dbe418db8fb3d73739a30b60703f15b3dc47b291
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2018
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Azure Container Instances を Jenkins ビルド エージェントとして使用する

Azure Container Instances は、コンテナー化ワークロードを実行するためのバースト対応のオンデマンド分離環境を提供します。 これらの特性により、Azure Container Instances は大規模な環境で Jenkins ビルド ジョブを実行するための優れたプラットフォームを作成します。 このドキュメントでは、ビルド ターゲットとして ACI で事前に構成されている Jenkins サーバーのデプロイと使用の手順について説明します。

Azure Container Instances について詳しくは、[Azure Container Instances][about-aci] に関する記事をご覧ください。

## <a name="deploy-jenkins-server"></a>Jenkins サーバーをデプロイする

Azure Portal で **[リソースの作成]** を選択し、**Jenkins** を検索します。 **Microsoft** が発行している Jenkins を選択し、**[作成]** を選択します。

基本フォームに次の情報を入力し、完了したら **[OK]** をクリックします。

- **名前** - Jenkins デプロイの名前。
- **ユーザー名** - このユーザー名は、Jenkins 仮想マシンの管理者ユーザーとして使用されます。
- **認証の種類** - SSH 公開キーをお勧めします。 選択した場合、Jenkins 仮想マシンにログインするときに使用する SSH 公開キーをコピーします。
- **サブスクリプション** - Azure サブスクリプションを選択します。
- **リソース グループ** - 新しいリソース グループを作成するか、既存のリソース グループを選択します。
- **場所** - Jenkins サーバーの場所を選択します。

![Jenkins ポータル デプロイの基本設定](./media/container-instances-jenkins/jenkins-portal-01.png)

追加の設定フォームに、次の項目を入力します。

- **サイズ**: Jenkins 仮想マシンの適切なサイズ オプションを選択します。
- **VM ディスクの種類** - Jenkins サーバーの HDD (ハード ディスク ドライブ) または SSD (ソリッドステート ドライブ) を指定します。
- **仮想ネットワーク** - (省略可能) 仮想ネットワークを選択して既定の設定を変更します。
- **サブネット** - サブネットを選択し、情報を確認して **[OK]** を選択します。
- **パブリック IP アドレス** - パブリック IP アドレスを選択すると、カスタムの名前を付け、SKU と割り当て方法を構成することができます。
- **ドメイン名ラベル** - 値を指定して Jenkins 仮想マシンの完全修飾 URL を作成します。
- **Jenkins リリースの種類** - オプションから必要なリリースの種類を選択します ([LTS]、[Weekly build]\(週次ビルド\)、または [Azure Verified]\(Azure 検証済み\))。

![Jenkins ポータル デプロイの追加設定](./media/container-instances-jenkins/jenkins-portal-02.png)

サービス プリンシパル統合では、**[Auto(MSI)]\(自動 (MSI)\)** を選択して [Azure 管理対象サービス ID][managed-service-identity] が Jenkins インスタンスの認証 ID を自動的に作成するようにします。 独自のサービス プリンシパル資格情報を提供するには [手動] を選択します。

クラウド エージェントは、Jenkins ビルド ジョブのクラウドベース プラットフォームを構成します。 このドキュメントでは、[ACI] を選択します。 ACI クラウド エージェントでは、各 Jenkins ビルド ジョブは Azure コンテナー インスタンス内で実行されます。

![Jenkins ポータル デプロイ クラウドの統合設定](./media/container-instances-jenkins/jenkins-portal-03.png)

統合設定を完了したら、**[OK]** をクリックし、検証の概要でもう一度 **[OK]** をクリックします。 使用条件の概要で **[作成]** をクリックします。 Jenkins サーバーのデプロイには数分かかります。

## <a name="configure-jenkins"></a>Jenkins を構成する

Azure Portal で、[Jenkins Resource Group]\(Jenkins リソース グループ\) に移動し、Jenkins 仮想マシンを選択して、DNS 名をメモします。

![Jenkins のログイン手順](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

Jenkins VM の DNS 名を参照し、返される SSH 文字列をコピーします。

![Jenkins のログイン手順](./media/container-instances-jenkins/jenkins-portal-04.png)

開発システム上でターミナル セッションを開き、前の手順の SSH 文字列を貼り付けます。 Jenkins サーバーをデプロイするときに指定したユーザー名で 'username' を更新します。

接続したら、次のコマンドを実行して、初期管理者パスワードを取得します。

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

SSH セッションとトンネルを実行したままにして、ブラウザーで http://localhost:8080 に移動します。 次の図に示すように、初期管理者パスワードをフィールドに貼り付けます。 完了したら、**[続行]** を選択します。

![Jenkins のロックを解除する](./media/container-instances-jenkins/jenkins-portal-05.png)

**[Install suggested plugins]\(推奨されるプラグインのインストール\)** を選択し、推奨されるすべての Jenkins プラグインをインストールします。

![Jenkins プラグインのインストール](./media/container-instances-jenkins/jenkins-portal-06.png)

新しい管理者ユーザー アカウントを作成します。 このアカウントは、Jenkins インスタンスへのログインと操作に使用します。

![Jenkins ユーザーを作成する](./media/container-instances-jenkins/jenkins-portal-07.png)

完了したら **[Save and Finish]\(保存して終了\)** を選択し、**[Start using Jenkins]\(Jenkins の使用を開始\)** を選択して構成を完了します。

Jenkins が構成され、コードのビルドとデプロイの準備が完了します。 この例では、単純な Java アプリケーションを使用して、Azure Container Instances 上での Jenkins のビルドを示します。

## <a name="create-build-job"></a>ビルド ジョブの作成

コンテナー イメージを Jenkins ビルド ターゲットとして使用する場合は、ビルドを成功させるために必要なすべてのツールが含まれたイメージを指定する必要があります。 イメージを指定するには、**[Manage Jenkins]\(Jenkins の管理\)** > **[システムの構成]** を選択し、**[クラウド]** セクションまでスクロールします。 この例では、Docker イメージの値を `microsoft/java-on-azure-jenkins-slave` に更新します。

完了したら、**[保存]** をクリックして Jenkins ダッシュボードに戻ります。

![Jenkins クラウドの構成](./media/container-instances-jenkins/jenkins-aci-image.png)

ここで、Jenkins ビルド ジョブを作成します。 **[新しい項目]** を選択し、プロジェクトに `aci-java-demo` などの名前を付け、**[Freestyle project]\(Freestyle プロジェクト\)** を選択して **[OK]** をクリックします。

![Jenkins ジョブを作成する](./media/container-instances-jenkins/jenkins-new-job.png)

**[全般]** で、**[Restrict where this project can be run]\(このプロジェクトを実行できる場所を制限する\)** が選択されていることを確認します。 [ラベル式] に「`linux`」と入力します。 この構成により、このビルド ジョブが ACI クラウド上で実行されます。

![Jenkins ジョブを作成する](./media/container-instances-jenkins/jenkins-job-01.png)

ソース コードの管理で、[`Git`] を選択し、[リポジトリ URL] に「`https://github.com/spring-projects/spring-petclinic.git`」と入力します。 この GitHub リポジトリには、サンプル アプリケーション コードが含まれています。

![Jenkins ジョブにソース コードを追加する](./media/container-instances-jenkins/jenkins-job-02.png)

[ビルド] で **[ビルド ステップの追加]** を選択し、[`Invoke top-level Maven targets`] を選択します。 ビルド ステップの目標として「`package`」と入力します。

![Jenkins ビルド ステップの追加](./media/container-instances-jenkins/jenkins-job-03.png)

完了したら、 **[保存]** を選択します。

## <a name="run-the-build-job"></a>ビルド ジョブの実行

ビルド ジョブをテストし、ビルド プラットフォームとしての Azure コンテナー インスタンスを確認するには、ビルドを手動で開始します。

**[Build Now]\(今すぐビルド\)** を選択してビルド ジョブを開始します。 ジョブの開始には数分かかり、実行されると次の図のような状態が表示されます。

![Jenkins ビルド ステップの追加](./media/container-instances-jenkins/jenkins-job-status.png)

ジョブの実行中に、Azure Portal を開き、Jenkins リソース グループを参照します。 Azure コンテナー インスタンスが作成されています。 Jenkins ジョブはこのインスタンス内で実行されています。

![ACI 内の Jenkins のビルド](./media/container-instances-jenkins/jenkins-aci.png)

Jenkins が Jenkins 実行子の数 (既定値は 2) より多くのジョブを実行すると、複数の Azure コンテナー インスタンスが作成されます。

![ACI 内の Jenkins のビルド](./media/container-instances-jenkins/jenkins-aci-multi.png)

すべてのビルド ジョブが完了すると、Azure コンテナー インスタンスが削除されます。

![ACI 内の Jenkins のビルド](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="next-steps"></a>次の手順

Azure 上の Jenkins について詳しくは、「[Azure と Jenkins][jenkins-azure]」をご覧ください。

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-service-identity]: ../active-directory/managed-service-identity/overview.md