---
title: "ドメイン参加済み HDInsight クラスターの構成 - Azure | Microsoft Docs"
description: "ドメイン参加済み HDInsight クラスターのセットアップと構成の方法について説明します"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 0cbb49cc-0de1-4a1a-b658-99897caf827c
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/10/2018
ms.author: saurinsh
ms.openlocfilehash: 4921e329c2ec8ce3d5bbf8a0851146e13d5f6cd3
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2018
---
# <a name="configure-domain-joined-hdinsight-sandbox-environment"></a>ドメイン参加済み HDInsight サンドボックス環境を構成する

Azure HDInsight クラスターとスタンドアロンの Active Directory および [Apache Ranger](http://hortonworks.com/apache/ranger/) をセットアップし、強力な認証機能と豊富なロールベースのアクセス制御 (RBAC) ポリシーを活用する方法について説明します。 詳細については、[ドメイン参加済み HDInsight クラスターの概要](apache-domain-joined-introduction.md)に関するページを参照してください。

ドメイン参加済み HDInsight クラスターではない場合は、各クラスターで使うことができるのは Hadoop HTTP ユーザー アカウントと SSH ユーザー アカウントだけです。  マルチユーザー認証は次のものを使って実現できます。

-   Azure IaaS 上で実行しているスタンドアロンの Active Directory。
-   Azure Active Directory。
-   お客様のオンプレミス環境で実行している Active Directory。

この記事では、Azure IaaS 上で実行しているスタンドアロンの Active Directory を使う方法について説明します。 これは、HDInsight 上でのマルチユーザー サポートを実現するためにお客様が利用できる最も簡単なアーキテクチャです。 この記事では、この構成の 2 つの方法を説明します。

- オプション 1: 1 つの Azure リソース管理テンプレートを使って、スタンドアロンの Active Directory と HDInsight クラスターの両方を作成します。
- オプション 2: プロセス全体は次の手順に分かれます。
    - テンプレートを使って Active Directory を作成する
    - LDAPS をセットアップする
    - AD のユーザーとグループを作成する
    - HDInsight クラスターの作成

> [!IMPORTANT]
> ドメイン参加済みの HDInsight では、Oozie は有効になっていません。

## <a name="prerequisite"></a>前提条件
* Azure サブスクリプション

## <a name="option-1-one-step-approach"></a>オプション 1: ワンステップ アプローチ
このセクションでは、Azure Portal から Azure リソース管理テンプレートを開きます。 このテンプレートを使って、スタンドアロンの Active Directory と HDInsight クラスターを作成します。 現在は、ドメイン参加済み Hadoop クラスター、Spark クラスター、Interactive Query クラスターを作成できます。

1. 次の画像をクリックして Azure ポータルでテンプレートを開きます。 テンプレートは「[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/)」にあります。
   
    Spark クラスターを作成するには:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Fspark%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Interactive Query クラスターを作成するには:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Finteractivequery%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Hadoop クラスターを作成するには:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Fhadoop%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 値を入力し、**[上記の使用条件に同意する]**、**[ダッシュボードにピン留めする]** の順に選んで、**[購入]** をクリックします。 説明を表示するには、フィールドの横にある説明記号をマウス カーソルでポイントします。 ほとんどの値が設定されています。 既定値を使っても、独自の値を使ってもかまいません。

    - **[リソース グループ]**: Azure リソース グループの名前を入力します。
    - **[場所]**: 近くの場所を選びます。
    - **[新しいストレージ アカウント名]**: Azure ストレージ アカウントの名前を入力します。 この新しいストレージ アカウントは、PDC、BDC、HDInsight クラスターによって既定のストレージ アカウントとして使われます。
    - **[管理ユーザー名]**: ドメイン管理者のユーザー名を入力します。
    - **[管理パスワード]**: ドメイン管理者のパスワードを入力します。
    - **[ドメイン名]**: 既定の名前は *contoso.com* です。ドメイン名を変更する場合は、**[セキュリティで保護された LDAP 証明書]** フィールドと **[Organizational Unit DN]\(組織単位 DN\)** フィールドも更新する必要があります。
    - **[クラスター名]**: HDInsight クラスターの名前を入力します。
    - **[クラスターの種類]**: この値は変更しないでください。 クラスターの種類を変更する場合は、最後のステップで対応するテンプレートを使います。

    一部の値は、テンプレートにハードコーディングされています (たとえば、worker ノードのインスタンス数は 2)。  ハード コーディングされた値を変更するには、**[テンプレートの編集]** をクリックします。

    ![HDInsight のドメイン参加済みクラスターのテンプレートの編集](./media/apache-domain-joined-configure/hdinsight-domain-joined-edit-template.png)

テンプレートの処理が正常に完了すると、リソース グループに 23 個のリソースが作成されます。

## <a name="option-2-multi-step-approach"></a>オプション 2: マルチステップ アプローチ

このセクションには、次の 4 つのステップがあります。

1. テンプレートを使って Active Directory を作成する
2. LDAPS をセットアップする
3. AD のユーザーとグループを作成する
4. HDInsight クラスターの作成

### <a name="create-an-active-directory"></a>Active Directory を作成する

Azure Resource Manager テンプレートを使うと、Azure リソースを簡単に作成できます。 このセクションでは、[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/active-directory-new-domain-ha-2-dc/)を使って、2 つの仮想マシンで新しいフォレストとドメインを作成します。 2 つの仮想マシンは、プライマリ ドメイン コントローラーおよびバックアップ ドメイン コントローラーとして機能します。

**2 つのドメイン コントローラーを使うドメインを作成するには**

1. 次の画像をクリックして Azure ポータルでテンプレートを開きます。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Factive-directory-new-domain-ha-2-dc%2Fazuredeploy.json" target="_blank"><img src="./media/apache-domain-joined-configure/deploy-to-azure.png" alt="Deploy to Azure"></a>

    テンプレートは次のような内容です。

    ![HDInsight ドメイン参加済み、フォレストの作成、ドメイン仮想マシン](./media/apache-domain-joined-configure/hdinsight-domain-joined-create-arm-template.png)

2. 次の値を入力します。

    - **[サブスクリプション]**: Azure サブスクリプションを選択します。
    - **[リソース グループ名]**: リソース グループの名前を入力します。  リソース グループは、プロジェクトに関連のある Azure リソースを管理するために使われます。
    - **[場所]**: 近くの Azure の場所を選びます。
    - **[管理ユーザー名]**: ドメイン管理者のユーザー名です。 このユーザーは、HDInsight クラスターの HTTP ユーザー アカウントではありません。 これは、チュートリアルをとおして使うアカウントです。
    - **[管理パスワード]**: ドメイン管理者のパスワードを入力します。
    - **[ドメイン名]**: ドメイン名は 2 部構成の名前である必要があります。 例: contoso.com、contoso.local、hdinsight.test。
    - **[DNS プレフィックス]**: DNS プレフィックスを入力します。
    - **[PDC RDP Port]\(PDC RDP ポート\)**: (このチュートリアルでは既定値を使います)
    - **[BDC RDP Port]\(BDC RDP ポート\)**: (このチュートリアルでは既定値を使います)
    - **[artifacts location]\(アーティファクトの場所\)**: (このチュートリアルでは既定値を使います)
    - **[artifacts location SAS token]\(アーティファクトの場所の SAS トークン\)**: (このチュートリアルでは空白のままにします)

リソースの作成には約 20 分かかります。

### <a name="setup-ldaps"></a>LDAPS をセットアップする

AD に対する読み書きには、ライトウェイト ディレクトリ アクセス プロトコル (LDAP) が使用されます。

**リモート デスクトップを使って PDC に接続するには**

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. リソース グループを開き、プライマリ ドメイン コントローラー (PDC) の仮想マシンを開きます。 PDC の既定の名前は adPDC です。 
3. リモート デスクトップを使って PDC に接続するには、**[接続]** をクリックします。

    ![HDInsight ドメイン参加済み、PDC への接続、リモート デスクトップ](./media/apache-domain-joined-configure/hdinsight-domain-joined-remote-desktop-pdc.png)


**Active Directory 証明書サービスを追加するには**

4. 開いていない場合は、**[サーバー マネージャー]** を開きます。
5. **[管理]** をクリックし、**[役割と機能の追加]** をクリックします。

    ![HDInsight ドメイン参加済み、役割と機能の追加](./media/apache-domain-joined-configure/hdinsight-domain-joined-add-roles.png)
5. [開始する前に] で **[次へ]** をクリックします。
6. **[役割ベースまたは機能ベースのインストール]** を選択し、**[次へ]** をクリックします。
7. PDC を選び、**[次へ]** をクリックします。  PDC の既定の名前は adPDC です。
8. **[Active Directory 証明書サービス]** を選びます。
9. ポップアップ ダイアログで **[機能の追加]** をクリックします。
10. ウィザードの指示に従い、残りの手順では既定の設定を使います。
11. **[閉じる]** をクリックしてウィザードを閉じます。

**AD FS 証明書を構成するには**

1. サーバー マネージャーで黄色の通知アイコンをクリックして、**[Active Directory 証明書サービスの構成]** をクリックします。

    ![HDInsight ドメイン参加済み、構成、AD 証明書](./media/apache-domain-joined-configure/hdinsight-domain-joined-configure-ad-certificate.png)

2. 左側の **[役割サービス]** をクリックし、**[証明機関]** を選んで、**[次へ]** をクリックします。
3. ウィザードの指示に従い、残りの手順では既定の設定を使います (最後のステップで **[構成]** をクリックします)。
4. **[閉じる]** をクリックしてウィザードを閉じます。

### <a name="optional-create-ad-users-and-groups"></a>(省略可能) AD のユーザーとグループを作成する

**AD でユーザーとグループを作成するには**
1. リモート デスクトップを使って PDC に接続します
1. **[Active Directory ユーザーとコンピューター]** を開きます。
2. 左側のウィンドウでドメイン名を選びます。
3. 上部メニューの **[現在のコンテナーに新しいユーザーを作成]** アイコンをクリックします。

    ![HDInsight ドメイン参加済み、ユーザーの作成](./media/apache-domain-joined-configure/hdinsight-domain-joined-create-ad-user.png)
4. 手順に従ってユーザーを何人か作成します。 たとえば、hiveuser1 と hiveuser2 を作成します。
5. 上部メニューの **[現在のコンテナーに新しいグループを作成]** アイコンをクリックします。
6. 手順に従って、**HDInsightUsers** という名前のグループを作成します。  このグループは、後で HDInsight クラスターを作成するときに使います。

> [!IMPORTANT]
> ドメイン参加済み HDInsight クラスターを作成する前に、PDC 仮想マシンを再起動する必要があります。

### <a name="create-an-hdinsight-cluster-in-the-vnet"></a>VNet に HDInsight クラスターを作成する

このセクションでは、前に Resource Manager テンプレートを使って作成した仮想ネットワークに、Azure Portal を使って HDInsight クラスターを追加します。 この記事では、ドメイン参加済みクラスターの構成に固有の情報についてのみ説明します。  一般的な情報については、「[Azure Portal を使用した HDInsight の Linux ベースのクラスターの作成](../hdinsight-hadoop-create-linux-clusters-portal.md)」をご覧ください。  

**ドメイン参加済み HDInsight クラスターを作成するには**

1. [Azure Portal](https://portal.azure.com) にサインオンします。
2. このチュートリアルで前に Resource Manager テンプレートを使って作成したリソース グループを開きます。
3. HDInsight クラスターをリソース グループに追加します。
4. **[カスタム]** オプションを選びます。

    ![HDInsight ドメイン参加済み、カスタム作成オプション](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-custom-configuration-option.png)

    カスタム構成オプションには、[基本]、[ストレージ]、[アプリケーション]、[クラスター サイズ]、[詳細設定]、[概要] の 6 つのセクションがあります。
5. **[基本]** セクションでの設定:

    - [クラスターの種類]: **[Enterprise セキュリティ パッケージ]** を選びます。 現在、Enterprise セキュリティ パッケージは、クラスターの種類が Hadoop、Interactive Query、Spark の場合にのみ有効にできます。

        ![HDInsight ドメイン参加済み、Enterprise セキュリティ パッケージ](./media/apache-domain-joined-configure/hdinsight-creation-enterprise-security-package.png)
    - [クラスター ログイン ユーザー名]: これは Hadoop HTTP ユーザーです。 このアカウントは、ドメイン管理者アカウントとは異なります。
    - [リソース グループ]: 前に Resource Manager テンプレートを使って作成したリソース グループを選びます。
    - [場所]: Resource Manager テンプレートを使って VNet と DC を作成するときに使ったものと同じ場所にする必要があります。

6. **[詳細設定]** セクションでの設定:

    - ドメインの設定:

        ![HDInsight ドメイン参加済み、詳細設定、ドメイン](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-advanced-domain-settings.png)
        
        - [ドメイン名]: [Active Directory の作成](#create-an-active-directory)で使ったドメイン名を入力します。
        - [ドメイン ユーザー名]: [Active Directory の作成](#create-an-active-directory)で使った AD 管理者のユーザー名を入力します。
        - [組織単位]: スクリーンショットの例をご覧ください。
        - [LDAPS の URL]: スクリーンショットの例をご覧ください
        - [アクセス ユーザー グループ]: [AD のユーザーとグループの作成](#optionally-createad-users-and-groups)で作成したユーザー グループ名を入力します
    - [仮想ネットワーク]: [Active Directory の作成](#create-an-active-directory)で作成した仮想ネットワークを選びます。 テンプレートで使われる既定の名前は **adVNET** です。
    - [サブネット]: テンプレートで使われる既定の名前は **adSubnet** です。



チュートリアルを完了したら、必要に応じてクラスターを削除できます。 HDInsight を使用すると、データは Azure Storage に格納されるため、クラスターは、使用されていない場合に安全に削除できます。 また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。 クラスターの削除手順については、「[Azure Portal を使用した HDInsight での Hadoop クラスターの管理](../hdinsight-administer-use-management-portal.md#delete-clusters)」を参照してください。

## <a name="next-steps"></a>次の手順
* Hive ポリシーの構成と Hive クエリの実行については、[ドメイン参加済み HDInsight クラスターの Hive ポリシーの構成](apache-domain-joined-run-hive.md)に関する記事をご覧ください。
* SSH を使用してドメイン参加済み HDInsight クラスターに接続する方法については、「[Linux、Unix、または OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)」を参照してください。

