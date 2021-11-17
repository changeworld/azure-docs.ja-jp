---
title: Azure 無料アカウントを使用して Azure Database for MySQL - フレキシブル サーバーを無料で試す
description: Azure 無料アカウントを使用して、Azure Database for MySQL - フレキシブル サーバーを無料でデプロイする方法についてのガイダンス。
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.topic: how-to
ms.date: 08/16/2021
ms.custom: template-how-to
ms.openlocfilehash: 619c65cb2f86c946d0e85573a967a23d718b38c9
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501440"
---
# <a name="use-an-azure-free-account-to-try-azure-database-for-mysql---flexible-server-for-free"></a>Azure 無料アカウントを使用して Azure Database for MySQL - フレキシブル サーバーを無料で試す

Azure Database for MySQL - フレキシブル サーバーは、高可用性の MySQL データベースをクラウド内で実行、管理、スケーリングするために使用されるマネージド サービスです。  Azure 無料アカウントを使用すると、フレキシブル サーバーを **12 か月間無料** でご利用いただけます。**1 か月あたりの上限** は次のとおりです。
- **Burstable B1MS** インスタンスを **750 時間**。これはデータベース インスタンスを毎月十分に継続実行できるだけの時間です。
- **32 GB のストレージと 32 GB のバックアップ ストレージ。** 

