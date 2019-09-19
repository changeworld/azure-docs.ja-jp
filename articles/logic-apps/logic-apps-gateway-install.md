---
title: オンプレミス データ ゲートウェイのインストール - Azure Logic Apps
description: Azure Logic Apps からオンプレミスのデータにアクセスするには、オンプレミス データ ゲートウェイをダウンロードしてインストールします。
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: 657bc704e33e89b1646dffa6123a27169e6c317a
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860815"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Azure Logic Apps 用のオンプレミス データ ゲートウェイのインストール

Azure Logic Apps からオンプレミスのデータ ソースに接続するには、ローカル コンピューターにオンプレミス データ ゲートウェイをダウンロードしてインストールします。 ゲートウェイは、(クラウドではなく) オンプレミスのデータ ソースとロジック アプリとの間でデータ転送と暗号化を高速で行うブリッジとして機能します。 同じゲートウェイ インストールを、Power BI、Microsoft Flow、PowerApps、Azure Analysis Services など、他のクラウド サービスで使用できます。 これらのサービスでゲートウェイを使用する方法については、次の記事を参照してください。

* [Microsoft Power BI オンプレミス データ ゲートウェイ](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft PowerApps オンプレミス データ ゲートウェイ](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Microsoft Flow オンプレミス データ ゲートウェイ](https://flow.microsoft.com/documentation/gateway-manage/)
* [Azure Analysis Services オンプレミス データ ゲートウェイ](../analysis-services/analysis-services-gateway.md)

この記事では、Azure Logic Apps からオンプレミスのデータ ソースにアクセスできるように、オンプレミス データ ゲートウェイをダウンロード、インストール、設定する方法について説明します。 [データ ゲートウェイのしくみ](#gateway-cloud-service)についても、このトピックで後ほど詳しく説明します。

<a name="supported-connections"></a>

ゲートウェイでは、以下のデータ ソースに関する Azure Logic Apps の[オンプレミス コネクタ](../connectors/apis-list.md#on-premises-connectors)がサポートされます。

* BizTalk Server 2016
* ファイル システム
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

ゲートウェイだけでは追加のコストは発生しませんが、これらのコネクタや Azure Logic Apps のその他の操作に [Logic Apps の価格モデル](../logic-apps/logic-apps-pricing.md)が適用されます。

<a name="requirements"></a>

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

  * ゲートウェイをインストールして管理するには、同じ Azure アカウントを使用する必要があります。 インストール中に、この Azure アカウントを使用して、コンピューター上のゲートウェイを Azure サブスクリプションに関連付けます。 後で、ゲートウェイ インストールに対して Azure portal でAzure リソースを作成するときにも、同じ Azure アカウントを使用します。 

  * `username@contoso.com` のような、職場アカウントまたは学校アカウント (*組織*アカウントとも呼ばれる) を使用してサインインする必要があります。 Azure B2B (ゲスト) アカウントや個人の Microsoft アカウント (@hotmail.com や @outlook.com など) は使用できません。

    > [!TIP]
    > Office 365 オファリングにサインアップして、仕事用メール アドレスを指定しなかった場合、アドレスは `username@domain.onmicrosoft.com` のようになります。 アカウントは Azure Active Directory (Azure AD) のテナント内に格納されます。 ほとんどの場合、Azure AD アカウントのユーザープリンシパル名 (UPN) は、メール アドレスと同じです。
    >
    > Microsoft アカウントに関連付けられている [Visual Studio Standard サブスクリプション](https://visualstudio.microsoft.com/vs/pricing/)を使用するには、まず [Azure AD でテナントを作成する](../active-directory/develop/quickstart-create-new-tenant.md)か、既定のディレクトリを使用します。 ディレクトリにパスワードを持つユーザーを追加した後、そのユーザーにサブスクリプションへのアクセス権を与えます。 
    > その後、ゲートウェイのインストール中に、このユーザー名とパスワードでサインインできます。

* ローカル コンピューターの要件を以下に示します。

  **最小要件**

  * .NET Framework 4.6
  * Windows 7 または Windows Server 2008 R2 (以降) の 64 ビット バージョン

  **推奨要件**

  * 8 コア CPU
  * 8 GB メモリ
  * Windows Server 2012 R2 以降 の 64 ビット バージョン
  * スプール用のソリッドステート ドライブ (SSD) ストレージ

  > [!NOTE]
  > このゲートウェイは、Windows Server 2016 Core をサポートしていません。

* **関連する考慮事項**

  * オンプレミス データ ゲートウェイはローカル コンピューターにのみインストールできます。ドメイン コントローラーにはインストールできません。 ただし、データ ソースと同じコンピューターにゲートウェイをインストールする必要はありません。 すべてのデータ ソースに関して必要なゲートウェイは 1 つだけです。そのため、データ ソースごとにゲートウェイをインストールする必要はありません。

    > [!TIP]
    > 待機時間を最小限に抑えるために、アクセス許可があることを前提として、データ ソースにできるだけ近いコンピューターまたは同じコンピューターにゲートウェイをインストールできます。

  * ワイヤード (有線) ネットワーク上にある、インターネットに接続され、常に電源が入っており、スリープ状態に移行しないコンピューターにゲートウェイをインストールします。 そうしないと、ゲートウェイを実行できず、ワイヤレス ネットワーク経由ではパフォーマンスが低下する可能性もあります。

  * Windows 認証を使用する予定の場合は、必ず、ご使用のデータ ソースと同じ Active Directory 環境のメンバーであるコンピューターにゲートウェイをインストールしてください。

  * ゲートウェイ インストール用に選択するリージョンは、後でロジック アプリ用の Azure ゲートウェイ リソースを作成するときに選択する必要がある場所と同じです。 既定では、このリージョンは、Azure アカウントを管理する Azure AD テナントと同じ場所です。 ただし、この場所はゲートウェイのインストール中に変更できます。

  * ゲートウェイには、標準モードと個人用モード (Power BI にのみ適用) の 2 つのモードがあります。 同じコンピューターにおいて同じモードで複数のゲートウェイを実行することはできません。

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>データ ゲートウェイをインストールする

1. [ゲートウェイ インストーラーをローカル コンピューターにダウンロードして実行します](https://aka.ms/on-premises-data-gateway-installer)。

1. インストーラーが開いたら、 **[次へ]** を選択します。

   ![インストーラーの開始](./media/logic-apps-gateway-install/gateway-intro-screen.png)

1. 標準モードである **［On-premises data gateway (recommended)\(オンプレミス データ ゲートウェイ (推奨)\)** を選択し、**次へ** を選択します。

   ![ゲートウェイ モードの選択](./media/logic-apps-gateway-install/select-gateway-mode.png)

1. 最小要件を確認し、既定のインストール パスをそのまま使用して、使用条件に同意してから、 **[インストール]** を選択します。

   ![要件の確認と使用条件への同意](./media/logic-apps-gateway-install/accept-terms.png)

1. ゲートウェイが正常にインストールされた後、組織アカウントのメール アドレスを指定し、 **[サインイン]** を選択します。例:

   ![職場または学校アカウントでのサインイン](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   これでアカウントにサインインしました。

1. **[このコンピューターに新しいゲートウェイを登録します]**  >  **[次へ]** の順に選択します。 この手順では、[ゲートウェイ クラウド サービス](#gateway-cloud-service)を使用して、ゲートウェイのインストールを登録します。

   ![ゲートウェイを登録](./media/logic-apps-gateway-install/register-gateway.png)

1. ゲートウェイ インストールについて以下の情報を入力します。

   * Azure AD テナント全体で一意のゲートウェイ名
   * 使用する回復キー。これは 8 文字以上である必要があります
   * 回復キーの確認

   ![ゲートウェイの設定](./media/logic-apps-gateway-install/set-up-gateway.png)

   > [!IMPORTANT]
   > 回復キーは、安全な場所に保存して管理してください。 このキーは、ゲートウェイ インストールの場所変更、移行、復旧、または引き継ぎを行う場合に必要になります。

   **[既存のゲートウェイ クラスターに追加します]** オプションに注意してください。これは、[高可用性のシナリオ](#high-availability)向けに追加のゲートウェイをインストールするときに選択します。

1. ゲートウェイ インストールで使用されるゲートウェイ クラウド サービスと [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) のリージョンを確認します。 既定では、このリージョンは、ご使用の Azure アカウントの Azure AD テナントと同じ場所です。

   ![リージョンのチェック](./media/logic-apps-gateway-install/check-region.png)

1. 既定のリージョンをそのまま使用するには、 **[構成]** を選択します。 ただし、既定のリージョンが最も近いものでなければ、リージョンを変更できます。

   "*ゲートウェイ インストールのリージョンを変更する理由*"

   たとえば、待ち時間を減らすために、ゲートウェイのリージョンをロジック アプリと同じリージョンに変更する場合があります。 または、オンプレミス データ ソースに最も近いリージョンを選択する場合があります。 "*Azure のゲートウェイ リソース*" とロジック アプリの場所は、違っていてもかまいません。

   1. 現在のリージョンの横にある **[リージョンの変更]** を選択します。

      ![リージョンの変更](./media/logic-apps-gateway-install/change-region.png)

   1. 次のページで、 **[リージョンの選択]** リストを開いて目的のリージョンを選択し、 **[完了]** を選択します。

      ![別のリージョンの選択](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. 最後の確認ウィンドウの情報を確認します。 この例では、Logic Apps、Power BI、PowerApps、Microsoft Flow に同じアカウントを使用するため、これらのすべてのサービスでこのゲートウェイを使用できます。 準備ができたら、 **[閉じる]** を選択します。

   ![完了したゲートウェイ](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. 次に、[ゲートウェイ インストール用の Azure リソースを作成します](../logic-apps/logic-apps-gateway-connection.md)。

<a name="high-availability"></a>

## <a name="high-availability-support"></a>高可用性のサポート

オンプレミスのデータ アクセスが単一障害点にならないようにするには、複数のゲートウェイ インストール (標準モードのみ) をそれぞれ別のコンピューターにインストールし、クラスターまたはグループとしてそれらを設定します。 このようにして、プライマリ ゲートウェイが使用できない場合、データ要求は 2 番目のゲートウェイにルーティングされます (それ以降も同様)。 1 台のコンピューターにインストールできる標準ゲートウェイは 1 つだけであるため、クラスター内の追加のゲートウェイをそれぞれ別のコンピューターにインストールする必要があります。 オンプレミス データ ゲートウェイと連携するすべてのコネクタにおいて高可用性がサポートされます。 

* 少なくとも 1 つのゲートウェイ インストールが既に、プライマリ ゲートウェイとそのインストールの回復キーと同じ Azure サブスクリプション内にある必要があります。

* プライマリ ゲートウェイでは、2017 年 11 月以降のゲートウェイ更新プログラムが実行されている必要があります。

プライマリ ゲートウェイを設定したら、別のゲートウェイのインストールに進むときに、 **[既存のゲートウェイ クラスターに追加します]** を選択して、インストールした最初のゲートウェイであるプライマリ ゲートウェイを選択し、そのゲートウェイの回復キーを指定します。 詳細については、[オンプレミス データ ゲートウェイのための高可用性クラスター](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster)に関するページを参照してください。

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>場所の変更、または既存のゲートウェイの移行、復元、引き継ぎ

ゲートウェイの場所の変更が必要な場合、または、新しいコンピューターへのゲートウェイ インストールの移行、破損したゲートウェイの復元、既存のゲートウェイの所有権の取得を行う必要がある場合、ゲートウェイのインストール中に提供された回復キーが必要です。

1. 既存のゲートウェイがあるコンピューターでゲートウェイ インストーラーを実行します。 最新のゲートウェイ インストーラーがない場合は、 [最新のゲートウェイ バージョンをダウンロードします](https://aka.ms/on-premises-data-gateway-installer)。

   > [!NOTE]
   > 元のゲートウェイ インストールがあるコンピューターでゲートウェイを復元する前に、まずそのコンピューターのゲートウェイをアンインストールする必要があります。 このアクションで、元のゲートウェイが切断されます。
   > 任意のクラウド サービスのゲートウェイ クラスターを削除した場合、そのクラスターを復元することはできません。

1. インストーラーが開いたら、ゲートウェイのインストールに使用したものと同じ Azure アカウントでサインインします。

1. **[既存のゲートウェイを移行、復元、または置き換えます。]**  >  **[次へ]** の順に選択します。例:

   ![[既存のゲートウェイを移行、復元、または置き換えます。] の選択](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. 使用可能なクラスターとゲートウェイから選択し、選択したゲートウェイの回復キーを入力します。例:

   ![ゲートウェイの選択](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. リージョンを変更するには、 **[リージョンの変更]** を選択し、新しいリージョンを選択します。

1. 準備ができたら、タスクを完了できるように **[構成]** を選択します。

## <a name="configure-proxy-or-firewall"></a>プロキシまたはファイアウォールの構成

お使いの作業環境で、インターネットにアクセスするためにそのトラフィックがプロキシを経由する必要がある場合は、この制限によって、オンプレミス データ ゲートウェイがゲートウェイ クラウド サービスおよび [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) に接続できない場合があります。 詳細については、「[オンプレミス データ ゲートウェイのプロキシ設定を構成する](https://docs.microsoft.com/power-bi/service-gateway-proxy)」を参照してください。

プロキシまたはファイアウォールが接続をブロックする可能性があるかどうかを確認するには、コンピューターがインターネットと Azure Service Bus に実際に接続できるかどうかを確認します。 PowerShell プロンプトから、次のコマンドを実行します。

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> このコマンドは、ネットワーク接続と、Azure Service Bus への接続のみをテストします。 ゲートウェイ、または資格情報とゲートウェイの詳細を暗号化して格納するゲートウェイ クラウド サービスに対しては、何も行われません。 
>
> また、このコマンドは、Windows Server 2012 R2 以降と Windows 8.1 以降でのみ使用できます。 それより前のバージョンの OS では、Telnet を使用して接続をテストすることができます。 詳細については、[Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) に関するページを参照してください。

結果は次の例のようになります。この例では、**TcpTestSucceeded** が **True** に設定されています。

```text
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

**TcpTestSucceeded** が **True** に設定されていない場合、ゲートウェイがファイアウォールによってブロックされている可能性があります。 包括的にテストしたい場合は、**ComputerName** と **Port** の値を、この記事の「[ポートの構成](#configure-ports)」の一覧に記載されている値に置き換えます。

Azure Service Bus から Azure データ センターへの接続も、ファイアウォールによってブロックされる可能性があります。 その場合は、リージョン内にあるそれらのデータ センターの IP アドレスをすべて承認 (ブロック解除) してください。 これらの IP アドレスについては、[こちら](https://www.microsoft.com/download/details.aspx?id=41653)で Azure の IP アドレスの一覧を取得してください。

<a name="configure-ports"></a>

## <a name="configure-ports"></a>ポートの構成

ゲートウェイは Azure Service Bus への送信接続を作成し、TCP 443 (既定)、5671、5672、9350 ～ 9354 の送信ポート上で通信します。 ゲートウェイでは受信ポートは必要ありません。 詳細については、[Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) に関するページを参照してください。

ゲートウェイでは、以下の完全修飾ドメイン名が使用されます。

| ドメイン名 | 送信ポート | 説明 |
| ------------ | -------------- | ----------- |
| *. analysis.windows.net | 443 | HTTPS |
| *.core.windows.net | 443 | HTTPS |
| *. frontend.clouddatahub.net | 443 | HTTPS |
| *.login.windows.net | 443 | HTTPS |
| *.microsoftonline-p.com | 443 | 構成によっては認証に使用されます。 |
| *. msftncsi.com | 443 | Power BI サービスによってゲートウェイにアクセスできない場合、インターネット接続性のテストに使用されます。 |
| *.servicebus.windows.net | 443、9350 ～ 9354 | TCP 経由での Service Bus Relay のリスナー (Access Control トークンの取得には 443 が必要) |
| *.servicebus.windows.net | 5671 ～ 5672 | Advanced Message Queuing Protocol (AMQP) |
| login.microsoftonline.com | 443 | HTTPS |
||||

Azure Service Bus 接続は、完全修飾ドメイン名ではなく IP アドレスを使用して行われる場合があります。 そのため、ファイアウォールにおいてデータ リージョンの IP アドレスのブロック解除が必要な場合があります。 ドメインではなく IP アドレスへのアクセスを許可するには、[Microsoft Azure データセンター IP 範囲リスト](https://www.microsoft.com/download/details.aspx?id=41653)をダウンロードして使用できます。 このリストの IP アドレスは、[Classless Inter-Domain Routing (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 表記で記述されています。

### <a name="force-https-communication-with-azure-service-bus"></a>Azure Service Bus との HTTPS 通信の強制

一部のプロキシでは、ポート 80 および 443 へのトラフィックのみ、通過が許されます。 既定では、Azure Service Bus との通信は 443 以外のポートで行われます。 ダイレクト TCPではなく HTTPS 経由で Azure Service Bus と通信するようゲートウェイに強制できます。ただし、これを行うと、パフォーマンスが大幅に低下するおそれがあります。 詳細については、「[Azure Service Bus との HTTPS 通信を強制する](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication#force-https-communication-with-azure-service-bus)」を参照してください。

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Windows サービス アカウント

既定では、ローカル コンピューター上のゲートウェイ インストールは、"オンプレミス データ ゲートウェイ サービス" という名前の Windows サービス アカウントとして実行されます。 ただし、ゲートウェイのインストールでは、その "ログオン" アカウントの資格情報に `NT SERVICE\PBIEgwService` という名前が使用され、"サービスとしてログオン" のアクセス許可が付与されています。

> [!NOTE]
> Windows サービス アカウントは、オンプレミスのデータ ソースへの接続に使用するアカウントとも、クラウド サービスへのサインイン時に使用する Azure アカウントとも異なります。

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>ゲートウェイの再起動

データ ゲートウェイは Windows サービスとして実行されるため、他の Windows サービスと同様に、開始と停止をさまざまな方法で行えます。 詳細については、「[オンプレミス データ ゲートウェイを再起動する](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart)」をご覧ください。

## <a name="tenant-level-administration"></a>テナント レベルの管理

Azure AD テナント内のすべてのオンプレミス データ ゲートウェイを可視化するには、そのテナントの全体管理者は、テナント管理者として [Power Platform 管理センター](https://powerplatform.microsoft.com)にサインインし、 **[データ ゲートウェイ]** オプションを選択します。 詳細については、「[オンプレミス データ ゲートウェイのテナント レベルの管理](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)」を参照してください。

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>ゲートウェイのしくみ

データ ゲートウェイによって、ロジック アプリ、ゲートウェイ クラウド サービス、オンプレミスのデータ ソース間に迅速かつセキュリティで保護された通信が促進されます。 ゲートウェイ クラウド サービスでは、データ ソースの資格情報とゲートウェイの詳細を暗号化して格納します。 サービスは、ロジック アプリ、オンプレミス データ ゲートウェイ、オンプレミスのデータ ソース間のクエリとその結果もルーティングします。

ゲートウェイはファイアウォールと共に動作し、ゲートウェイでは送信接続のみが使用されます。 すべてのトラフィックは、ゲートウェイ エージェントからの安全な送信トラフィックとして生成されます。 ゲートウェイは、オンプレミスのソースから、Azure Service Bus 経由の暗号化されたチャネルでデータを中継します。 このサービス バスによって、ゲートウェイと呼び出しサービスとの間のチャネルが作成されます。ただし、データは格納されません。 ゲートウェイを経由するすべてのデータは暗号化されます。

![オンプレミス データ ゲートウェイのアーキテクチャ](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

以下の手順では、オンプレミス データ ソースに接続された要素をクラウドのユーザーが操作した場合に何が起こるかについて説明します。

1. ゲートウェイ クラウド サービスが、データ ソース用の暗号化された資格情報と併せてクエリを作成し、そのクエリを処理するためのゲートウェイのキューに送信します。

1. ゲートウェイ クラウド サービスはクエリを分析し、Azure Service Bus に要求をプッシュします。

1. オンプレミス データ ゲートウェイは、保留中の要求がないか Azure Service Bus をポーリングします。

1. ゲートウェイはクエリを取得して資格情報を復号化し、その資格情報でデータ ソースに接続します。

1. ゲートウェイは、実行するためにクエリをデータ ソースに送信します。

1. 結果がデータ ソースからゲートウェイに返送され、その後ゲートウェイ クラウド サービスに送信されます。 ゲートウェイ クラウド サービスが結果を使用します。

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="general"></a>全般

**Q**: クラウド内のデータ ソース (Azure SQL Database など) にゲートウェイは必要ですか? <br/>
**A**: いいえ。ゲートウェイは、オンプレミスのデータ ソースにのみ接続します。

**Q**: ゲートウェイをデータ ソースと同じコンピューターにインストールする必要がありますか? <br/>
**A**: いいえ。ゲートウェイは、指定された接続情報を使用してデータ ソースに接続します。 その意味ではゲートウェイをクライアント アプリケーションと見なすことができます。 ゲートウェイに必要なのは、指定されたサーバー名に接続する機能だけです。

<a name="why-azure-work-school-account"></a>

**Q**: 職場または学校アカウントを使用してサインインする必要があるのはなぜですか? <br/>
**A**: オンプレミス データ ゲートウェイをインストールする場合は、職場または学校アカウントしか使用できません。 サインイン アカウントは、Azure Active Directory (Azure AD) によって管理されるテナントに格納されます。 通常、Azure AD アカウントのユーザー プリンシパル名 (UPN) は電子メール アドレスと一致します。

**Q**: 自分の資格情報はどこに格納されますか? <br/>
**A**: データ ソースのために入力した資格情報は暗号化され、ゲートウェイ クラウド サービスに格納されます。 資格情報の復号化はオンプレミス データ ゲートウェイで行われます。

**Q**: ネットワーク帯域幅に何か要件はありますか? <br/>
**A**: ネットワーク接続のスループットが良好なことを確認してください。 すべての環境は異なっており、送信されるデータの量が結果に影響を及ぼす可能性があります。 オンプレミス データ ソースと Azure データセンターの間のスループット レベルを確保するには、[Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) をお試しください。 スループットを測定するには、Azure Speed Test などの外部ツールをお試しください。

**Q**: ゲートウェイからデータ ソースにクエリを実行するための待ち時間とは何ですか? 最適なアーキテクチャとは何ですか? <br/>
**A**: ネットワーク待ち時間を削減するには、ゲートウェイをデータ ソースのできるだけ近くにインストールします。 実際のデータ ソース上にゲートウェイをインストールできれば、その近接性によって待ち時間の発生を最小限に抑えることができます。 さらに、Azure データセンターとの近接性を考慮します。 たとえば、お客様のサービスで米国西部のデータセンターを使用しており、SQL Server が Azure VM でホストされている場合は、その Azure VM も米国西部リージョンに配置されているのがよいでしょう。 こうすることで待ち時間は最短となり、Azure VM でデータ送信料金が発生することもありません。

**Q**: 結果はクラウドにどのように送り返されますか? <br/>
**A**: 結果は Azure Service Bus 経由で送信されます。

**Q**: クラウドからゲートウェイへの受信接続はありますか? <br/>
**A**: いいえ。ゲートウェイは、Azure Service Bus への送信接続を使用します。

**Q**: 送信接続をブロックするとどうなりますか? 開くために何をする必要がありますか? <br/>
**A**: ゲートウェイが使用するポートとホストを確認してください。

**Q**: 実際の Windows サービスは何と呼ばれていますか? <br/>
**A**: タスク マネージャーの [サービス] タブでは、サービス名は "PBIEgwService" (つまり Power BI Enterprise Gateway Service) です。 サービス コンソールでは、サービス名は "オンプレミス データ ゲートウェイ サービス" です。 Windows サービスでは、サービス SID (SSID) として "NT SERVICE\PBIEgwService" が使用されます。

**Q**: ゲートウェイ Windows サービスは、Azure Active Directory アカウントを使用して実行できますか? <br/>
**A**: いいえ。Windows サービスには有効な Windows アカウントが必要です。

### <a name="disaster-recovery"></a>障害復旧

**Q**: ディザスター リカバリーにはどのようなオプションを使用できますか? <br/>
**A**: 回復キーを使用してゲートウェイを復元または移動できます。 ゲートウェイをインストールするときに、回復キーを指定します。

**Q**: 回復キーの利点は何ですか? <br/>
**A**: 回復キーは、障害発生後にゲートウェイの設定を移行または復旧する方法を提供します。

## <a name="troubleshooting"></a>トラブルシューティング

このセクションでは、オンプレミス データ ゲートウェイの設定時および使用時に発生する可能性がある一般的な問題をいくつか取り上げます。

**Q**: ゲートウェイのインストールが失敗するのはなぜですか? <br/>
**A**: この問題は、ターゲット コンピューター上のウイルス対策ソフトウェアが古くなった場合に発生することがあります。 ウイルス対策ソフトウェアを更新できます。または、ゲートウェイのインストール中にのみウイルス対策ソフトウェアを無効にして、ソフトウェアを再び有効にできます。

**Q**: Azure でゲートウェイ リソースを作成するときにゲートウェイ インストールが表示されないはなぜですか? <br/>
**A**: この問題は、次の理由で発生することがあります。

* ゲートウェイ インストールが登録済みで、Azure の別のゲートウェイ リソースによって既に要求されている。 ゲートウェイ インストールは、そのゲートウェイ リソースが作成された後、インスタンスの一覧に表示されません。 Azure portal でゲートウェイの登録を確認するには、"*すべて*" の Azure サブスクリプションについて、種類が**オンプレミス データ ゲートウェイ**であるすべての Azure リソースを確認します。

* ゲートウェイをインストールしたユーザーの Azure AD ID が、Azure portal にサインインしたユーザーのものと異なる。 ゲートウェイをインストールしたのと同じ ID でサインインしていることを確認してください。

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**Q**: ゲートウェイのログはどこにありますか? <br/>
**A**: この記事の後の方にある「[**ログ**」セクション](#logs)を参照してください。

**Q**: オンプレミスのデータ ソースに送信されるクエリを確認するにはどうすればよいですか? <br/>
**A**: 送信されるクエリを含むクエリ トレースを有効にすることができます。 クエリ トレースは、トラブルシューティングが完了したら忘れずに元の値に戻してください。 クエリ トレースをオンのままにしておくと、ログのサイズが増大します。

クエリをトレースするためにデータ ソースに用意されているツールを使用することもできます。 たとえば、SQL Server 用の拡張イベントまたは SQL Profiler や Analysis Services を使用できます。

### <a name="outdated-gateway-version"></a>期限切れのゲートウェイ バージョン

ゲートウェイのバージョンが古くなるにつれ、数多くの問題が表面化する可能性があります。 一般的には、常に最新バージョンを使用することが最適な対処法となります。 1 か月以上ゲートウェイを更新していない場合は、最新バージョンのゲートウェイをインストールし、問題が再発するかどうかを確認してください。

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>エラー:ユーザーをグループに追加できませんでした。 (-2147463168 PBIEgwService Performance Log Users)

ドメイン コントローラー (サポート対象外) にゲートウェイをインストールしようとすると、このエラーが表示されることがあります。 ゲートウェイは、必ずドメイン コントローラー以外のマシンにデプロイしてください。

<a name="logs"></a>

### <a name="logs"></a>ログ

トラブルシューティングを行いやすいよう、いつでも最初にゲートウェイ ログを収集して確認します。 ログを収集する方法はいくつかありますが、ゲートウェイのインストール後で最も簡単なのは、ゲートウェイ インストーラーのユーザー インターフェイスを使用する方法です。

1. コンピューターで、オンプレミス データ ゲートウェイ インストーラーを開きます。

1. 左側のメニューで、 **[診断]** を選択します。

1. **[ゲートウェイ ログ]** で **[ログのエクスポート]** を選択します。

   ![ゲートウェイ インストーラーでのログのエクスポート](./media/logic-apps-gateway-install/export-logs.png)

各種のログがある他の場所は以下のとおりです。

| ログのタイプ | Location |
|----------|----------|
| **インストーラー ログ** | %localappdata%\Temp\On-premises_data_gateway_<*yyyymmdd*>.<*数値*>.log |
| **構成ログ** | C:\Users\<*ユーザー名*>\AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator<*yyyymmdd*>.<*数値*>.log |
| **エンタープライズ ゲートウェイ サービスのログ** | C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\Gateway<*yyyymmdd*>.<*数値*>.log |
|||

**イベント ログ**

ゲートウェイのイベント ログを見つけるには、以下の手順に従います。

1. ゲートウェイがインストールされたコンピューターで、**イベント ビューアー**を開きます。

1. **[イベント ビューアー (ローカル)]**  >  **[アプリケーションとサービス ログ]** の順に展開します。

1. **[On-premises data gateway service]\(オンプレミス データ ゲートウェイ サービス\)** を選択します。

   ![ゲートウェイのイベント ログの表示](./media/logic-apps-gateway-install/event-viewer.png)

### <a name="review-slow-query-performance"></a>低速なクエリ パフォーマンスの確認

クエリがゲートウェイを通過する速度が遅いことがわかった場合、クエリとその実行時間が出力される追加のログを有効にできます。 これらのログは、低速なクエリまたは実行時間の長いクエリがどれであるかを特定するのに役立ちます。 クエリ パフォーマンスを調整するには、たとえば SQL Server クエリのインデックスの調整など、データ ソースの変更が必要な場合があります。

クエリの実行時間を確認するには、以下の手順に従います。

1. ゲートウェイ クライアントと同じ場所を参照します。これは通常、```C:\Program Files\On-premises data gateway``` にあります。

   そうでない場合にクライアントの場所を探すには、同じコンピューターのサービス コンソールを開き、**オンプレミス データ ゲートウェイ サービス**を探して、**実行可能ファイルへのパス** プロパティを表示します。

1. 下記のとおり、以下の構成ファイルを開いて編集します。

   * **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

     このファイルで、**EmitQueryTraces** の値を **false** から **true** に変更します。これで、ゲートウェイからデータ ソースに送信されたクエリが、ゲートウェイによって記録されるようになります。

     ```html
     <setting name="EmitQueryTraces" serializeAs="String">
        <value>true</value>
     </setting>
     ```

     > [!IMPORTANT]
     > EmitQueryTraces 設定を有効にすると、ゲートウェイの使用状況によってはログのサイズが非常に大きくなる可能性があります。 ログの確認が完了したら、必ず EmitQueryTraces を **false** に設定し直して、この設定が長時間有効なままでないようにします。

   * **Microsoft.PowerBI.DataMovement.Pipeline.Diagnostics.dll.config**

     実行時間を示すエントリなど、ゲートウェイ ログの詳細エントリを取得するには、いずれかの手順を実行して、**TracingVerbosity** の値を **4** から **5** に変更します。

     * この構成ファイルで、**TracingVerbosity** の値を **4** から **5** に変更します。

       ```html
       <setting name="TracingVerbosity" serializeAs="String">
          <value>5</value>
       </setting>
       ```

     * ゲートウェイ インストーラーを開いて **[診断]** を選択し、 **[追加ログ]** を有効にしてから、 **[適用]** を選択します。

       ![追加のログの有効化](./media/logic-apps-gateway-install/turn-on-additional-logging.png)

     > [!IMPORTANT]
     > TracingVerbosity 設定を有効にすると、ゲートウェイの使用状況によってはログのサイズが非常に大きくなる可能性があります。 ログの確認が完了したら、必ずゲートウェイ インストーラーで **[追加ログ]** を無効にするか、構成ファイルで TracingVerbosity を **4** に設定し直すかして、この設定が長時間有効なままでないようにします。

1. クエリの実行時間を確認するには、以下の手順に従います。

   1. ゲートウェイ ログを[エクスポート](#logs)して開きます。

   1. クエリを見つけるには、アクティビティの種類を検索します。例:

      | アクティビティの種類 | 説明 |
      |---------------|-------------|
      | MGEQ | ADO.NET で実行されるクエリ |
      | MGEO | OLEDB で実行されるクエリ |
      | MGEM | マッシュアップ エンジンから実行されるクエリ |
      |||

   1. 2 つ目の GUID をメモします。これは要求 ID です。

   1. 実行時間をミリ秒で示す "FireActivityCompletedSuccessfullyEvent" という名前のエントリが見つかるまで、アクティビティの種類を探し続けます。 エントリに同じ要求 ID が含まれていることを確認します。例:

      ```text
      DM.EnterpriseGateway Verbose: 0 : 2016-09-26T23:08:56.7940067Z DM.EnterpriseGateway    baf40f21-2eb4-4af1-9c59-0950ef11ec4a    5f99f566-106d-c8ac-c864-c0808c41a606    MGEQ    21f96cc4-7496-bfdd-748c-b4915cb4b70c    B8DFCF12 [DM.Pipeline.Common.TracingTelemetryService] Event: FireActivityCompletedSuccessfullyEvent (duration=5004)
      ```

      > [!NOTE]
      > "FireActivityCompletedSuccessfullyEvent" エントリは詳細エントリであり、"TracingVerbosity" がレベル 5 に設定されない限り記録されません。

### <a name="trace-traffic-with-fiddler"></a>Fiddler によるトラフィックのトレース

[Fiddler](https://www.telerik.com/fiddler) は、HTTP トラフィックを監視する Telerik の無料ツールです。 Power BI サービスに関するこのトラフィックをクライアント マシンで確認できます。 このサービスを利用することで、エラーやその他の関連情報が明らかになる場合があります。

## <a name="next-steps"></a>次の手順

* [ロジック アプリからオンプレミスのデータに接続する](../logic-apps/logic-apps-gateway-connection.md)
* [エンタープライズ統合機能](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Azure Logic Apps のコネクタ](../connectors/apis-list.md)
