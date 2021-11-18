---
title: Azure 無料アカウントを使用して Azure Database for PostgreSQL - フレキシブル サーバーを無料で試す
description: Azure 無料アカウントを使用して、Azure Database for PostgreSQL - フレキシブル サーバーを無料でデプロイする方法についてのガイダンス。
author: shreyaaithal
ms.author: shaithal
ms.service: postgresql
ms.topic: how-to
ms.date: 08/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 95f67a28b1aec292150bd5124638febddf66a164
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132722128"
---
# <a name="use-an-azure-free-account-to-try-azure-database-for-postgresql---flexible-server-for-free"></a>Azure 無料アカウントを使用して Azure Database for PostgreSQL - フレキシブル サーバーを無料で試す

Azure Database for PostgreSQL - フレキシブル サーバー (プレビュー) は、高可用性の PostgreSQL データベースをクラウド内で実行、管理、スケーリングするために使用されるマネージド サービスです。 Azure 無料アカウントを使用すると、フレキシブル サーバーを **12 か月間無料** で使用できます。**1 か月あたりの上限** は次のとおりです。
- **Burstable B1MS** インスタンスを **750 時間**。これはデータベース インスタンスを毎月十分に継続実行できるだけの時間です。
- **32 GB のストレージと 32 GB のバックアップ ストレージ**。

