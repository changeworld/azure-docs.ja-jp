---
title: "Power BI Embedded に Power BI ワークスペース コレクションのコンテンツを移行する方法 | Microsoft Docs"
description: "Power BI Embedded に Power BI ワークスペース コレクションのコンテンツを移行してアプリ内に埋め込む最新技術について説明します。"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: 069f31c8213bd0d8586f7ca50e543acfdad8a2b3
ms.sourcegitcommit: 09a2485ce249c3ec8204615ab759e3b58c81d8cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2018
---
# <a name="how-to-migrate-power-bi-workspace-collection-content-to-power-bi-embedded"></a>Power BI Embedded に Power BI ワークスペース コレクションのコンテンツを移行する方法

Power BI Embedded に Power BI ワークスペース コレクションのコンテンツを移行する方法について説明します。 この記事では、Azure の Power BI ワークスペース コレクションから Power BI Embedded に移行する方法について説明します。 また、アプリケーションの変更についても説明します。

Power BI ワークスペース コレクションのリソースは、Power BI Premium リリースの一般提供後、一定期間、使用を続けられます。 エンタープライズ契約を契約いただいているお客様は、既存の契約の有効期限中は、既存のワークスペース コレクションをご利用いただけます。 Power BI ワークスペース コレクションを Direct または CSP チャネルを通じてご利用のお客様は、Power BI Premium の一般提供期間から 1 年間、ご利用いただけます。

> [!IMPORTANT]
> 移行には Power BI サービスを使用しますが、**埋め込みトークン**を使用すれば、お客様のアプリケーションのユーザーは Power BI を使用する必要はありません。 アプリケーションに埋め込まれたコンテンツを表示するために、Power BI にサインアップする必要はありません。 お客様は、お客様の顧客に、この Power BI 埋め込み機能を使用できます。

![Power BI Embedded のフロー](media/migrate-from-power-bi-workspace-collections/powerbi-embed-flow.png)

## <a name="prepare-for-the-migration"></a>移行を準備する

Power BI ワークスペース コレクション サービスから Power BI Embedded へ移行するためには、必要な準備がいくつかあります。 Power BI Pro ライセンスを持つユーザーと、テナントが必要です。

