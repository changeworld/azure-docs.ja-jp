---
title: GCP アカウントを Microsoft Defender for Cloud に接続する
description: Microsoft Defender for Cloud からの GCP リソースの監視
author: memildin
ms.author: memildin
ms.date: 11/09/2021
ms.topic: quickstart
ms.service: defender-for-cloud
manager: rkarlin
ms.openlocfilehash: da578e8d691a26d17a7aab329ce8263cf5066335
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525592"
---
#  <a name="connect-your-gcp-accounts-to-microsoft-defender-for-cloud"></a>GCP アカウントを Microsoft Defender for Cloud に接続する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

通常、クラウド ワークロードは複数のクラウド プラットフォームにまたがるため、クラウド セキュリティサービスもそうである必要があります。

Microsoft Defender for Cloud は、Azure、アマゾン ウェブ サービス (AWS)、および Google Cloud Platform (GCP) のワークロードを保護します。

Azure サブスクリプションに GCP アカウントを追加すると、GCP セキュリティ コマンドを使用して Defender for Cloud に接続できます。 これにより、Defender for Cloud はこの両方のクラウド環境のリソースを保護し、次の機能を提供できます。

- セキュリティ構成ミスの検出
- Defender for Cloud の推奨事項と GCP Security Command Center の検出結果を 1 つのビューに表示する
- Defender for Cloud のセキュリティ スコアの計算に GCP リソースを組み込む
- CIS 標準に基づく GCP Security Command Center の推奨事項を Defender for Cloud の規制コンプライアンス ダッシュボードに統合する

> [!IMPORTANT]
> Ignite Fall 2021 では、他のクラウド プロバイダーのアカウントを接続するための新たな方法を発表しました。 これには、新しい **[環境設定]** ページを使用します。 GCP アカウントは、そのページではサポートされていません。 GCP アカウントを Azure サブスクリプションに接続するには、下で説明するようにクラシック クラウド コネクタ エクスペリエンスを使用する必要があります。

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-account-in-overview.png" alt-text="Microsoft Defender for Cloud の概要ダッシュボードに GCP プロジェクトが表示されている状態のスクリーンショット。" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般公開 (GA)|
|価格:|[Microsoft Defender for servers](defender-for-servers-introduction.md) が必要|
|必要なロールとアクセス許可:|関連する Azure サブスクリプションの **所有者** または **共同作成者**|
|クラウド:|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/no-icon.png"::: 各国 (Azure Government、Azure China 21Vianet)|
|||

## <a name="connect-your-gcp-account"></a>GCP アカウントを接続する

Defender for Cloud から監視する組織ごとにコネクタを作成します。

