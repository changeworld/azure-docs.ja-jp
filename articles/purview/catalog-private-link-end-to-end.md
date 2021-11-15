---
title: プライベートかつ安全な Azure Purview への接続とデータ ソースのスキャン
description: この記事では、Purview アカウントに接続し、制限されたネットワークからデータソースをスキャンして、エンド ツー エンドの分離を確保するためのプライベート エンドポイントの設定方法について説明します
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 2d0a74b5674bba6850cbe38f1811c1204acb5e69
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848213"
---
# <a name="connect-to-your-azure-purview-and-scan-data-sources-privately-and-securely"></a>プライベートかつ安全な Azure Purview への接続とデータ ソースのスキャン

このガイドでは、Purview アカウントにアクセスし、セルフホステッド統合ランタイムを使用して安全かつプライベートにデータ ソースをスキャンし、エンド ツー エンドのネットワークの分離を可能にするために、Azure Purview アカウントの "_アカウント_"、"_ポータル_"、"_インジェスト_" プライベート エンドポイントをデプロイする方法について説明します。

Azure Purview の "_アカウント_" プライベート エンドポイントは、仮想ネットワーク内から発信されたクライアント呼び出しのみが Azure Purview アカウントへのアクセスを許可されるシナリオを可能にすることで、セキュリティを強化するために使用されます。 このプライベート エンドポイントは、ポータルのプライベート エンドポイントの前提条件でもあります。

