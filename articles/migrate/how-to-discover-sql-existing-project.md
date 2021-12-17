---
title: 既存の Azure Migrate プロジェクト内の SQL Server インスタンスを検出する
description: 既存の Azure Migrate プロジェクト内の SQL Server インスタンスを検出する方法について説明します。
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 38169324211a22012426b895d66bc6d0015f5587
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740356"
---
# <a name="discover-web-apps-and-sql-server-instances-in-an-existing-project"></a>既存のプロジェクト内の Web アプリと SQL Server インスタンスを検出する

この記事では、Azure SQL 評価機能のプレビューと Azure App Service 評価機能のプレビューのいずれかまたは両方より前に作成された [Azure Migrate](./migrate-services-overview.md) プロジェクト内にある Web アプリ、SQL Server インスタンス、データベースを検出する方法について説明します。

オンプレミス マシンで実行されている ASP.NET Web アプリおよび SQL Server インスタンスとデータベースを検出すると、Azure への移行パスを特定し、調整するのに役立ちます。 Azure Migrate アプライアンスでは、ターゲット サーバーで実行されている SQL Server インスタンスとデータベースにアクセスできる、Windows OS ドメインまたは非ドメインの資格情報または SQL Server 認証資格情報を使用して、この検出が実行されます。
この検出プロセスはエージェントレスです。つまり、ターゲット サーバーには何もインストールされていません。

## <a name="before-you-start"></a>開始する前に

- 次を完了したことを確認します。
    - お客様のリージョンに対する SQL と Web アプリの評価機能が発表される前に、[Azure Migrate プロジェクト](./create-manage-projects.md)を作成した
    - プロジェクトに [Azure Migrate: 検出および評価](./how-to-assess.md)ツールを追加した
- [アプリ検出のサポートと要件](./migrate-support-matrix-vmware.md#vmware-requirements)を確認します。
-  アプリ検出を実行するサーバーに PowerShell バージョン 2.0 以降がインストールされていること、および VMware ツール (10.2.0 より後) がインストールされていることを確認します。
- Azure Migrate アプライアンスを展開するための[要件](./migrate-appliance.md)を確認します。
- リソースを作成するために[必要なロール](./create-manage-projects.md#verify-permissions)がサブスクリプションにあることを確認します。
- アプライアンスがインターネットにアクセスできることを確認します

## <a name="enable-discovery-of-aspnet-web-apps-and-sql-server-instances-and-databases"></a>ASP.NET Web アプリおよび SQL Server インスタンスとデータベースの検出を有効にする

1. Azure Migrate プロジェクトで、次のいずれかを実行します。
    - ハブ タイルで **[無効]** を選択します。:::image type="content" source="./media/how-to-discover-sql-existing-project/hub-not-enabled.png" alt-text="SQL と Web アプリの検出が無効になっている Azure Migrate のハブ タイル":::
    - サーバー検出ページの [SQL インスタンス] 列または [Web アプリ] 列のいずれかのエントリで、 **[無効]** を選択します。:::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-not-enabled.png" alt-text="SQL と Web アプリの検出が無効になっている Azure Migrate の [検出済みサーバー] ブレード":::
2. ASP.NET Web アプリおよび SQL Server インスタンスとデータベースを検出するには、次の手順に従います。
    - **[アップグレード]** を選択して、必要なリソースを作成します。
        :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-upgrade-appliance.png" alt-text="Azure Migrate アプライアンスをアップグレードするボタン":::
    - アプライアンスで実行しているサービスが最新バージョンに更新されていることを確認します。 これを行うには、アプライアンス サーバーからアプライアンス構成マネージャーを起動し、[前提条件のセットアップ] パネルから [アプライアンス サービスを表示] を選択します。
        - アプライアンスとそのコンポーネントが自動的に更新されます:::image type="content" source="./media/how-to-discover-sql-existing-project/appliance-services-version.png" alt-text="アプライアンスのバージョンを確認します":::
    - アプライアンス構成マネージャーの [資格情報と検出ソースの管理] パネルで、検出される SQL Server インスタンスとデータベースに対して Sysadmin アクセス権を持つドメインまたは SQL Server 認証の資格情報を追加します。
    - 使用されるアカウントにサーバーでのローカル管理者特権がある限り、ASP.NET Web アプリの検出は、ドメインとドメイン以外の両方の Windows OS 資格情報で機能します。
    [こちら](./tutorial-discover-vmware.md#start-continuous-discovery)で強調されているように、アプライアンスの自動資格情報マッピング機能を利用できます。

    注意：
    - ソフトウェア インベントリが既に有効になっていることを確認するか、またはドメインまたはドメイン以外の資格情報を指定してソフトウェア インベントリを有効にしてください。 SQL Server インスタンスと ASP.NET Web アプリを検出するには、ソフトウェア インベントリを実行する必要があります。
    - アプライアンスでは、ドメイン資格情報が追加されたときに、AD で検証を試みます。 資格情報に関連付けられている AD サーバーへのネットワーク通信経路がアプライアンス サーバーにあることを確認してください。 ドメイン以外の資格情報と SQL Server 認証に関連付けられている資格情報は、検証されません。

3. 必要な資格情報が追加されたら、[検出の開始] を選択してスキャンを開始します。

> [!Note]
>Web アプリと SQL の検出をしばらく実行させてから、Azure App Service または Azure SQL の評価を作成してください。 Web アプリおよび SQL Server インスタンスとデータベースの検出を完了できない場合、評価レポートでそれぞれのインスタンスが **[不明]** としてマークされます。

## <a name="next-steps"></a>次のステップ

- [Azure SQL 評価](./how-to-create-azure-sql-assessment.md)を作成する方法をご覧ください
- [Azure SQL 評価](./concepts-azure-sql-assessment-calculation.md)の詳細をご覧ください
- [Azure App Service の評価](./how-to-create-azure-app-service-assessment.md)の作成方法を確認します
- [Azure App Service の評価](./concepts-azure-webapps-assessment-calculation.md)の詳細を確認します