GCP アカウントを特定の Azure サブスクリプションに接続するときは、[Google Cloud リソース階層](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#resource-hierarchy-detail)と次のガイドラインを考慮してください。

- GCP アカウントは、"*組織*" レベルで Defender for Cloud に接続できます
- 複数の組織を 1 つの Azure サブスクリプションに接続できます
- 複数の組織を複数の Azure サブスクリプションに接続できます
- 組織を接続すると、その組織内のすべての "*プロジェク* ト" が Defender for Cloud に追加されます

GCP クラウド コネクタを作成するには、次の手順に従います。 

### <a name="step-1-set-up-gcp-security-command-center-with-security-health-analytics"></a>手順 1. Security Health Analytics を使用して GCP Security Command Center を設定する

組織内のすべての GCP プロジェクトについて、次のことも行う必要があります。

1. [GCP のドキュメントに記載されたこちらの手順](https://cloud.google.com/security-command-center/docs/quickstart-scc-setup)を使用して、**GCP Security Command Center** を設定します。
1. [GCP のドキュメントに記載されたこちらの手順](https://cloud.google.com/security-command-center/docs/how-to-use-security-health-analytics)を使用して、**Security Health Analytics** を有効にします。
1. Security Command Center にデータが流れていることを確認します。

セキュリティ構成のために GCP 環境に接続するための手順は、セキュリティ構成の推奨事項を利用するための Google の推奨事項に従っています。 統合では Google Security Command Center が活用され、課金に影響する可能性のある追加のリソースが消費されます。

Security Health Analytics を初めて有効にしたときは、データが使用可能になるまで数時間かかることがあります。


### <a name="step-2-enable-gcp-security-command-center-api"></a>手順 2. GCP Security Command Center API を有効にする

1. Google の **Cloud Console API ライブラリ** から、Microsoft Defender for Cloud に接続する組織内の各プロジェクトを選択します。
1. API ライブラリで、**Security Command Center API** を見つけて選択します。
1. API のページで、 **[有効にする]** を選択します。

Security Command Center API の詳細については、[こちら](https://cloud.google.com/security-command-center/docs/reference/rest/)をご覧ください。


### <a name="step-3-create-a-dedicated-service-account-for-the-security-configuration-integration"></a>手順 3. セキュリティ構成統合用の専用サービス アカウントを作成する

1. **GCP コンソール** で、必要なサービス アカウントを作成する組織のプロジェクトを選択します。 

    > [!NOTE]
    > このサービス アカウントは、組織レベルで追加されると、Security Command Center によって組織内の他のすべての有効なプロジェクトから収集されたデータへのアクセスに使用されます。 

1. **ナビゲーション メニュー** の **[IAM と管理]** のオプションで、 **[サービス アカウント]** を選択します。
1. **[サービス アカウントを作成]** を選択します。
1. アカウント名を入力し、 **[作成]** を選択します。
1. **ロール** として **[Defender for Cloud Admin Viewer]\(Defender for Cloud 管理者ビューアー\)** を指定し、 **[続行]** を選択します。
1. **[ユーザーにこのサービス アカウントへのアクセスを許可]** セクションは省略可能です。 **[完了]** を選択します。
1. 作成したサービス アカウントの **[メール] の値** をコピーし、後で使用できるように保存します。
1. **ナビゲーション メニュー** の **[IAM と管理]** のオプションで、 **[IAM]** を選択します。
    1. 組織レベルに切り替えます。
    1. **[追加]** を選択します。
    1. **[新しいメンバー]** フィールドに、先ほどコピーした **[メール] の値** を貼り付けます。
    1. ロールとして **[Defender for Cloud Admin Viewer]\(Defender for Cloud 管理者ビューアー\)** を指定し、 **[保存]** を選択します。
        :::image type="content" source="./media/quickstart-onboard-gcp/iam-settings-gcp-permissions-admin-viewer.png" alt-text="関連する GCP のアクセス許可の設定。":::


### <a name="step-4-create-a-private-key-for-the-dedicated-service-account"></a>手順 4. 専用サービス アカウントの秘密キーを作成する
1. プロジェクト レベルに切り替えます。
1. **ナビゲーション メニュー** の **[IAM と管理]** のオプションで、 **[サービス アカウント]** を選択します。
1. 専用サービス アカウントを開き、[編集] を選択します。
1. **[キー]** セクションで、 **[キーを追加]** 、 **[新しいキーの作成]** の順に選択します。
1. 秘密キーの作成画面で、 **[JSON]** を選択し、 **[作成]** を選択します。
1. 後で使用するために、この JSON ファイルを保存します。


### <a name="step-5-connect-gcp-to-defender-for-cloud"></a>手順 5. GCP から Defender for Cloud に接続する
1. Defender for Cloud のメニューから **[環境設定]** を開き、クラシック コネクタ エクスペリエンスに切り替えるオプションを選択します。

    :::image type="content" source="media/quickstart-onboard-gcp/classic-connectors-experience.png" alt-text="Defender for Cloud のクラシック クラウド コネクタ エクスペリエンスに切り替える。":::

1. [add GCP account]\(GCP アカウントの追加\) を選択します。
1. オンボード ページで次の操作を行い、 **[次へ]** を選択します。
    1. 選択したサブスクリプションを検証します。
    1. **[表示名]** フィールドに、コネクタの表示名を入力します。
    1. **[組織 ID]** フィールドに、組織の ID を入力します。 不明な場合は、「[組織の作成と管理](https://cloud.google.com/resource-manager/docs/creating-managing-organization)」をご覧ください。
    1. **[秘密キー ファイル]** ボックスで、「[手順 4. 専用サービス アカウントの秘密キーを作成する](#step-4-create-a-private-key-for-the-dedicated-service-account)」でダウンロードした JSON ファイルを参照します。


### <a name="step-6-confirmation"></a>手順 6. 確認

コネクタが正常に作成され、GCP Security Command Center が正しく構成されたら、次のようになります。

- Defender for Cloud の規制コンプライアンス ダッシュボードに GCP CIS 標準が表示されます。
- GCP リソースへのセキュリティに関する推奨事項は、オンボードの完了後 5 から 10 分経過すると Defender for Cloud ポータルと規制コンプライアンス ダッシュボードに表示されます。:::image type="content" source="./media/quickstart-onboard-gcp/gcp-resources-in-recommendations.png" alt-text="Defender for Cloud の推奨事項ページの GCP リソースと推奨事項":::


## <a name="monitoring-your-gcp-resources"></a>GCP リソースの監視

上記のように、Microsoft Defender for Cloud のセキュリティに関する推奨事項ページには、Azure および AWS リソースと共に GCP リソースが表示され、真のマルチクラウド ビューが実現します。

リソースの種類別に、リソースのアクティブな推奨事項をすべて表示するには、Defender for Cloud の資産インベントリ ページを使用し、関心のある GCP リソースの種類にフィルターを適用します。

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-resource-types-in-inventory.png" alt-text="GCP オプションを示す資産インベントリ ページのリソースの種類のフィルター"::: 


## <a name="faq---connecting-gcp-accounts-to-microsoft-defender-for-cloud"></a>FAQ - Microsoft Defender for Cloud への GCP アカウントの接続

### <a name="can-i-connect-multiple-gcp-organizations-to-defender-for-cloud"></a>複数の GCP 組織を Defender for Cloud に接続できますか?
正解です。 Defender for Cloud の GCP コネクタは、"*組織*" レベルで Google Cloud リソースを接続します。 

Defender for Cloud から監視する GCP 組織ごとにコネクタを作成します。 組織を接続すると、その組織内のすべてのプロジェクトが Defender for Cloud に追加されます。

Google Cloud リソース階層については、[Google のオンライン ドキュメント](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy)をご覧ください。


### <a name="is-there-an-api-for-connecting-my-gcp-resources-to-defender-for-cloud"></a>GCP リソースを Defender for Cloud に接続するための API はありますか?
正解です。 REST API を使用して Defender for Cloud クラウド コネクタを作成、編集、または削除するには、[Connectors API](/rest/api/securitycenter/connectors) の詳細をご覧ください。

## <a name="next-steps"></a>次の手順

GCP アカウントの接続は、Microsoft Defender for Cloud で利用できるマルチクラウド エクスペリエンスの一部です。 関連情報については、次のページを参照してください。

- [AWS アカウントを Microsoft Defender for Cloud に接続する](quickstart-onboard-aws.md)
- [Google Cloud リソース階層](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy) -- Google Cloud リソース階層については、Google のオンライン ドキュメントをご覧ください