Azure Purview の "_ポータル_" プライベート エンドポイントは、プライベート ネットワークを使用して [Azure Purview Studio](https://web.purview.azure.com/resource/) に接続できるようにするために必要です。

Azure Purview では、"_インジェスト_" プライベート エンドポイントを使用して、Azure またはオンプレミス環境のデータ ソースをスキャンできます。 インジェスト プライベート エンドポイントが作成されると、3 つのプライベート エンドポイント リソースをデプロイして、Azure Purview 管理対象リソースにリンクする必要があります。

 - BLOB プライベート エンドポイントは、Azure Purview マネージド ストレージ アカウントにリンクされます。
 - キュー プライベート エンドポイントは、Azure Purview マネージド ストレージ アカウントにリンクされます。
 - 名前空間プライベート エンドポイントは、Azure Purview マネージド Event Hub 名前空間にリンクされます。

  :::image type="content" source="media/catalog-private-link/purview-private-link-architecture.png" alt-text="Azure Purview と Private Link アーキテクチャを示す図。":::

## <a name="deployment-checklist"></a>展開のチェックリスト
このガイドで詳細に説明するデプロイ オプションの 1 つを使用することにより、"_アカウント_"、"_ポータル_"、_インジェスト_ プライベート エンドポイントを使用して新しい Azure Purview アカウントをデプロイするか、既存の Azure Purview アカウントのこれらのプライベート エンドポイントをデプロイするかを選択できます。

1. Azure Purview プライベート エンドポイントをデプロイするのに適した Azure 仮想ネットワークとサブネットを選択します。 以下のオプションの 1 つを選択します。
   - お使いの Azure サブスクリプションに[新しい仮想ネットワーク](../virtual-network/quick-create-portal.md)をデプロイします。
   - Azure サブスクリプションで既存の Azure 仮想ネットワークとサブネットを検索します。
  
2. 適切な [DNS 名前解決方法](./catalog-private-link-name-resolution.md#deployment-options)を定義して、プライベート ネットワークを使用して Azure Purview アカウントにアクセスし、データソースをスキャンできるようにします。 次のオプションをどれでも使用できます。
   - このガイドで詳細に説明する手順を使用して、新しい Azure DNS ゾーンをデプロイします。
   - このガイドで詳細に説明する手順を使用して、必要な DNS レコードを既存の Azure DNS ゾーンに追加します。
   - このガイドの手順を完了したら、必要な DNS A レコードを既存の DNS サーバーに手動で追加します。
3. アカウント、ポータル、およびインジェスト プライベート エンドポイントを使用して[新しい Purview アカウント](#option-1---deploy-a-new-azure-purview-account-with-account-portal-and-ingestion-private-endpoints)をデプロイするか、[既存の Purview アカウント](#option-2---enable-account-portal-and-ingestion-private-endpoint-on-existing-azure-purview-accounts)のプライベート エンドポイントをデプロイします。
4. プライベート ネットワークに、すべてのパブリック インターネット トラフィックを拒否するように設定されたネットワーク セキュリティ グループ ルールがある場合、[Azure Active Directory へのアクセスを有効にします](#enable-access-to-azure-active-directory)。
5. Azure Purview インジェスト プライベート エンドポイントがデプロイされているのと同じ VNet 内に[セルフホステッド統合ランタイム](#deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources)をデプロイして登録します。
6. このガイドを完了したら、必要に応じて、DNS 構成を調整します。
7. 管理マシン、セルフホステッド IR VM、Azure Purview へのデータソース間のネットワークと名前解決を検証します。 

## <a name="option-1---deploy-a-new-azure-purview-account-with-_account_-_portal_-and-_ingestion_-private-endpoints"></a>オプション 1 - "_アカウント_"、"_ポータル_"、_インジェスト_ プライベート エンドポイントを使用して新しい Azure Purview アカウントをデプロイする

1. [Azure portal](https://portal.azure.com) に移動し、 **[Purview アカウント]** に移動します。 **[+ 作成]** を選択して、新しい Azure Purview アカウントを作成します。

2. 基本情報を入力し、 **[ネットワーク]** タブで、接続方法を **[プライベート エンドポイント]** に設定します。 有効にするプライベート エンドポイントを **[Account, Portal and ingestion]\(アカウント、ポータル、インジェスト\)** に設定します。

3. **[アカウントとポータル]** で、 **[+ 追加]** を選択して、お使いの Azure Purview アカウントのプライベート エンドポイントを追加します。

   :::image type="content" source="media/catalog-private-link/purview-pe-deploy-end-to-end.png" alt-text="プライベート エンドポイントのエンド ツー エンドを作成するページの選択項目を示すスクリーンショット。":::

4. **[プライベート エンドポイントの作成]** ページの **[Purview サブリソース]** で、場所を選択し、"_アカウント_" プライベート エンドポイントの名前を指定し、 **[アカウント]** を選択します。 **[ネットワーク]** で、お使いの仮想ネットワークとサブネットを選択し、必要に応じて、 **[プライベート DNS ゾーンと統合する]** をオンにして新しい Azure プライベート DNS ゾーンを作成します。 

   :::image type="content" source="media/catalog-private-link/purview-pe-deploy-account.png" alt-text="アカウントのプライベート エンドポイントを作成するページを示すスクリーンショット。":::

   > [!NOTE]
   > また、既存の Azure プライベート DNS ゾーンを使用することも、後で DNS サーバーに DNS レコードを手動で作成することもできます。 詳細については、[プライベート エンドポイントの DNS 名前解決の構成](./catalog-private-link-name-resolution.md)に関するページを参照してください

5. **[OK]** を選択します。
   
6. **[アカウントとポータル]** ウィザードで、 **[+ 追加]** を再度選択して、"_ポータル_" プライベート エンドポイントを追加します。 
  
7. **[プライベート エンドポイントの作成]** ページの **[Purview サブリソース]** で、場所を選択し、"_ポータル_" プライベート エンドポイントの名前を指定し、 **[ポータル]** を選択します。 **[ネットワーク]** で、お使いの仮想ネットワークとサブネットを選択し、必要に応じて、 **[プライベート DNS ゾーンと統合する]** をオンにして新しい Azure プライベート DNS ゾーンを作成します。 

   :::image type="content" source="media/catalog-private-link/purview-pe-deploy-portal.png" alt-text="ポータルのプライベート エンドポイント ページの作成を示すスクリーンショット。":::
   
   > [!NOTE]
   > また、既存の Azure プライベート DNS ゾーンを使用することも、後で DNS サーバーに DNS レコードを手動で作成することもできます。 詳細については、[プライベート エンドポイントの DNS 名前解決の構成](./catalog-private-link-name-resolution.md)に関するページを参照してください

8. **[OK]** を選択します。

9.  **[インジェスト]** で、プライベート エンドポイントとペアリングする **[サブスクリプション]** 、 **[仮想ネットワーク]** 、 **[サブネット]** の詳細を指定して、インジェスト プライベート エンドポイントを設定します。

10. 必要に応じて、 **[プライベート DNS の統合]** をオンにして、Azure プライベート DNS ゾーンを使用します。
   
      :::image type="content" source="media/catalog-private-link/purview-pe-deploy-ingestion.png" alt-text="プライベート エンドポイントの概要を作成するページを示すスクリーンショット。":::

      > [!IMPORTANT]
      > Azure Purview とデータ ソース間で正しい名前解決を可能にするために、正しい Azure プライベート DNS ゾーンを選択することが重要です。 また、既存の Azure プライベート DNS ゾーンを使用することも、後で DNS サーバーに DNS レコードを手動で作成することもできます。 詳細については、[プライベート エンドポイントの DNS 名前解決の構成](./catalog-private-link-name-resolution.md)に関するページを参照してください。

11. **[確認および作成]** を選択します。 **[確認および作成]** ページで、Azure によって構成が検証されます。

12. "検証に成功しました" というメッセージが表示されたら、 **[作成]** を選択します。
   

## <a name="option-2---enable-_account_-_portal_-and-_ingestion_-private-endpoint-on-existing-azure-purview-accounts"></a>オプション 2 - 既存の Azure Purview アカウントで "_アカウント_"、"_ポータル_"、"_インジェスト_" プライベート エンドポイントを有効にする

1. [Azure portal](https://portal.azure.com) に移動し、お使いの Azure Purview アカウントを選択して、 **[設定]** で **[ネットワーク]** を選択し、 **[プライベート エンドポイント接続]** を選択します。

    :::image type="content" source="media/catalog-private-link/purview-pe-add-to-existing.png" alt-text="アカウントのプライベート エンドポイントの作成方法を示すスクリーンショット。":::

2. **[+ プライベート エンドポイント]** を選択して、新しいプライベート エンドポイントを作成します。

3. 基本情報を入力します。

4. **[リソース]** タブで、 **[リソースの種類]** として **[Microsoft.Purview/accounts]** を選択します。

5. **[リソース]** として Azure Purview アカウントを選択し、 **[対象サブリソース]** として **[アカウント]** を選択します。

6. **[構成]** タブで仮想ネットワークを選択し、必要に応じて、Azure プライベート DNS ゾーンを選択して新しい Azure DNS ゾーンを作成します。
   
   > [!NOTE]
   > DNS 構成の場合、ドロップダウン リストから既存の Azure プライベート DNS ゾーンを使用することも、後で必要な DNS レコードを DNS サーバーに手動で追加することもできます。 詳細については、[プライベート エンドポイントの DNS 名前解決の構成](./catalog-private-link-name-resolution.md)に関するページを参照してください

7. [概要] ページに移動し、 **[作成]** を選択してポータルのプライベート エンドポイントを作成します。
   
8. **[対象サブリソース]** に **[ポータル]** を選択した場合も同じ手順に従います。
   
9. Azure Purview アカウントの **[設定]** で **[ネットワーク]** を選択し、 **[Ingestion private endpoint connections]\(インジェスト プライベート エンドポイント接続\)** を選択します。

10. [Ingestion private endpoint connections]\(インジェスト プライベート エンドポイント接続\) で、 **[+ 新規]** を選択して新しいインジェスト プライベート エンドポイントを作成します。

      :::image type="content" source="media/catalog-private-link/purview-pe-add-ingestion-to-existing.png" alt-text="プライベート エンドポイントを既存のアカウントに追加する方法を示すスクリーンショット。":::

11. 基本情報を入力し、既存の仮想ネットワークとサブネットの詳細を選択します。 必要に応じて、 **[プライベート DNS の統合]** をオンにして、Azure プライベート DNS ゾーンを使用します。 各一覧から適切な Azure プライベート DNS ゾーンを選択します。

      > [!NOTE]
      > また、既存の Azure プライベート DNS ゾーンを使用することも、後で DNS サーバーに DNS レコードを手動で作成することもできます。 詳細については、[プライベート エンドポイントの DNS 名前解決の構成](./catalog-private-link-name-resolution.md)に関するページを参照してください

12. **[作成]** を選択して設定を終了します。

## <a name="enable-access-to-azure-active-directory"></a>Azure Active Directory へのアクセスを有効にする

> [!NOTE]
> VM、VPN ゲートウェイ、または VNet ピアリング ゲートウェイは、パブリック インターネットにアクセスできる場合、Azure Purview ポータルと、プライベート エンドポイントで有効になっている Azure Purview アカウントにアクセスできます。 このため、残りの手順に従う必要はありません。 プライベート ネットワークで、ネットワーク セキュリティ グループの規則がすべてのパブリック インターネット トラフィックを拒否するように設定されている場合、Azure Active Directory (Azure AD) アクセスを有効にするためにいくつかの規則を追加する必要があります。 そのためには、この手順に従ってください。

これらの手順は、Azure VM から Azure Purview に安全にアクセスするために提供されています。 VPN または他の VNet ピアリング ゲートウェイを使用している場合も同様の手順に従う必要があります。

1. Azure portal で自分の VM に移動し、 **[設定]** の **[ネットワーク]** を選択します。 次に、 **[送信ポートの規則]**  >  **[送信ポートの規則を追加する]** を選択します。

   :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="アウトバウンド規則の追加を示すスクリーンショット。":::

1. **[送信セキュリティ規則の追加]** ペインで:

   1. **[宛先]** で **[サービス タグ]** を選択します。
   1. **[宛先サービス タグ]** で、 **[AzureActiveDirectory]** を選択します。
   1. **[宛先ポート範囲]** で、[*] を選択します。
   1. **[アクション]** で、 **[許可]** を選択します。
   1. **[優先度]** の値は、すべてのインターネット トラフィックを拒否した規則よりも高くする必要があります。
   
   ルールを作成します。

   :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="アウトバウンド規則の詳細の追加を示すスクリーンショット。":::

1. 同じ手順に従って、**AzureResourceManager** サービス タグを許可する別の規則を作成します。 Azure portal にアクセスする必要がある場合は、**AzurePortal** サービス タグの規則も追加できます。

1. VM に接続し、ブラウザーを開きます。 Ctrl + Shift + J キーを押してブラウザー コンソールに移動し、[ネットワーク] タブに切り替えてネットワーク要求を監視します。 URL ボックスに「web.purview.azure.com」と入力し、ご自分の Azure AD の資格情報を使用してサインインを試みます。 サインインはおそらく失敗します。コンソールの **[ネットワーク]** タブで、Azure AD が aadcdn.msauth.net にアクセスする試みがブロックされたことを確認できます。

   :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="サインインの失敗の詳細を示すスクリーンショット。":::

1. この場合、VM でコマンド プロンプトを開き、aadcdn.msauth.net に ping を実行し、その IP を取得してから、VM のネットワーク セキュリティ規則で IP の送信ポート規則を追加します。 **[宛先]** を **[IP アドレス]** に設定し、 **[宛先 IP アドレス]** を aadcdn の IP に設定します。 Azure Load Balancer と Azure Traffic Manager のため、Azure AD Content Delivery Network の IP は動的である可能性があります。 その IP を取得した後、それを VM のホスト ファイルに追加して、ブラウザーがその IP にアクセスして、Azure AD Content Delivery Network を取得するように強制することをお勧めします。

   :::image type="content" source="media/catalog-private-link/ping.png" alt-text="テスト ping を示すスクリーンショット。":::

   :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="Azure AD Content Delivery Network 規則を示すスクリーンショット。":::

1. 新しい規則が作成された後、VM に戻り、お使いの Azure AD の資格情報を再度使用してサインインを試みます。 サインインに成功すると、Azure Purview ポータルを使用できるようになります。 ただし、Azure AD は、顧客のアカウントの種類に基づいてサインインするために、他のドメインにリダイレクトする場合があります。 たとえば、live.com アカウントの場合、Azure AD は、live.com にリダイレクトしてサインインします。その後、それらの要求は再度ブロックされます。 Microsoft 従業員アカウントの場合、Azure AD から、msft.sts.microsoft.com にアクセスが行われてサインイン情報が取得されます。

   ブラウザーの **[ネットワーク]** タブでネットワーク要求をチェックして、ブロックされているドメインの要求を確認し、前の手順を再実行して IP を取得し、その IP の要求を許可するための送信ポート規則をネットワーク セキュリティ グループに追加します。 可能な場合は、URL と IP を VM のホスト ファイルに追加して DNS 解決を修正します。 サインイン ドメインの正確な IP 範囲がわかっている場合は、それらをネットワーク規則に直接追加することもできます。

1. これで、Azure AD のサインインが成功するはずです。 Azure Purview ポータルは正常に読み込まれますが、特定の Azure Purview アカウントにしかアクセスできないため、すべての Azure Purview アカウントの一覧表示は機能しません。 「`web.purview.azure.com/resource/{PurviewAccountName}`」と入力して、プライベート エンドポイントを正常に設定した Azure Purview アカウントに直接アクセスします。

## <a name="deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources"></a>セルフホステッド統合ランタイム (IR) をデプロイし、データ ソースをスキャンします。
Azure Purview のインジェスト プライベート エンドポイントをデプロイした後、1 つ以上のセルフホステッド統合ランタイム (IR) を設定して登録する必要があります。

- 現在、Microsoft SQL Server、Oracle、SAP など、オンプレミスのすべての種類のソースは、セルフホステッド IR ベースのスキャンでのみサポートされています。 セルフホステッド IR は、プライベート ネットワーク内で実行し、Azure 内の自分の仮想ネットワークとピアリングする必要があります。 
   
- Azure Blob Storage や Azure SQL Database などのすべての種類の Azure ソースについては、Azure Purview インジェスト プライベート エンドポイントと同じ VNet にデプロイされているセルフホステッド統合ランタイムを使用して、スキャンの実行を明示的に選択する必要があります。 

「[セルフホステッド統合ランタイムの作成および管理](manage-integration-runtimes.md)」の手順に従って、セルフホステッド IR を設定します。 その後、 **[統合ランタイム経由で接続]** ドロップダウンでそのセルフホステッド IR を選択して Azure ソースでのスキャンを設定し、ネットワークの分離を確保します。
    
   :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="セルフホステッド IR を使用して Azure スキャンを実行する方法を示すスクリーンショット。":::

> [!IMPORTANT]
> 必ず [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=39717)から最新バージョンのセルフホステッド統合ランタイムをダウンロードしてインストールしてください。

## <a name="firewalls-to-restrict-public-access"></a>パブリック アクセスを制限するファイアウォール

パブリック インターネットからの Azure Purview アカウントへのアクセスを完全に遮断するには、これらの手順に従います。 この設定は、プライベート エンドポイントとインジェスト プライベート エンドポイントの両方の接続に適用されます。

1. Azure portal から Azure Purview アカウントに移動し、 **[設定]**  >  **[ネットワーク]** で **[プライベート エンドポイント接続]** を選択します。

1. **[ファイアウォール]** タブに移動し、トグルが **[拒否]** に設定されていることを確認します。

   :::image type="content" source="media/catalog-private-link/private-endpoint-firewall.png" alt-text="プライベート エンドポイントのファイアウォール設定を示すスクリーンショット。":::

## <a name="next-steps"></a>次の手順

-  [プライベート エンドポイントの解決を検証する](./catalog-private-link-name-resolution.md)
-  [Azure Purview でデータ ソースを管理する](./manage-data-sources.md)
-  [Azure Purview アカウントのプライベート エンドポイント構成のトラブルシューティング](./catalog-private-link-troubleshoot.md)
