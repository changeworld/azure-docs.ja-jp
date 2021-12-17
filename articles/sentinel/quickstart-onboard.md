---
title: 'クイックスタート: Microsoft Sentinel でオンボードする'
description: このクイックスタートでは、最初に Microsoft Sentinel を有効にし、それからデータ ソースを接続することで Sentinel をオンボードする方法について説明します。
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.topic: quickstart
ms.date: 11/09/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 3c9eab035b48b419a607f5fcdc0738502bc732e4
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712369"
---
# <a name="quickstart-on-board-microsoft-sentinel"></a>クイックスタート: Microsoft Sentinel をオンボードする

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

このクイックスタートでは、Microsoft Sentinel をオンボードする方法について説明します。 Microsoft Sentinel をオンボードするには、まず Microsoft Sentinel を有効にしてから、データ ソースを接続する必要があります。

Microsoft Sentinel には、Microsoft 365 Defender (旧 Microsoft Threat Protection) ソリューション、Microsoft 365 ソース (Office 365 を含む)、Azure AD、Microsoft Defender for Identity (旧 Azure ATP)、Microsoft Cloud App Security、Microsoft Defender for Cloud からのアラートなど、すぐに使用できる、かつリアルタイムの統合を提供する Microsoft ソリューション用コネクタが多数付属しています。 さらに、Microsoft 以外のソリューション用のより広範なセキュリティ エコシステムへの組み込みコネクタがあります。 一般的なイベント形 (CEF)、Syslog、または REST-API を使用して、データ ソースを Microsoft Sentinel に接続することもできます。

データ ソースを接続した後、優れた設計のブックのギャラリーから選択し、データに基づいて分析情報を表示できます。 これらのブックは、ニーズに合わせて簡単にカスタマイズできます。