この記事では、[Azure 無料アカウント](https://azure.microsoft.com/free/)を使用して、フレキシブル サーバーを無料で作成、使用する方法について説明します。 

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーは現在パブリック プレビュー段階です。


## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

- Azure の無料アカウント。 まだ持っていない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。 


## <a name="create-an-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - フレキシブル サーバーを作成する

1. Azure 無料アカウントで [Azure portal](https://portal.azure.com/) にサインインします。
    
    既定のビューはサービス ダッシュボードです。

1. PostgreSQL フレキシブル サーバー データベースを作成するには、「**Azure Database for PostgreSQL servers**」を検索して選択します。

    
    :::image type="content" source="media/how-to-deploy-on-azure-free-account/select-postgresql.png" alt-text="Azure portal で Azure Database for PostgreSQL を検索して選択する方法を示すスクリーンショット。":::


    または、「**無料サービス**」を検索してそこに移動し、一覧から **[Azure Database for PostgreSQL]** タイルを選択します。


    :::image type="content" source="media/how-to-deploy-on-azure-free-account/free-services-all-postgresql.png" alt-text="Azure portal の無料の全サービスが一覧表示されており、PostgreSQL が強調表示されている画面のスクリーンショット。":::

1. **［作成］** を選択します

1. **[Azure Database for PostgreSQL デプロイ オプションを選択する]** ページで、 **[フレキシブル サーバー (プレビュー)]** を選択します。
    
    :::image type="content" source="media/how-to-deploy-on-azure-free-account/select-postgresql-deployment-option.png" alt-text="フレキシブル サーバーのデプロイ オプションを選択する画面のスクリーンショット。":::

1. 新しい **フレキシブル サーバー** の基本設定を入力します。

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/basic-settings-postgresql.png" alt-text="フレキシブル サーバーを作成するための基本設定を示すスクリーンショット。":::

    |設定    |推奨値    |説明 |
    |-------|------|------|
    |サブスクリプション   |お使いのサブスクリプション名 |無料試用版の Azure サブスクリプションを選択します。|
    |Resource group |該当するリソース グループ    |新しいリソース グループ、またはサブスクリプションの既存のものを入力します。|
    |サーバー名    |mydemoserver-pgsql |フレキシブル サーバーを特定するための一意の名前を指定します。 指定したサーバー名にドメイン名 postgres.database.azure.com が追加されます。 サーバー名に含めることができるのは、英小文字、数字、およびハイフン (-) のみであり、 3 から 63 文字にする必要があります。 |
    |Region |ユーザーに最も近いリージョン   |一覧から場所を選択します。可能であれば、ユーザーに最も近い場所を選択します。|
    |ワークロードの種類  |開発    |無料試用版では、**開発** ワークロードを選択します。 運用環境のワークロードでは、ユーザーの要件に応じて、小中規模または大規模を選択します。 |
    |可用性ゾーン  |[優先設定なし]  |(Azure VM、仮想マシン スケール セット、または AKS インスタンスをホストとする) アプリケーションを特定の可用性ゾーンにプロビジョニングする場合は、同じ可用性ゾーンにフレキシブル サーバーを作成してください。 アプリケーションとデータベースをまとめることでゾーン間のネットワーク待ち時間が短縮され、パフォーマンスが向上します。 **[優先設定なし]** を選択した場合は、既定の AZ が選択されます。|
    |PostgreSQL のバージョン |最新のメジャー バージョン   |他の特定の要件がない場合は、最新の PostgreSQL メジャー バージョンを使用します。|
    |高可用性  |Default    |[高可用性] オプションはオフのままにします。|
    |管理者ユーザー名 |myadmin    |サーバーに接続するときに使用するサインイン アカウントを作成します。 管理者のユーザー名を **pg_** で開始することはできません。また、**azure_superuser、azure_pg_admin、admin、administrator、root、guest、** または **public** を使用することはできません。 |
    |Password   |お使いのパスワード  |サーバー管理者アカウントのパスワードを指定します。 パスワードは 8 から 128 文字にする必要があります。 また、これには、英大文字、英小文字、数字 (0 から 9)、英数字以外の文字 (!、$、#、% など) という 4 つのカテゴリのうち、3 つのカテゴリの文字が含まれている必要があります。|

1. **[コンピューティングとストレージ]** の設定では、 **[開発]** のワークロードの種類を選択したときに設定される既定の値のままにします。 
    
    :::image type="content" source="media/how-to-deploy-on-azure-free-account/compute-storage-default-postgresql.png" alt-text="[コンピューティングとストレージ] の設定の既定値を示すスクリーンショット。":::

    **[サーバーの構成]** を選択して、 **[コンピューティングとストレージ]** 設定を確認してカスタマイズします。
    
    Burstable B1MS インスタンス (1-2 仮想コア) を選択し、32 GB 以下のストレージを指定し、それ以外のオプションは、既定の設定のままにするようにしてください。

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/compute-storage-postgresql.png" alt-text="[コンピューティングとストレージ] の [サーバーの構成] ブレードで B1MS SKU と 32GB ストレージを選択する画面のスクリーンショット。":::

    **[保存]** を選択して構成に進みます。

1.  **[ネットワーク]** タブを選択して、サーバーへの到達方法を構成します。

    Azure Database for PostgreSQL - フレキシブル サーバーには、次の 2 通りの接続方法が用意されています。
    - パブリック アクセス (許可された IP アドレス)
    - プライベート アクセス (VNet 統合)
    
    パブリック アクセスを使用する場合、サーバーへのアクセスは、ファイアウォール規則に追加されている許可された IP アドレスに限定されます。 この方法では、特定の IP アドレスまたは範囲に対してファイアウォールを開く規則を作成しない限り、外部のアプリケーションやツールはサーバーとサーバー上のデータベースに接続できなくなります。
    
    プライベート アクセス (VNet 統合) を使用する場合、サーバーへのアクセスは仮想ネットワークに限定されます。 接続方法の詳細については、[**ネットワークの概要を参照してください**](./concepts-networking.md)。
    
    このチュートリアルでは、その目的上、パブリック アクセスによるサーバーへの接続を有効にします。

1. **[ネットワーク]** タブの **[接続方法]** で、 **[パブリック アクセス]** を選択します。

1. **ファイアウォール規則** の構成で、 **[現在のクライアント IP アドレスを追加する]** を選択します。
    
    :::image type="content" source="media/how-to-deploy-on-azure-free-account/networking-postgresql.png" alt-text="選択するネットワーク オプションを示すスクリーンショット。[現在のクライアント IP アドレスを追加する] ボタンが強調表示されています。":::

1. フレキシブル サーバーの構成を確認するために、 **[確認と作成]** を選択します。

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/review-create-postgresql.png" alt-text="[確認と作成] ブレードを示すスクリーンショット。":::

    >[!IMPORTANT]
    >Azure の無料アカウントからフレキシブル サーバー インスタンスを作成するとき、 **[コンピューティング + ストレージ : コストの概要]** ブレードと **[確認および作成]** タブに **[月ごとの推定コスト]** が表示されます。ただし、Azure の無料アカウントを使用しているとき、無料のサービス使用料が月あたりの上限内である限り (使用量情報を見るには、「[**無料サービス使用量を監視、追跡する**](#monitor-and-track-free-services-usage)」を参照)、サービスの料金は請求されません。 Microsoft は現在、無料サービスの **[コストの概要]** のエクスペリエンス向上に向けて取り組んでいます。 

1. **[作成]** を選択して、サーバーをプロビジョニングします。

    プロビジョニングには数分かかる場合があります

1.  デプロイ プロセスを監視するには、ツール バーの **[通知]** (ベル アイコン) を選択します。

    デプロイが完了したら、 **[ダッシュボードにピン留めする]** を選択して、Azure portal ダッシュボードにフレキシブル サーバーのタイルを作成します。 このタイルは、サーバーの **[概要]** ページへのショートカットです。 **[リソースに移動]** を選択すると、サーバーの **[概要]** ページが開きます。

    既定では、**postgres** データベースがサーバーに作成されます。 postgres は既定のデータベースで、ユーザー、ユーティリティ、サード パーティ製のアプリケーションが使用するためのものです。 (その他の既定のデータベースは **azure_maintenance** です。 その機能は、管理されたサービス プロセスをユーザーのアクションから分離することです。 このデータベースにはアクセスできません。)


## <a name="connect-and-query"></a>接続とクエリ

Azure Database for PostgreSQL フレキシブル サーバーをリソース グループに作成したら、サーバーに接続してデータベースにクエリを実行することができます。次に示した「接続とクエリ」のクイックスタートを参照してください。
- [psql](quickstart-create-server-portal.md#connect-to-the-postgresql-database-using-psql)
- [Azure CLI](connect-azure-cli.md)
- [Python](connect-python.md)
- [Java](connect-java.md)
- [.NET](connect-csharp.md)
- [VNET 内のサーバーに接続する](quickstart-create-connect-server-vnet.md)


## <a name="monitor-and-track-free-services-usage"></a>無料サービス使用量を監視および追跡する

Azure の無料アカウントに無料で含まれている Azure Database for PostgreSQL - フレキシブル サーバー サービスについては、無料サービスの上限を超えない限り課金されません。 上限を超えないよう、Azure portal を使用して無料サービス使用量を追跡、監視してください。

1.  Azure portal で「**サブスクリプション**」を検索し、Azure の無料アカウント - **無料試用版** サブスクリプションを選択します。
1.  **[概要]** ページで、下へスクロールして **[Top free services by usage]\(無料サービス使用量ランキング\)** タイルを表示し、 **[View all free services]\(無料サービスをすべて表示\)** を選択します。

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/free-services-usage-overview.png" alt-text="無料試用版サブスクリプションの概要ページを示すスクリーンショット。[View all free services]\(無料サービスをすべて表示\) が強調表示されている。":::

1. **Azure Database for PostgreSQL - フレキシブル サーバー** に関連した以下のメーターを見つけて使用量を追跡します。

    |測定  |説明    |月ごとの上限|
    |-------|---------|-------|
    |Azure Database for PostgreSQL, Flexible Server Burstable BS Series Compute, B1MS (Azure Database for PostgreSQL、フレキシブル サーバー Burstable BS シリーズ コンピューティング、B1MS)   |コンピューティング使用量を実行時間数の観点から追跡します    |1 か月あたり 750 時間 - バースト可能な B1MS コンピューティング レベル|
    |Azure Database for PostgreSQL, Flexible Server Storage, Data Stored (Azure Database for PostgreSQL、フレキシブル サーバー ストレージ、格納データ)    |1 か月に使用された GB の観点から、プロビジョニングされたデータ ストレージを追跡します。  |32 GB/月|

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/free-services-tracking.png" alt-text="全無料サービスの使用量情報を確認、追跡するためのブレードが表示された Azure portal 画面のスクリーンショット。":::

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
    
開発ワークロード、テスト ワークロード、または予測可能な期限付き運用ワークロードにフレキシブル サーバーを使用する場合は、必要に応じてサーバーを開始および停止することで使用量を最適化してください。 サーバーを停止した後、すぐに再起動しない限り、7 日間はサーバーが停止状態のままとなります。 詳細については、TCO を下げるためにサーバーを開始または停止する方法に関するページを参照してください。 フレキシブル サーバーを停止すると、コンピューティングの使用量は発生しなくなりますが、ストレージの使用量は引き続き考慮されます。

または、これらのリソースが今後不要であると思われる場合は、リソース グループを削除してリソースを削除するか、単にこの PostgreSQL サーバーを削除してください。

- リソース グループを削除するには、次の手順に従います。
    1. Azure portal で、「**リソース グループ**」を検索して選択します。
    1. リソース グループの一覧で、リソース グループの名前を選択します。
    1. 対象のリソース グループの **[概要]** ページで、 **[リソース グループの削除]** を選択します。
    1. 確認のダイアログ ボックスでリソース グループの名前を入力し、 **[削除]** を選択します。
    
- PostgreSQL サーバーを削除するには、サーバーの **[概要]** ページで **[削除]** を選択します。
 


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
>[App Service と PostgreSQL を使用して Django アプリをデプロイする](tutorial-django-app-service-postgres.md)