---
title: Azure および Azure Stack を使用して地理的分散アプリ ソリューションを作成する | Microsoft Docs
description: Azure および Azure Stack を使用して地理的分散アプリ ソリューションを作成する方法について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: f1151c845797d74bbb9a5e50feeeb288a4ab349b
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714850"
---
# <a name="tutorial-create-a-geo-distributed-app-solution-with-azure-and-azure-stack"></a>チュートリアル: Azure と Azure Stack を使用して地理的に分散されたアプリ ソリューションを作成する

*適用先:Azure Stack 統合システムと Azure Stack Development Kit*

地理的分散アプリ パターンを使用して、さまざまなメトリックに基づき、特定のエンドポイントにトラフィックを送信する方法について説明します。 地理的ベースのルーティングとエンドポイント構成で Traffic Manager プロファイルを作成すると、リージョンの要件、企業および国際的な規制、およびデータ ニーズに基づいて、情報がエンドポイントにルーティングされます。

このチュートリアルでは、以下を実現するためのサンプル環境を構築します。

> [!div class="checklist"]
> - 地理的分散アプリを作成します。
> - Traffic Manager を使用して、アプリを対象にします。

## <a name="use-the-geo-distributed-apps-pattern"></a>地理的分散アプリ パターンを使用する

地理的分散パターンを使用すると、アプリは複数のリージョンに及ぶことができます。 パブリック クラウドを既定として設定できますが、ユーザーの中には自身のリージョンにデータを残す必要のある場合もあります。 ユーザーをその要件に基づいて最も適したクラウドに送信できます。

### <a name="issues-and-considerations"></a>問題と注意事項

#### <a name="scalability-considerations"></a>スケーラビリティに関する考慮事項

このチュートリアルで作成するソリューションは、スケーラビリティに対応しません。 ただし、他の Azure とオンプレミスのテクノロジとソリューションと組み合わせて使用すれば、スケーラビリティ要件に対応できます。 Traffic Manager を介した自動スケーリングを伴うハイブリッド ソリューションの作成に関する詳細については、「[Azure でクラウド間スケーリング ソリューションを作成する](azure-stack-solution-cloud-burst.md)」を参照してください。

#### <a name="availability-considerations"></a>可用性に関する考慮事項

スケーラビリティに関する考慮事項と同様に、このソリューションでは可用性を直接扱いません。 ただし、当社のスケーラビリティに関する考慮事項と同様に、Azure およびオンプレミスのテクノロジとソリューションをこのソリューション内に実装して、関連するすべてのコンポーネントの高可用性を確保できます。

### <a name="when-to-use-this-pattern"></a>このパターンを使用する状況

- 組織には、リージョンのセキュリティおよび分散に関するカスタム ポリシーが必要な支店が世界中にあります。

- 組織の支店それぞれは、従業員、ビジネス、および施設のデータをプルしますが、これには地域の規制やタイム ゾーンに従ったレポート アクティビティが必要になります。

- 高スケール要件を満たすには、極端に負荷の大きい要件に対応できるように、単一のリージョン内、または複数のリージョンにわたって、複数のアプリ デプロイメントを使用してアプリを水平方向に拡張する必要があります。

### <a name="planning-the-topology"></a>トポロジの計画

分散アプリのフットプリントを構築する前に、次の知識を得ておくと作業がスムーズになります。

-   **アプリのカスタム ドメイン:** 顧客がアプリへのアクセスに使用するカスタム ドメイン名が必要です。 サンプル アプリでは、カスタム ドメイン名は *www.scalableasedemo.com.* です。

-   **Traffic Manager ドメイン:** [Azure Traffic Manager プロファイル](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-manage-profiles)の作成時に、ドメイン名を選択する必要があります。 この名前は、Traffic Manager が管理するドメイン エントリを登録する際に、 *trafficmanager.net* サフィックスと組み合わされます。 サンプル アプリでは、選択される名前は *scalable-ase-demo*です。 そのため、Traffic Manager で管理される完全なドメイン名は、*scalable-ase-demo.trafficmanager.net* になります。

-   **アプリ フットプリントのスケーリングに関する戦略:** アプリケーションのフットプリントは単一リージョン内の複数の App Service Environment に分散されるのか、 複数のリージョンなのか、 両方のアプローチの組み合わせか、 この決定は、顧客のトラフィックが発生する場所に加えて、アプリをサポートするバックエンド インフラストラクチャの他の要素のスケーラビリティに関する期待事項に基づく必要があります。 たとえば、完全にステートレスなアプリケーションでは、各 Azure リージョンで複数の App Service Environment を組み合わせ、さらに複数の Azure リージョンにデプロイされた App Service Environment を掛け合わせることで、大規模なスケーリングを実施できます。 選択できるグローバルな Azure リージョンは 15 以上あるため、顧客はスケーラビリティのきわめて高いアプリケーション フットプリントを世界規模で構築できます。 この記事のサンプル アプリでは、単一の Azure リージョン (米国中南部) に 3 つの App Service 環境が作成されています。