>[!IMPORTANT]
> Microsoft Sentinel の使用に伴う料金については、「[Microsoft Sentinel の価格](https://azure.microsoft.com/pricing/details/azure-sentinel/)」および「[Microsoft Sentinel のコストと課金](billing.md)」を参照してください。

## <a name="global-prerequisites"></a>グローバルな前提条件

- アクティブな Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

- Log Analytics ワークスペース。 [Log Analytics ワークスペースの作成方法](../azure-monitor/logs/quick-create-workspace.md)を確認してください。 Log Analytics ワークスペースの詳細については、「[Azure Monitor ログのデプロイの設計](../azure-monitor/logs/design-logs-deployment.md)」を参照してください。

- Microsoft Sentinel を有効にするには、Microsoft Sentinel ワークスペースが存在するサブスクリプションへの共同作成者のアクセス許可が必要です。 
- Microsoft Sentinel を使用するには、ワークスペースが属しているリソース グループに対する共同作成者または閲覧者のいずれかのアクセス許可が必要です。
- 特定のデータ ソースに接続するには、追加のアクセス許可が必要になる場合があります。
- Microsoft Sentinel は有料サービスです。 詳細については、「[Microsoft Sentinel とは](https://go.microsoft.com/fwlink/?linkid=2104058)」を参照してください。

詳細については、「[Microsoft Sentinel のデプロイ前のアクティビティとデプロイの前提条件](prerequisites.md)」を参照してください。

### <a name="geographical-availability-and-data-residency"></a>リージョン別の提供状況とデータの保存場所

- Microsoft Sentinel は、中国およびドイツ (ソブリン) リージョンを除く、[Log Analytics のほとんどの GA リージョン](https://azure.microsoft.com/global-infrastructure/services/?products=monitor)のワークスペースで実行できます。 Log Analytics の新しいリージョンでは、Microsoft Sentinel サービスのオンボードに時間がかかる場合があります。 

- インシデント、ブックマーク、分析ルールなど、Microsoft Sentinel によって生成されたデータには、顧客の Log Analytics ワークスペースをソースとする顧客データが含まれている可能性があります。 Microsoft Sentinel で生成されたこのデータは、ワークスペースが配置されている地理的な場所に応じて、次の表に示す地理的な場所に保存されます。

    | ワークスペースの地理的な場所 | Microsoft Sentinel で生成されたデータの地理的な場所 |
    | --- | --- |
<<<<<<< HEAD
    | アメリカ<br>インド<br>ブラジル<br>アフリカ<br>韓国<br>アラブ首長国連邦 | United States |
    | ヨーロッパ<br>フランス<br>スイス | ヨーロッパ |
=======
    | United States<br>インド | United States |
    | ヨーロッパ<br>フランス | ヨーロッパ |
>>>>>>> repo_sync_working_branch
    | オーストラリア | オーストラリア |
    | イギリス | イギリス |
    | カナダ | カナダ |
    | 日本 | 日本 |
    | アジア太平洋 | アジア太平洋 * |
    | ブラジル | ブラジル * |
    | ノルウェー | ノルウェー |
    | アフリカ | アフリカ |
    | 韓国 | 韓国 |
    | ドイツ | ドイツ |
    | アラブ首長国連邦 | アラブ首長国連邦 |
    | スイス | スイス |
    |

    \* 現在、単一リージョンのデータ所在地は、アジア太平洋地域の東南アジア リージョン (シンガポール) と、ブラジル地域のブラジル南部リージョン (サンパウロ州) でのみ使用できます。 その他のすべてのリージョンでは、Sentinel がオンボードされているワークスペースの任意の地理的な場所に顧客データを格納できます。

    > [!IMPORTANT]
    > - 機械学習 (ML) エンジンを利用する特定の規則を有効にすることで、機械学習エンジンがこれらの規則を処理するために必要な場合に、**Microsoft Sentinel ワークスペースの地理的な場所以外で取り込まれた関連データをコピーするためのアクセス許可を Microsoft に付与** します。

## <a name="enable-microsoft-sentinel"></a>Microsoft Sentinel を有効にする<a name="enable"></a>

1. Azure portal にサインインします。 Microsoft Sentinel が作成されたときのサブスクリプションが選択されていることをご確認ください。

1. **Microsoft Sentinel** を検索して選択します。

   ![サービスの検索](./media/quickstart-onboard/search-product.png)

1. **[追加]** を選択します。

1. 使用するワークスペースを選択するか、新しいワークスペースを作成します。 複数のワークスペースで Microsoft Sentinel を実行できますが、データは 1 つのワークスペースに分離されます。

   ![ワークスペースの選択](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Microsoft Defender for Cloud によって作成された既定のワークスペースは、一覧に表示されません。Microsoft Sentinel をそれらにインストールすることはできません。
   >

   >[!IMPORTANT]
   >
   > - Microsoft Sentinel がワークスペースにデプロイされた後に、そのワークスペースを他のリソース グループやサブスクリプションに移動することは、**現在はサポートされていません**。 
   >
   >   ワークスペースを既に移動している場合は、 **[Analytics]** の下のアクティブなルールをすべて無効にし、5 分後に再び有効にします。 これは、ほとんどの場合に効果的です。ただし、繰り返しますが、サポートされておらず、ご自身の責任で行ってください。

1. **[Microsoft Sentinel の追加]** を選びます。

## <a name="connect-data-sources"></a>データ ソースの接続

Microsoft Sentinel でサービスとアプリからのデータを取り込むには、サービスに接続して、Microsoft Sentinel にイベントとログを転送します。 物理マシンと仮想マシンの場合、ログを収集して Microsoft Sentinel に転送する Log Analytics エージェントをインストールできます。 ファイアウォールとプロキシの場合は、Microsoft Sentinel によって Log Analytics エージェントが Linux Syslog サーバーにインストールされます。エージェントがここでログ ファイルを収集して Microsoft Sentinel に転送します。 
 
1. メイン メニューで **[Data connectors]\(データ コネクタ\)** を選択します。 これにより、データ コネクタ ギャラリーが開きます。

1. このギャラリーは、接続できるすべてのデータ ソースの一覧です。 データ ソースを選択してから、 **[Open connector page]\(コネクタ ページを開く\)** ボタンを選択します。

1. コネクタ ページには、コネクタを構成するための手順や、必要になる可能性のあるその他の手順が表示されます。

    たとえば、Azure AD から Microsoft Sentinel にログをストリーム配信できる **Azure Active Directory** データ ソースを選択した場合、取得するログの種類 (サインイン ログまたは監査ログ、あるいはその両方) を選択できます。 <br> インストール手順を実行するか、[関連する接続のガイドを参照](data-connectors-reference.md)して詳細をご確認ください。 データ コネクタについては、「[Microsoft Sentinel のデータ コネクタ](connect-data-sources.md)」をご覧ください。

1. コネクタ ページの **[次の手順]** タブには、データ コネクタに付随する組み込みのブック、サンプル クエリ、および分析ルール テンプレートが表示されます。 これらはそのまま使用することも、変更することもできます。どちらの場合も、データに関する興味深い分析情報をすぐに得ることができます。

データ ソースが接続されると、データは Microsoft Sentinel にストリーミングされ、操作を開始できます。 [組み込みのブック](get-visibility.md)でログを表示したり、Log Analytics でクエリを作成して[データを調査](investigate-cases.md)したりできます。

詳細については、[データ収集のベスト プラクティス](best-practices-data.md)を参照してください。

## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

- **代替のデプロイ/管理オプション**:

    - [ARM テンプレートを使用して Microsoft Sentinel をデプロイする](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-all-in-one-accelerator/ba-p/1807933)
    - [API を使用して Microsoft Sentinel を管理する](/rest/api/securityinsights/)
    - [PowerShell を使用して Microsoft Sentinel を管理する](https://www.powershellgallery.com/packages/Az.SecurityInsights/0.1.0)

- **作業開始**:
    - [Microsoft Quantum の概要](get-visibility.md)
    - [脅威を検出するためのカスタム分析規則を作成する](detect-threats-custom.md)
    - [共通イベント形式を使用して外部ソリューションを接続する](connect-common-event-format.md)
