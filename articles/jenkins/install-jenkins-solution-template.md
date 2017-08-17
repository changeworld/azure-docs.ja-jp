---
title: "Azure で Linux (Ubuntu) VM に初めての Jenkins マスターを作成する"
description: "ソリューション テンプレートを使って Jenkins をデプロイします。"
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: multiple
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: Jenkins
ms.translationtype: HT
ms.sourcegitcommit: 80fd9ee9b9de5c7547b9f840ac78a60d52153a5a
ms.openlocfilehash: 06d6d305eb9711768dc62a04726359e6280d1b69
ms.contentlocale: ja-jp
ms.lasthandoff: 08/14/2017

---

# <a name="create-your-first-jenkins-master-on-a-linux-ubuntu-vm-on-azure"></a>Azure で Linux (Ubuntu) VM に初めての Jenkins マスターを作成する

このクイックスタートでは、最新の安定したバージョンの Jenkins と、Azure で動作するように構成されたツールおよびプラグインを、Linux (Ubuntu 14.04 LTS) VM にインストールする方法について説明します。 ツールには次のものが含まれます。
<ul>
<li>ソース管理用の Git</li>
<li>安全に接続するための Azure 資格情報プラグイン</li>
<li>エラスティック ビルド、テスト、継続的インテグレーションのための Azure VM エージェント プラグイン</li>
<li>成果物を格納するための Azure Storage プラグイン</li>
<li>スクリプトを使ってアプリをデプロイするための Azure CLI</li>
</ul>

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 無料の Azure アカウントを作成します。
> * ソリューション テンプレートを使って Azure VM に Jenkins マスターを作成します。 
> * Jenkins 用の初期構成を行います。
> * 推奨されるプラグインをインストールします。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-the-vm-in-azure-by-deploying-the-solution-template-for-jenkins"></a>Jenkins 用のソリューション テンプレートをデプロイして Azure に VM を作成する

Azure クイックスタート テンプレートを使うと、Azure に複雑なテクノロジを迅速かつ確実にデプロイできます。  Azure Resource Manager では、[宣言型のテンプレート](https://azure.microsoft.com/en-us/resources/templates/?term=jenkins)を使ってアプリケーションをプロビジョニングすることができます。 1 つのテンプレートで、複数のサービスをその依存関係と共にデプロイできます。 アプリケーション ライフサイクルの各ステージで、同じテンプレートを使用してアプリケーションを繰り返しデプロイします。

コストのオプションについては、このテンプレートの[プランと価格](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-oss.jenkins?tab=Overview)に関する情報をご覧ください。

[Jenkins の Marketplace イメージ](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-oss.jenkins?tab=Overview)に移動し、**[今すぐ入手する]** をクリックします。  

Azure Portal で、**[作成]** をクリックします。  このテンプレートでは、テンプレート モデルのドロップダウンが無効になっているため、Resource Manager を使う必要があります。
   
![Azure Portal ダイアログ](./media/install-jenkins-solution-template/ap-create.png)

**[基本設定の構成]** タブで次のようにします。

![基本設定を構成する](./media/install-jenkins-solution-template/ap-basic.png)

* Jenkins インスタンスの名前を指定します。
* VM ディスクの種類を選択します。  運用環境のワークロードでは、パフォーマンス向上のために大容量の VM と SSD を選択します。  Azure ディスクの種類について詳しくは、[こちら](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage)をご覧ください。
* ユーザー名: 長さの要件を満たし、予約語またはサポートされない文字が含まれないようにする必要があります。 "admin" のような名前を指定することはできません。  ユーザー名とパスワードの要件について詳しくは、[こちら](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/faq)をご覧ください。
* 認証の種類: パスワードまたは [SSH 公開キー](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows)で保護されているインスタンスを作成します。 パスワードを使う場合は、1 つの小文字、1 つの大文字、1 つの数字、1 つの特殊文字という要件のうち、3 つを満たしている必要があります。
* Jenkins のリリースの種類は、**LTS** のままにしておきます
* サブスクリプションを選択します。
* リソース グループを作成するか、空の既存のグループを使用します。 
* 場所を選択します。

**[Configure additional options\(追加オプションの構成\)]** タブで次のようにします。

![追加オプションの設定](./media/install-jenkins-solution-template/ap-addtional.png)

* Jenkins マスターを一意に識別するドメイン名ラベルを指定します。

**[OK]** をクリックして次のステップに進みます。 

検証に合格したら、**[OK]** をクリックしてテンプレートとパラメーターをダウンロードします。 

次に、**[購入]** を選択してすべてのリソースをプロビジョニングします。

## <a name="setup-ssh-port-forwarding"></a>SSH ポート フォワーディングを設定する

既定では、Jenkins インスタンスは http プロトコルを使い、ポート 8080 でリッスンします。 ユーザーは、セキュリティ保護されていないプロトコルで認証を行わないようにする必要があります。
    
ポート フォワーディングを設定して、ローカル コンピューターに Jenkins UI を表示します。

### <a name="if-you-are-using-windows"></a>Windows を使っている場合:

パスワードを使って Jenkins を保護している場合は、PuTTY をインストールして次のコマンドを実行します。
```
putty.exe -ssh -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```
* ログインするには、パスワードを入力します。

![ログインするには、パスワードを入力します。](./media/install-jenkins-solution-template/jenkins-pwd.png)

SSH を使っている場合は、次のコマンドを実行します。
```
putty -i <private key file including path> -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```

### <a name="if-you-are-using-linux-or-mac"></a>Linux または Mac を使っている場合:

パスワードを使って Jenkins マスターを保護している場合は、次のコマンドを実行します。
```
ssh -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```
* ログインするには、パスワードを入力します。

SSH を使っている場合は、次のコマンドを実行します。
```
ssh -i <private key file including path> -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```

## <a name="connect-to-jenkins"></a>Jenkins に接続する
トンネルを開始した後、ローカル コンピューターで http://localhost:8080/ に移動します。

初めて使うときは、初期管理パスワードを使って Jenkins ダッシュボードのロックを解除します。

![Jenkins のロックを解除する](./media/install-jenkins-solution-template/jenkins-unlock.png)

トークンを取得するには、VM に SSH で接続し、`sudo cat /var/lib/jenkins/secrets/initialAdminPassword` を実行します。

![Jenkins のロックを解除する](./media/install-jenkins-solution-template/jenkins-ssh.png)

推奨されるプラグインのインストールを求められます。

次に、Jenkins マスターの管理者ユーザーを作成します。

Jenkins インスタンスを使用する準備ができました。 http://\<作成したインスタンスのパブリック DNS 名\> に移動することで、読み取り専用のビューにアクセスできます。

![Jenkins の準備ができました。](./media/install-jenkins-solution-template/jenkins-welcome.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * ソリューション テンプレートを使って Jenkins マスターを作成しました。
> * Jenkins の初期構成を実行しました。
> * プラグインをインストールしました。

Jenkins との継続的インテグレーションのために Azure VM エージェントを使う方法については、次のリンクをご覧ください。

> [!div class="nextstepaction"]
> [Jenkins エージェントとしての Azure VM](jenkins-azure-vm-agents.md)

