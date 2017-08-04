---
title: "Jenkins との継続的インテグレーションのために Azure VM エージェントを使用する"
description: "Jenkins スレーブとしての Azure VM エージェント。"
services: multiple
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: Jenkins
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: 5f2df414b4d0e8798b7ed6d90d0ea0fb79d42fc2
ms.contentlocale: ja-jp
ms.lasthandoff: 06/21/2017

---
# <a name="use-azure-vm-agents-for-continuous-integration-with-jenkins"></a>Jenkins との継続的インテグレーションのために Azure VM エージェントを使用する

このクイックスタートでは、Jenkins Azure VM エージェント プラグインを使って、Azure にオンデマンド Linux (Ubuntu) エージェントを作成する方法を示します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。

* Jenkins マスターがまだない場合は、[ソリューション テンプレート](install-jenkins-solution-template.md)で開始することができます 
* Azure サービス プリンシパルがまだない場合は、「[Azure CLI 2.0 で Azure サービス プリンシパルを作成する](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)」をご覧ください

## <a name="install-azure-vm-agents-plugin"></a>Azure VM エージェント プラグインをインストールする

[ソリューション テンプレート](install-jenkins-solution-template.md)から始める場合は、Jenkins マスターに Azure VM エージェント プラグインがインストールされます。

それ以外の場合は、Jenkins ダッシュボード内から **Azure VM エージェント** プラグインをインストールします。

## <a name="configure-the-plugin"></a>プラグインを構成する

* Jenkins ダッシュボードで、**[Manage Jenkins\(Jenkins の管理\)] -> [Configure System\(システムの構成\)]** の順にクリックします。 ページの下部までスクロールし、**[Add new cloud\(新しいクラウドの追加\)]** というドロップダウンがあるセクションを探します。 メニューから、**[Microsoft Azure VM Agents\(Microsoft Azure VM エージェント\)]** を選択します。
* [Azure Credentials\(Azure 資格情報\)] ドロップダウンから既存のアカウントを選びます。  新しい **Microsoft Azure サービス プリンシパル**を追加するには、サブスクリプション ID、クライアント ID、クライアント シークレット、OAuth 2.0 トークン エンドポイントの各値を入力します。

![Azure 資格情報](./media/jenkins-azure-vm-agents/service-principal.png)

* **[Verify configuration\(構成の確認\)]** をクリックして、プロファイルの構成が正しいことを確認します。
* 構成を保存し、次の手順に進みます。

## <a name="template-configuration"></a>テンプレートの構成

### <a name="general-configuration"></a>全般的な構成
次に、Azure VM エージェントの定義に使うテンプレートを構成します。 

* **[追加]** をクリックしてテンプレートを追加します。 
* 新しいテンプレートの名前を指定します。 
* ラベルには、「ubuntu」と入力します。 このラベルは、ジョブの構成時に使われます。
* コンボ ボックスから目的のリージョンを選びます。
* 目的の VM サイズを選びます。
* Azure Storage アカウント名を指定するか、空白のままにして既定値 "jenkinsarmst" を使います。
* 保有期間を分単位で指定します。 この設定は、Jenkins がアイドル状態のエージェントを自動的に削除するまで待機する分単位の時間を定義します。 アイドル状態のエージェントが自動的に削除されないようにする場合は、0 を指定します。

![全般的な構成](./media/jenkins-azure-vm-agents/general-config.png)

### <a name="image-configuration"></a>イメージの構成

Linux (Ubuntu) エージェントを作成するには、**[Image reference\(イメージの参照\)]** を選択し、例のように次の構成を使います。 Azure がサポートする最新のイメージについては、[Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) をご覧ください。

* [Image Publisher\(イメージ発行元\)]: Canonical
* [Image Offer\(イメージ プラン\)]: UbuntuServer
* [Image Sku\(イメージ SKU\)]: 14.04.5-LTS
* [Image version\(イメージのバージョン\)]: latest\(最新\)
* [OS Type\(OS の種類\)]: Linux
* [Launch method\(起動方法\)]: SSH
* 管理者の資格情報を指定します
* VM 初期化スクリプトの場合は、次のように入力します。
```
# Install Java
sudo apt-get -y update
sudo apt-get install -y openjdk-7-jdk
sudo apt-get -y update --fix-missing
sudo apt-get install -y openjdk-7-jdk
```
![イメージの構成](./media/jenkins-azure-vm-agents/image-config.png)

* **[Verify Template\(テンプレートの確認\)]** をクリックして、構成を確認します。
* [ **Save**] をクリックします。

## <a name="create-a-job-in-jenkins"></a>Jenkins でジョブを作成する

* Jenkins ダッシュボードで、 **[New Item]**をクリックします。 
* 名前を入力し、**[Freestyle project\(フリースタイル プロジェクト\)]** を選択して、**[OK]** をクリックします。
* **[General\(全般\)]** タブで、[Restrict where project can be run\(プロジェクトを実行できる場所を制限する\)] をオンにして、[Label Expression\(ラベル式\)] に「ubuntu」と入力します。 ドロップダウンに "ubuntu" が表示されるようになります。
* [ **Save**] をクリックします。

![ジョブを設定する](./media/jenkins-azure-vm-agents/job-config.png)

## <a name="build-your-new-project"></a>新しいプロジェクトをビルドする

* Jenkins ダッシュボードに戻ります。
* 作成した新しいジョブを右クリックして、**[Build now\(今すぐビルド\)]** をクリックします。 ビルドが開始されます。 
* ビルドが完了したら、**[Console output\(コンソール出力\)]** に移動します。 ビルドが Azure でリモートに実行されたことがわかります。

![コンソール出力](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="reference"></a>リファレンス

* Azure Friday ビデオ: [Continuous Integration with Jenkins using Azure VM agents](https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents) (Azure VM エージェントを使用した Jenkins との継続的インテグレーション)
* サポート情報と構成オプション: [Azure VM エージェント Jenkins プラグイン Wiki](https://wiki.jenkins-ci.org/display/JENKINS/Azure+VM+Agents+Plugin) 


