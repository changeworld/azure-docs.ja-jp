---
title: ITSM Connector での問題のトラブルシューティング
description: IT Service Management Connector での問題のトラブルシューティング
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 2ffe7c8994d32917a08896c7d25f20d4adf09066
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/20/2021
ms.locfileid: "98601904"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>ITSM Connector での問題のトラブルシューティング

この記事では、ITSM Connector の一般的な問題とそのトラブルシューティングの方法について説明します。

Azure Monitor のアラートは、監視データで重要な状態が見つかると事前に通知します。 管理者は、その通知を見て、システムのユーザーが問題に気付く前に問題を識別して対処できます。 アラートの詳細については、「Microsoft Azure のアラートの概要」を参照してください。
お客様は、メール、SMS、Webhook を使用するか、ソリューションを自動化するかどうかにかかわらず、アラートを通知する方法を選択できます。 通知を受けるもう 1 つのオプションは、ITSM を使用するものです。
ITSM には、ServiceNow などの外部のチケット発行システムにアラートを送信するオプションが用意されています。

## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>インシデントと変更要求データを視覚化および分析する

ITSMC では、接続を設定したときの構成に応じて、最大 120 日分のインシデントと変更要求データを同期できます。 このデータのログ レコードのスキーマについては、この記事の[追加情報のセクション](./itsmc-synced-data.md)をご覧ください。

インシデントと変更要求データは、ITSMC ダッシュボードを使用して視覚化できます。

![ITSMC ダッシュボードを示すスクリーンショット。](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

ダッシュボードでは、コネクタの状態に関する情報も提供されます。これは、接続の問題を分析する際の開始点として利用できます。

ダッシュボードでの調査の詳細については、「[ダッシュボードを使用したエラー調査](./itsmc-dashboard.md)」を参照してください。

### <a name="service-map"></a>Service Map

Service Map で、影響を受けたコンピューターに対して同期されたインシデントを視覚化することもできます。

サービス マップは、Windows と Linux システムのアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップします。 これを使用すると、重要なサービスを提供する相互接続されたシステムとしてサーバーを表示できます。 Service Map には、TCP 接続の任意のアーキテクチャ全体にわたるサーバー、プロセス、ポートの間の接続が表示されます。 エージェントのインストール以外に構成は必要ありません。 詳細については、[Service Map の使用](../insights/service-map.md)に関するページを参照してください。

Service Map を使用している場合は、次に示すように、ITSM ソリューションで作成されたサービス デスク項目を表示できます。

![Log Analytics の画面を示すスクリーンショット。](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="troubleshoot-itsm-connections"></a>ITSM 接続のトラブルシューティング

- ITSM システムに接続できず、"**接続の保存中にエラーが発生しました**" というメッセージが表示される場合、次の手順を実行してください。
   - ServiceNow、Cherwell、Provance の接続の場合:  
     - 各接続のユーザー名、パスワード、クライアント ID、クライアント シークレットが正しく入力されていることを確認します。  
     - 対応する ITSM 製品で、接続を作成するための十分な特権を持っていることを確認します。  
   - Service Manager 接続の場合:  
     - Web アプリが正常にデプロイされていること、およびハイブリッド接続が作成されていることを確認します。 オンプレミスの Service Manager コンピューターとの接続が正常に確立されていることを確認するには、[ハイブリッド接続](./itsmc-connections-scsm.md#configure-the-hybrid-connection)の作成に関するドキュメントで説明しているように Web アプリの URL に移動します。  

- Log Analytics アラートが発生しても ITSM 製品内に作業項目が作成されない場合、構成項目が作成されなかったり作業項目にリンクされなかったりする場合、その他の情報が必要な場合は、以下のリソースを参照してください。
   -  ITSMC: このソリューションには、[接続の概要](itsmc-dashboard.md)、作業項目、コンピューターなどが表示されます。 **[コネクタの状態]** というラベルのタイルを選択します。 そうすると、適切なクエリを使用して **ログ検索** が行われます。 詳細については、`LogType_S` が `ERROR` になっているログ レコードを参照してください。
   テーブル内のメッセージの詳細については、[こちら](itsmc-dashboard-errors.md)を参照してください。
   - **[ログ検索]** ページ:クエリ `*ServiceDeskLog_CL*` を使用して、エラーと関連情報を直接表示します。

## <a name="common-symptoms---how-it-should-be-resolved"></a>一般的な症状 - 解決方法

下の一覧に、一般的な症状とその解決方法を記載します。

* **現象**:重複する作業項目が作成される

    **原因**: 原因として考えられるのは、次の 2 つのいずれかです。
    * アラートに対して複数の ITSM アクションが定義されている。
    * アラートが解決されている。

    **解決方法**:2 つの解決策があります。
    * アラートごとに 1 つの ITSM アクション グループがあることを確認します。
    * アラートが解決した際、一致する作業項目の状態の更新は ITSM Connector でサポートされていません。 新しい解決済みの作業項目が作成されます。
* **現象**:作業項目が作成されない

    **原因**:この現象には、いくつかの原因が考えられます。
    * ServiceNow 側でのコードの変更
    * アクセス許可の構成の誤り
    * ServiceNow のレート制限が高すぎる/低すぎる
    * 更新トークンの有効期限が切れている
    * ITSM Connector が削除された

    **解決方法**:[ダッシュボード](itsmc-dashboard.md)を確認し、[コネクタの状態] セクションでエラーを確認します。 [[一般的なエラー]](itsmc-dashboard-errors.md) を確認し、エラーの解決方法を確認します。

* **現象**:アクション グループに対して ITSM アクションを作成できない

    **原因**: 新しく作成された ITSM Connector で、初期同期がまだ完了していない。

    **解決方法**: [一般的な UI エラー](itsmc-dashboard-errors.md#ui-common-errors)を確認し、エラーの解決方法を確認します。