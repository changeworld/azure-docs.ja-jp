---
title: データ ソースを Azure Sentinel に接続する | Microsoft Docs
description: Microsoft 365 Defender (以前の Microsoft Threat Protection)、Microsoft 365、Office 365、Azure AD、ATP、Cloud App Security などのデータ ソースを Azure Sentinel に接続する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2021
ms.author: yelevin
ms.openlocfilehash: c9bce99f26d7d23d2bf655719373f43e78b5a8cb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749367"
---
# <a name="connect-data-sources"></a>データ ソースの接続

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Azure Sentinel を有効にした後、まずデータ ソースを接続する必要があります。 Azure Sentinel には、すぐに使用できる Microsoft 製品用コネクタが多数用意されているため、リアルタイムの統合が提供されます。 たとえば、サービス間コネクタには、Microsoft 365 Defender コネクタと、Office 365、Azure Active Directory (Azure AD)、Microsoft Defender for Identity、Microsoft Cloud App Security などの Microsoft 365 ソースが含まれます。

Microsoft 以外の製品用のより広範なセキュリティ エコシステムへの組み込みコネクタを有効にすることもできます。 たとえば、[Syslog](#syslog)、[Common Event Format (CEF)](#common-event-format-cef)、または [REST API](#rest-api-integration) を使用して、お使いのデータ ソースを Azure Sentinel に接続することもできます。

詳細については、「[Azure Sentinel パートナー データ コネクタ](partner-data-connectors.md)」および「[Azure Sentinel ソリューション カタログ](sentinel-solutions-catalog.md)」を参照してください。

## <a name="enable-a-data-connector"></a>データ コネクタを更新する

Azure Sentinel でデータ コネクタを有効にするには、次の手順を実行します。

1. Azure Sentinel の左側のナビゲーション メニューで、 **[データ コネクタ]** を選択します。 **[データ コネクタ]** ページに、Azure Sentinel で提供されているコネクタとその状態の完全な一覧が表示されます。 接続するコネクタを選択し、 **[コネクタ ページを開く]** を選択します。 

   ![データ コネクタ ギャラリー](./media/collect-data/collect-data-page.png)
   
   詳細については、「[サービス間の統合](#service-to-service-integration)」および「[Azure Sentinel パートナー データ コネクタ ](partner-data-connectors.md)」を参照してください。
   

1. コネクタ ページで、すべての前提条件を満たしていることを確認し、指示に従って Azure Sentinel にデータを接続します。 ログで Azure Sentinel との同期が開始されるまでには、しばらく時間がかかる場合があります。 接続した後、データの概要 ( **[受信したデータ]** グラフ) とデータの種類の接続状態が表示されます。

   ![データ コネクタを構成する](./media/collect-data/opened-connector-page.png)
  
1. **[次のステップ]** タブを選択すると、特定のデータの種類に対して Azure Sentinel によって提供されるコンテンツが表示されます。

   ![コネクタの次のステップ](./media/collect-data/data-insights.png)

<a name="agent-options"></a>
## <a name="data-connection-methods"></a>データ接続方法
<a name="map-data-types-with-azure-sentinel-connection-options"></a>

Azure Sentinel では、次のデータ接続方法がサポートされています。

### <a name="service-to-service-integration"></a>サービス間の統合

Azure Sentinel は、Azure 基盤を使用して、次の Microsoft サービスとアマゾン ウェブ サービスに対する組み込みのサービス間サポートを提供します。

- [アマゾン ウェブ サービス - CloudTrail](connect-aws.md)
- [Azure Active Directory](connect-azure-active-directory.md) (監査ログとサインイン ログを含む)
- [Azure Active Directory Identity Protection](connect-azure-ad-identity-protection.md)
- [Azure アクティビティ](connect-azure-activity.md)
- [Azure DDoS Protection](connect-azure-ddos-protection.md)
- Azure Security Center からの [Azure Defender アラート](connect-azure-security-center.md)
- [Azure Defender for IoT](connect-asc-iot.md) (以前の Azure Security Center for IoT)
- [Azure Firewall](connect-azure-firewall.md)
- [Azure Information Protection](connect-azure-information-protection.md)。 詳細については、「[レポートを変更し、カスタム クエリを作成する方法](/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)」を参照してください。
- [Azure Key Vault](connect-azure-key-vault.md)
- [Azure Kubernetes Service (AKS)](connect-azure-kubernetes-service.md)
- [Azure SQL Databases](connect-azure-sql-logs.md)
- [Azure Storage アカウント](connect-azure-storage-account.md)
- [Azure Web Application Firewall (WAF)](connect-azure-waf.md) (以前の Microsoft WAF)
- [ドメイン ネーム サーバー](connect-dns.md)
- [Dynamics 365](connect-dynamics-365.md)
- [Microsoft 365 Defender](connect-microsoft-365-defender.md) (Microsoft 365 Defender for Endpoint から取得される Microsoft 365 Defender インシデントと生データを含む)
- [Microsoft Cloud App Security (MCAS)](connect-cloud-app-security.md)
- [Microsoft Defender for Endpoint](connect-microsoft-defender-advanced-threat-protection.md) (以前の Microsoft Defender Advanced Threat Protection)
- [Microsoft Defender for Identity](connect-azure-atp.md) (以前の Azure Advanced Threat Protection)
- [Microsoft Defender for Office 365](connect-office-365-advanced-threat-protection.md) (以前の Office 365 Advanced Threat Protection)
- [Office 365](connect-office-365.md) (Microsoft Teams を含む)
- [セキュリティ イベント (Windows)](connect-windows-security-events.md)。 詳細については、[セキュリティで保護されていないプロトコル ブックの設定](./get-visibility.md#use-built-in-workbooks)に関するページを参照してください。
- [Windows ファイアウォール](connect-windows-firewall.md)

### <a name="rest-api-integration"></a>REST API 統合

多くのセキュリティ テクノロジでは、ログ ファイルを取得するための一連の API が提供されています。一部のデータ ソースでは、これらの API を使用して Azure Sentinel に接続します。

次のセクションで説明するように、API を使用するデータ コネクタは、プロバイダー側から統合するか、Azure Functions を使用して統合します。

これらのコネクタの完全な一覧と情報については、「[Azure Sentinel パートナー データ コネクタ](partner-data-connectors.md)」を参照してください。

#### <a name="rest-api-integration-on-the-provider-side"></a>プロバイダー側での REST API 統合

プロバイダーによって構築された API 統合は、プロバイダーのデータ ソースに接続し、[Azure Monitor Data Collector API](../azure-monitor/logs/data-collector-api.md) を使用して、Azure Sentinel カスタム ログ テーブルにデータをプッシュします。

これらのデータ コネクタを構成してログを Azure Sentinel に送信する方法の詳細については、関連するプロバイダーのドキュメントを参照してください。 

#### <a name="rest-api-integration-using-azure-functions"></a>Azure Functions を使用した REST API 統合

[Azure Functions](../azure-functions/index.yml) を使用してプロバイダー API に接続する統合では、最初にデータを書式設定してから、[Azure Monitor Data Collector API](../azure-monitor/logs/data-collector-api.md) を使用して Azure Sentinel カスタム ログ テーブルに送信します。

プロバイダー API に接続し、Azure Sentinel でログを収集するためにこれらのデータ コネクタを構成する方法の詳細については、Azure Sentinel のデータ コネクタごとに示されている手順に従ってください。

また、Azure Functions を使用する統合では、Azure Sentinel データ コネクタ ページに **[Azure へのデプロイ]** ボタンが表示されます。 これらの統合については、次の手順に従って構成を簡略化します。

1. **[Azure へのデプロイ]** を選択します。
1. プロバイダー API に接続するパラメーター値を入力します。
1. Azure Sentinel ワークスペース ID とキーを入力し、Log Analytics に接続して、データを Azure Sentinel に送信します。

> [!IMPORTANT]
> Azure テナントで Azure Functions をホストするので、Azure Functions を使用する統合では、追加のデータ インジェスト コストが発生する可能性があります。 詳細については、[Azure Functions の価格に関するページ](https://azure.microsoft.com/pricing/details/functions/)を参照してください。

### <a name="agent-based-integration"></a>エージェントベースの統合

Azure Sentinel では、Syslog プロトコルを使用して、リアルタイムのログ ストリーミングを実行できるどのデータ ソースにも、エージェントを介して接続できます。 たとえば、ほとんどのオンプレミス データ ソースでは、エージェントベースの統合を介して接続します。
以下のセクションでは、Azure Sentinel エージェントベースのデータ コネクタのさまざまな種類について説明します。 エージェントベースのメカニズムを使用して接続を構成するには、各 Azure Sentinel データ コネクタ ページの手順に従います。

CEF または Syslog を介して Azure Sentinel に接続するファイアウォール、プロキシ、エンドポイントの完全な一覧については、「[Azure Sentinel パートナー データ コネクタ](partner-data-connectors.md)」を参照してください。

#### <a name="syslog"></a>syslog

Linux 用 Log Analytics エージェント (旧称 OMS エージェント) を使用して、Linux ベースの Syslog 対応デバイスから Azure Sentinel にイベントをストリーミングできます。 Log Analytics エージェントは、Log Analytics エージェントをデバイスに直接インストールできる任意のデバイスでサポートされています。

デバイスの組み込み Syslog デーモンは、指定された種類のローカル イベントを収集し、それらをローカルでエージェントに転送します。その後、ローカル イベントは Log Analytics ワークスペースにストリーミングされます。 構成が完了すると、Log Analytics Syslog テーブルにデータが表示されます。

デバイスの種類により、エージェントは、デバイス上に直接、または専用の Linux ベースのログ フォワーダー上にインストールされます。 Log Analytics エージェントは、UDP を使用して Syslog デーモンからイベントを受信します。 Linux マシンで大量の Syslog イベントを収集することが予想される場合、イベントは TCP 経由で Syslog デーモンからエージェントに送信され、そこから Log Analytics に送信されます。

詳細については、「[Syslog ベースのアプライアンスを Azure Sentinel に接続する](connect-syslog.md)」を参照してください。 

#### <a name="common-event-format-cef"></a>Common Event Format (CEF)

ログの形式は異なりますが、多くのソースでは CEF ベースの書式設定がサポートされています。 Azure Sentinel エージェントは実際には Log Analytics エージェントであり、CEF 形式のログを、Log Analytics で取り込める形式に変換します。

CEF でデータを出力するデータ ソースの場合は、Syslog エージェントを設定してから、CEF データ フローを構成します。 構成が成功すると、**CommonSecurityLog** テーブルにデータが表示されます。

詳細については、[CEF ベースのアプライアンスの Azure Sentinel への接続](connect-common-event-format.md)に関するページを参照してください。

#### <a name="custom-logs"></a>カスタム ログ

一部のデータ ソースには、Windows または Linux 上のファイルとして、収集に使用できるログがあります。 これらのログは、Log Analytics カスタム ログ収集エージェントを使用して収集できます。

Log Analytics カスタム ログ収集エージェントを使用して接続するには、各 Azure Sentinel データ コネクタ ページの手順に従います。 構成が完了すると、データはカスタム テーブルに表示されます。

詳細については、[Log Analytics エージェントを使用したカスタム ログの収集](../azure-monitor/agents/data-sources-custom-logs.md)に関するページを参照してください。

## <a name="data-connector-support"></a>データ コネクタのサポート

Microsoft と他の組織の両方が Azure Sentinel データ コネクタを作成しています。 各データ コネクタには、次のいずれかのサポートの種類があります。

| サポートの種類| 説明|
|-------------|------------|
|**Microsoft によるサポート**|適用対象:<ul><li>Microsoft がデータ プロバイダーであり、作成者であるデータ ソースのデータ コネクタ。</li><li>Microsoft 以外のデータ ソース用の Microsoft が作成した一部のデータ コネクタ。</li></ul>Microsoft では、[Microsoft Azure サポート プラン](https://azure.microsoft.com/support/options/#overview)に従って、このカテゴリのデータ コネクタをサポートし、管理しています。<br><br>パートナーまたはコミュニティでは、Microsoft 以外の任意のパーティによって作成されたデータ コネクタをサポートしています。|
|**パートナーによるサポート**|Microsoft 以外の取引先によって作成されたデータ コネクタに適用されます。<br><br>パートナー企業は、これらのデータ コネクタのサポートまたはメンテナンスを提供します。 パートナー企業は、独立系ソフトウェア ベンダー、マネージド サービス プロバイダー (MSP/MSSP)、システム インテグレーター (SI)、またはそのデータ コネクタの Azure Sentinel ページに連絡先情報が提供されている任意の組織である場合があります。<br><br>パートナーがサポートするデータ コネクタに関する問題については、指定されたデータ コネクタのサポート連絡先にお問い合わせください。|
|**コミュニティによるサポート**|Azure Sentinel の指定されたデータ コネクタ ページにデータ コネクタのサポートとメンテナンスの連絡先が記載されていない Microsoft またはパートナー開発者によって作成されたデータ コネクタに適用されます。<br><br>これらのデータ コネクタについて質問または問題がある場合は、[Azure Sentinel GitHub コミュニティ](https://aka.ms/threathunters)で[問題を報告する](https://github.com/Azure/Azure-Sentinel/issues/new/choose)ことができます。|

### <a name="find-the-support-contact-for-a-data-connector"></a>データ コネクタのサポート連絡先を見つける

データ コネクタのサポート連絡先情報を見つけるには、次の手順を実行します。

1. Azure Sentinel の左側のメニューで、 **[データ コネクタ]** を選択します。
   
1. サポート情報を検索するコネクタを選択します。
   
1. データ コネクタのサイド パネルで、 **[サポート元]** フィールドを確認します。
   
   ![Azure Sentinel でデータ コネクタの [サポート元] フィールドを表示するスクリーンショット。](./media/collect-data/connectors.png)
   
   **[サポート元]** フィールドには、選択したデータ コネクタのサポートとメンテナンスにアクセスするために使用できるサポート連絡先リンクがあります。



## <a name="next-steps"></a>次のステップ

- Azure Sentinel を使用するには、Microsoft Azure のサブスクリプションが必要です。 サブスクリプションがない場合は、[無料試用版](https://azure.microsoft.com/free/)にサインアップできます。
- [データを Azure Sentinel にオンボード](quickstart-onboard.md)し、[データや潜在的な脅威を視覚化する](get-visibility.md)方法を確認します。