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
ms.date: 11/02/2016
ms.author: saurinsh
ms.openlocfilehash: 649d138a85ca47440e43c00637ee92b86f4eb03e
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2017
---
# <a name="configure-domain-joined-hdinsight-clusters"></a>ドメイン参加済み HDInsight クラスターの構成

Azure HDInsight クラスターと Azure Active Directory (Azure AD) および [Apache Ranger](http://hortonworks.com/apache/ranger/) をセットアップし、強力な認証機能と豊富なロールベースのアクセス制御 (RBAC) ポリシーを活用する方法について説明します。  ドメイン参加済み HDInsight は、Linux ベースのクラスターのみで構成できます。 詳細については、[ドメイン参加済み HDInsight クラスターの概要](apache-domain-joined-introduction.md)に関するページを参照してください。

> [!IMPORTANT]
> ドメイン参加済みの HDInsight では、Oozie は有効になっていません。

この記事は、以下のシリーズの最初のチュートリアルです。

* Azure Directory Domain Services 機能を通じて Azure AD に接続される HDInsight クラスターを、Apache Ranger を有効にして作成する。
* Apache Ranger を通じて Hive ポリシーを作成および適用して、ユーザー (たとえばデータ サイエンティスト) が ODBC ベースのツール (Excel、Tableau など) を使用して Hive に接続できるようにする。マイクロソフトでは、HBase や Storm などの他のワークロードもできるだけ早くドメイン参加済み HDInsight に追加できるように作業を進めています。

Azure サービス名は、グローバルに一意である必要があります。 このチュートリアルでは、次の名前を使用しています。 Contoso は架空の名前です。 チュートリアルを進めるときには、*contoso* を別の名前に置き換える必要があります。 

**名前:**

| プロパティ | 値 |
| --- | --- |
| Azure AD ディレクトリ |contosoaaddirectory |
| Azure AD ドメイン名 |contoso (contoso.onmicrosoft.com) |
| HDInsight VNet |contosohdivnet |
| HDInsight VNet のリソース グループ |contosohdirg |
| HDInsight クラスター |contosohdicluster |

このチュートリアルでは、ドメイン参加済み HDInsight クラスターを構成するための手順を説明します。 各セクションには、詳細な背景情報が記載されている他の記事へのリンクがあります。

## <a name="prerequisite"></a>前提条件:
* [Azure AD ドメイン サービス](https://azure.microsoft.com/services/active-directory-ds/)とその[料金](https://azure.microsoft.com/pricing/details/active-directory-ds/)体系を理解している。
* サブスクリプションが、このパブリック プレビューのホワイトリストに登録されている。 登録するには、サブスクリプション ID を明記して、hdipreview@microsoft.com に電子メールを送信します。
* ドメインの署名機関によって署名された SSL 証明書または自己署名証明書。 セキュリティで保護された LDAP を構成するために、証明書が必要です。

## <a name="procedures"></a>プロシージャ
1. Azure リソース管理モードで HDInsight VNet を作成します。
2. Azure AD と Azure AD DS を作成および構成します。
3. HDInsight クラスターを作成します。

> [!NOTE]
> このチュートリアルでは、Azure AD がないことを前提としています。 ある場合は、その部分を省略できます。
> 
> 

## <a name="create-a-resource-manager-vnet-for-hdinsight-cluster"></a>HDInsight クラスターの Resource Manager VNet の作成
このセクションでは、HDInsight クラスターに使用される Azure Resource Manager VNet を作成します。 他の方法による Azure VNet の作成の詳細については、[仮想ネットワークの作成](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md)に関するページを参照してください。

VNet を作成した後、その VNet を使用する Azure AD DS を構成します。

**Resource Manager VNet を作成するには**

1. [Azure Portal](https://portal.azure.com) にサインオンします。
2. **[新規]**、**[ネットワーキング]**、**[仮想ネットワーク]** の順にクリックします。 
3. **[デプロイ モデルの選択]** で **[リソース マネージャー]** を選択し、**[作成]** をクリックします。
4. 次の値を入力または選択します。
   
   * **名前**: contosohdivnet
   * **アドレス空間**: 10.0.0.0/16
   * **サブネット名**: Subnet1
   * **サブネットのアドレス範囲**: 10.0.0.0/24
   * **サブスクリプション**: (Azure サブスクリプションを選択します。)
   * **リソース グループ**: contosohdirg
   * **場所**: (Azure AD VNet と同じ場所を選択します。 例: contosoaadvnet)
5. **Create** をクリックしてください。

**Resource Manager VNet の DNS を構成するには**

1. [Azure Portal](https://portal.azure.com) で、**[その他のサービス]** > **[仮想ネットワーク]** の順にクリックします。 **[仮想ネットワーク (クラシック)]** をクリックしないようにしてください。
2. **[contosohdivnet]** をクリックします。
3. 新しいブレードの左側で、**[DNS サーバー]** をクリックします。
4. **[カスタム]** をクリックし、次の値を入力します。
   
   * 10.0.0.4
   * 10.0.0.5     
     
5. **[Save]** をクリックします。

## <a name="create-and-configure-azure-ad-ds-for-your-azure-ad"></a>Azure AD 用の Azure AD DS の作成と構成
このセクションでは、次の作業を行います。

1. Azure AD を作成します。
2. Azure AD ユーザーを作成します。 これらのユーザーは、ドメイン ユーザーです。 最初のユーザーを使用して、HDInsight クラスターと Azure AD を構成します。  その他の 2 種類のユーザーは、このチュートリアルではオプションです。 これらのユーザーは、Apache Ranger ポリシーを構成するときに、[ドメイン参加済み HDInsight クラスターの Hive ポリシーの構成](apache-domain-joined-run-hive.md)で使用されます。
3. AAD DC 管理者グループを作成し、Azure AD ユーザーをグループに追加します。 このユーザーを使用して、組織単位を作成します。
4. Azure AD の Azure AD ドメイン サービス (Azure AD DS) を有効にします。
5. Azure AD の LDAPS を構成します。 Azure AD に対する読み書きには、ライトウェイト ディレクトリ アクセス プロトコル (LDAP) が使用されます。

既存の Azure AD を使用する場合は、手順 1. と 2. を省略できます。

**Azure AD を作成するには**

1. [Azure クラシック ポータル](https://manage.windowsazure.com)で、**[新規]** > **[App Services]** > **[Active Directory]** > **[ディレクトリ]** > **[カスタム作成]** の順にクリックします。 
2. 次の値を入力または選択します。
   
   * **名前**: contosoaaddirectory
   * **ドメイン名**: contoso。  この名前はグローバルに一意である必要があります。
   * **国またはリージョン**: 国またはリージョンを選択します。
3. **[完了]** をクリックします。

**Azure AD ユーザーの作成**

1. [Azure ポータル](https://portal.azure.com)で、**[Azure Active Directory]** > **[contosoaaddirectory]** > **[ユーザーとグループ]** をクリックします。 
2. メニューから **[すべてのユーザー]** をクリックします。
3. **[新しいユーザー]**をクリックします。
4. **[名前]** と **[ユーザー名]** を入力し、**[次へ]** をクリックします。 
5. ユーザー プロファイルを構成します。**[ロール]** で **[全体管理者]** を選択し、**[次へ]** をクリックします。  組織単位を作成するには、全体管理者ロールが必要です。
6. 一時パスワードのコピーを作成します。
7. **Create** をクリックしてください。 このチュートリアルの後半で、この全体管理者ユーザーを使用して HDInsight クラスターを作成します。

同じ手順で、**ユーザー** ロールのユーザーをさらに 2 つ作成します (hiveuser1 と hiveuser2)。 これらのユーザーは、[ドメイン参加済み HDInsight クラスターの Hive ポリシーの構成](apache-domain-joined-run-hive.md)で使用されます。

**AAD DC 管理者グループを作成し、Azure AD ユーザーを追加するには**

1. [Azure ポータル](https://portal.azure.com)で、**[Azure Active Directory]** > **[contosoaaddirectory]** > **[ユーザーとグループ]** をクリックします。 
2. 上部のメニューで **[すべてのグループ]** をクリックします。
3. **[新しいグループ]** をクリックします。
4. 次の値を入力または選択します。
   
   * **名前**: AAD DC Administrators。  グループ名は変更しないでください。
   * **メンバーシップの種類**: 割り当てられています。
5. **[選択]**をクリックします。
6. **[メンバー]** をクリックします。
7. 前の手順で作成した最初のユーザーを選択し、**[選択]** をクリックします。
8. 同じ手順を繰り返して、**HiveUsers** という名前のもう 1 つのグループを作成し、そのグループに 2 つの Hive ユーザーを追加します。

詳細については、[Azure AD ドメイン サービス (プレビュー) に関するページにある、"AAD DC 管理者" グループの作成](../../active-directory-domain-services/active-directory-ds-getting-started.md)についてのセクションを参照してください。

**Azure AD 用の Azure AD DS を有効化するには**

1. [Azure ポータル](https://portal.azure.com)で、**[リソースの作成]** > **[セキュリティ + ID]** > **[Azure AD Domain Services]** > **[追加]** をクリックします。 
2. 次の値を入力または選択します。
   * **ディレクトリ名**: contosoaaddirectory
   * **[DNS ドメイン名]**: ここには、Azure ディレクトリの既定の DNS 名が表示されます。 たとえば、contoso.onmicrosoft.com です。
   * **場所**: リージョンを選択します。
   * **ネットワーク**: 先ほど作成した仮想ネットワークとサブネットを選択します。 例: **contosohdivnet**
3. [概要] ページで **[OK]** をクリックします。 通知の下に **[デプロイを実行しています...]** が表示されます。
4. **[デプロイを実行しています...]** が消えるまで待つと、**[IP アドレス]** に値が設定されます。 2 つの IP アドレスに、値が設定されます。 これらは、ドメイン サービスによってプロビジョニングされるドメイン コントローラーの IP アドレスです。 該当するドメイン コントローラーがプロビジョニングされ、準備が完了した後で、各 IP アドレスが表示されます。 2 つの IP アドレスを書き留めておきます。 この情報は後で必要になります。

詳細については、「[Azure Portal を使用して Azure Active Directory Domain Services を有効にする](../../active-directory-domain-services/active-directory-ds-getting-started.md)」を参照してください。

**パスワードを同期するには**

独自のドメインを使用する場合は、パスワードを同期する必要があります。 [クラウド専用 Azure AD ディレクトリでの Azure AD Domain Services に対するパスワード同期の有効化](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)に関するページを参照してください。

**Azure AD の LDAPS を構成するには**

1. ドメインの署名機関によって署名された SSL 証明書を取得します。
2. [Azure ポータル](https://portal.azure.com)で、**[Azure AD Domain Services]** > **[contoso.onmicrosoft.com]** をクリックします。 
3. **[セキュリティで保護された LDAP]** を有効にします。
6. 指示に従って、証明書ファイルとパスワードを指定します。  
7. **[Secure LDAP 証明書]** に値が設定されるまで待ちます。 10 分以上かかる場合があります。

> [!NOTE]
> Azure AD DS でいくつかのバックグラウンド タスクが実行中の場合、証明書のアップロード中に、<i>"There is an operation being performed for this tenant.Please try again later (このテナントに対して実行中の操作があります。後でやり直してください)"</i> というエラーが表示されることがあります。  このエラーが発生した場合は、しばらくしてからやり直してください。 2 番目のドメイン コントローラーの IP は、プロビジョニングされるまでに最大で 3 時間かかる場合があります。
> 
> 

詳細については、「[Azure AD ドメイン サービスの管理対象ドメインに対するセキュリティで保護された LDAP (LDAPS) の構成](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)」を参照してください。

## <a name="create-hdinsight-cluster"></a>HDInsight クラスターの作成
このセクションでは、Azure Portal または [Azure Resource Manager テンプレート](../../azure-resource-manager/resource-group-template-deploy.md)を利用して、HDInsight で Linux ベースの Hadoop クラスターを作成します。 その他のクラスター作成方法と設定の詳細については、「 [HDInsight での Linux ベースの Hadoop クラスターの作成](../hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。 Resource Manager テンプレートを利用して HDInsight で Hadoop クラスターを作成する方法の詳細については、[Resource Manager テンプレートを使用した HDInsight での Hadoop クラスターの作成](../hdinsight-hadoop-create-windows-clusters-arm-templates.md)に関するページを参照してください。

**Azure Portal を使用して、ドメイン参加済み HDInsight クラスターを作成するには**

1. [Azure Portal](https://portal.azure.com) にサインオンします。
2. **[新規]**、**[インテリジェンス + 分析]**、**[HDInsight]** の順にクリックします。
3. **[新しい HDInsight クラスター]** ブレードで、次の値を入力または選択します。
   
   * **クラスター名**: ドメイン参加済み HDInsight クラスターの新しいクラスター名を入力します。
   * **サブスクリプション**: このクラスターの作成に使用する Azure サブスクリプションを選択します。
   * **クラスター構成**:
     
     * **クラスターの種類**: Hadoop。 ドメイン参加済み HDInsight は、現在 Hadoop, Spark、および Interactive Query クラスターのみでサポートされています。
     * **オペレーティング システム**: Linux。  ドメイン参加済み HDInsight は、Linux ベースの HDInsight クラスターのみでサポートされています。
     * **バージョン**: HDI 3.6。 ドメイン参加済み HDInsight は、HDInsight クラスター バージョン 3.6 のみでサポートされています。
     * **クラスターの種類**: PREMIUM
       
       **[選択]** をクリックして変更を保存します。
   * **資格情報**: クラスター ユーザーと SSH ユーザーの両方の資格情報を構成します。
   * **データ ソース**: HDInsight クラスターの既定のストレージ アカウントとして、新しいストレージ アカウントを作成するか、既存のストレージ アカウントを使用します。 場所は、2 つの VNet と同じである必要があります。  また、場所は HDInsight クラスターの場所でもあります。
   * **価格**: クラスターのワーカー ノードの数を選択します。
   * **Advanced configurations (詳細な構成)**: 
     
     * **Domain-joining & Vnet/Subnet (ドメイン参加と Vnet/サブネット)**: 
       
       * **ドメイン設定**: 
         
         * **ドメイン名**: contoso.onmicrosoft.com
         * **ドメイン ユーザー名**: ドメイン ユーザー名を入力します。 このドメインは、次の特権を持つ必要があります。コンピューターをドメインに参加させ、クラスター作成時に指定した組織単位に配置する特権。クラスター作成時に指定した組織単位内でサービス プリンシパルを作成する特権。および、逆引き DNS エントリを作成する特権です。 このドメイン ユーザーは、このドメイン参加済み HDInsight クラスターの管理者になります。
         * **ドメイン パスワード**: ドメイン ユーザーのパスワードを入力します。
         * **組織単位**: HDInsight クラスターで使用する OU の識別名を入力します。 例: OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com。この OU が存在しない場合は、HDInsight クラスターがこの OU を作成しようとします。 OU が既に存在するか、ドメイン アカウントが新しい OU を作成するアクセス許可を持っていることを確認してください。 AADDC 管理者の一部であるドメイン アカウントを使用する場合は、OU を作成するために必要なアクセス許可を持っています。
         * **LDAPS URL**: ldaps://contoso.onmicrosoft.com:636
         * **アクセス ユーザー グループ**: クラスターに同期させるユーザーが属しているセキュリティ グループを指定します。 たとえば、HiveUsers です。
           
           **[選択]** をクリックして変更を保存します。
           
           ![ドメイン参加済み HDInsight のポータルでのドメイン設定の構成](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-domain-setting.png)
       * **仮想ネットワーク**: contosohdivnet
       * **サブネット**: Subnet1
         
         **[選択]** をクリックして変更を保存します。        
         **[選択]** をクリックして変更を保存します。
   * **リソース グループ**: HDInsight VNet (contosohdirg) 用に使用されるリソース グループを選択します。
4. **Create** をクリックしてください。  

ドメイン参加済み HDInsight クラスターを作成するためのもう 1 つのオプションは、Azure Resource Manager テンプレートを使用する方法です。 次の手順は、その方法を示しています。

**Resource Manager テンプレートを使用してドメイン参加済み HDInsight クラスターを作成するには**

1. 次の画像をクリックして、Azure Portal で Resource Manager テンプレートを開きます。 Resource Manager テンプレートは、パブリック BLOB コンテナー内にあります。 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="./media/apache-domain-joined-configure/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. **[パラメーター]** ブレードで、次の各項目を入力します。
   
   * **サブスクリプション**: (Azure サブスクリプションを選択します)。
   * **リソース グループ**: **[既存のものを使用]** をクリックして、使用してきた同じリソース グループを指定します。  たとえば contosohdirg です。 
   * **場所**: リソース グループの場所を指定します。
   * **クラスター名**: 作成する Hadoop クラスターの名前を入力します。 たとえば contosohdicluster です。
   * **クラスターの種類**: クラスターの種類を選択します。  既定値は **hadoop** です。
   * **場所**: クラスターの場所を選択します。  既定のストレージ アカウントは、同じ場所を使用します。
   * **Cluster Worker Node count (クラスターのワーカー ノードの数)**: ワーカー ノードの数を選択します。
   * **クラスターのログイン名とパスワード**: 既定のログイン名は **admin** です。
   * **SSH ユーザー名とパスワード**: 既定のユーザー名は **sshuser** です。  この名前は変更できます。 
   * **仮想ネットワーク ID**: /subscriptions/&lt;SubscriptionID>/resourceGroups/&lt;ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/&lt;VNetName>
   * **Virtual Network Subnet (仮想ネットワークのサブネット)**: /subscriptions/&lt;SubscriptionID>/resourceGroups/&lt;ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/&lt;VNetName>/subnets/Subnet1
   * **ドメイン名**: contoso.onmicrosoft.com
   * **Organization Unit DN (組織単位 DN)**: OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com
   * **クラスター ユーザー グループ DN**: [\"HiveUsers\"]
   * **LDAPUrls**: ["ldaps://contoso.onmicrosoft.com:636"]
   * **DomainAdminUserName**: (ドメイン管理者のユーザー名を入力します)
   * **DomainAdminPassword**: (ドメイン管理者のユーザー パスワードを入力します)
   * **上記の使用条件に同意する**: (オン)
   * **ダッシュボードにピン留めする**: (オン)
3. **[購入]** をクリックします。 " **テンプレートのデプロイのデプロイ中**" という新しいタイルが表示されます。 クラスターの作成には約 20 分かかります。 クラスターが作成されたら、ポータルのクラスター ブレードをクリックして開きます。

チュートリアルを完了したら、必要に応じてクラスターを削除できます。 HDInsight を使用すると、データは Azure Storage に格納されるため、クラスターは、使用されていない場合に安全に削除できます。 また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。 クラスターの削除手順については、「[Azure Portal を使用した HDInsight での Hadoop クラスターの管理](../hdinsight-administer-use-management-portal.md#delete-clusters)」を参照してください。

## <a name="next-steps"></a>次のステップ
* Hive ポリシーの構成と Hive クエリの実行については、[ドメイン参加済み HDInsight クラスターの Hive ポリシーの構成](apache-domain-joined-run-hive.md)に関する記事をご覧ください。
* SSH を使用してドメイン参加済み HDInsight クラスターに接続する方法については、「[Linux、Unix、または OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)」を参照してください。

