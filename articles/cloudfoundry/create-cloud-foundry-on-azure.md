---
title: Azure で Cloud Foundry を作成する
description: Azure で Cloud Foundry PCF クラスターをプロビジョニングするために必要なパラメーターの設定方法について説明します
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: Cloud Foundry
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: a0a3379a8a2579080d9b686917395feec9cf8f3d
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2018
ms.locfileid: "48250625"
---
# <a name="create-cloud-foundry-on-azure"></a>Azure で Cloud Foundry を作成する

このチュートリアルでは、Azure で Pivotal Cloud Foundry PCF クラスターをプロビジョニングするために必要なパラメーターを作成および生成する際の簡単な手順を示します。  Pivotal Cloud Foundry ソリューションは、Azure [MarketPlace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry) で検索を実行すると見つかります。

![イメージ テキスト](media/deploy/pcf-marketplace.png "Azure で Pivotal Cloud Foundry を検索する")


## <a name="generate-an-ssh-public-key"></a>SSH 公開キーを生成する

Windows、Mac、または Linux を使用して SSH 公開キーを生成する方法はいくつかあります。

```Bash
ssh-keygen -t rsa -b 2048
```
- ここをクリックして、お使いの環境での[手順]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)を参照してください。

## <a name="create-a-service-principal"></a>サービス プリンシパルを作成する

> [!NOTE]
>
> サービス プリンシパルを作成するには、所有者アカウントのアクセス許可が必要です。  さらに、サービス プリンシパルの作成を自動化するスクリプトを記述することができます。 たとえば、Azure CLI で [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest) を使用します。

1. Azure アカウントにログインします。

    `az login`

    ![イメージ テキスト](media/deploy/az-login-output.png "Azure CLI のログイン")
 
    後で使用するため、**サブスクリプション ID** としての "id" 値と、**tenantId** 値をコピーします。

2. この構成の既定のサブスクリプションを設定します。

    `az account set -s {id}`

3. PCF 用の AAD アプリケーションを作成し、一意の英数字のパスワードを指定します。  後で使用するため、パスワードを **clientSecret** として保存します。

    `az ad app create --display-name "Svc Prinicipal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}`

    後で使用するため、出力内の "appId" 値を **ClientID** としてコピーします。

    > [!NOTE]
    >
    > 独自のアプリケーションのホーム ページと識別子 URI を選択します。  例を示します: http://www.contoso.com。

4. 新しい "appId" でサービス プリンシパルを作成します。

    `az ad sp create --id {appId}`

5. サービス プリンシパルのアクセス許可のロールは、**共同作成者**として設定します。

    `az role assignment create --assignee “{enter-your-homepage}” --role “Contributor” `

    または、次を使用することもできます。

    `az role assignment create --assignee {service-princ-name} --role “Contributor” `

    ![イメージ テキスト](media/deploy/svc-princ.png "サービス プリンシパルのロールの割り当て")

6. 前述の appId、パスワード、および tenantId を使用してサービス プリンシパルに正しくログインできることを確認します。

    `az login --service-principal -u {appId} -p {your-passward}  --tenant {tenantId}`

7. 上でコピーした**サブスクリプション ID**、**tenantId**、**clientID**、および **clientSecret** の値をすべて使用して、次の形式で .json ファイルを作成します。  ファイルを保存します。

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Pivotal Network トークンを取得する

1. [Pivotal Network](https://network.pivotal.io) アカウントに登録またはログインします。
2. ページの右上にある自分のプロファイル名をクリックし、[**Edit Profile”] を選択します。
3. ページの一番下までスクロールして、**[LEGACY API TOKEN]** 値をコピーします。  これが後で使用される **Pivotal Network トークン**の値です。

## <a name="provision-your-cloud-foundry-on-azure"></a>Azure に Cloud Foundry をプロビジョニングする

1. これで、[Pivotal Cloud Foundry を Azure ](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)クラスターにプロビジョニングするために必要なパラメーターがすべて揃いました。
2. パラメーターを入力し、PCF クラスターを作成します。

## <a name="verify-the-deployment-and-log-into-the-pivotal-ops-manager"></a>デプロイを確認して Pivotal Ops Manager にログインする

1. PCF クラスターに、デプロイの状態が表示されるはずです。

    ![イメージ テキスト](media/deploy/deployment.png "Azure デプロイの状態")

2. 左側のナビゲーションの **[Deployments]** リンクをクリックし、PCF Ops Manager に対する資格情報を取得してから、次のページの **[Deployment Name]** リンクをクリックします。
3. 左側のナビゲーションで **[Outputs]** リンクをクリックして、PCF Ops Manager に対する、URL、ユーザー名、およびパスワードを表示します。  "OPSMAN-FQDN" 値が URL です。
 
    ![イメージ テキスト](media/deploy/deploy-outputs.png "Cloud Foundry のデプロイの出力")
 
4. Web ブラウザーで URL を開き、前の手順の資格情報を入力してログインします。

    ![イメージ テキスト](media/deploy/pivotal-login.png "Pivotal のログイン ページ")
         
    > [!NOTE]
    >
    > サイトが安全でないという警告メッセージのために Internet Explorer ブラウザーが失敗する場合は、[More information]\(情報) と [Go on to the webpage]\(Web ページに進む) をクリックします。  Firefox の場合は、[Advance] をクリックし、証明書を追加して続行します。

5. PCF Ops Manager には、デプロイされた Azure インスタンスが表示されるはずです。 これで、ここからアプリケーションの展開と管理を開始できます。
               
    ![イメージ テキスト](media/deploy/ops-mgr.png "Pivotal でのデプロイされた Azure インスタンス")
 