-   **App Service Environment の名前付け規則:** 各 App Service Environment には一意の名前が必要です。 1 つや 2 つではなく数の多い App Service 環境では、各 App Service 環境を識別しやすい命名規則があると便利です。 サンプル アプリでは、シンプルな命名規則が使用されています。 3 つの App Service Environment の名前は *fe1ase*、*fe2ase*、*fe3ase* です。

-   **アプリの名前付け規則**:アプリのインスタンスが複数デプロイされるため、デプロイされたアプリの各インスタンスに名前が必要です。 App Service 環境の場合、同じアプリ名を複数の App Service 環境で使用できます。 App Service 環境ごとに一意のドメイン サフィックスがあるため、開発者は各環境でまったく同じアプリ名を再利用できます。 たとえば、開発者は、*myapp.foo1.p.azurewebsites.net*、*myapp.foo2.p.azurewebsites.net*、*myapp.foo3.p.azurewebsites.net* のようなアプリ名を設定できます。このシナリオのアプリでは、各アプリ インスタンスに一意の名前が付けられます。 使用されているアプリ インスタンス名は *webfrontend1*、*webfrontend2*、*webfrontend3* です。

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack は Azure の拡張機能です。 Azure Stack は、オンプレミスの環境にクラウド コンピューティングの俊敏性とイノベーションを提供します。これにより、どこでもハイブリッド アプリをビルドしてデプロイできる唯一のハイブリッド クラウドが実現されます。  
> 
> [ハイブリッド アプリケーションのための設計の考慮事項](https://aka.ms/hybrid-cloud-applications-pillars)に関するホワイト ペーパーでは、ハイブリッド アプリケーションを設計、デプロイ、および運用するためのソフトウェア品質の重要な要素 (配置、スケーラビリティ、可用性、回復性、管理容易性、およびセキュリティ) についてレビューしています。 これらの設計の考慮事項は、ハイブリッド アプリケーションの設計を最適化したり、運用環境での課題を最小限に抑えたりするのに役立ちます。

## <a name="part-1-create-a-geo-distributed-app"></a>パート 1:地理的分散アプリを作成する

このパートでは、Web アプリを作成します。

> [!div class="checklist"]
> - Web アプリを作成し発行する
> - Azure Repos にコードを追加する
> - アプリ ビルドを複数のクラウド ターゲットにポイントします。
> - CD プロセスを管理および構成する

### <a name="prerequisites"></a>前提条件

Azure サブスクリプションと Azure Stack のインストールが必要です。

### <a name="geo-distributed-app-steps"></a>地理的に分散されたアプリの手順

### <a name="obtain-a-custom-domain-and-configure-dns"></a>カスタム ドメインを取得し DNS を構成する

Web アプリ作成用の DNS ゾーン ファイルを更新し、ドメインを発行します。 Azure AD は続いて、カスタム ドメイン名の所有権を確認できます。 Azure 内の Azure/Office 365/外部 DNS レコードに [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) を使用するか、または[別の DNS レジストラー](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)で DNS エントリを追加します。

1. パブリック レジストラーでカスタム ドメインを登録します。

2. ドメインのドメイン名レジストラーにサインインします。 DNS の更新を行うには、承認された管理者が必要になることがあります。

3. Azure AD から提供された DNS エントリを追加して、ドメインの DNS ゾーン ファイルを更新します。 メール ルーティングや Web ホスティングなどの動作は変更されません。

### <a name="create-web-apps-and-publish"></a>Web アプリを作成し発行する

ハイブリッド CI/CD を設定して、Web アプリを Azure および Azure Stack にデプロイし、両方のクラウドに変更を自動プッシュします。

> [!Note]  
> (Windows Server と SQL の) 実行および App Service のデプロイには、適切なイメージがシンジケート化された Azure Stack が必要です。 App Service ドキュメントの「[Azure Stack 上の App Service を開始する前に](/articles/azure-stack/azure-stack-app-service-before-you-get-started)」の Azure Stack オペレーター用セクションを参照してください。

#### <a name="add-code-to-azure-repos"></a>Azure Repos にコードを追加する

1. Azure Repos 上で**プロジェクト作成権限が付与されているアカウント**を使用して、Visual Studio にサインインします。

    ハイブリッドの継続的インテグレーション/継続的デリバリー (CI/CD) は、アプリケーション コードとインフラストラクチャ コードの両方に適用できます。 プライベート クラウド開発とホステッド クラウド開発の両方に、[Azure Resource Manager テンプレート](https://azure.microsoft.com/resources/templates/)を使用します。

    ![Alt text](media/azure-stack-solution-geo-distributed/image1.JPG)

2. 既定の Web アプリを作成して開くことで、**リポジトリを複製**します。

    ![Alt text](media/azure-stack-solution-geo-distributed/image2.png)

### <a name="create-web-app-deployment-in-both-clouds"></a>両方のクラウドで Web アプリ デプロイを作成する

1.  **WebApplication.csproj** ファイルを編集します。Runtimeidentifier を選択し、**win10 x64** を追加します。 (「[自己完結型デプロイ](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)」に関するドキュメントを参照してください)。

    ![Alt text](media/azure-stack-solution-geo-distributed/image3.png)

1.  チーム エクスプローラーを使用して、**コードを Azure Repos にチェックインします**。

2.  **アプリケーション コード**が Azure Repos にチェックインされたことを確認します。

### <a name="create-the-build-definition"></a>ビルド定義を作成する

1. **Azure Pipelines にログイン**して、ビルド定義を作成する機能を確認します。

2. **-r win10-x64** コードを追加します。 これは、.Net Core を使用して自己完結型のデプロイをトリガーするために必要です。

    ![Alt text](media/azure-stack-solution-geo-distributed/image4.png)

3. **ビルドを実行します**。 [自己完結型のデプロイ ビルド](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)のプロセスにより、Azure および Azure Stack 上で実行できる成果物が発行されます。

**Azure ホステッド エージェントを使用する**

Web アプリをビルドおよびデプロイする場合、Azure Pipelines でホステッド エージェントを使用すると便利です。 Microsoft Azure によってメンテナンスやアップグレードが自動的に実施され、開発、テスト、デプロイについても、継続的に、かつ中断されることなく実行できます。

### <a name="manage-and-configure-the-cd-process"></a>CD プロセスを管理および構成する

Azure DevOps および Azure DevOps Server が提供するパイプラインは自由に構成でき、管理性にも優れ、開発、ステージング、QA、運用など、さまざまな環境へのリリースに使用できます。また、特定のステージで承認を要求することもできます。

#### <a name="create-release-definition"></a>リリース定義の作成


![Alt text](media/azure-stack-solution-geo-distributed/image5.png)

1.  Visual Studio Online (VSO) の [ビルドとリリース] ページの **[リリース] タブ**で **[+]** ボタンを選択して、新しいリリースを追加します。

    ![Alt text](media/azure-stack-solution-geo-distributed/image6.png)

2. **Azure App Service の配置**テンプレートを適用します。

    ![Alt text](media/azure-stack-solution-geo-distributed/image7.png)

3. [成果物の追加] プルダウン メニューで、Azure Cloud ビルド アプリに対して**成果物を追加**します。

    ![Alt text](media/azure-stack-solution-geo-distributed/image8.png)

4. [パイプライン] タブで、環境の**フェーズ、タスク** リンクを選択し、Azure のクラウド環境の値を設定します。

    ![Alt text](media/azure-stack-solution-geo-distributed/image9.png)

5. **環境名**を設定し、Azure クラウド エンドポイントに対して Azure **サブスクリプション**を選択します。

    ![Alt text](media/azure-stack-solution-geo-distributed/image10.png)

6. [環境名] で、必須の **Azure アプリ サービス名**を設定します。

    ![Alt text](media/azure-stack-solution-geo-distributed/image11.png)

7. Azure クラウドでホストされる環境のエージェント キューで、「**Hosted VS2017**」と入力します。

    ![Alt text](media/azure-stack-solution-geo-distributed/image12.png)

8. [Azure App Service 配置] メニューで、環境に対して有効な**パッケージまたはフォルダー**を選択します。 [OK] を選択して、**フォルダーの場所**を選択します。

    ![Alt text](media/azure-stack-solution-geo-distributed/image13.png)

    ![Alt text](media/azure-stack-solution-geo-distributed/image14.png)

9. すべての変更を保存し、**リリース パイプライン**に戻ります。

    ![Alt text](media/azure-stack-solution-geo-distributed/image15.png)

10. Azure Stack アプリのビルドを選択して、**新しい成果物**を追加します。

    ![Alt text](media/azure-stack-solution-geo-distributed/image16.png)

11. **Azure App Service の配置**を適用して、環境をもう 1 つ追加します。

    ![Alt text](media/azure-stack-solution-geo-distributed/image17.png)

12. 新しい環境に **Azure Stack** という名前を付けます。

    ![Alt text](media/azure-stack-solution-geo-distributed/image18.png)

13. **[タスク]** タブで Azure Stack 環境を見つけます。

    ![Alt text](media/azure-stack-solution-geo-distributed/image19.png)

14. Azure Stack エンドポイントの**サブスクリプション**を選択します。

  ![Alt text](media/azure-stack-solution-geo-distributed/image20.png)

15. **[App Service の名前]** として、Azure Stack Web アプリの名前を設定します。

    ![Alt text](media/azure-stack-solution-geo-distributed/image21.png)

16. **Azure Stack エージェント**を選択します。

    ![Alt text](media/azure-stack-solution-geo-distributed/image22.png)

17. [Azure App Service 配置] セクションで、環境に対して有効な**パッケージまたはフォルダー**を選択します。 [OK] を選択して、**フォルダーの場所**を選択します。

    ![Alt text](media/azure-stack-solution-geo-distributed/image23.png)

    ![Alt text](media/azure-stack-solution-geo-distributed/image24.png)

18. **[変数]** タブで、`VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS` という名前の変数を追加し、その値を `true` に設定し、スコープを `Azure Stack` に設定します。

    ![Alt text](media/azure-stack-solution-geo-distributed/image25.png)

19. 両方の成果物で**継続的**配置トリガー アイコンを選択し、**継続的**配置トリガーを有効にします。

    ![Alt text](media/azure-stack-solution-geo-distributed/image26.png)

20. Azure Stack 環境で**配置前**条件アイコンを選択し、トリガーを**リリース後**に設定します。

21. すべての変更を保存します。

> [!Note]  
>  タスクの一部の設定は、テンプレートからリリース定義を作成したときに、[環境変数](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables)として自動的に定義されている可能性があります。 こうした設定は、タスクの設定では変更できません。これらの設定を編集するには、親環境項目を選択する必要があります。

## <a name="part-2-update-web-app-options"></a>パート 2:Web アプリ オプションを更新する

[Azure App Service](https://docs.microsoft.com/azure/app-service/overview) では、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供しています。 

![Alt text](media/azure-stack-solution-geo-distributed/image27.png)

> [!div class="checklist"]
> - 既存のカスタム DNS 名を Azure Web Apps にマップする
> - **CNAME レコーダー **A レコード** を使用して、カスタム DNS 名を App Service にマップします。

### <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>既存のカスタム DNS 名を Azure Web Apps にマップする

> [!Note]  
>  ルート ドメイン (northwind.com など) を除くすべてのカスタム DNS 名に CNAME を使用します。

ライブ サイトとその DNS ドメイン名を App Service に移行する方法については、「[Azure App Service へのアクティブな DNS 名の移行](https://docs.microsoft.com/azure/app-service/manage-custom-dns-migrate-domain)」をご覧ください。

### <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

-   [App Service アプリを作成する](https://docs.microsoft.com/azure/app-service/)か、別のチュートリアルで作成したアプリを使用します。

-   ドメイン名を購入し、ドメイン プロバイダーの DNS レジストリへのアクセスを確認します。

ドメインの DNS ゾーン ファイルを更新します。 Azure AD は、カスタム ドメイン名の所有権を確認します。 Azure 内の Azure/Office 365/外部 DNS レコードに [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) を使用するか、または[別の DNS レジストラー](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)で DNS エントリを追加します。

-   パブリック レジストラーでカスタム ドメインを登録します。

-   ドメインのドメイン名レジストラーにサインインします。 (DNS の更新を行うには、承認された管理者が必要になることがあります)。

-   Azure AD から提供された DNS エントリを追加して、ドメインの DNS ゾーン ファイルを更新します。

たとえば、DNS エントリ fornorthwindcloud.comand www.northwindcloud.com を追加するには、thenorthwindcloud.com ルート ドメインの DNS 設定を構成します。

> [!Note]  
>  ドメイン名は [Microsoft Azure portal](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain) を使用して購入できます。  
> Web アプリにカスタム DNS 名をマップするには、Web アプリの [App Service プラン](https://azure.microsoft.com/pricing/details/app-service/)が有料レベル (**Shared**、**Basic**、**Standard**、または **Premium**) である必要があります。



### <a name="create-and-map-cname-and-a-records"></a>CNAME および A レコードを作成してマップする

#### <a name="access-dns-records-with-domain-provider"></a>ドメイン プロバイダーで DNS レコードにアクセスする

> [!Note]  
>  Azure DNS を使用して、Azure Web Apps のカスタム DNS 名を構成します。 詳細については、「[Azure DNS を使用して Azure サービス用のカスタム ドメイン設定を提供する](https://docs.microsoft.com/azure/dns/dns-custom-domain)」をご覧ください。

1.  ドメイン プロバイダーの Web サイトにサインインします。

2.  DNS レコードの管理ページを探します。 各ドメイン プロバイダーは、独自の DNS レコード インターフェイスを保有します。 **[ドメイン名]**、**[DNS]**、**[ネーム サーバー管理]** というラベルが付いたサイトの領域を探します。

DNS レコード ページは、**[My domains] (マイ ドメイン)** で表示できます。 **[ゾーン ファイル]**、**[DNS レコード]**、または **[詳細構成]** という名前のリンクを見つけます。

以下のスクリーンショットは、DNS レコード ページの例です。

![DNS レコード ページの例](media/azure-stack-solution-geo-distributed/image28.png)

1.  ドメイン名レジストラーで、**[Add or Create] (追加または作成)** を選択してレコードを作成します。 プロバイダーによっては、追加するレコード タイプごとに異なるリンクが用意されています。 プロバイダーのドキュメントを参照してください。

2.  CNAME レコードを追加して、サブドメインをアプリの既定のホスト名にマップします。

  www.northwindcloud.comdomain の例では、namewwwto<app\_name>.azurewebsites.net をマップする CNAME レコードを追加します。

CNAME を追加した後の DNS レコード ページは次の例のようになります。

![Azure アプリへのポータル ナビゲーション](media/azure-stack-solution-geo-distributed/image29.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>Azure で CNAME レコード マッピングを有効にする

1.  新しいタブで、Microsoft Azure portal にサインインします。

2.  App Services に移動します。

3.  Web アプリを選択します。

4.  Azure Portal のアプリ ページの左側のナビゲーションで、**[カスタム ドメイン]** を選択します。

5.  **[ホスト名の追加]** の横の **+** アイコンを選択します。

1.  `www.northwindcloud.com` のように完全修飾ドメイン名を入力します。

2.  **[検証]** を選択します。

3.  指示された場合は、他の種類 (`A` または `TXT`) の追加レコードをドメイン名レジストラー DNS レコードに追加します。 Azure は、これらのレコードの値と種類を提供します。

    a.  アプリの IP アドレスにマップするための **A** レコード。

    b.  アプリの既定のホスト名 <app_name>.azurewebsites.net にマップするための **TXT** レコード。 App Service は、このレコードを、カスタム ドメインの所有者を検証するために構成時にのみ使用します。 検証後、TXT レコードを削除してください。

4.  ドメイン レジスター タブでこのタスクを完了し、**[ホスト名の追加]** ボタンがアクティブになるまで、再検証します。

5.  **[ホスト名レコード タイプ] が **[CNAME (www.example.com または任意のサブドメイン)]** に設定されていることを確認します。

6.  **[ホスト名の追加]** を選択します。

7.  `northwindcloud.com` のように完全修飾ドメイン名を入力します。

8.  **[検証]** を選択します。

9.  **[追加]** がアクティブになります。

10. **[ホスト名レコード タイプ] が **[A レコード (example.com)]** に設定されていることを確認します。

11. **ホスト名を追加します**。

  アプリの **[カスタム ドメイン]** ページに新しいホスト名が反映されるまで時間がかかることがあります。 データを更新するために、ブラウザーの表示を更新してみてください。
  
  ![Alt text](media/azure-stack-solution-geo-distributed/image31.png) 
  
  エラーが発生した場合は、検証エラーの通知がページの下部に表示されます。 ![検証エラー](media/azure-stack-solution-geo-distributed/image32.png)

> [!Note]  
>  上記の手順を繰り返して、ワイルドカード ドメイン (\*northwindcloud.com) をマップできます。 これにより、それぞれに個別の CNAME レコードを作成せずに、このアプリ サービスに別のサブドメインを追加できます。 レジストラーの指示に従って、この設定を構成します。

#### <a name="test-in-a-browser"></a>ブラウザーでテストする

先ほど構成した DNS 名 (たとえば、`northwindcloud.com` や www.northwindcloud.com) を参照します。

## <a name="part-3-bind-a-custom-ssl-cert"></a>パート 3: カスタム SSL 証明書をバインドする

このパートでの作業:

> [!div class="checklist"]
> - カスタム SSL 証明書を App Service にバインドする
> - アプリに HTTPS を適用する
> - スクリプトで SSL 証明書のバインドを自動化する

> [!Note]  
> 必要に応じて、Microsoft Azure portal で顧客の SSL 証明書を取得し、それを Web アプリにバインドします。 [App Service 証明書のチュートリアル](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site)に従ってください。

### <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

-   [App Service アプリを作成する](https://docs.microsoft.com/azure/app-service/)
-   [カスタム DNS 名を Web アプリにマップする](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)
-   信頼された証明機関から SSL 証明書を取得し、キーを使用して要求に署名する

### <a name="requirements-for-your-ssl-certificate"></a>SSL 証明書の必要条件

App Service で証明書を使用するには、証明書が次のすべての要件を満たしている必要があります。

-   信頼された証明機関によって署名されている

-   パスワードで保護された PFX ファイルとしてエクスポートされている

-   少なくとも 2048 ビット長の秘密キーが含まれている

-   証明書チェーン内のすべての中間証明書が含まれている

> [!Note]  
>  **楕円曲線暗号 (ECC) 証明書**は、App Service で有効ですが、このガイドでは取り上げていません。 ECC 証明書の作成でサポートが必要なときは、証明機関に問い合わせてください。 

#### <a name="prepare-the-web-app"></a>Web アプリを用意する

カスタム SSL 証明書を Web アプリにバインドするには、[App Service プラン](https://azure.microsoft.com/pricing/details/app-service/)が **Basic**、**Standard** または **Premium** のいずれかのレベルである必要があります。

#### <a name="sign-in-to-azure"></a>Azure へのサインイン

1.  [Microsoft Azure portal](https://portal.azure.com/) を開き、Web アプリに移動します。

2.  左側のメニューで、**[App Services]** を選択し、Web アプリ名を選択します。

![Web アプリの選択](media/azure-stack-solution-geo-distributed/image33.png)

#### <a name="check-the-pricing-tier"></a>価格レベルの確認

1.  Web アプリ ページの左側のナビゲーションで **[設定]** セクションまでスクロールし、**[スケール アップ (App Service プラン)]** を選択します。

    ![スケール アップ メニュー](media/azure-stack-solution-geo-distributed/image34.png)

1.  Web アプリが **Free** レベルまたは **Shared** レベルに含まれていないことを確認します。 Web アプリの現在の層が濃青色のボックスに強調表示されます。

    ![価格レベルの確認](media/azure-stack-solution-geo-distributed/image35.png)

カスタム SSL は、**Free** レベルまたは **Shared** レベルではサポートされていません。 アップスケールするには、次のセクション、**[価格レベルの選択]** ページの手順に従い、[[Upload and bind your SSL certificate] (SSL 証明書のアップロードおよびバインド)](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl) にスキップします。

#### <a name="scale-up-your-app-service-plan"></a>App Service プランのスケール アップ

1.  **Basic**、**Standard**、**Premium** のいずれかのレベルを選択します。

2.  **[選択]** を選択します。

![価格レベルの選択](media/azure-stack-solution-geo-distributed/image36.png)

通知が表示されたら、スケール操作は完了です。

![スケール アップの通知](media/azure-stack-solution-geo-distributed/image37.png)

#### <a name="bind-your-ssl-certificate-and-merge-intermediate-certificates"></a>SSL 証明書をバインドし、中間証明書を結合する

チェーン内の複数の証明書を結合します。

1. テキストエディタで、受信した**それぞれの証明書を開きます**。

2. 結合した証明書用に *mergedcertificate.crt* という名前のファイルを作成します。 テキスト エディターで、このファイルに各証明書の内容をコピーします。 証明書の順序は、証明書チェーンの順番に従う必要があります。自分の証明書から始まり、ルート証明書で終わります。 次の例のようになります。

    ```Text

    -----BEGIN CERTIFICATE-----

    <your entire Base64 encoded SSL certificate>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 1>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 2>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded root certificate>

    -----END CERTIFICATE-----
    ```

#### <a name="export-certificate-to-pfx"></a>PFX への証明書のエクスポート

証明書で生成された秘密キーを使用して、結合した SSL 証明書をエクスポートします。

秘密キー ファイルは OpenSSL 経由で作成されます。 証明書を PFX にエクスポートするには、*<private-key-file>* と *<merged-certificate-file>* のプレースホルダーを秘密キーのパスと結合した証明書ファイルに置き換えて次のコマンドを実行します。

```PowerShell
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

プロンプトが表示されたら、後から SSL 証明書を App Service にアップロードするためのエクスポート パスワードを定義します。

IIS または **Certreq.exe** を使用して証明書の要求を生成した場合は、ローカル コンピューターに証明書をインストールした後で[証明書を PFX にエクスポート](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx)します。

#### <a name="upload-the-ssl-certificate"></a>SSL 証明書をアップロードする

1.  Web アプリの左側のナビゲーションで **[SSL 設定]** を選択します。

2.  **[証明書のアップロード]** を選択します。

3.  **[PFX 証明書ファイル]** で、PFX ファイルを選択します。

4.  4. **証明書のパスワード**、PFX ファイルをエクスポートするときに作成したパスワードを入力します。

5.  **[アップロード]** を選択します。

![証明書のアップロード](media/azure-stack-solution-geo-distributed/image38.png)

App Service による証明書のアップロードが完了すると、**[SSL 設定]** ページにアップロードした証明書が表示されます。

![Alt text](media/azure-stack-solution-geo-distributed/image39.png)

#### <a name="bind-your-ssl-certificate"></a>SSL 証明書のバインド

1.  **[SSL バインド]** セクションで **[バインドの追加]** を選択します。

    > [!Note]  
    >  証明書をアップロードしたのに **[ホスト名]** ドロップダウンにドメイン名が表示されない場合は、ブラウザのページを最新の情報に更新してみてください。

1.  **[SSL バインディングの追加]** ページで、ドロップダウンから保護するドメインの名前と使用する証明書を選択します。

2.  **[SSL Type] \(SSL の種類)** で、[**Server Name Indication (SNI)**](http://en.wikipedia.org/wiki/Server_Name_Indication) ベースの SSL を使用するか IP ベースの SSL を使用するかを選択します。

-   **SNI ベースの SSL** - 複数の SNI ベースの SSL バインドを追加できます。 このオプションでは、複数の SSL 証明書を使用して、同一の IP アドレス上の複数のドメインを保護できます。 最新のブラウザーのほとんど (Inernet Explorer、Chrome、Firefox、Opera など) が SNI をサポートしています (ブラウザーのサポートに関するより包括的な情報については、「[Server Name Indication](http://wikipedia.org/wiki/Server_Name_Indication)」を参照してください)。

-   **IP ベースの SSL** - IP ベースの SSL バインドは 1 つだけ追加できます。 このオプションでは、SSL 証明書を 1 つだけ使用して、専用のパブリック IP アドレスを保護します。 複数のドメインを保護するには、同じ SSL 証明書を使用してすべてのドメインを保護します。 これは、SSL バインドの従来のオプションです。

    1.  **[バインドの追加]** を選択します。

    ![Alt text](media/azure-stack-solution-geo-distributed/image40.png)

App Service による証明書のアップロードが完了すると、**[SSL バインド]** セクションにアップロードした証明書が表示されます。

![Alt text](media/azure-stack-solution-geo-distributed/image41.png)

#### <a name="remap-the-a-record-for-ip-ssl"></a>IP SSL の A レコードを再マップする

Web アプリで IP ベースの SSL を使用していない場合、[カスタム ドメインの HTTPS のテスト](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)に関するセクションにスキップしてください。

既定では、Web アプリは、共有のパブリック IP アドレスを使用します。 IP ベースの SSL で証明書をバインドすると、Web アプリ用の新規の専用 IP アドレスが App Service によって作成されます。

A レコードが Web アプリにマップされた場合、ドメイン レジストリを専用の IP アドレスで更新する必要があります。

**[カスタム ドメイン]** ページが、新規の専用 IP アドレスで更新されます。 [この IP アドレスをコピー](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)して、この新しい IP アドレスに [A レコードを再マップ](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)します。

#### <a name="test-https"></a>HTTPS のテスト

さまざまなブラウザーで https://<your.custom.domain> を参照して、Web アプリが提供されていることを確認します。

![Alt text](media/azure-stack-solution-geo-distributed/image42.png)

> [!Note]  
> 証明書の検証エラーが発生した場合、自己署名証明書が原因であるか、PFX ファイルにエクスポートするときに中間証明書が除外された可能性があります。

#### <a name="enforce-https"></a>HTTPS の適用

既定では、どなたでも HTTP を使用して Web アプリにアクセスできます。 HTTPS ポートへのすべての HTTP 要求をリダイレクトできます。

Web アプリページで、**[SSL 設定]** を選択します。 その後、**[HTTPS のみ]** で、**[On]** を選択します。

![HTTPS の適用](media/azure-stack-solution-geo-distributed/image43.png)

操作が完了すると、アプリを指定する HTTP URL のいずれかに移動します。 例: 

-   http://<app_name>.azurewebsites.net
-   http://northwindcloud.com
-   <http://www.northwindcloud.com>

#### <a name="enforce-tls-1112"></a>TLS 1.1/1.2 の適用

アプリでは既定で [TLS 1.0](https://wikipedia.org/wiki/Transport_Layer_Security) が有効です。これは、[PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard) などの業界標準によって安全であると見なされなくなっています。 より上位の TLS バージョンを適用するには、次の手順に従います。

1.  Web アプリ ページで、左側のナビゲーションにある **[SSL 設定]** を選択します。

2.  **[TLS version] (TLS バージョン)** で、最低限の TLS バージョンを選択します。

![TLS 1.1/1.2 の適用](media/azure-stack-solution-geo-distributed/image44.png)

### <a name="create-a-traffic-manager-profile"></a>Traffic Manager プロファイルの作成

1.  **[リソースの作成]** > **[ネットワーク]** > **[Traffic Manager プロファイル]** > **[作成]** の順に選択します。

2.  **[Traffic Manager プロファイルの作成]** で、以下を実行します。

    1.  **[名前]** に、プロファイルの名前を入力します。 この名前は trafficmanager.net ゾーン内で一意である必要があります。結果的に、Traffic Manager プロファイルへのアクセスに使用される DNS 名 trafficmanager.net になるためです。

    2.  **[ルーティング方法]** で、**地理的ルーティング方法**を選びます。

    3.  **[サブスクリプション]** で、このプロファイルを作成するサブスクリプションを選択します。

    4.  **[リソース グループ]** で、このプロファイルを配置する新しいリソース グループを作成します。

    5.  **[リソース グループの場所]** で、リソース グループの場所を選択します。 これはリソース グループの場所を指定する設定であり、グローバルにデプロイされる Traffic Manager プロファイルには影響しません。

    6.  **作成**を選択します。

    7.  Traffic Manager プロファイルは、グローバルなデプロイが完了すると、それぞれのリソース グループ内にリソースの 1 つとして表示されます。

    ![Alt text](media/azure-stack-solution-geo-distributed/image45.png)

### <a name="add-traffic-manager-endpoints"></a>Traffic Manager エンドポイントの追加

1.  ポータルの検索バーで、前のセクションで作成した **Traffic Manager プロファイル** の名前を検索し、表示された結果から Traffic Manager プロファイルを選択します。

2.  **[Traffic Manager プロファイル]** の **[設定]** セクションで、**[エンドポイント]** を選択します。

3.  **[追加]** を選択します。

4.  Azure Stack エンドポイントを追加します。

5.  **[Type] (種類)** で、**[外部エンドポイント]** を選択します。

6.  このエンドポイントの**名前**を、理想的には Azure Stack の名前を入力します。

7.  完全修飾ドメイン名 (**FQDN**) については、Azure Stack Web アプリの外部 URL を使用します。

8.  地理的マッピングで、リソースが置かれているリージョン/大陸を選択します (たとえば**ヨーロッパ**)。

9.  表示される [Country/Region] (国/リージョン) ドロップダウンで、このエンドポイントに適用される国を選択します (たとえば**ドイツ**)。

10. **[Add as disabled (無効として追加)]** はオフのままにします。

11. **[OK]** を選択します。

12. Azure エンドポイントの追加:

    1.  **[Type] (種類)** で、**[Azure エンドポイント]** を選択します。

    2.  このエンドポイントの **[名前]** を入力します。

    3.  **[ターゲット リソースの種類]** で、**[App Service]** を選択します。

    4.  **[ターゲット リソース]** で、**[アプリ サービスの選択]** を選択し、同じサブスクリプションにある Web Apps の一覧を表示します。 **[リソース]** で、最初のエンドポイントとして使用する App Service を選択します。

13. 地理的マッピングで、リソースが置かれているリージョン/大陸を選択します (たとえば**北米/中米/カリブ海**)。

14. 表示される [Country/Region] (国/リージョン) ドロップダウンで、これを空白のままにして、上記のリージョンのグループすべてを選択します。

15. **[Add as disabled (無効として追加)]** はオフのままにします。

16. **[OK]** を選択します。

  > [!Note]  
  >  [All (World)] (すべて (世界)) の地理的範囲を持つ少なくとも 1 つのエンドポイントを作成して、リソースの既定のエンドポイントとして機能します。

1.  両方のエンドポイントは、追加が完了すると、**[Traffic Manager プロファイル]** に、監視ステータスが **[オンライン]** の状態で表示されます。

  ![Alt text](media/azure-stack-solution-geo-distributed/image46.png)

**グローバル エンタープライズは、地理的分散機能に依存する**

Azure Traffic Manager と地理固有のエンドポイント経由でデータ トラフィックを送信すると、グローバル企業は、リージョンの規制を遵守でき、リモート ロケーション全体でローカル ビジネスの成功にとって重要なデータのコンプライアンスとセキュリティを確保できます。

## <a name="next-steps"></a>次の手順

- Azure のクラウド パターンの詳細については、「[Cloud Design Pattern (クラウド設計パターン)](https://docs.microsoft.com/azure/architecture/patterns)」を参照してください。
