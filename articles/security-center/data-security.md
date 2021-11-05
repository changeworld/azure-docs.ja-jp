---
title: Microsoft Defender for Cloud のデータ セキュリティ | Microsoft Docs
description: Microsoft Defender for Cloud でデータが管理および保護されるしくみについて説明します。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 10/13/2020
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: e0f09e842fe7a50a9b978d5882405ad6b11576a4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091944"
---
# <a name="microsoft-defender-for-cloud-data-security"></a>Microsoft Defender for Cloud のデータ セキュリティ

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud では、脅威の防止、検出、脅威への対応を支援するために、構成情報、メタデータ、イベント ログなど、セキュリティ関連のデータを収集および処理します。 Microsoft ではコーディングからサービスの運用まで、厳密なコンプライアンスとセキュリティのガイドラインに準拠しています。

この記事では、Defender for Cloud でデータが管理および保護されるしくみについて説明します。

## <a name="data-sources"></a>データ ソース
Defender for Cloud では、次のソースからのデータを分析して、セキュリティの状態の可視化、脆弱性の特定、軽減策の提案、アクティブな脅威の検出を行います。

- **Azure サービス**:デプロイされた Azure サービスの構成についての情報を、サービスのリソース プロバイダーと通信して使用します。
- **ネットワーク トラフィック**: 送信元/送信先 IP/ポート、パケット サイズ、ネットワーク プロトコルなど、Microsoft のインフラストラクチャからサンプリングされたネットワーク トラフィック メタデータを使用します。
- **パートナー ソリューション**: ファイアウォールやマルウェア対策ソリューションなど、統合されたパートナー ソリューションからのセキュリティの警告を使用します。
- **マシン**: ご利用のマシンからの構成の詳細、セキュリティ イベントに関する情報 (Windows のイベントおよび監査ログなど)、および syslog メッセージを使用します。

## <a name="data-protection"></a>データの保護

### <a name="data-segregation"></a>データの分離
データはサービス全体を通じて、各コンポーネントに論理的に分割されて保存されます。 すべてのデータは組織ごとにタグ付けされます。 このタグ付けはデータのライフ サイクルにおいて継続され、サービスの各層で強制されます。

### <a name="data-access"></a>データ アクセス
セキュリティに関する推奨事項を提供したり、潜在的なセキュリティの脅威を調査したりするために、Azure サービスによって収集または分析された情報 (プロセス作成イベントおよびその他の成果物を含む) に Microsoft の担当者がアクセスする場合があります。これらの情報には、マシンにある顧客データや個人データが意図せずに含まれていることがあります。 

Microsoft は、[Microsoft Online Services のデータ保護追加契約](https://www.microsoftvolumelicensing.com/Downloader.aspx?DocumentId=17880)を遵守しています。これらの文書には、Microsoft が宣伝や他の商業上の目的のために顧客データを使用したり、そのデータから情報を取り出したりすることはないと記載されています。 ユーザーに Azure サービスを提供するため、およびそれに準じた目的のためにだけ、顧客データを使用します。 顧客データに対するすべての権限は、お客様が保持します。

### <a name="data-use"></a>データの使用
Microsoft は、防止と検出の機能を強化するために、複数のテナントにわたって見られるパターンおよび脅威インテリジェンスを使用します。その際に、[プライバシーに関する声明](https://privacy.microsoft.com/privacystatement)で説明されている誓約に従います。

## <a name="manage-data-collection-from-machines"></a>仮想マシンからデータ収集を管理する
Azure で Defender for Cloud を有効にすると、各 Azure サブスクリプションのデータ収集が有効になります。 Defender for Cloud で、サブスクリプションのデータ収集を有効にすることもできます。 データ収集が有効になっている場合、Defender for Cloud によって、サポートされている既存の全 Azure 仮想マシンと新規に作成される仮想マシン上に Log Analytics エージェントがプロビジョニングされます。

Log Analytics エージェントは、さまざまなセキュリティ関連の構成をスキャンして、[Windows イベント トレーシング](/windows/win32/etw/event-tracing-portal) (ETW) トレースにイベント化します。 さらに、オペレーティング システムは、マシンの実行中にイベント ログ イベントを発生させます。 このようなデータの例として、オペレーティング システムの種類とバージョン、オペレーティング システムのログ (Windows イベント ログ)、実行中のプロセス、コンピューター名、IP アドレス、ログイン ユーザー、テナント ID などがあります。 Log Analytics エージェントは、イベント ログ エントリと ETW トレースを読み取り、分析のためにそれらをワークスペースにコピーします。 また、Log Analytics エージェントによって、プロセス作成イベントとコマンド ラインの監査が有効化されます。

Microsoft Defender for Cloud の強化されたセキュリティ機能を使用していない場合は、セキュリティ ポリシーで仮想マシンからのデータ収集を無効にすることもできます。 強化されたセキュリティ機能によって保護されているサブスクリプションには、データ収集が必須です。 データ収集が無効になっていても、VM ディスクのスナップショットとアーティファクトの収集は引き続き有効になります。

ご利用のマシンから収集したデータが格納されるワークスペースとリージョンを指定できます。 既定では、マシンから収集されたデータは、次の表に示すように最も近いワークスペースに格納されます。

| VM Geo                                      | ワークスペース Geo  |
|---------------------------------------------|----------------|
| 米国、ブラジル、南アフリカ         | United States  |
| Canada                                      | Canada         |
| ヨーロッパ (英国を除く)           | ヨーロッパ         |
| イギリス                              | イギリス |
| アジア (インド、日本、韓国、中国を除く) | アジア太平洋   |
| 韓国                                       | アジア太平洋   |
| インド                                       | インド          |
| 日本                                       | 日本          |
| 中国                                       | 中国          |
| オーストラリア                                   | オーストラリア      |
|                                             |                |

> [!NOTE]
> **Microsoft Defender for Storage** を使用すると、関連する Azure リソースの場所に応じて成果物がリージョンごとに格納されます。 詳細については、「[Microsoft Defender for Storage の概要](defender-for-storage-introduction.md)」を参照してください。


## <a name="data-consumption"></a>データの使用
お客様は、次のデータ ストリームから Defender for Cloud 関連のデータにアクセスできます。

| ストリーム                                                                                | データ型                                                                                                                                                                                                          |
|---------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Azure アクティビティ ログ](../azure-monitor/essentials/activity-log.md)                       | すべてのセキュリティ アラート、Defender for Cloud の承認された [Just-In-Time](just-in-time-access-usage.md) アクセス要求、[適応型アプリケーション制御](adaptive-application-controls.md)によって生成されたすべてのアラート。|
| [Azure Monitor ログ](../azure-monitor/data-platform.md)                      | すべてのセキュリティ アラート。                                                                                                                                                                                                |
| [Azure Resource Graph](../governance/resource-graph/overview.md)                      | セキュリティ アラート、セキュリティに関する推奨事項、脆弱性評価の結果、セキュリティで保護されたスコア情報、コンプライアンス チェックのステータスなど。                                                                       |
| [Microsoft Defender for Cloud REST API](/rest/api/securitycenter/) | セキュリティ アラート、セキュリティに関する推奨事項など。                                                                                                                                                                |
|                                                                                       |                                                                                                                                                                                                                     |

## <a name="next-steps"></a>次のステップ

この記事では、Microsoft Defender for Cloud でデータが管理および保護されるしくみについて説明しました。 

Microsoft Defender for Cloud の詳細については、「[Microsoft Defender for Cloud とは](defender-for-cloud-introduction.md)」を参照してください。
