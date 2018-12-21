---
title: Azure Stack で高可用性 MySQL データベースを提供する | Microsoft Docs
description: Azure Stack で MySQL Server リソース プロバイダーのホスト コンピューターと高可用性 MySQL データベースを作成する方法について説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: bee684409b2ef3fffeb9f175c2b469d3736b6484
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993838"
---
# <a name="tutorial-offer-highly-available-mysql-databases"></a>チュートリアル: 高可用性 MySQL データベースの提供

Azure Stack オペレーターとして、MySQL Server データベースをホストするようにサーバー VM を構成できます。 MySQL クラスターが正常に作成され、Azure Stack によって管理されていると、MySQL サービスにサブスクライブしているユーザーは高可用性 MySQL データベースを簡単に作成できます。

このチュートリアルでは、Azure Stack マーケットプレース項目を使用して[レプリケーション クラスター付きの MySQL](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster) を作成する方法について説明します。 このソリューションは、複数の VM を使用して、マスター ノードから構成可能な数のレプリカにデータベースをレプリケートします。 作成したクラスターは、Azure Stack MySQL ホスティング サーバーとして追加することができます。また、ユーザーは高可用性 MySQL データベースを作成できるようになります。

> [!IMPORTANT]
> **レプリケーション付きの MySQL** Azure Stack マーケットプレース項目は、すべての Azure クラウド サブスクリプション環境で利用できない場合があります。 このチュートリアルの残りの部分を実行する前に、マーケットプレース項目をサブスクリプションで使用できることを確認してください。

学習内容

> [!div class="checklist"]
> * マーケットプレース項目から MySQL Server クラスターを作成する
> * Azure Stack MySQL ホスティング サーバーを作成する
> * 高可用性 MySQL データベースを作成する

このチュートリアルでは、入手できる Azure Stack マーケットプレース項目を使用して、3 つの VM MySQL Server クラスターを作成および構成します。 

このチュートリアルの手順を開始する前に、[MySQL Server リソース プロバイダー](azure-stack-mysql-resource-provider-deploy.md)が正常にインストールされ、Azure Stack マーケットプレースで次の項目を入手できることを確認してください。

> [!IMPORTANT]
> MySQL クラスターを作成するには、以下のすべてが必要です。

- [レプリケーション付きの MySQL](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster)。 これは、MySQL クラスターのデプロイに使用される Bitnami ソリューション テンプレートです。
- [Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.Debian)。 credativ によって提供されている Microsoft Azure のバックポート カーネル付き Debian 8 "Jessie"。 Debian GNU/Linux は最も人気のある Linux ディストリビューションの 1 つです。
- [Linux 2.0 用のカスタム スクリプト](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft.custom-script-linux?tab=Overview)。 カスタム スクリプト拡張機能は、VM のプロビジョニング後に VM のカスタマイズ タスクを実行するためのツールです。 この拡張機能を仮想マシンに追加すると、Azure ストレージからスクリプトをダウンロードして VM 上で実行できます。 カスタム スクリプト拡張タスクは、Azure PowerShell コマンドレットと Azure クロスプラットフォーム コマンドライン インターフェイス (xPlat CLI) を使用して自動化することもできます。
- Linux Extension 1.4.7 用 VM アクセス。 VM アクセス拡張機能を使用すると、パスワード、SSH キー、または SSH 構成をリセットして、VM へのアクセスを再獲得することができます。 また、この拡張機能を使用して、パスワードまたは SSH キーを使用して新しいユーザーを追加したり、ユーザーを削除したりすることもできます。 この拡張機能は、Linux VM をターゲットとしています。

Azure Stack マーケットプレースに項目を追加する方法については、「[Azure Stack Marketplace の概要](azure-stack-marketplace.md)」を参照してください。

さらに、Linux VM のデプロイ後、それらにログインするために [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) などの SSH クライアントが必要です。

## <a name="create-a-mysql-server-cluster"></a>MySQL Server クラスターを作成する。 
このセクションの手順を使用して、[レプリケーション付き MySQL](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster) マーケットプレース項目を使用して MySQL Server クラスターをデプロイします。 このテンプレートで、高可用性 MySQL クラスターで構成された 3 つの MySQL Server インスタンスがデプロイされます。 既定で、次のリソースが作成されます。

- 仮想ネットワーク
- ネットワーク セキュリティ グループ
- ストレージ アカウント
- 可用性セット
- 3 つのネットワーク インターフェイス (既定の VM ごとに 1 つ)。
- パブリック IP アドレス (プライマリ MySQL クラスター VM の場合)
- MySQL クラスターをホストする 3 つの Linux VM