1. Azure Active Directory (Azure AD) テナントにアクセスできることを確認してください。

    どのテナントを使用しますか。

    * 既存の企業 Power BI テナントを使用しますか。
    * アプリケーションごとに個別のテナントを使用しますか。
    * 顧客ごとに個別のテナントを使用しますか。

    アプリケーション、または顧客ごとに新しいテナントを作成する場合は、次のいずれかを参照してください。

    * [Azure Active Directory テナントの作成](https://powerbi.microsoft.com/documentation/powerbi-developer-create-an-azure-active-directory-tenant/)
    * [Azure Active Directory テナントを取得する方法](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant)

2. アプリケーションの "マスター" アカウントとして動作する、この新しいテナントの中にユーザーを作成します。 そのアカウントは Power BI にサインアップするために必要で、Power BI Pro ライセンスが付与されている必要があります。

## <a name="accounts-within-azure-ad"></a>Azure AD 内のアカウント

次のアカウントが、テナント内に存在する必要があります。

> [!NOTE]
> アプリのワークスペースを使用するためには、これらのアカウントに Power BI Pro ライセンスが付与されている必要があります。

1. テナント管理者ユーザー。

    埋め込むアプリ ワークスペースに、テナント管理者をメンバーとして含めることをお勧めします。

2. コンテンツを作成するアナリスト向けアカウント。

    必要に応じて、アプリのワークスペースにこれらのユーザーを割り当てる必要があります。

3. アプリケーション *マスター* ユーザー アカウントまたはサービス アカウント。

    アプリケーションのバックエンドで、このアカウントの資格情報が格納されます。 *マスター* アカウントを使用して、Power BI REST API で使用する Azure AD トークンを取得します。 このアカウントは、アプリケーションの埋め込みトークンの生成に使用されます。 *マスター* アカウントは、埋め込み用に作成されたアプリのワークスペースの管理者である必要があります。

    **このアカウントは、組織の通常のユーザー アカウントですが、埋め込みの目的で使用されます。**

## <a name="app-registration-and-permissions"></a>アプリの登録とアクセス許可

REST API を呼び出すには、アプリケーションを Azure AD に登録します。 Power BI アプリ登録ページだけでなく、Microsoft Azure ポータル内でも、追加構成が適用されます。 詳細については、「[Azure AD アプリを登録して Power BI コンテンツを埋め込む](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/)」を参照してください。

アプリケーションの**マスター** アカウントを使用してアプリケーションを登録することをお勧めします。

## <a name="create-app-workspaces-required"></a>アプリ ワークスペース (必須) を作成します。

アプリケーションで複数の顧客にサービスを提供している場合は、アプリのワークスペースを利用して、より効果的に分離できます。 ダッシュ ボードとレポートは、顧客ごとに分離されます。 アプリ ワークスペースごとに異なる Power BI アカウントを使用すれば、1 つのアカウントだけで簡単に顧客のアプリケーション エクスペリエンスを分離できます。

> [!IMPORTANT]
> 顧客への埋め込み機能は、個人用ワークスペース ("マイ ワークスペース") では使用できません。

Power BI 内でアプリケーション ワークスペースを作成するには、Pro ライセンスを持つユーザーが必要です。 アプリ ワークスペースを作成する Power BI ユーザーとは、既定で、そのワークスペースの管理者です。 **アプリケーション *マスター* アカウントは、ワークスペースの管理者である必要があります。**

## <a name="content-migration"></a>コンテンツの移行

ワークスペース コレクションから Power BI Embedded へのコンテンツの移行は、現在のソリューションと並行して行うことができ、ダウンタイムが必要ありません。

**移行ツール**を使用すると、Power BI ワークスペース コレクションから Power BI Embedded にコンテツを簡単にコピーすることができます。 これは、特に多数のレポートを使用している場合に便利です。 詳細については、「[Power BI Embedded 移行ツール](migrate-tool.md)」を参照してください。

コンテンツの移行には、主に 2 つの API が使用されます。

1. Download PBIX - この API は、2016 年 10 月以降 Power BI にアップロードされた PBIX ファイルをダウンロードできます。
2. Import PBIX - この API は、Power BI に PBIX をアップロードします。

関連するコード スニペットについては、「[Power BI Embedded からコンテンツを移行するためのコード スニペット](migrate-code-snippets.md)」を参照してください。

### <a name="report-types"></a>レポートの種類

レポートにはいくつか種類があり、それぞれ異なる移行フローが必要です。

#### <a name="cached-dataset-and-report"></a>キャッシュされたデータセットとレポート

キャッシュされたデータセットとは、ライブ接続または DirectQuery 接続とは異なり、データがインポートされた PBIX ファイルを指します。

**フロー**

1. Power BI ワークスペース コレクション ワークスペースから Download PBIX API を呼び出します。
2. PBIX を保存します。
3. Power BI Embedded ワークスペースに Import PBIX を呼び出します。

#### <a name="directquery-dataset-and-report"></a>DirectQuery データセットとレポート

**フロー**

1. GET https://api.powerbi.com/v1.0/collections/{collection_id}/workspaces/{wid}/datasets/{dataset_id}/Default.GetBoundGatewayDataSources を呼び出し、受信した接続文字列を保存します。
2. Power BI ワークスペース コレクション ワークスペースから Download PBIX API を呼び出します。
3. PBIX を保存します。
4. Power BI Embedded ワークスペースに Import PBIX を呼び出します。
5. POST  https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.SetAllConnections を呼び出して接続文字列を更新します。
6. GET https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.GetBoundGatewayDataSources を呼び出して GW ID とデータ ソース ID を取得します。
7. PATCH https://api.powerbi.com/v1.0/myorg/gateways/{gateway_id}/datasources/{datasource_id} を呼び出してユーザーの資格情報を更新します。

#### <a name="old-dataset-and-reports"></a>古いデータセットとレポート

2016 年 10 月より前にアップロードされたレポートは、PBIX のダウンロード機能をサポートしていません。

**フロー**

1. 開発環境 (内部のソース管理) から PBIX を取得します。
2. Power BI Embedded ワークスペースに Import PBIX を呼び出します。

#### <a name="push-dataset-and-report"></a>データセットとレポートのプッシュ

Download PBIX は、*Push API* データセットをサポートしていません。 Push API データセットのデータを、Power BI ワークスペース コレクションから Power BI Embedded に移植することはできません。

**フロー**

1. データセット Json を使って、"Create dataset" API を呼び出して、Power BI Embedded ワークスペースを作成します。
2. 作成したデータセットのレポートを再構築します。*

次のような回避策を使用すれば、Push API レポートを Power BI ワークスペース コレクションから Power BI Embedded に移行することができます。

1. Power BI ワークスペース コレクション ワークスペースにダミー PBIX をアップロードします。
2. Push API レポートを複製し、手順 1 で作ったダミー PBIX にバインドします。
3. ダミー PBIX を使って Push API レポートをダウンロードします。
4. ダミー PBIX を Power BI Embedded ワークスペースにアップロードします。
5. Power BI Embedded ワークスペースで Push データセットを作成します。
6. レポートを Push API データセットに再バインドします。

## <a name="create-and-upload-new-reports"></a>新しいレポートを作成し、アップロードします。

Power BI ワークスペース コレクションから移行したコンテンツだけでなく、Power BI Desktop を使用してレポートやデータセットを作成し、アプリのワークスペースでこれらのレポートを発行することもできます。 アプリのワークスペースにレポートを発行するエンドユーザーは、Power BI Pro ライセンスを持っている必要があります。

## <a name="rebuild-your-application"></a>アプリケーションのリビルド

1. Power BI REST API と powerbi.com 内のレポート場所を使用するよう、アプリケーションを変更します。

2. アプリケーションの*マスター* アカウントを使用して AuthN/AuthZ 認証を再構築します。 [埋め込みトークン](https://msdn.microsoft.com/library/mt784614.aspx)を使用することで、このユーザーを他のユーザーの代理として機能させることができます。

3. Power BI Embedded からアプリケーションにレポートを埋め込みます。 詳細については、「[Power BI ダッシュ ボード、レポートおよびタイルを埋め込む方法](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/)」を参照してください。

## <a name="map-your-users-to-a-power-bi-user"></a>Power BI のユーザーに、ユーザーをマップする

アプリケーションの中から、アプリケーション内で管理するユーザーを、アプリケーションのための *マスター* Power BI 資格情報に割り当てます。 この Power BI *マスター* アカウントの資格情報は、アプリケーション内に格納され、埋め込みトークンの作成に使用されます。

## <a name="what-to-do-when-you-are-ready-for-production"></a>実稼働環境の準備ができたら

実稼働環境に移行する準備ができたら、次の操作が必要です。

- 開発用に別のテナントを使用している場合は、アプリ ワークスペース、およびダッシュ ボードとレポートが、実稼働環境で利用できることを確認する必要があります。 Azure AD で実稼働環境テナント用に アプリケーションを作成し、手順 1 で説明されている適切なアプリのアクセス許可が割り当てられていることを確認してください。

- ニーズに合った容量を購入します。 必要な容量については、「[Embedded analytics capacity planning whitepaper](https://aka.ms/pbiewhitepaper)」 (埋め込み分析の容量計画に関するホワイト ペーパー) を参照してください。 購入する準備ができたら、Azure ポータルで Power BI Embedded のリソースを購入できます。

- アプリ ワークスペースを編集し、[詳細] で容量を割り当てます。

    ![アプリ ワークスペースを powerbi.com 内の容量に割り当てる](media/migrate-from-power-bi-workspace-collections/embedded-capacity.png)

- 更新されたアプリケーションを実稼働環境に展開し、Power BI Embedded からのレポートの埋め込みを開始します。

## <a name="after-migration"></a>移行後

Power BI ワークスペース コレクション内でクリーン アップを行う必要があります。

- Power BI ワークスペース コレクションの Azure サービス内で、配置されたソリューションからすべてのワークスペースを削除します。
- Azure 内に存在するすべてのワークスペース コレクションを削除します。

## <a name="next-steps"></a>次の手順

おめでとうございます。 これで、アプリケーションは Power BI Embedded に移行されました。 Power BI ダッシュ ボード、レポート、データセットを埋め込む方法については、「[Power BI ダッシュ ボード、レポートおよびタイルを埋め込む方法](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/)」を参照してください。

ご質問は、 [Power BI コミュニティで質問してみてください](http://community.powerbi.com/)。