この記事では、[Azure 無料アカウント](https://azure.microsoft.com/free/)を使用して、フレキシブル サーバーを無料で作成、使用する方法について説明します。 


## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

- Azure の無料アカウント。 まだ持っていない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。 


## <a name="create-an-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL - フレキシブル サーバーを作成する

この記事では、Azure portal を使用してフレキシブル サーバーとパブリック アクセスとの接続方法を作成します。 または、それぞれのクイックスタートを参照して、[Azure CLI](./quickstart-create-server-cli.md) または [ARM テンプレート](./quickstart-create-arm-template.md)を使用するか、[VNET 内](./quickstart-create-connect-server-vnet.md)でフレキシブル サーバーを作成します。


1. Azure 無料アカウントで [Azure portal](https://portal.azure.com/) にサインインします。 
    
    既定のビューはサービス ダッシュボードです。 

1. MySQL フレキシブル サーバー データベースを作成するために、 **[Azure Database for MySQL servers]\(Azure Database for MySQL サーバー\)** を検索して選択します。
    
    :::image type="content" source="./media/how-to-deploy-on-azure-free-account/search-select-azure-database-for-mysql.png" alt-text="Azure portal で Azure Database for MySQL を検索して選択する方法を示すスクリーンショット。":::


    または、"**無料サービス**" を検索してそこに移動し、一覧から **[Azure Database for MySQL]** タイルを選択します。
    
    :::image type="content" source="media/how-to-deploy-on-azure-free-account/all-free-services.png" alt-text="Azure portal にある無料の全サービスを一覧表示する画面のスクリーンショット。":::

1. **［作成］** を選択します

1. **[Azure Database for MySQL デプロイ オプションの選択]** ページで、 **[フレキシブル サーバー]** を選択します。 

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/select-deployment-option.png" alt-text="フレキシブル サーバーのデプロイ オプションを選択する画面のスクリーンショット。":::

1. 新しい **フレキシブル サーバー** の基本設定を入力します。


    :::image type="content" source="media/how-to-deploy-on-azure-free-account/basic-settings-all.PNG" alt-text="フレキシブル サーバーを作成するための基本設定を示すスクリーンショット。":::


    |設定    |推奨値    |説明  |
    |---------|---------|---------|
    |サブスクリプション    |該当するサブスクリプション    |無料試用版 Azure サブスクリプションを選択します。|
    |Resource group    |該当するリソース グループ    |新しいリソース グループ、またはサブスクリプションの既存のものを入力します。|
    |サーバー名    |mydemoserver-mysql    |フレキシブル サーバーを特定するための一意の名前を指定します。 指定したサーバー名にドメイン名 mysql.database.azure.com が追加されます。 サーバー名に含めることができるのは、英小文字、数字、およびハイフン (-) のみであり、 3 から 63 文字にする必要があります。|
    |Region    |ユーザーに最も近いリージョン    |一覧から場所を選択します。可能であれば、ユーザーに最も近い場所を選択します。|
    |ワークロードの種類    |開発    |無料試用版では、開発ワークロードを選択します。 運用環境のワークロードでは、[max_connections](concepts-server-parameters.md) の要件に応じて、小中規模または大規模を選択します。|
    |可用性ゾーン    |[優先設定なし]    |(Azure VM、仮想マシン スケール セット、または AKS インスタンスをホストとする) アプリケーションを特定の可用性ゾーンにプロビジョニングする場合は、同じ可用性ゾーンにフレキシブル サーバーを作成してください。 アプリケーションとデータベースをまとめることでゾーン間のネットワーク待ち時間が短縮され、パフォーマンスが向上します。|
    |高可用性    |Default    |[高可用性] オプションはオフのままにします。|
    |MySQL のバージョン    |最新のメジャー バージョン    |最新のメジャー バージョンを使用します。 詳細については、[サポート対象の全バージョン](../concepts-supported-versions.md)に関するページを参照してください。|
    |管理者ユーザー名     |myadmin        |サーバーに接続するときに使用するサインイン アカウントを作成します。 管理者ユーザー名に、azure_superuser、admin、administrator、root、guest、public は使用できません。         |
    |Password     |お使いのパスワード         |サーバー管理者アカウントの新しいパスワードを指定します。 パスワードは 8 から 128 文字にする必要があります。 また、パスワードには、英大文字、英小文字、数字 (0 から 9)、英数字以外の文字 (!、$、#、% など) という 4 つのカテゴリのうち、3 つのカテゴリの文字が含まれている必要があります。         |

1. **[コンピューティングとストレージ]** 設定で、 **[サーバーの構成]** を選択します。
 
    Burstable B1MS インスタンス (1 から 2 個の仮想コア) を選択し、32 GB 以下のストレージを指定します。それ以外のオプションは、既定の設定のままにしてください。

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/compute-storage.png" alt-text="[コンピューティングとストレージ] の [サーバーの構成] ブレードで B1MS SKU と 32GB のストレージを選択する画面のスクリーンショット。":::

1. **[保存]** を選択して構成に進みます。


1. **[ネットワーク]** タブを選択して、サーバーへの到達方法を構成します。

     Azure Database for MySQL - フレキシブル サーバーには、次の 2 とおりの接続方法が用意されています。
     
    - パブリック アクセス (許可された IP アドレス)
    - プライベート アクセス (VNet 統合)
     
    パブリック アクセスを使用する場合、サーバーへのアクセスは、ファイアウォール規則に追加されている許可された IP アドレスに限定されます。 この方法では、特定の IP アドレスまたは範囲に対してファイアウォールを開く規則を作成しない限り、外部のアプリケーションやツールはサーバーとサーバー上のデータベースに接続できなくなります。 

    プライベート アクセス (VNet 統合) を使用する場合、サーバーへのアクセスは仮想ネットワークに限定されます。 接続方法の詳細については、[接続およびネットワークの概念](./concepts-networking.md)に関するページを参照してください。
     
    このチュートリアルでは、その目的上、パブリック アクセスによるサーバーへの接続を有効にします。 
    
1. **[ネットワーク]** タブの **[接続方法]** で、 **[パブリック アクセス]** を選択します。 
1. **ファイアウォール規則** の構成で、 **[現在のクライアント IP アドレスを追加する]** を選択します。
    
    :::image type="content" source="media/how-to-deploy-on-azure-free-account/networking.png" alt-text="選択するネットワーク オプションを示すスクリーンショット。[現在のクライアント IP アドレスを追加する] ボタンが強調表示されている。":::

    > [!NOTE]
    > サーバーの作成後に接続方法を変更することはできません。 たとえば、サーバーの作成時に **[パブリック アクセス (使用できる IP アドレス)]** を選択した場合、サーバーの作成後に **[プライベート アクセス (VNet 統合)]** に変更することはできません。 VNet 統合によってサーバーへのアクセスをセキュリティで保護できるように、プライベート アクセスを指定してサーバーを作成することを強くお勧めします。 プライベート アクセスの詳細については、[接続およびネットワークの概念](./concepts-networking.md)に関するページを参照してください。

1. フレキシブル サーバーの構成を確認するために、 **[確認と作成]** を選択します。

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/review-and-create.png" alt-text="[確認と作成] ブレードを示すスクリーンショット。":::

    >[!IMPORTANT]
    >Azure の無料アカウントからフレキシブル サーバー インスタンスを作成するとき、 **[コンピューティング + ストレージ : コストの概要]** ブレードと **[確認および作成]** タブに **[月ごとの推定コスト]** が表示されます。ただし、Azure の無料アカウントを使用しているとき、無料のサービス使用料が月あたりの上限内である限り (使用量情報を見るには、「[**無料サービス使用量を監視、追跡する**](#monitor-and-track-free-services-usage)」を参照)、サービスの料金は請求されません。 Microsoft は現在、無料サービスの **[コストの概要]** のエクスペリエンス向上に向けて取り組んでいます。 
    
1. **[作成]** を選択して、サーバーをプロビジョニングします。 
 
    プロビジョニングには数分かかる場合があります

1. デプロイ プロセスを監視するには、ツール バーの **[通知]** (ベル アイコン) を選択します。 
    
    デプロイが完了したら、 **[ダッシュボードにピン留めする]** を選択して、Azure portal ダッシュボードにフレキシブル サーバーのタイルを作成します。 このタイルは、サーバーの **[概要]** ページへのショートカットです。 **[リソースに移動]** を選択すると、サーバーの **[概要]** ページが開きます。

    既定では、information_schema、mysql、performance_schema、sys の各データベースがサーバーに含まれています。


## <a name="connect-and-query"></a>接続とクエリ

Azure Database for MySQL - フレキシブル サーバーをリソース グループに作成したら、次に示した「接続とクエリ」のクイックスタートを参考に、サーバーに接続してデータベースに対してクエリを実行することができます。

- [mysql.exe](quickstart-create-server-portal.md)
- [MySQL Workbench](connect-workbench.md)
- [Azure CLI](connect-azure-cli.md)
- [PHP](connect-php.md) 
- [Java](connect-java.md)
- [Python](connect-python.md)
- [.NET](connect-csharp.md)


## <a name="monitor-and-track-free-services-usage"></a>無料サービス使用量を監視、追跡する

Azure 無料アカウントに無料で含まれている Azure Database for MySQL - フレキシブル サーバー サービスについては、無料サービスの上限を超えない限り課金されません。 上限を超えないよう、Azure portal を使用して無料サービス使用量を追跡、監視してください。

1. Azure portal で "**サブスクリプション**" を検索し、Azure の無料アカウント - **無料試用版** サブスクリプションを選択します。

1. **[概要]** ページで、下へスクロールして **[Top free services by usage]\(無料サービス使用量ランキング\)** タイルを表示し、 **[View all free services]\(無料サービスをすべて表示\)** を選択します。

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/free-services-usage-overview.png" alt-text="無料試用版サブスクリプションの概要ページを示すスクリーンショット。[View all free services]\(無料サービスをすべて表示\) が強調表示されている。":::

1. **Azure Database for MySQL - フレキシブル サーバー** に関連したメーターを見つけて使用量を追跡します。

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/free-services-tracking.png" alt-text="全無料サービスの使用量情報を確認、追跡するためのブレードが表示された Azure portal 画面のスクリーンショット。":::

    |測定    |説明    |月ごとの上限  |
    |---------|---------|---------|
    |Azure Database for MySQL, Flexible Server Burstable BS Series Compute, B1MS (Azure Database for MySQL、フレキシブル サーバー Burstable BS シリーズ コンピューティング、B1MS) | コンピューティング使用量を実行時間数の観点から追跡します | 750 時間/月 - Burstable B1MS コンピューティング レベル|
    |Azure Database for MySQL, Flexible Server Storage, Data Stored (Azure Database for MySQL、フレキシブル サーバー ストレージ、格納データ) | 1 か月に使用された GiB の観点から、プロビジョニングされたデータ ストレージを追跡します | 32 GB/月 |

    
    - 測定: 使用しているサービスの測定単位を示します。
    - 使用状況/使用制限: 当月の使用状況とメーターの使用制限。
    - 状態: サービスの使用状況ステータス。 使用状況によって、次のいずれかの状態が表示されます。
    - [未使用]: メーターを使用しなかった、またはメーターの使用状況が課金システムに伝わらなかった。
    - [\<Date\> に超えました]: \<Date\> にメーターの使用制限を超過した。
    - [超える可能性は低い]: メーターの使用制限を超過する可能性が低い。
    - [\<Date\> に超えます]: \<Date\> にメーターの使用制限を超過する可能性がある。
    

    >[!IMPORTANT]
    >Azure 無料アカウントでは、30 日間使用できる $200 のクレジットも与えられます。 この期間中、月ごとの無料分を超えるサービス使用量は、このクレジットから差し引かれます。
    >最初の 30 日間の終わり、または $200 のクレジットを費やした後 (どちらか早い方)、1 か月あたりの無料分を超えて使用したサービスの料金のみが課金されます。 30 日が経過した後も引き続き無料サービスを利用するには、従量課金制価格に移行してください。 従量課金制に移行しなかった場合、$200 のクレジットを超える Azure サービスは購入できず、最終的にはご利用のアカウントとサービスが無効となります。
    >詳細については、「[**Azure 無料アカウント FAQ**](https://azure.microsoft.com/free/free-account-faq/)」を参照してください。


## <a name="clean-up-resources"></a>リソースをクリーンアップする

開発ワークロード、テスト ワークロード、または予測可能な期限付き運用ワークロードにフレキシブル サーバーを使用する場合は、必要に応じてサーバーを開始および停止することで使用量を最適化してください。 サーバーを停止した後、すぐに再起動しない限り、7 日間はサーバーが停止状態のままとなります。 詳細については、[サーバーの概念](concept-servers.md)に関する記事を参照してください。
フレキシブル サーバーが停止されると、コンピューティングの使用量は発生しませんが、ストレージの使用量は引き続き考慮されます。 

または、これらのリソースが今後不要であると思われる場合、リソース グループを削除してリソースを削除するか、単にこの MySQL サーバーを削除してください。 

- リソース グループを削除するには次の手順に従います。

    1. Azure portal で、「**リソース グループ**」を検索して選択します。
    1. リソース グループの一覧で、リソース グループの名前を選択します。
    1. 対象のリソース グループの **[概要]** ページで、 **[リソース グループの削除]** を選択します。
    1. 確認のダイアログ ボックスでリソース グループの名前を入力し、 **[削除]** を選択します。

- MySQL サーバーを削除するには、サーバーの **[概要]** ページで **[削除]** を選択します。


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [MySQL を使用した PHP (Laravel) Web アプリを作成する](tutorial-php-database-app.md)