1. 
[!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. **\+[** **リソースの作成]** > **[計算]** の順に選択し、**[MySQL with Replication]\(レプリケーション付き MySQL\)** を選択します。

   ![カスタム テンプレートのデプロイ](media/azure-stack-tutorial-mysqlrp/1.png)

3. **[基本]** ページで基本的なデプロイ情報を提供します。 既定値を確認し、必要に応じて変更して **[OK]** をクリックします。<br><br>少なくとも以下を指定します。
   - デプロイ名 (既定値は mymysql)
   - アプリケーションのルート パスワード。 **特殊文字を含めずに** 12 文字の英数字のパスワードを入力してください
   - アプリケーション データベース名 (既定値は bitnami)
   - 作成する MySQL データベースのレプリカ VM の数 (既定値は 2)
   - 使用するサブスクリプションを選択します
   - 使用するリソース グループを選択するか、新しいリソース グループを作成します
   - 場所を選択します (ASDK の場合、既定値は local)

   [![](media/azure-stack-tutorial-mysqlrp/2-sm.PNG "デプロイの基本")](media/azure-stack-tutorial-mysqlrp/2-lg.PNG#lightbox)

4. **[Environment Configuration]\(環境の構成\)** ページに次の情報を入力し、**[OK]** をクリックします。 
   - Secure Shell (SSH) 認証に使用するパスワードまたは SSH パブリック キー。 パスワードを使用する場合は、英字、数字を含める必要があります。また、特殊文字を**含めることもできます**。
   - VM サイズ (既定値は Standard D1 v2 VM)
   - データ ディスク サイズ (GB) を入力し、**[OK]** をクリックします。

   [![](media/azure-stack-tutorial-mysqlrp/3-sm.PNG "環境の構成")](media/azure-stack-tutorial-mysqlrp/3-lg.PNG#lightbox)

5. デプロイの **[概要]** を確認します。 必要に応じて、カスタマイズしたテンプレートとパラメーターをダウンロードし、**[OK]** をクリックします。

   [![](media/azure-stack-tutorial-mysqlrp/4-sm.PNG "概要")](media/azure-stack-tutorial-mysqlrp/4-lg.PNG#lightbox)

6. **[購入]** ページで **[作成]** をクリックしてデプロイを開始します。

   ![購入](media/azure-stack-tutorial-mysqlrp/5.png)

    > [!NOTE]
    > デプロイには約 1 時間かかります。 続行する前に、デプロイが完了し、MySQL クラスターの構成が完了していることを確認してください。 

7. すべてのデプロイが正常に完了したら、リソース グループ項目を確認し、**mysqlip** パブリック IP アドレス項目を選択します。 クラスターのパブリック IP アドレスと完全な FQDN をメモします。<br><br>この MySQL クラスターを利用する MySQL ホスティング サーバーを作成できるようにするには、この情報を Azure Stack Operator に渡す必要があります。


### <a name="create-a-network-security-group-rule"></a>ネットワーク セキュリティ グループ規則を作成する
既定では、MySQL からホスト VM へのパブリック アクセスは構成されません。 Azure Stack MySQL リソース プロバイダーが MySQL クラスターを接続して管理するには、受信ネットワーク セキュリティ グループ (NSG) 規則を作成する必要があります。

1. 管理者ポータルで、MySQL クラスターのデプロイ時に作成されたリソース グループに移動し、ネットワーク セキュリティ グループ (**default-subnet-sg**) を選択します。

   ![オープン](media/azure-stack-tutorial-mysqlrp/6.png)

2. **[受信セキュリティ規則]**、**[追加]** の順に選択します。<br><br>**[宛先ポート範囲]** に「**3306**」と入力し、必要に応じて **[名前]** フィールドと **[説明]** フィールドに説明を入力します。 [追加] をクリックして受信セキュリティ規則ダイアログを閉じます。

   ![オープン](media/azure-stack-tutorial-mysqlrp/7.png)

### <a name="configure-external-access-to-the-mysql-cluster"></a>MySQL クラスターへの外部アクセスを構成する
MySQL クラスターを Azure Stack MySQL Server ホストとして追加する前に、外部アクセスを有効にする必要があります。

1. この例では、SSH クライアントを使用して [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) を使用し、パブリック IP にアクセスできるコンピューターからプライマリ MySQL マシンにログインします。 通常、プライマリ MySQL VM 名は末尾が **0** であり、パブリック IP が割り当てられています。<br><br>パブリック IP を使用して、ユーザー名 **bitnami** と、前に作成した特殊文字を使用しないアプリケーション パスワードを入力してログインします。

   ![LinuxLogin](media/azure-stack-tutorial-mysqlrp/bitnami1.png)


2. SSH クライアント ウィンドウで、次のコマンドを使用して、bitnami サービスがアクティブで実行中であることを確認します。 プロンプトが表示されたら、もう一度 bitnami パスワードを入力します。

   `sudo service bitnami status`

   ![サービスを確認する](media/azure-stack-tutorial-mysqlrp/bitnami2.png)

3. Azure Stack MySQL ホスティング サーバーが MySQL に接続し、SSH クライアントを終了するために使用するリモート アクセス ユーザー アカウントを作成します。<br><br>次のコマンドを使用して、以前に作成したルート パスワードを入力してルートとして MySQL にログインして、新しい管理者ユーザーを作成し、環境に合わせ、必要に応じて*\<ユーザー名\>* と*\<パスワード\>* を置き換えます。 この例では、作成するユーザーの名前は **sqlsa** であり、強力なパスワードが使用されています。

   ```mysql
   mysql -u root -p
   create user <username>@'%' identified by '<password>';
   grant all privileges on *.* to <username>@'%' with grant option;
   flush privileges;
   ```
   ![管理者ユーザーを作成する](media/azure-stack-tutorial-mysqlrp/bitnami3.png)


4. 新しい MySQL ユーザー情報をメモします。<br><br>Azure Stack Operator には、このユーザー名とパスワードと共に、クラスターのパブリック IP アドレスまたはパブリック IP の完全な FQDN を渡して、この MySQL クラスターを使用して MySQL ホスティング サーバーを作成できるようにする必要があります。


## <a name="create-an-azure-stack-mysql-hosting-server"></a>Azure Stack MySQL ホスティング サーバーを作成する
MySQL クラスターが作成され、適切に構成された後、Azure Stack Operator は Azure Stack MySQL ホスティング サーバーを作成して、ユーザーがデータベースを作成できるように追加容量を作成する必要があります。 

以前に MySQL クラスターのリソース グループを作成したときにメモした、MySQL クラスターのプライマリ VM のパブリック IP、またはパブリック IP の完全な FQDN を必ず使用してください (**mysqlip**)。 また、Operator は、MySQL クラスター データベースにリモート アクセスするために作成した MySQL Server の認証資格情報を知る必要があります。

> [!NOTE]
> この手順は、Azure Stack Operator が Azure Stack 管理ポータルから実行する必要があります。

Azure Stack Operator は、MySQL クラスターのパブリック IP と MySQL の認証ログイン情報を利用し、[新しい MySQL クラスターを使用して MySQL ホスティング サーバーを作成](azure-stack-mysql-resource-provider-hosting-servers.md#connect-to-a-mysql-hosting-server)できるようになりました。 

また、MySQL データベースをユーザーが作成できるようにするプランとオファーが作成してあることも確認してください。 Operator は **Microsoft.MySqlAdapter** サービスをプランに追加し、高可用性データベース専用の新しいクォータを作成する必要があります。 プランの作成の詳細については、「[プラン、オファー、クォータ、サブスクリプションの概要](azure-stack-plan-offer-quota-overview.md)」を参照してください。

> [!TIP]
> **Microsoft.MySqlAdapter** サービスは、[MySQL Server リソース プロバイダーがデプロイされる](azure-stack-mysql-resource-provider-deploy.md)までプランに追加することができません。



## <a name="create-a-highly-available-mysql-database"></a>高可用性 MySQL データベースを作成する
MySQL クラスターが Azure Stack Operator によって Azure StackSQL ホスティング サーバーとして作成、構成、および追加された後、MySQL Server データベース機能を含むサブスクリプションを持つテナント ユーザーは、このセクションの次の手順に従って、高可用性 MySQL データベースを作成できます。 

> [!NOTE]
> これらの手順は、MySQL Server 機能 (Microsoft.MySQLAdapter サービス) を提供するサブスクリプションを持つテナント ユーザーとして、Azure Stack ユーザー ポータルから実行します。

1. 
[!INCLUDE [azs-user-portal](../../includes/azs-user-portal.md)]

2. **\+[** **リソースの作成]** > **[データ \+ ストレージ]** の順に選択し、**[MySQL Database]** を選択します。<br><br>名前、照合順序、使用するサブスクリプション、デプロイに使用する場所などの必須のデータベースのプロパティを指定します。 

   ![MySQL データベースを作成する](./media/azure-stack-tutorial-mysqlrp/createdb1.png)

3. **[SKU]** を選択し、使用する適切な MySQL ホスティング サーバー SKU を選択します。 この例で、Azure Stack Operator は、MySQL クラスター データベースの高可用性をサポートするために **MySQL-HA** SKU を作成しました。

   ![SKU を選択する](./media/azure-stack-tutorial-mysqlrp/createdb2.png)

4. **[ログイン]** > **[Create a new login]\(新しいログインの作成\)** の順に選択し、新しいデータベースに使用する MySQL の認証資格情報を入力します。 完了したら **[OK]**、**[作成]** の順にクリックし、データベースのデプロイ プロセスを開始します。

   ![ログインを追加する](./media/azure-stack-tutorial-mysqlrp/createdb3.png)

5. MySQL データベースのデプロイが正常に完了したら、データベースのプロパティを確認し、新しい高可用性データベースへの接続に使用する接続文字列を見つけます。 

   ![接続文字列を表示する](./media/azure-stack-tutorial-mysqlrp/createdb4.png)

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * マーケットプレース項目から MySQL Server クラスターを作成する
> * Azure Stack MySQL ホスティング サーバーを作成する
> * 高可用性 MySQL データベースを作成する

次のチュートリアルに進み、次の操作方法を確認してください。
> [!div class="nextstepaction"]
> [Web アプリの提供](azure-stack-tutorial-app-service.md)
