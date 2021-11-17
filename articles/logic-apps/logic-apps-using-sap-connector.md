---
title: SAP システムへの接続
description: Azure Logic Apps を使用してワークフローを自動化することにより、SAP のリソースへアクセスし管理します。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, azla
ms.topic: how-to
ms.date: 11/01/2021
tags: connectors
ms.openlocfilehash: a400c8e5ac118250afedd27f2f8e79b678546727
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438647"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Azure Logic Apps から SAP システムに接続する

この記事では、[SAP コネクタ](/connectors/sap/)を使用して Azure Logic Apps から SAP リソースにアクセスする方法について説明します。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

* SAP リソースにアクセスするためのロジック アプリ ワークフロー。 Azure Logic Apps を初めて使用する場合は、「[Azure Logic Apps の概要](logic-apps-overview.md)」と、「[Azure portal で初めてのロジック アプリ ワークフローを作成する方法のクイックスタート](quickstart-create-first-logic-app-workflow.md)」を参照してください。

  * 非推奨となっている以前のバージョンの SAP コネクタを使用したことがある場合は、SAP サーバーに接続する前に、[現在のコネクタに移行](#migrate-to-current-connector)する必要があります。

  * マルチテナント Azure でロジック アプリ ワークフローを実行している場合は、「[マルチテナントの前提条件](#multi-tenant-azure-prerequisites)」を参照してください。

  * Premium レベルの[統合サービス環境 (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) でロジック アプリ ワークフローを実行している場合は、「[ISE の前提条件](#ise-prerequisites)」を参照してください。

* Azure Logic Apps からアクセスする [SAP アプリケーション サーバー](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server)または [SAP メッセージ サーバー](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)。 このコネクタをサポートする SAP サーバーの詳細については、「[SAP の互換性](#sap-compatibility)」を参照してください。

  > [!IMPORTANT]
  > RFC の使用を許可するには、必ず SAP サーバーとユーザー アカウントを設定してください。 サポートされているユーザー アカウントの種類と、各アクションの種類 (RFC、BAPI、IDOC) に必要な最小限の承認が含まれる詳細については、「SAP Note: [460089 - 外部 RFC プログラムの最小承認プロファイル](https://launchpad.support.sap.com/#/notes/460089)」を参照してください。 
  >
  > * RFC アクションの場合、ユーザー アカウントにはさらに、関数モジュール `RFC_GROUP_SEARCH` と `DD_LANGU_TO_ISOLA` へのアクセスが必要です。
  > * BAPI アクションの場合、ユーザー アカウントには、関数モジュール `BAPI_TRANSACTION_COMMIT`、`BAPI_TRANSACTION_ROLLBACK`、`RPY_BOR_TREE_INIT`、`SWO_QUERY_METHODS` と `SWO_QUERY_API_METHODS` へのアクセスも必要です。
  > * IDOC アクションの場合、ユーザー アカウントには、関数モジュール `IDOCTYPES_LIST_WITH_MESSAGES`、`IDOCTYPES_FOR_MESTYPE_READ`、`INBOUND_IDOCS_FOR_TID`、`OUTBOUND_IDOCS_FOR_TID`、`GET_STATUS_FROM_IDOCNR` と `IDOC_RECORD_READ` へのアクセスも必要です。
  > * **Read Table** アクションの場合、ユーザー アカウントには関数モジュール `RFC BBP_RFC_READ_TABLE` または `RFC_READ_TABLE` の *いずれか* にアクセスする必要があります。

* SAP サーバーに送信するメッセージの内容 (サンプル IDoc ファイルなど)。 この内容は XML 形式とし、使用する [SAP アクション](#actions)の名前空間を含める必要があります。 [XML エンベロープにラップすることによって、フラット ファイル スキーマを持つ IDoc を送信する](#send-flat-file-idocs)ことができます。

* **SAP からメッセージを受信した場合** のトリガーを使用する場合は、以下のタスクも行う必要があります。

  * SAP ゲートウェイのセキュリティ アクセス許可またはアクセス制御リスト (ACL) を設定します。 [Gateway Monitor (ゲートウェイ・モニター)] ダイアログ ボックスの [T コード SMGW] に表示される **secinfo** と **reginfo** ファイルで、 **[Goto] > [Expert Functions (専門機能)] > [External Security (外部セキュリティ)] > [Maintenance of ACL Files (ACL ファイルのメンテナンス)]** を実行します。 次のアクセス許可設定が必要です。

    `P TP=LOGICAPP HOST=<on-premises-gateway-server-IP-address> ACCESS=*`

    この行の形式は次のとおりです。

    `P TP=<trading-partner-identifier-(program-name)-or-*-for-all-partners> HOST=<comma-separated-list-with-external-host-IP-or-network-names-that-can-register-the-program> ACCESS=<*-for-all-permissions-or-a-comma-separated-list-of-permissions>`

    SAP ゲートウェイのセキュリティ アクセス許可を構成しない場合は、次のエラーが発生する可能性があります。

    `Registration of tp Microsoft.PowerBI.EnterpriseGateway from host <host-name> not allowed`

    詳細については、[SAP Note 1850230 - GW: 「tp &lt;プログラム ID&gt; の登録が許可されていない」](https://userapps.support.sap.com/sap/support/knowledge/en/1850230)を参照してください。

  * アクセス制御リスト (ACL) の問題を検索するために、SAP ゲートウェイのセキュリティ ログを設定します。 詳細については、「[ゲートウェイのログ記録の設定に関する SAP ヘルプ トピック](https://help.sap.com/erp_hcm_ias2_2015_02/helpdata/en/48/b2a710ca1c3079e10000000a42189b/frameset.htm)」を参照してください。

  * **[Configuration of RFC Connections (RFC 接続の構成)]** (T コード SM59) ダイアログ ボックスで、**TCP/IP** タイプの RFC 接続を作成します。 **[Activation Type (アクティブ化のタイプ)]** は **[Registered Server Program (登録済みサーバーのプログラム)]** を選択する必要があります。 RFC 接続の **[Communication Type with Target System (対象システムとの通信タイプ)]** の値を **Unicode** に設定します。

  * このSAP トリガーの **IDOC Format** パラメーターを **FlatFile** に設定して [Flat File Decode action](logic-apps-enterprise-integration-flatfile.md) と一緒に使用する場合、フラット ファイル スキーマの `early_terminate_optional_fields` プロパティを `true` に設定する必要があります。

    この要件が必要なのは、tRFC 呼び出し `IDOC_INBOUND_ASYNCHRONOUS` では、SAP によって送信されるフラット ファイル IDoc データ レコードが、SDATA フィールドの長さ全体にパディングされないためです。 Azure Logic Apps では、SAP から受信したままの、パディングされていないフラット ファイル IDoc の元のデータが提供されます。 また、この SAP トリガーをフラット ファイル デコード アクションと組み合わせる場合、アクションで提供されるスキーマと一致する必要があります。

  > [!NOTE]
  > この SAP トリガーでは、Webhook サブスクリプションの更新と登録解除の両方に同じ URI の場所を使用します。 更新操作では HTTP `PATCH` メソッドを使用し、登録解除操作では HTTP `DELETE` メソッドを使用します。 この動作により、トリガーの履歴に更新操作が登録解除操作として表示される場合がありますが、トリガーでは HTTP メソッドとして `DELETE` ではなく `PATCH` を使用しているため、その操作は更新になります。

### <a name="sap-compatibility"></a>SAP 互換性

SAP コネクタは、次の種類の SAP システムと互換性があります。

* オンプレミスおよびクラウドベースの HANA ベースの SAP システム (S/4 HANA など)。

* 従来のオンプレミスの SAP システム (R/3 や ECC など)。

SAP コネクタでは、SAP NetWeaver ベースのシステムの、次のメッセージとデータ統合の種類がサポートされています。

* Intermediate Document (IDoc)

* ビジネス アプリケーション プログラミング インターフェイス (BAPI)

* Remote Function Call (RFC) と Transactional RFC (tRFC)

SAP コネクタでは、[SAP .NET Connector (NCo) ライブラリ](https://support.sap.com/en/product/connectors/msnet.html)が使用されています。

使用可能な [SAP トリガー](#triggers)と [SAP アクション](#actions)を使用するには、最初に自分の接続を認証する必要があります。 ユーザー名とパスワードを使用して接続を認証できます。 SAP コネクタでは、[SAP Secure Network Communications (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true) 認証もサポートされています。 SNC は、SAP NetWeaver シングル サインオン (SSO)、または外部製品の追加のセキュリティ機能に使用できます。 SNC を使用する場合は、「[SNC の前提条件](#snc-prerequisites)「と「[ISE コネクタ用の SNC の前提条件](#snc-prerequisites-ise)」を確認してください。

### <a name="migrate-to-current-connector"></a>最新のコネクタに移行する

以前の SAP アプリケーション サーバーおよび SAP メッセージ サーバー コネクタは、2020 年 2 月 29 日に非推奨となりました。 現在の SAP コネクタに移行するには、次の手順を実行します。

1. [オンプレミス データ ゲートウェイ](https://www.microsoft.com/download/details.aspx?id=53127)を現在のバージョンに更新します。 詳細については、「[Azure Logic Apps 用のオンプレミス データ ゲートウェイのインストール](logic-apps-gateway-install.md)」を参照してください。

1. 非推奨の SAP コネクタが使用されているロジック アプリ ワークフローで、**SAP に送信する** アクションを削除します。

1. 現在の SAP コネクタから **[Send message to SAP]\(SAP にメッセージを送信する\)** アクションを追加します。

1. 新しいアクションの SAP システムに再接続します。

1. ロジック アプリ ワークフローを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

## <a name="multi-tenant-azure-prerequisites"></a>マルチテナント Azure の前提条件

これらの前提条件は、お使いのロジック アプリ ワークフローがマルチテナント Azure で実行されている場合に適用されます。 マネージド SAP コネクタは、[ISE](connect-virtual-network-vnet-isolated-environment-overview.md) でネイティブには実行されません。

> [!TIP]
> Premium レベルの ISE を使用している場合は、マネージド SAP コネクタではなく、SAP ISE コネクタを使用できます。 詳細については、「[ISE の前提条件](#ise-prerequisites)」を参照してください。

マネージド SAP コネクタは、[オンプレミス データ ゲートウェイ](logic-apps-gateway-connection.md)を介して SAP システムと統合します。 たとえば、メッセージの送信シナリオでは、ロジック アプリ ワークフローから SAP システムにメッセージが送信されると、データ ゲートウェイが RFC クライアントとして機能し、ロジック アプリ ワークフローから受信した要求を SAP に転送します。 同様に、メッセージの受信シナリオでは、SAP から要求を受信し、これらをロジック アプリ ワークフローに転送する RFC サーバーとしてデータ ゲートウェイが機能します。

1. 接続先の SAP システムと同じ仮想ネットワーク内に存在するホスト コンピューターまたは仮想マシンに、[オンプレミス データ ゲートウェイをダウンロードしてインストールします](logic-apps-gateway-install.md)。

1. Azure portal で、オンプレミス データ ゲートウェイ用の [Azure ゲートウェイ リソースを作成](logic-apps-gateway-connection.md#create-azure-gateway-resource)します。 このゲートウェイを使用することで、オンプレミスのデータやリソースに安全にアクセスすることができます。 使用しているゲートウェイが、サポートされているバージョンであることを確認してください。

  > [!TIP]
  > ゲートウェイに問題が発生した場合は、[最新バージョンへのアップグレード](https://aka.ms/on-premises-data-gateway-installer)を試してみてください。これには、問題を解決する更新プログラムが含まれている可能性があります。

1. オンプレミス データ ゲートウェイと同じローカル コンピューターに、[最新の SAP クライアント ライブラリをダウンロードしてインストールします](#sap-client-library-prerequisites)。

1. オンプレミス データ ゲートウェイをインストールしたホスト マシンのネットワーク ホスト名とサービス名解決を構成します。

  Azure Logic Apps からの接続にホスト名またはサービス名を使用する場合は、名前解決のために各 SAP アプリケーション、メッセージ、ゲートウェイ サーバーとそのサービスを設定する必要があります。 ネットワーク ホストの名前解決は、`%windir%\System32\drivers\etc\hosts` ファイルまたはオンプレミス のデータ ゲートウェイ ホスト コンピューターで使用できる DNS サーバーで構成されます。 サービス名解決は `%windir%\System32\drivers\etc\services` で構成されます。 接続にネットワーク ホスト名またはサービス名を使用しない場合は、代わりにホスト IP アドレスとサービス ポート番号を使用できます。

   SAP システムの DNS エントリをお持ちではない場合、次の例は hosts ファイルのサンプル エントリを示しています。

   ```text
   10.0.1.9           sapserver                   # SAP single-instance system host IP by simple computer name
   10.0.1.9           sapserver.contoso.com       # SAP single-instance system host IP by fully qualified DNS name
   ```

   サービス ファイルのエントリのサンプル セットを次に示します。

   ```text
   sapdp00            3200/tcp              # SAP system instance 00 dialog (application) service port
   sapgw00            3300/tcp              # SAP system instance 00 gateway service port
   sapmsDV6           3601/tcp              # SAP system ID DV6 message service port
   ```

## <a name="ise-prerequisites"></a>ISE の前提条件

ISE では、Azure 仮想ネットワークによって保護されているリソースへのアクセスが提供されます。また、オンプレミス データ ゲートウェイを使用せずにロジック アプリ ワークフローからオンプレミスのリソースに直接アクセスできるようにするその他の ISE ネイティブのコネクタが提供されます。

1. BLOB コンテナーがある Azure Storage アカウントをまだお持ちでない場合は、[Azure portal](../storage/blobs/storage-quickstart-blobs-portal.md) または [Azure Storage Explorer](../storage/blobs/quickstart-storage-explorer.md) のいずれかを使用して、コンテナーを作成します。

1. ローカル コンピューターに、[最新の SAP クライアント ライブラリをダウンロードしてインストールします](#sap-client-library-prerequisites)。 次のアセンブリ ファイルがあるはずです。

   * libicudecnumber.dll

   * rscp4n.dll

   * sapnco.dll

   * sapnco_utils.dll

1. これらのアセンブリ ファイルを含む .zip ファイルを作成します。 Azure Storage で、このパッケージを BLOB コンテナーにアップロードします。

1. Azure portal または Azure Storage Explorer のいずれかで、.zip ファイルをアップロードしたコンテナーの場所を参照します。

1. コンテナーの場所の URL をコピーします。 SAS トークンが承認されるように、Shared Access Signature (SAS) トークンが含まれていることを確認します。 そうしない場合、SAP ISE コネクタのデプロイは失敗します。

1. ISE に SAP コネクタをインストールしてデプロイします。 詳細については、「[ISE のコネクタの追加](add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment)」を参照してください。

   1. [Azure portal](https://portal.azure.com) で、ご利用の ISE を探して開きます。

   1. ISE のメニューで、 **[マネージド コネクタ]** &gt; **[追加]** の順に選択します。 コネクタの一覧で、 **[SAP]** を探して選択します。

   1. **[新しいマネージド コネクタの追加]** ペインの **[SAP パッケージ]** ボックスに、SAP アセンブリを含む .zip ファイルの URL を貼り付けます。 ここでも、SAS トークンが含まれていることを確認してください。

   1. **[作成する]** を選択して、ISE コネクタの作成を完了します。

1. SAP インスタンスと ISE が異なる仮想ネットワークにある場合は、[それらのネットワークをピアリングして](../virtual-network/tutorial-connect-virtual-networks-portal.md)接続されるようにする必要もあります。 また、「[ISE コネクタ用の SNC の前提条件](#snc-prerequisites-ise)」も確認してください。

1. ロジック アプリ ワークフローからの接続に使用する予定の SAP アプリケーション、メッセージ、ゲートウェイ サーバーの IP アドレスを取得します。 ISE の SAP 接続では、ネットワーク名解決は使用できません。

1. ロジック アプリとの接続に使用する予定の SAP アプリケーション、メッセージ、ゲートウェイ サービスのポート番号を取得します。 ISE の SAP コネクタでは、サービス名解決は使用できません。

### <a name="sap-client-library-prerequisites"></a>SAP クライアント ライブラリの前提条件

コネクタで使用している SAP クライアント ライブラリの前提条件を次に一覧します。

* [.NET Framework 4.0 - Windows 64 ビット (x64) でコンパイルされた Microsoft .NET 3.0.24.0 向けの SAP コネクタ (NCo 3.0)](https://support.sap.com/en/product/connectors/msnet.html) の最新バージョンがインストールされていることを確認します。 以前のバージョンの SAP NCo では、次の問題が発生する可能性があります。

  * 複数の IDoc メッセージが同時に送信されると、SAP 転送先に送信されるすべてのメッセージがブロックされ、メッセージがタイムアウトになります。

  * セッションのリークにより、セッションのアクティブ化に失敗する可能性があります。 この状態では、ロジック アプリ ワークフロー トリガーに対して SAP によって送信された呼び出しがブロックされることがあります。

  * オンプレミス データ ゲートウェイ (2021 年6月リリース) は、SAP NCo の `SAP.Middleware.Connector.RfcConfigParameters.Dispose()` メソッドに依存してリソースを解放します。

* データ ゲートウェイは 64 ビット システムでのみ実行されるため、SAP クライアント ライブラリの 64 ビット版がインストールされている必要があります。 サポートされていない 32 ビット版をインストールすると、"イメージが無効" エラーが発生します。

* 次のシナリオに基づいて、既定のインストール フォルダーから別の場所にアセンブリ ファイルをコピーします。

  * ISE で実行されているロジック アプリ ワークフローの場合は、代わりに「[ISE の前提条件](#ise-prerequisites)」に従ってください。

  * マルチテナント Azure で実行され、オンプレミス データ ゲートウェイを使用しているロジック アプリ ワークフローの場合は、アセンブリ ファイルをデータ ゲートウェイのインストール フォルダーにコピーします。 

    > [!NOTE]
    > SAP 接続が失敗し、"**Please check your account info and/or permissions and try again" (お使いのアカウント情報やアクセス許可を確認し、もう一度試してください)** というエラー メッセージが表示された場合は、アセンブリ ファイルがデータ ゲートウェイのインストール フォルダーにコピーされているかどうかを確認します。
    > 
    > [.NET アセンブリ バインド ログ ビューアー](/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer)を使用して、さらに問題のトラブルシューティングを行うことができます。 
    > このツールを使用すると、アセンブリ ファイルが正しい場所にあるかどうかを確認できます。 
  
  * 必要に応じて、SAP クライアント ライブラリをインストールするときに、 **[Global Assembly Cache registration (グローバル アセンブリ キャッシュの登録)]** オプションを選択します。

SAP クライアント ライブラリ、.NET Framework、.NET ランタイム、ゲートウェイの次の関係を確認します。

* Microsoft SAP アダプターとデータ ゲートウェイ ホスト サービスでは、どちらも .NET Framework 4.7.2 が使用されます。

* SAP NCo for .NET Framework 4.0 は、.NET ランタイム 4.0 から 4.8 を使用するプロセスとの組み合わせで正しく動作します。

* SAP NCo for .NET Framework 2.0 は、.NET ランタイム 2.0 から 3.5 を使用するプロセスで正しく動作しますが、最新のゲートウェイでは動作しなくなりました。

### <a name="snc-prerequisites"></a>SNC の前提条件

マルチテナント Azure でのみサポートされている、オプションの SNC でオンプレミス データ ゲートウェイを使用する場合は、次の追加設定を構成する必要があります。 また、ISE を使用している場合は、「[ISE コネクタ用の SNC の前提条件](#snc-prerequisites-ise)」も確認してください

SNC を SSO で使用している場合は、データ ゲートウェイ サービスが、SAP ユーザーに対してマップされたユーザーとして実行されていることを確認してください。 既定のアカウントを変更するには、 **[アカウントを変更]** を選択し、ユーザーの資格情報を入力します。

![オンプレミス データゲートウェイを設定した Microsoft Azure portal と、ゲートウェイのサービス アカウントを変更するボタンが選択された [サービス設定] ページのスクリーンショット。](./media/logic-apps-using-sap-connector/gateway-account.png)

外部のセキュリティ製品を通じて SNC を有効にする場合は、データ ゲートウェイがインストールされているのと同じコンピューター上に SNC ライブラリまたはファイルをコピーします。 SNC 製品の例をいくつか挙げると、[sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)、Kerberos、NTLM などがあります。 データ ゲートウェイに対して SNC を有効にする方法の詳細については、「[Secure Network Communications (SNC) を有効にする](#enable-secure-network-communications)」を参照してください。

> [!TIP]
> SNC ライブラリのバージョンとその依存関係は、SAP 環境と互換性がある必要があります。
>
> * SAPGENPSE ユーティリティとして `sapgenpse.exe` を特別に使用する必要があります。
> * オンプレミス データ ゲートウェイを使用する場合は、これらの同じバイナリ ファイルをインストール フォルダーにもコピーします。
> * 接続に PSE が提供されている場合は、オンプレミス データ ゲートウェイの PSE と SECUDIR をコピーして設定する必要はありません。
> * また、オンプレミス データ ゲートウェイを使用して、ライブラリの互換性に関する問題のトラブルシューティングを行うこともできます。

#### <a name="snc-prerequisites-ise"></a>SNC の前提条件 (ISE)

ISE バージョンの SAP コネクタは、SNC X.509 をサポートしています。 次の手順に従って、SAP ISE 接続の SNC を有効にできます。

> [!IMPORTANT]
> SNC を使用するように既存の SAP コネクタを再デプロイする前に、古いコネクタへのすべての接続を削除する必要があります。 複数のロジック アプリ ワークフローで SAP への同じ接続を使用できます。 そのため、ISE のすべてのロジック アプリ ワークフローからすべての SAP 接続を削除する必要があります。 次に、古いコネクタを削除する必要があります。
>
> 古いコネクタを削除しても、このコネクタを使用するロジック アプリ ワークフローは保持できます。 コネクタを再デプロイした後、これらのロジック アプリ ワークフローの SAP トリガーとアクションで新しい接続を認証できます。

まず、SNC または SAPGENPSE ライブラリを使用せずに SAP コネクタをデプロイしている場合は、すべての接続とコネクタを削除します。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. ロジック アプリ ワークフローから SAP コネクタへのすべての接続を削除します。

   1. Azure portal で、ロジック アプリ リソースを開きます。

   1. ロジック アプリ メニューの **[開発ツール (Development Tools)]** で、 **[API 接続 (API connections)]** を選択します。

   1. **[API 接続 (API connections)]** ページで、SAP 接続を選択します。

   1. 接続のページで、 **[削除する]** を選択します。

   1. 確認メッセージに同意して、接続を削除します。

   1. 接続の削除が完了したことを示すポータルの通知を受信するまで待機します。

1. または、ISE の API 接続から SAP コネクタへの接続を削除します。

   1. Microsoft Azure portal で ISE リソースを開きます。

   1. ISE メニューの **[設定]** で **[API connections (API 接続)]** を選択します。

   1. **[API 接続 (API connections)]** ページで、SAP 接続を選択します。

   1. 接続のページで、 **[削除する]** を選択します。

   1. 確認メッセージに同意して、接続を削除します。

   1. 接続の削除が完了したことを示すポータルの通知を受信するまで待機します。

次に、ISE から SAP コネクタを削除します。 コネクタを削除する前に、すべてのロジック アプリでこのコネクタへの接続をすべて削除する必要があります。 まだすべての接続を削除していない場合は、前の手順を確認してください。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 再び、Microsoft Azure portal で ISE リソースを開きます。

1. ISE メニューの **[設定]** で、 **[Managed connectors (マネージド コネクタ)]** を選択します。

1. **[Managed connectors (マネージド コネクタ)]** ページで、SAP コネクタのチェックボックスを選択します。

1. ツールバーの **[削除する]** を選択します。

1. 確認メッセージに同意して、コネクタを削除します。

1. コネクタの削除が完了したことを示すポータルの通知を受信するまで待機します。

次に、SAP コネクタを ISE にデプロイまたは再デプロイします。

1. SAP コネクタのデプロイで使用する新しい zip アーカイブ ファイルを準備します。 SNC ライブラリと、SAPGENPSE ユーティリティを含める必要があります。

   1. すべての SNC、SAPGENPSE、NCo ライブラリを、zip アーカイブのルート フォルダーにコピーします。 これらのバイナリをサブフォルダーに入れないでください。

   1. 64 ビット版の SNC ライブラリを使用する必要があります。 32 ビット版のサポートはありません。

   1. SNC ライブラリのバージョンとその依存関係は、SAP 環境と互換性がある必要があります。 互換性の確認方法については、「[ISE の前提条件](#ise-prerequisites)」を参照してください。

1. [ISE の前提条件](#ise-prerequisites)のデプロイ手順に従って、新しい ZIP アーカイブを作成してください。

最後に、SAP コネクタを使用するすべてのロジックアプリで SNC を使用する新しい接続を作成します。 各接続について、次の手順を実行します。

1. ワークフロー デザイナーでワークフローを再度開きます。

1. SAP コネクタを使用するステップを作成または編集します。

1. SAP 接続に関する必要な情報を入力します。

   :::image type="content" source=".\media\logic-apps-using-sap-connector\ise-connector-settings.png" alt-text="SAP 接続設定を示すワークフロー デザイナーのスクリーンショット" lightbox=".\media\logic-apps-using-sap-connector\ise-connector-settings.png":::

   > [!NOTE]
   > フィールド **[SAP ユーザー名]** と **[SAP パスワード]** はオプションです。 ユーザー名とパスワードを指定しない場合、コネクタは、後の手順で提供されるクライアント証明書を認証に使用します。

1. SNC を有効にします。

   1. **[Use SNC (SNC を使用する)]** では、チェック ボックスを選択します。

   1. **[SNC Library (SNC ライブラリ)]** では、SNC ライブラリの名前を入力します。 たとえば、「 `sapcrypto.dll` 」のように入力します。

   1. **[SNC Partner Name (SNC パートナー名)]** では、バックエンドの SNC の名前を入力します。 たとえば、「 `p:CN=DV3, OU=LA, O=MS, C=US` 」のように入力します。

   1. **[SNC Certificate (SNC 証明書)]** では、SNC クライアントの公開証明書を Base64 エンコード形式で入力します。 PEM ヘッダーまたはフッターは含めないでください。 PSE には複数のプライベート証明書が含まれている可能性があるため、ここにはプライベート証明書を入力しないでください。ただし、この **SNC 証明書** パラメーターは、この接続に使用する必要がある証明書を特定します。 詳細については、下記の注意を確認してください。

   1. 必要に応じて、 **[SNC My Name]** 、 **[SNC Quality of Protection (SNC 保護の品質)]** の SNC 設定を入力します。

   :::image type="content" source=".\media\logic-apps-using-sap-connector\ise-connector-settings-snc.png" alt-text="ワークフロー デザイナーと、新しい SAP 接続の SNC 構成設定を示すスクリーンショット。" lightbox=".\media\logic-apps-using-sap-connector\ise-connector-settings-snc.png":::

1. PSE 設定を構成します。 **[PSE]** には、SNC PSE を Base64 エンコードしたバイナリで入力します。

   * PSE には非公開クライアント証明書が含まれている必要があります。拇印は、前の手順で指定した公開クライアント証明書と一致します。

   * PSE には、追加のクライアント証明書が含まれている場合があります。

   * PSE には PIN があってはなりません。 必要に応じて、SAPGENPSE ユーティリティを使用して PIN を空に設定します。

   証明書のローテーションの場合は、次の手順に従います。

   1. ISE で SAP ISE X.509 を使用するすべての接続について、base64 でエンコードされたバイナリ PSE を更新します。

   1. 新しい証明書を PSE のコピーにインポートします。

   1. PSE ファイルを base64 エンコードされたバイナリとしてエンコードします。

   1. SAP コネクタの API 接続を編集し、そこに新しい PSE ファイルを保存します。

   コネクタは PSE の変更を検出し、次の接続要求中に独自のコピーを更新します。

   バイナリの PSE ファイルを base64 でエンコードされた形式に変換するには、次の手順を実行します。
   
   1. 以下のように、PowerShell スクリプトを使用します。

      ```powershell
      Param ([Parameter(Mandatory=$true)][string]$psePath, [string]$base64OutputPath)
      $base64String = [convert]::ToBase64String((Get-Content -path $psePath -Encoding byte))
      if ($base64OutputPath -eq $null)
      {
          Write-Output $base64String
      }
      else
      {
          Set-Content -Path $base64OutputPath -Value $base64String
          Write-Output "Output written to $base64OutputPath"
      } 
      ```

   1. スクリプトを `pseConvert.ps1` ファイルとして保存してから、スクリプトを呼び出します。次に例を示します。

      ```output
      .\pseConvert.ps1 -psePath "C:\Temp\SECUDIR\request.pse" -base64OutputPath "connectionInput.txt"
      Output written to connectionInput.txt 
      ```

      出力パス パラメーターが指定されていない場合、スクリプトのコンソールへの出力には改行が入ります。 接続入力パラメーターの base64 でエンコードされた文字列の改行を削除します。

   > [!NOTE]
   > ISE に複数の SNC クライアント証明書を使用している場合は、すべての接続に同じ PSE を指定する必要があります。 PSE には、すべての接続のクライアント プライベート証明書が含まれている必要があります。 クライアントの公開証明書パラメーターを、ISE で使用する各接続の特定のプライベート証明書と一致するように設定する必要があります。

1. **[作成]** を選択して接続を作成します。 パラメーターが正しい場合は、接続が作成されます。 パラメーターに問題がある場合は、接続の作成ダイアログにエラー メッセージが表示されます。

   > [!TIP]
   > 接続パラメーターの問題をトラブルシューティングするには、オンプレミス データ ゲートウェイとゲートウェイのローカル ログを使用できます。

1. ワークフロー デザイナーのツール バーで **[Save (保存)]** を選択して、変更を保存します。

## <a name="send-idoc-messages-to-sap-server"></a>IDoc メッセージを SAP サーバーに送信する

次の例に従って、IDoc メッセージを SAP サーバーに送信し、応答を返すロジック アプリ ワークフローを作成します。

1. [HTTP 要求によってトリガーされるロジック アプリ ワークフローを作成します。](#add-http-request-trigger)

1. [SAP にメッセージを送信するアクションをワークフローに作成します。](#create-sap-action-to-send-message)

1. [ワークフローで HTTP 応答アクションを作成します。](#create-http-response-action)

1. [RFC を使用して SAP ABAP からの応答を受信している場合は、リモート関数呼び出し (RFC) の要求 - 応答パターンを作成します。](#create-rfc-request-response)

1. [ロジック アプリをテストします。](#test-your-logic-app-workflow)

<a name="add-http-request-trigger"></a>

### <a name="add-an-http-request-trigger"></a>HTTP 要求トリガーを追加する

ロジック アプリワークフローで、XML HTTP を使用して SAP から IDoc を受信するには、[要求トリガー](../connectors/connectors-native-reqres.md)を使用できます。

> [!TIP]
> 共通プログラミング インターフェイス通信 (CPIC) を使用して IDoc をプレーン XML またはフラット ファイルとして受信するには、「[SAP からのメッセージの受信](#receive-message-sap)」セクションを確認してください。

このセクションでは引き続き要求トリガーを使用します。そのため、まず、*HTTP POST* 要求をワークフローに送信する Azure のエンドポイントを使用してロジック アプリ ワークフローを作成します。 ロジック アプリ ワークフローがこれらの HTTP 要求を受信すると、トリガーが作動してワークフロー内の次のステップが実行されます。

1. [Azure portal](https://portal.azure.com) で空のロジック アプリを作成し、ワークフロー デザイナーを開きます。

1. 検索ボックスに、フィルターとして「`http request`」と入力します。 **[トリガー]** の一覧から、 **[HTTP 要求の受信時]** を選択します。

   ![ロジック アプリ ワークフローに新しい要求トリガーが追加されたワークフロー デザイナーを示すスクリーンショット。](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. 要求を受信できるエンドポイント URL を生成するロジック アプリ ワークフローを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。 これにより、エンドポイントの URL がトリガーに表示されます。

   ![生成された POST URL がコピーされた要求トリガーを含むワークフロー デザイナーを示すスクリーンショット。](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="create-sap-action-to-send-message"></a>メッセージを送信する SAP アクションの作成

次に、[要求トリガー](#add-http-request-trigger)が発生したときに IDoc メッセージを SAP に送信するアクションを作成します。 既定では、厳密な型指定は、スキーマに照らした XML 検証を実行することによって無効な値をチェックするために使用します。 この動作により、問題を初期段階で検出できます。 **[安全な型指定]** オプションは、旧バージョンとの互換性のために使用でき、文字列の長さのみをチェックします。 詳しくは、[[安全な型指定] オプション](#safe-typing)に関する記事をご覧ください。

1. ワークフロー デザイナーのトリガーで、 **[New step (新しいステップ)]** を選択します。

   ![ワークフロー デザイナーを示すスクリーンショット。ワークフローを編集して新しいステップを追加します。](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. 検索ボックスに、フィルターとして「`send message sap`」と入力します。 **[アクション]** の一覧から、 **[Send message to SAP]\(SAP にメッセージを送信する\)** を選択します。

   ![SAP にメッセージを送信するアクションが選択されているワークフロー デザイナーを示すスクリーンショット。](./media/logic-apps-using-sap-connector/select-sap-send-action.png)

   または、 **[Enterprise]** タブを選択し、SAP アクションを選択します。

   ![[Enterprise] タブで、SAP にメッセージを送信するアクションが選択されているワークフロー デザイナーを示すスクリーンショット。](./media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. 接続が既に存在する場合は、次の手順に進みます。 新しい接続を作成するように求めるメッセージが表示されたら、次の情報を入力して、オンプレミスの SAP サーバーに接続します。

   1. 接続の名前を入力します。

   1. データ ゲートウェイを使用している場合は、次の手順を実行します。

      1. **[データ ゲートウェイ]** セクションの **[サブスクリプション]** で、まず、データ ゲートウェイのインストールのために Azure portal で作成したデータ ゲートウェイ リソースの Azure サブスクリプションを選択します。

      1. **[接続ゲートウェイ]** で、Azure のデータ ゲートウェイ リソースを選択します。

   1. 次の手順は、 **[Logon Type (ログオンの種類)]** のプロパティが、 **[Application Server (アプリケーション サーバー)]** または **[Group (グループ)]** のどちらに設定されているかに基づきます。

      * **[アプリケーション サーバー]** の場合、通常は任意として表示される次のプロパティが必須になります。

        ![SAP アプリケーション サーバー接続を作成する方法を示すスクリーンショット。](./media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * **[グループ]** の場合、通常は任意として表示される次のプロパティが必須になります。

        ![SAP メッセージ サーバー接続を作成する方法を示すスクリーンショット。](./media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

        SAP でログオン グループをメンテナンスするには、 **[CCMS: Maintain Logon Groups]** (T コード SMLG) ダイアログ ボックスを開きます。 詳細については、「[SAP Note 26317 - 自動負荷分散のための LOGON グループの設定](https://service.sap.com/sap/support/notes/26317)」を参照してください。

      既定では、厳密な型指定は、スキーマに照らした XML 検証を実行することによって無効な値をチェックするために使用します。 この動作により、問題を初期段階で検出できます。 **[安全な型指定]** オプションは、旧バージョンとの互換性のために使用でき、文字列の長さのみをチェックします。 詳しくは、[[安全な型指定] オプション](#safe-typing)に関する記事をご覧ください。

   1. 完了したら、 **[作成]** をクリックします。

      Azure Logic Apps により、接続の設定とテストが行われ、適切に機能していることが確認されます。

      > [!NOTE]
      > 次のエラーが発生した場合は、SAP NCo クライアント ライブラリのインストールに問題があります。 
      >
      > **Test connection failed. (接続のテストに失敗しました。)Error 'Failed to process request. (エラー '要求を処理できませんでした。)Error details: 'could not load file or assembly 'sapnco, Version=3.0.0.42, Culture=neutral, PublicKeyToken 50436dca5c7f7d23' or one of its dependencies. (エラーの詳細: 'ファイルまたはアセンブリ 'sapnco, Version=3.0.0.42, Culture=neutral, PublicKeyToken 50436dca5c7f7d23' またはその依存関係の 1 つの読み込みに失敗しました。)The system cannot find the file specified.'.' (指定されたファイルが見つかりません。'。')**
      >
      > [必要なバージョンの SAP NCo クライアント ライブラリをインストールし、その他のすべての前提条件が満たされていることを確認してください](#sap-client-library-prerequisites)。

1. 次に、SAP サーバーのアクションを検索して選択します。

   1. **[SAP アクション]** ボックスのフォルダー アイコンを選択します。 ファイルの一覧から、使用する SAP メッセージを検索して選択します。 一覧内を移動するには矢印を使用します。

      この例では、種類が **[Orders]** の IDoc を選択しています。

      ![IDoc アクションの検索と選択を示すスクリーンショット。](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      目的のアクションが見つからない場合は、パスを手動で入力してください。その例を次に示します。

      ![IDoc アクションへのパスを手動で指定する方法を示すスクリーンショット。](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > 式エディターで **SAP アクション** の値を入力します。 これにより、異なる種類のメッセージに対して同じアクションを使用できます。

      IDoc の操作の詳細については、「[IDoc 操作のメッセージ スキーマ](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)」を参照してください。

   1. **[入力メッセージ]** ボックス内をクリックして、動的コンテンツ リストを表示します。 このリストの **[HTTP 要求の受信時]** で、 **[本文]** フィールドを選択します。

      このステップで、HTTP 要求トリガーから Body コンテンツが取り込まれ、その出力が SAP サーバーに送信されます。

      ![要求トリガーの 「Body」プロパティの選択を示すスクリーンショット。](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      完成した SAP アクションは次の例のようになります。

      ![SAP アクションの完了を示すスクリーンショット。](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. ロジック アプリ ワークフローを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

### <a name="send-flat-file-idocs"></a>フラット ファイル IDoc の送信

XML エンベロープにラップすることによって、フラット ファイル スキーマを持つ IDoc を使用することができます。 フラット ファイル IDoc を送信するには、[IDoc メッセージを送信する SAP アクションを作成する](#create-sap-action-to-send-message)ための一般的な手順を次のように変更して使用します。

1. **SAP にメッセージを送信する** アクションには、SAP アクション URI `http://Microsoft.LobServices.Sap/2007/03/Idoc/SendIdoc` を使用します。

1. XML エンベロープを使用して入力メッセージの書式を設定します。

例については、次のXML ペイロードの例を参照してください。

```xml
<SendIdoc xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/">
<idocData>EDI_DC 3000000001017945375750 30INVOIC011BTSVLINV30KUABCABCFPPC LDCA X004010810 4 SAPMSX LSEDI ABCABCFPPC 000d3ae4-723e-1edb-9ca4-cc017365c9fd 20210217054521INVOICINVOIC01ZINVOIC2RE 20210217054520
E2EDK010013000000001017945375000001E2EDK01001000000010 ABCABC1.00000 0060 INVO9988298128 298.000 298.000 LB Z4LR EN 0005065828 L
E2EDKA1 3000000001017945375000002E2EDKA1 000000020 RS ABCABCFPPC 0005065828 ABCABCABC ABCABC Inc. Limited Risk Distributor ABCABC 1950 ABCABCABCA Blvd ABCABAABCAB L5N8L9 CA ABCABC E ON V-ABCABC LDCA
E2EDKA1 3000000001017945375000003E2EDKA1 000000020 AG 0005065828 ABCABCFPPC ABCABC ABCABC ABCABC - FPP ONLY 88 ABCABC Crescent ABCABAABCAB L5R 4A2 CA ABCABC 111 111 1111 E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000004E2EDKA1 000000020 RE 0005065828 ABCABCFPPC ABCABC ABCABC ABCABC - FPP ONLY 88 ABCABC Crescent ABCABAABCAB L5R 4A2 CA ABCABC 111 111 1111 E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000005E2EDKA1 000000020 RG 0005065828 ABCABCFPPC ABCABC ABCABC ABCABC - FPP ONLY 88 ABCABC Crescent ABCABAABCAB L5R 4A2 CA ABCABC 111 111 1111 E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000006E2EDKA1 000000020 WE 0005001847 41 ABCABC ABCABC INC (ABCABC) DC A. ABCABCAB 88 ABCABC CRESCENT ABCABAABCAB L5R 4A2 CA ABCABC 111-111-1111 E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000007E2EDKA1 000000020 Z3 0005533050 ABCABCABC ABCABC Inc. ABCA Bank Swift Code -ABCABCABCAB Sort Code - 1950 ABCABCABCA Blvd. Acc No -1111111111 ABCABAABCAB L5N8L9 CA ABCABC E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000008E2EDKA1 000000020 BK 1075 ABCABCABC ABCABC Inc 1950 ABCABCABCA Blvd ABCABAABCAB ON L5N 8L9 CA ABCABC (111) 111-1111 (111) 111-1111 ON
E2EDKA1 3000000001017945375000009E2EDKA1 000000020 CR 1075 CONTACT ABCABCABC 1950 ABCABCABCA Blvd ABCABAABCAB ON L5N 8L9 CA ABCABC (111) 111-1111 (111) 111-1111 ON
E2EDK02 3000000001017945375000010E2EDK02 000000020 0099988298128 20210217
E2EDK02 3000000001017945375000011E2EDK02 000000020 00140-N6260-S 20210205
E2EDK02 3000000001017945375000012E2EDK02 000000020 0026336270425 20210217
E2EDK02 3000000001017945375000013E2EDK02 000000020 0128026580537 20210224
E2EDK02 3000000001017945375000014E2EDK02 000000020 01740-N6260-S
E2EDK02 3000000001017945375000015E2EDK02 000000020 900IAC
E2EDK02 3000000001017945375000016E2EDK02 000000020 901ZSH
E2EDK02 3000000001017945375000017E2EDK02 000000020 9078026580537 20210217
E2EDK03 3000000001017945375000018E2EDK03 000000020 02620210217
E2EDK03 3000000001017945375000019E2EDK03 000000020 00120210224
E2EDK03 3000000001017945375000020E2EDK03 000000020 02220210205
E2EDK03 3000000001017945375000021E2EDK03 000000020 01220210217
E2EDK03 3000000001017945375000022E2EDK03 000000020 01120210217
E2EDK03 3000000001017945375000023E2EDK03 000000020 02420210217
E2EDK03 3000000001017945375000024E2EDK03 000000020 02820210418
E2EDK03 3000000001017945375000025E2EDK03 000000020 04820210217
E2EDK17 3000000001017945375000026E2EDK17 000000020 001DDPDelivered Duty Paid
E2EDK17 3000000001017945375000027E2EDK17 000000020 002DDPdestination
E2EDK18 3000000001017945375000028E2EDK18 000000020 00160 0 Up to 04/18/2021 without deduction
E2EDK28 3000000001017945375000029E2EDK28 000000020 CA BOFACATT Bank of ABCABAB ABCABC ABCABAB 50127217 ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000030E2EDK28 000000020 CA 026000082 ABCAbank ABCABC ABCABAB 201456700OLD ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000031E2EDK28 000000020 GB ABCAGB2L ABCAbank N.A ABCABA E14, 5LB GB63ABCA18500803115593 ABCABCABC ABCABC Inc. GB63ABCA18500803115593
E2EDK28 3000000001017945375000032E2EDK28 000000020 CA 020012328 ABCABANK ABCABC ABCABAB ON M5J 2M3 2014567007 ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000033E2EDK28 000000020 CA 03722010 ABCABABC ABCABABC Bank of Commerce ABCABAABCAB 64-04812 ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000034E2EDK28 000000020 IE IHCC In-House Cash Center IHCC1075 ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000035E2EDK28 000000020 CA 000300002 ABCAB Bank of ABCABC ABCABAB 0021520584OLD ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000036E2EDK28 000000020 US USCC US Cash Center (IHC) city USCC1075 ABCABCABC ABCABC Inc.
E2EDK29 3000000001017945375000037E2EDK29 000000020 0064848944US A CAD CA ABCABC CA United States US CA A Air Air
E2EDKT1 3000000001017945375000038E2EDKT1 000000020 ZJ32E EN
E2EDKT2 3000000001017945375000039E2EDKT2 000038030 GST/HST877845941RT0001 *
E2EDKT2 3000000001017945375000040E2EDKT2 000038030 QST1021036966TQ0001 *
E2EDKT1 3000000001017945375000041E2EDKT1 000000020 Z4VL
E2EDKT2 3000000001017945375000042E2EDKT2 000041030 0.000 *
E2EDKT1 3000000001017945375000043E2EDKT1 000000020 Z4VH
E2EDKT2 3000000001017945375000044E2EDKT2 000043030 *
E2EDK14 3000000001017945375000045E2EDK14 000000020 008LDCA
E2EDK14 3000000001017945375000046E2EDK14 000000020 00710
E2EDK14 3000000001017945375000047E2EDK14 000000020 00610
E2EDK14 3000000001017945375000048E2EDK14 000000020 015Z4F2
E2EDK14 3000000001017945375000049E2EDK14 000000020 0031075
E2EDK14 3000000001017945375000050E2EDK14 000000020 021M
E2EDK14 3000000001017945375000051E2EDK14 000000020 0161075
E2EDK14 3000000001017945375000052E2EDK14 000000020 962M
E2EDP010013000000001017945375000053E2EDP01001000000020 000011 2980.000 EA 298.000 LB MOUSE 298.000 Z4TN 4260
E2EDP02 3000000001017945375000054E2EDP02 000053030 00140-N6260-S 00000120210205 DFUE
E2EDP02 3000000001017945375000055E2EDP02 000053030 0026336270425 00001120210217
E2EDP02 3000000001017945375000056E2EDP02 000053030 0168026580537 00001020210224
E2EDP02 3000000001017945375000057E2EDP02 000053030 9100000 00000120210205 DFUE
E2EDP02 3000000001017945375000058E2EDP02 000053030 911A 00000120210205 DFUE
E2EDP02 3000000001017945375000059E2EDP02 000053030 912PP 00000120210205 DFUE
E2EDP02 3000000001017945375000060E2EDP02 000053030 91300 00000120210205 DFUE
E2EDP02 3000000001017945375000061E2EDP02 000053030 914CONTACT ABCABCABC 00000120210205 DFUE
E2EDP02 3000000001017945375000062E2EDP02 000053030 963 00000120210205 DFUE
E2EDP02 3000000001017945375000063E2EDP02 000053030 965 00000120210205 DFUE
E2EDP02 3000000001017945375000064E2EDP02 000053030 9666336270425 00000120210205 DFUE
E2EDP02 3000000001017945375000065E2EDP02 000053030 9078026580537 00001020210205 DFUE
E2EDP03 3000000001017945375000066E2EDP03 000053030 02920210217
E2EDP03 3000000001017945375000067E2EDP03 000053030 00120210224
E2EDP03 3000000001017945375000068E2EDP03 000053030 01120210217
E2EDP03 3000000001017945375000069E2EDP03 000053030 02520210217
E2EDP03 3000000001017945375000070E2EDP03 000053030 02720210217
E2EDP03 3000000001017945375000071E2EDP03 000053030 02320210217
E2EDP03 3000000001017945375000072E2EDP03 000053030 02220210205
E2EDP19 3000000001017945375000073E2EDP19 000053030 001418VVZ
E2EDP19 3000000001017945375000074E2EDP19 000053030 002RJR-00001 AB ABCABCABC Mouse FORBUS BLUETOOTH
E2EDP19 3000000001017945375000075E2EDP19 000053030 0078471609000
E2EDP19 3000000001017945375000076E2EDP19 000053030 003889842532685
E2EDP19 3000000001017945375000077E2EDP19 000053030 011CN
E2EDP26 3000000001017945375000078E2EDP26 000053030 00459064.20
E2EDP26 3000000001017945375000079E2EDP26 000053030 00352269.20
E2EDP26 3000000001017945375000080E2EDP26 000053030 01052269.20
E2EDP26 3000000001017945375000081E2EDP26 000053030 01152269.20
E2EDP26 3000000001017945375000082E2EDP26 000053030 0126795.00
E2EDP26 3000000001017945375000083E2EDP26 000053030 01552269.20
E2EDP26 3000000001017945375000084E2EDP26 000053030 00117.54
E2EDP26 3000000001017945375000085E2EDP26 000053030 00252269.20
E2EDP26 3000000001017945375000086E2EDP26 000053030 940 2980.000
E2EDP26 3000000001017945375000087E2EDP26 000053030 939 2980.000
E2EDP05 3000000001017945375000088E2EDP05 000053030 + Z400MS List Price 52269.20 17.54 1 EA CAD 2980
E2EDP05 3000000001017945375000089E2EDP05 000053030 + XR1 Tax Jur Code Level 6795.00 13.000 52269.20
E2EDP05 3000000001017945375000090E2EDP05 000053030 + Tax Subtotal1 6795.00 2.28 1 EA CAD 2980
E2EDP05 3000000001017945375000091E2EDP05 000053030 + Taxable Amount + TaxSubtotal1 59064.20 19.82 1 EA CAD 2980
E2EDP04 3000000001017945375000092E2EDP04 000053030 CX 13.000 6795.00 7000000000
E2EDP04 3000000001017945375000093E2EDP04 000053030 CX 0 0 7001500000
E2EDP04 3000000001017945375000094E2EDP04 000053030 CX 0 0 7001505690
E2EDP28 3000000001017945375000095E2EDP28 000053030 00648489440000108471609000 CN CN ABCAB ZZ 298.000 298.000 LB US 400 United Stat KY
E2EDPT1 3000000001017945375000096E2EDPT1 000053030 0001E EN
E2EDPT2 3000000001017945375000097E2EDPT2 000096040 AB ABCABCABC Mouse forBus Bluetooth EN/XC/XD/XX Hdwr Black For Bsnss *
E2EDS01 3000000001017945375000098E2EDS01 000000020 0011
E2EDS01 3000000001017945375000099E2EDS01 000000020 01259064.20 CAD
E2EDS01 3000000001017945375000100E2EDS01 000000020 0056795.00 CAD
E2EDS01 3000000001017945375000101E2EDS01 000000020 01159064.20 CAD
E2EDS01 3000000001017945375000102E2EDS01 000000020 01052269.20 CAD
E2EDS01 3000000001017945375000103E2EDS01 000000020 94200000 CAD
E2EDS01 3000000001017945375000104E2EDS01 000000020 9440.00 CAD
E2EDS01 3000000001017945375000105E2EDS01 000000020 9450.00 CAD
E2EDS01 3000000001017945375000106E2EDS01 000000020 94659064.20 CAD
E2EDS01 3000000001017945375000107E2EDS01 000000020 94752269.20 CAD
E2EDS01 3000000001017945375000108E2EDS01 000000020 EXT
Z2XSK010003000000001017945375000109Z2XSK01000000108030 Z400 52269.20
Z2XSK010003000000001017945375000110Z2XSK01000000108030 XR1 13.000 6795.00 CX
</idocData>
</SendIdoc>
```

### <a name="create-http-response-action"></a>HTTP 応答アクションの作成

ロジック アプリのワークフローに応答アクションを追加し、SAP アクションからの出力を取り込みます。 このように SAP サーバーから受け取った結果が、ロジック アプリ ワークフローから要求元に返されます。

1. ワークフロー デザイナーで、SAP アクションの下に表示される **[New step (新しいステップ)]** を選択します。

1. 検索ボックスに、フィルターとして「`response`」と入力します。 **[アクション]** の一覧で、 **[応答]** を選択します。

1. **[本文]** ボックス内をクリックして、動的コンテンツ リストを表示します。 このリストの **[Send message to SAP]\(SAP にメッセージを送信する\)** で、 **[本文]** フィールドを選択します。

   ![SAP アクションの完了を示すスクリーンショット。](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. ロジック アプリ ワークフローを保存します。

### <a name="create-rfc-request-response"></a>RFC の要求 - 応答を作成する

Azure Logic Apps へのリモート関数呼び出し (RFC) を使用して SAP ABAP から応答を受け取る必要がある場合は、要求と応答のパターンを作成する必要があります。 ロジック アプリ ワークフローで IDoc を受信するには、最初のアクションを、状態コードが`200 OK` でコンテンツが含まれない [応答アクション](../connectors/connectors-native-reqres.md#add-a-response-action)にする必要があります。 この推奨される手順では、直ちに tRFC 経由で SAP Logical Unit of Work (LUW) の非同期転送が実行され、SAP CPIC の会話が再び使用可能になります。 その後、以降の転送をブロックすることなく、受信した IDoc を処理するための追加アクションをロジック アプリ ワークフローに追加できます。

> [!NOTE]
> SAP トリガーでは、tRFC 経由で IDoc が受信されますが、仕様上は応答パラメーターはありません。

要求と応答のパターンを実装するには、最初に、[`generate schema` コマンド](#generate-schemas-for-artifacts-in-sap)を使用して RFC スキーマを検出する必要があります。 生成されるスキーマには、2 つのルート ノードが存在する可能性があります。 

* 要求ノード。これは SAP から受信する呼び出しです。
* 応答ノード。これは SAP に戻す応答です。

次の例の要求と応答のパターンは、`STFC_CONNECTION` RFC モジュールから生成されます。 要求の XML が解析されて、SAP 要求が `<ECHOTEXT>` であるノード値が抽出されます。 応答により、現在のタイムスタンプが動的な値として挿入されます。 ロジック アプリ ワークフローから SAP に `STFC_CONNECTION` RFC を送信すると、同様の応答を受け取ります。

```xml
<STFC_CONNECTIONResponse xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <ECHOTEXT>@{first(xpath(xml(triggerBody()?['Content']), '/*[local-name()="STFC_CONNECTION"]/*[local-name()="REQUTEXT"]/text()'))}</ECHOTEXT>
  <RESPTEXT>Azure Logic Apps @{utcNow()}</RESPTEXT>
```

### <a name="test-your-logic-app-workflow"></a>ロジック アプリ ワークフローをテストする

1. まだロジック アプリが有効になっていない場合は、ロジック アプリのメニューで **[概要]** を選択します。 ツールバーで、 **[Enable]\(有効化\)** を選択します。

1. デザイナーのツール バーで、 **[実行]** を選択します。 この手順では、ロジック アプリ ワークフローを手動で起動します。

1. 要求トリガーの URL に HTTP POST 要求を送信してロジック アプリ ワークフローをトリガーします。 要求にメッセージの内容を追加します。 要求は、[Postman](https://www.getpostman.com/apps) などのツールを使用して送信することができます。

   この記事の要求で送信するのは IDoc ファイルです。このファイルは XML 形式であること、また、使用する SAP アクションの名前空間を含んでいる必要があります。その例を次に示します。

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
   <idocData>
   <...>
   </idocData>
   </Send>
   ```

1. HTTP 要求を送信したら、ロジック アプリ ワークフローからの応答を待機します。

   > [!NOTE]
   > 応答に必要なすべての手順が[要求タイムアウト制限](logic-apps-limits-and-config.md)内に完了しない場合、ロジック アプリ ワークフローがタイムアウトします。 この状況に陥ると、要求がブロック状態になります。 問題の診断については、[ロジック アプリをチェックしたり監視したりする方法](monitor-logic-apps.md)に関するページを参照してください。

これで、SAP サーバーとやり取りすることのできるロジック アプリ ワークフローが完成しました。 ロジック アプリ ワークフローに使用する SAP 接続をセットアップしたら、BAPI や RFC など、他に利用できる SAP アクションを探してみましょう。

<a name="receive-message-sap"></a>

## <a name="receive-message-from-sap"></a>SAP からのメッセージの受信

この例では、アプリが SAP システムからメッセージを受信するときにトリガーするロジック アプリ ワークフローを使用します。

### <a name="add-an-sap-trigger"></a>SAP トリガーを追加する

1. Azure portal で空のロジック アプリを作成し、ワークフロー デザイナーを開きます。

1. 検索ボックスに、フィルターとして「`when message received sap`」と入力します。 **[トリガー]** の一覧から、 **[When a message is received from SAP]\(SAP からメッセージを受信したとき\)** というトリガーを選択します。

   ![SAP トリガーの追加を示すスクリーンショット。](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   または、 **[Enterprise]** タブを選択し、トリガーを選択します。

   ![[Enterprise] タブからの SAP トリガーの追加を示すスクリーンショット。](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. 接続が既に存在する場合は、次の手順に進んで SAP トリガーを設定します。 ただし、接続の詳細を確認するメッセージが表示されたら、オンプレミスの SAP サーバーへの接続を今すぐ作成できるように情報を提供します。

   1. 接続の名前を入力します。

   1. データ ゲートウェイを使用している場合は、次の手順を実行します。

      1. **[データ ゲートウェイ]** セクションの **[サブスクリプション]** で、まず、データ ゲートウェイのインストールのために Azure portal で作成したデータ ゲートウェイ リソースの Azure サブスクリプションを選択します。

      1. **[接続ゲートウェイ]** で、Azure のデータ ゲートウェイ リソースを選択します。

   1. 接続に関する情報を引き続き入力します。 **[ログオンの種類]** プロパティで、プロパティが **[アプリケーション サーバー]** または **[グループ]** のどちらに設定されているかに基づいて、手順に従います。

      * **[アプリケーション サーバー]** の場合、通常は任意として表示される次のプロパティが必須になります。

        ![SAP アプリケーション サーバーでの接続の作成を示すスクリーンショット。](./media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * **[グループ]** の場合、通常は任意として表示される次のプロパティが必須になります。

        ![SAP メッセージ サーバーでの接続の作成を示すスクリーンショット](./media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      既定では、厳密な型指定は、スキーマに照らした XML 検証を実行することによって無効な値をチェックするために使用します。 この動作により、問題を初期段階で検出できます。 **[安全な型指定]** オプションは、旧バージョンとの互換性のために使用でき、文字列の長さのみをチェックします。 詳しくは、[[安全な型指定] オプション](#safe-typing)に関する記事をご覧ください。

   1. 完了したら、 **[作成]** をクリックします。

      Azure Logic Apps により、接続の設定とテストが行われ、適切に機能していることが確認されます。

1. SAP システムの構成に基づいて、[必要なパラメーター](#parameters)を指定し、このトリガーで使用できるその他のパラメーターを追加します。次に例を示します。

   * IDocs をプレーンな XML として受信するには、SAP のプレーン XML 形式をサポートする **When a message is received from SAP (SAP からのメッセージを受信した場合)** トリガにおいて、 **[IDOC Format]** パラメーターを **SapPlainXml** に追加設定します。

   * 同じ SAP トリガーを使用して IDocs をフラット ファイルとして受信するには、 **[IDOC Format]** パラメーターを追加して **FlatFile** に設定します。 [フラット ファイル デコード アクション](logic-apps-enterprise-integration-flatfile.md)も使用する場合、フラット ファイル スキーマでは、`early_terminate_optional_fields` プロパティを使用し、値を `true` に設定する必要があります。

     この要件が必要なのは、tRFC 呼び出し `IDOC_INBOUND_ASYNCHRONOUS` では、SAP によって送信されるフラット ファイル IDoc データ レコードが、SDATA フィールドの長さ全体にパディングされないためです。 Azure Logic Apps では、SAP から受信したままの、パディングされていないフラット ファイル IDoc の元のデータが提供されます。 また、この SAP トリガーをフラット ファイル デコード アクションと組み合わせる場合、アクションで提供されるスキーマと一致する必要があります。

   * [SAP アクションのリストを指定することで、SAP サーバーから受信するメッセージをフィルター処理](#filter-with-sap-actions)できます。

     SAP アクションはファイル ピッカーから選択できます。

     ![SAP アクションをロジック アプリ ワークフローに追加する方法を示すスクリーンショット。](./media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

     あるいは、次のようにアクションを手動で指定できます。

     ![使用する SAP アクションを手動で入力するスクリーンショット。](./media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

     複数のメッセージを受信するようにトリガーを設定したときのアクションの表示例を次に示します。

     ![複数のメッセージを受信するトリガーの例を示すスクリーンショット。](./media/logic-apps-using-sap-connector/example-trigger.png)

     SAP アクションの詳細については、「[IDoc 操作のメッセージ スキーマ](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)」を参照してください

1. それでは、SAP システムからメッセージを受信できるようにロジック アプリ ワークフローを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

   これでロジック アプリ ワークフローは SAP システムからメッセージを受信できます。

   > [!NOTE]
   > これらの手順の SAP トリガーは、ポーリング トリガーではなく、webhook ベースのトリガーです。 データ ゲートウェイを使用している場合、トリガーはメッセージが存在するときにのみデータ ゲートウェイから呼び出されるため、ポーリングは必要ありません。

1. ロジック アプリ ワークフローのトリガー履歴で、トリガーの登録が成功したことを確認します。

**500 Bad Gateway** または **400 Bad Request** のエラーで、「**service 'sapgw00' unknown**」のようなメッセージが表示された場合は、ポート番号へのネットワーク サービス名解決に失敗しているなどの可能性があります。

```json
{
   "body": {
      "error": {
         "code": 500,
         "source": "EXAMPLE-FLOW-NAME.eastus.environments.microsoftazurelogicapps.net",
         "clientRequestId": "00000000-0000-0000-0000-000000000000",
         "message": "BadGateway",
         "innerError": {
            "error": {
               "code": "UnhandledException",
               "message": "\nERROR service 'sapgw00' unknown\nTIME Wed Nov 11 19:37:50 2020\nRELEASE 721\nCOMPONENT NI (network interface)\nVERSION 40\nRC -3\nMODULE ninti.c\nLINE 933\nDETAIL NiPGetServByName: 'sapgw00' not found\nSYSTEM CALL getaddrinfo\nCOUNTER 1\n\nRETURN CODE: 20"
            }
         }
      }
   }
}
```

* **オプション 1:** API 接続とトリガー構成で、ゲートウェイ サービス名をそのポート番号に置き換えます。 次のエラーの例では、`sapgw00` を実際のポート番号 (`3300` など) に置き換える必要があります。 これは ISE のみで使用可能なオプションです。

* **オプション 2:** オンプレミス データ ゲートウェイを使用している場合は、`%windir%\System32\drivers\etc\services` のポート マッピングにゲートウェイ サービス名を追加し、オンプレミスデータ ゲートウェイ サービスを再起動できます。次に例を示します。

  ```text
  sapgw00  3300/tcp
  ```

SAP アプリケーション サーバーまたはメッセージ サーバーの名前が IP アドレスに解決されると、同様のエラーが表示されることがあります。 ISE の場合、SAP アプリケーション サーバーまたはメッセージ サーバーの IP アドレスを指定する必要があります。 オンプレミス データ ゲートウェイの場合は、次のように、`%windir%\System32\drivers\etc\hosts` で IP アドレス マッピングに名前を追加できます。

```text
10.0.1.9 SAPDBSERVER01 # SAP System Server VPN IP by computer name
10.0.1.9 SAPDBSERVER01.someguid.xx.xxxxxxx.cloudapp.net # SAP System Server VPN IP by fully qualified computer name
```

### <a name="parameters"></a>パラメーター

SAP コネクタは、単純な文字列と数値の入力と共に、次のテーブル パラメーター (`Type=ITAB` 入力) を受け取ります。

* 以前の SAP リリースのテーブル方向パラメーター (入力と出力の両方)。

* パラメーターの変更。これにより、新しい SAP リリースのテーブル方向パラメーターが置き換えられます。

* 階層テーブル パラメーター

### <a name="filter-with-sap-actions"></a>SAP アクションでフィルター処理する

必要に応じて、1 つまたは複数の SAP アクションを含むリスト (配列) を指定することによって、ロジック アプリ ワークフローが SAP サーバーから受信するメッセージをフィルター処理できます。 既定では、この配列は空です。つまり、ロジック アプリは、フィルター処理を行わずに SAP サーバーからすべてのメッセージを受信します。

配列フィルターを設定すると、トリガーは指定された SAP アクションの種類のメッセージだけを受信し、SAP サーバーからの他のすべてのメッセージを拒否します。 ただし、このフィルターは、受信したペイロードの型指定が厳密かそうでないかには影響しません。

SAP アクションによるフィルター処理はすべて、オンプレミス データ ゲートウェイ用の SAP アダプターのレベルで実行されます。 詳細については、「[SAP から Logic Apps にテスト用 IDoc を送信する方法](#test-sending-idocs-from-sap)」を参照してください。

SAP からロジック アプリ ワークフローのトリガーに IDoc パケットを送信できない場合は、[SAP tRFC (T コード SM58)] ダイアログボックスの Transactional RFC (tRFC) の呼び出し拒否メッセージを確認してください。 SAP インターフェイスに、 **[Status Text]\(状態テキスト\)** フィールドの部分文字列の制限によって切り詰められている次のエラー メッセージが表示される場合があります。

#### <a name="the-requestcontext-on-the-ireplychannel-was-closed-without-a-reply-being-sent"></a>The RequestContext on the IReplyChannel was closed without a reply being sent (IReplyChannel の RequestContext は、応答が送信されずに閉じられました)

このエラー メッセージは、エラーによってチャネルの汎用ハンドラーがチャネルを終了し、他のメッセージを処理するためにチャネルを再構築した場合に、予期しないエラーが発生することを意味します。

ロジック アプリ ワークフローが IDoc を受信したことを確認するには、`200 OK` 状態コードを返す[応答アクションを追加](../connectors/connectors-native-reqres.md#add-a-response-action)します。 本文は空のままにして、変更したりヘッダーに追加したりしないでください。 IDoc は tRFC 経由で転送され、応答ペイロードは許可されません。

代わりに IDoc を拒否するには、`200 OK` 以外の HTTP 状態コードを使用して応答します。 その後、ユーザーに代わって、SAP アダプターによって例外が SAP に返されます。 IDoc を拒否するのは、アプリケーションが処理できない、誤って転送された IDoc などの転送エラーを SAP に通知する場合のみです。 IDoc に含まれているデータに関する問題など、アプリケーション レベルのエラーの場合は IDoc を拒否しないでください。 アプリケーション レベルの検証のために転送の受け入れを遅らせた場合、接続において他の IDoc の転送がブロックされるため、パフォーマンスが低下するおそれがあります。

このエラー メッセージを受信し、Azure Logic Apps の呼び出しでシステムの障害が発生した場合は、お客様の特定の環境のオンプレミス データ ゲートウェイ サービスに対してネットワーク設定が構成されていることを確認してください。 たとえば、ネットワーク環境で Azure エンドポイントを呼び出すためにプロキシを使用する必要がある場合は、プロキシを使用するようにオンプレミス データ ゲートウェイ サービスを構成する必要があります。 詳細については、「[プロキシの構成](/dotnet/framework/network-programming/proxy-configuration)」を参照してください。

このエラー メッセージを受信し、Azure Logic Apps の呼び出しで断続的なエラーが発生する場合は、再試行回数や再試行間隔を増やす必要があることがあります。

1. オンプレミス データ ゲートウェイ サービスの構成ファイル `Microsoft.PowerBI.EnterpriseGateway.exe.config` で、SAP の設定を確認します。

   再試行回数の設定は、`WebhookRetryMaximumCount="2"` のようになります。 再試行間隔の設定は、`WebhookRetryDefaultDelay="00:00:00.10"` のようになります。期間の形式は `HH:mm:ss.ff` です。

1. 変更を保存します。 オンプレミス データ ゲートウェイの再起動

#### <a name="the-segment-or-group-definition-e2edk36001-was-not-found-in-the-idoc-meta"></a>The segment or group definition E2EDK36001 was not found in the IDoc meta (セグメントまたはグループ定義 E2EDK36001 が IDoc メタで見つかりませんでした)

このエラー メッセージは、他のエラーによって予期されるエラーが発生することを意味します。 たとえば、IDoc XML ペイロードが生成されないエラーは、そのセグメントが SAP によって解放されないことに起因します。 その結果、変換に必要なセグメントの種類のメタデータが見つかりません。

これらのセグメントを SAP にリリースしてもらうには、SAP システムの ABAP エンジニアに連絡してください。

### <a name="asynchronous-request-reply-for-triggers"></a>トリガーの非同期要求-応答

SAP コネクタでは、Azure Logic Apps トリガー用の Azure の[非同期要求 - 応答パターン](/azure/architecture/patterns/async-request-reply)がサポートされています。 このパターンを使用すると、既定の同期要求-応答パターンであれば失敗していた要求を成功させることができます。

> [!TIP]
> 複数の応答アクションを持つロジック アプリ ワークフローでは、すべての応答アクションで同じ要求-応答パターンを使用する必要があります。 たとえば、ロジック アプリ ワークフローで複数の応答アクションが可能なスイッチ コントロールを使用している場合は、すべての応答アクションで同期または非同期の同じ要求 - 応答パターンを使用するように設定する必要があります。

応答アクションに対して非同期応答を有効にすると、ロジック アプリ ワークフローは、要求の処理を受け入れると `202 Accepted`の応答を返すことができます。 この応答には、要求の最終状態を取得するために使用できる場所ヘッダーが含まれています。

SAP コネクタを使用してロジック アプリ ワークフローの非同期要求 - 応答パターンを構成するには、次の手順を実行します。

1. ワークフロー デザイナーでロジック アプリを開きます。

1. SAP コネクタがロジック アプリ ワークフローのトリガーであることを確認します。

1. ロジック アプリ ワークフローの **応答** アクションを開きます。 アクションのタイトル バーで、メニュー ( **...** ) &gt; **[設定]** の順に選択します。

1. 応答アクションの **[設定]** で、 **[非同期応答]** の下にあるトグルをオンにします。 [完了] を選択します。

1. ロジック アプリ ワークフローへの変更を保存します。

## <a name="find-extended-error-logs"></a>拡張エラー ログを確認する

完全なエラー メッセージについては、SAP アダプターの拡張ログを確認してください。 また、[SAP コネクタの拡張ログ ファイルを有効にする](#extended-sap-logging-in-on-premises-data-gateway)こともできます。

* 2020 年 4 月以前のオンプレミス データ ゲートウェイ リリースでは、ログは既定で無効になっています。

* 2020 年 6 月以降のオンプレミス データ ゲートウェイ リリースでは、[アプリ設定でゲートウェイ ログを有効にする](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app)ことができます。

  * 既定のログ レベルは **[警告]** です。

  * オンプレミス データ ゲートウェイ アプリの **[診断]** 設定で **[追加のログ記録]** を有効にすると、ログ記録レベルが **[情報]** に上がります。

  * ログ記録レベルを **[詳細]** に上げるには、構成ファイルで次の設定を更新します。 通常、構成ファイルは `C:\Program Files\On-premises data gateway\Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config` にあります。

    ```xml
    <setting name="SapTraceLevel" serializeAs="String">
       <value>Verbose</value>
    </setting>
    ```

### <a name="extended-sap-logging-in-on-premises-data-gateway"></a>オンプレミス データ ゲートウェイでの拡張 SAP ログ

[Azure Logic Apps 用のオンプレミス データ ゲートウェイ](logic-apps-gateway-install.md)を使用する場合は、SAP コネクタ用に拡張ログ ファイルを構成できます。 オンプレミス データ ゲートウェイを使用して、Event Tracing for Windows (ETW) イベントをゲートウェイの .zip 形式のログ ファイルに含まれているローテーション ログ ファイルにリダイレクトできます。

ゲートウェイ アプリの設定から、[ゲートウェイの構成とサービス ログをすべて](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app) .zip ファイルにエクスポート ことができます。

> [!NOTE]
> 拡張ログが常に有効になっていると、ロジック アプリ ワークフローのパフォーマンスに影響を与える可能性があります。 問題の分析とトラブルシューティングを完了した後は、拡張ログ ファイルをオフにすることをお勧めします。

#### <a name="capture-etw-events"></a>ETW イベントのキャプチャ

必要に応じて、上級ユーザーは ETW イベントを直接キャプチャすることができます。 その後、[Azure Diagnostics のデータを Event Hubs で使用したり](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md)、[Azure Monitor Logs にデータを収集する](../azure-monitor/agents/diagnostics-extension-logs.md)ことができます。 詳細については、「[データを収集して格納するためのベスト プラクティス](/azure/architecture/best-practices/monitoring#collecting-and-storing-data)」を参照してください。 [PerfView](https://github.com/Microsoft/perfview/blob/master/README.md) を使用して、生成される ETL ファイルを操作したり、独自のプログラムを作成したりすることができます。 このチュートリアルでは、PerfView を使用します。

1. [PerfView] メニューで、 **[Collect]\(収集\)** &gt; **[Collect]\(収集\)** を選択して、イベントをキャプチャします。

1. **[追加のプロバイダー]** フィールドに `*Microsoft-LobAdapter` と入力し、SAP アダプタ イベントをキャプチャする SAP プロバイダーを指定します。 この情報を指定しない場合、トレースには一般的な ETW イベントのみが含まれます。

1. その他の既定の設定はそのままにします。 必要に応じて、 **[データ ファイル]** フィールドでファイル名または場所を変更できます。

1. **[Start Collection]\(収集の開始\)** を選択して、トレースを開始します。

1. 問題を再現した後、または十分な分析データを収集したら、 **[Stop Collection]\(収集の停止\)** を選択します。

1. Azure サポート エンジニアなどの別のパーティとデータを共有するには、ETL ファイルを圧縮します。

1. トレースの内容を表示するには、次の手順を実行します。

   1. PerfView で、 **[ファイル]** &gt; **[開く]** を選択し、先ほど生成した ETL ファイルを選択します。

   1. PerfView のサイドバーでは、ETL ファイルの下の **[イベント]** セクションです。

   1. **[フィルター]** で `Microsoft-LobAdapter` でフィルター処理して、関連するイベントとゲートウェイ プロセスのみを表示します。

### <a name="test-your-workflow"></a>ワークフローのテスト

1. ロジック アプリ ワークフローをトリガーするには、SAP システムからメッセージを送信します。

1. ロジック アプリのメニューで、 **[概要]** を選択します。 **[実行履歴]** に、ロジック アプリ ワークフローの新しい実行がないか確認します。

1. 最新の実行を開きます。トリガー出力セクションに SAP システムから送信されたメッセージが表示されます。

### <a name="test-sending-idocs-from-sap"></a>SAP からの IDoc の送信をテストする

SAP から自分のロジック アプリ ワークフローに IDoc を送信するには、次の最小構成が必要です。

> [!IMPORTANT]
> これらの手順は、自分のロジック アプリ ワークフローで SAP 構成をテストする場合にのみ使用します。 運用環境では追加の構成が必要になります。

1. [SAP で RFC 宛先を構成する。](#create-rfc-destination)

1. [RFC 宛先への ABAP 接続を作成する。](#create-abap-connection)

1. [受信側ポートを作成する。](#create-receiver-port)

1. [送信側ポートを作成する。](#create-sender-port)

1. [論理システム パートナーを作成する。](#create-logical-system-partner)

1. [パートナー プロファイルを作成する。](#create-partner-profiles)

1. [メッセージの送信をテストする。](#test-sending-messages)

#### <a name="create-rfc-destination"></a>RFC 宛先を作成する

1. **[Configuration of RFC Connections (RFC 接続の構成)]** 設定を開くには、SAP インターフェイスで、プレフィックスが **/n** の **sm59** トランザクション コード (T コード) を使用します。

1. **[TCP/IP Connections]\(TCP/IP 接続\)**  >  **[Create]\(作成\)** の順に選択します。

1. 次の設定を使用して新しい RFC 宛先を作成します。

    1. **[RFC Destination]\(RFC 宛先\)** に名前を入力します。

    1. **[Technical Settings]\(技術設定\)** タブの **[Activation Type]\(アクティブ化の種類\)** で、 **[Registered Server Program]\(登録済みサーバー プログラム\)** を選択します。

    1. **[Program ID]\(プログラム ID\)** に値を入力します。 SAP では、この識別子を使用してロジック アプリ ワークフローのトリガーが登録されます。

       > [!IMPORTANT]
       > SAP の **プログラム ID** では大文字と小文字が区別されます。 ロジック アプリ ワークフローと SAP サーバーを構成するときに、**プログラム ID** に同じ大文字と小文字の形式を一貫して使用していることを確認してください。 そうしないと、IDoc を SAP に送信しようとしたときに、tRFC Monitor (T コード SM58) に次のエラーが表示されることがあります。
       >
       > * **関数 IDOC_INBOUND_ASYNCHRONOUS が見つかりません**
       > * **非 ABAP RFC クライアント (パートナー タイプ) はサポートされていません**
       >
       > SAP の詳しい情報は、以下のノートを参照してください (ログインが必要です)。
       >
       > * [https://launchpad.support.sap.com/#/notes/2399329](https://launchpad.support.sap.com/#/notes/2399329)
       > * [https://launchpad.support.sap.com/#/notes/353597](https://launchpad.support.sap.com/#/notes/353597)

    1. **[Unicode]** タブの **[Communication Type with Target System]\(ターゲット システムとの通信の種類\)** で、 **[Unicode]** を選択します。

       > [!NOTE]
       > SAP .NET クライアント ライブラリでは、Unicode 文字エンコードのみをサポートしています。 SAP から Azure Logic Apps に IDoc を送信するときにエラー `Non-ABAP RFC client (partner type ) not supported` が発生する場合は、 **[Communication Type with Target System]** の値が **Unicode** に設定されていることを確認してください。

1. 変更を保存します。

1. 新しい **プログラム ID** を Azure Logic Apps に登録するには、 **[When a message is received from SAP (SAP からのメッセージを受信した場合)]** という SAP トリガーで始まるロジック アプリ ワークフローを作成します。

   このようにして、ワークフローを保存すると、Azure Logic Apps はSAP ゲートウェイに **プログラム ID** を登録します。

1. ワークフローのトリガー履歴、オンプレミス データ ゲートウェイの SAP アダプター ログ、SAP ゲートウェイ トレース ログで、登録状態を確認します。 [SAP Gateway monitor (SAP ゲートウェイ モニター)] ダイアログ ボックス (T コード SMGW) で、**Logged-On Clients** の下に、**Registered Server** として新しい登録が表示されます。

1. 接続をテストするには、SAP インターフェイスの新しい **RFC 宛先** で、 **[Connection Test]\(接続テスト\)** を選択します。

#### <a name="create-abap-connection"></a>ABAP 接続を作成する

1. **[Configuration of RFC Connections (RFC 接続の構成)]** 設定を開くには、SAP インターフェイスで、プレフィックスが */n* の **sm59** トランザクション コード (T コード) を使用します。

1. **[ABAP Connections]\(ABAP 接続\)**  >  **[Create]\(作成\)** の順に選択します。

1. **[RFC Destination]\(RFC 宛先\)** に、[テスト用 SAP システム](#create-rfc-destination)の識別子を入力します。

1. 変更を保存します。

1. 接続をテストするには、 **[Connection Test]\(接続テスト\)** を選択します。

#### <a name="create-receiver-port"></a>受信側ポートを作成する

1. **[Ports In IDOC processing (IDOC 処理のポート)]** 設定を開くには、SAP インターフェイスで、 **/n** プレフィックスを付けた **we21** トランザクション コード (T コード) を使用します。

1. **[Ports]\(ポート\)**  >  **[Transactional RFC]\(トランザクション RFC\)**  >  **[Create]\(作成\)** の順に選択します。

1. 表示された設定ボックスで、 **[own port name]\(独自のポート名\)** を選択します。 テスト ポートの **名前** を入力します。 変更を保存します。

1. 新しい受信側ポートの設定で、 **[RFC destination]\(RFC 宛先\)** に [テスト用 RFC 宛先](#create-rfc-destination)の識別子を入力します。

1. 変更を保存します。

#### <a name="create-sender-port"></a>送信側ポートを作成する

1. **[Ports In IDOC processing (IDOC 処理のポート)]** 設定を開くには、SAP インターフェイスで、 **/n** プレフィックスを付けた **we21** トランザクション コード (T コード) を使用します。

1. **[Ports]\(ポート\)**  >  **[Transactional RFC]\(トランザクション RFC\)**  >  **[Create]\(作成\)** の順に選択します。

1. 表示された設定ボックスで、 **[own port name]\(独自のポート名\)** を選択します。 テスト ポートについて、**SAP** で始まる **名前** を入力します。 送信側ポート名はすべて、**SAP** という文字で始まる必要があります (例: **SAPTEST**)。 変更を保存します。

1. 新しい送信側ポートの設定で、 **[RFC destination]\(RFC 宛先\)** に [ABAP 接続](#create-abap-connection)の識別子を入力します。

1. 変更を保存します。

#### <a name="create-logical-system-partner"></a>論理システム パートナーを作成する

1. **[Change View 「Logical Systems」: Overview (「論理システム」ビューの変更: 概要)]** 設定を開くには、SAP インターフェイスで、**bd54** トランザクション コード (T コード) を使用します。

1. 表示される警告メッセージを受け入れます。 **[Caution: The table is cross-client]\(注意: このテーブルはクロスクライアントです\)**

1. 既存の論理システムを示す一覧の上にある **[New Entries]\(新規エントリ\)** を選択します。

1. 新しい論理システムについて、**Log.System** 識別子と **名前** の簡単な説明を入力します。 変更を保存します。

1. **[Prompt for Workbench]\(ワークベンチのプロンプト\)** が表示されたら、説明を入力して新しい要求を作成します。要求を既に作成している場合は、この手順をスキップします。

1. ワークベンチ要求を作成したら、その要求をテーブル更新要求にリンクします。 テーブルが更新されたことを確認するために、変更を保存します。

#### <a name="create-partner-profiles"></a>パートナー プロファイルを作成する

運用環境では、2 つのパートナー プロファイルを作成する必要があります。 1 つ目のプロファイルは、送信側 (組織および SAP システム) 用です。 2 つ目のプロファイルは、受信側 (ロジック アプリ) 用です。

1. **[Partner profiles (パートナー プロファイル)]** 設定を開くには、SAP インターフェイスで、 **/n** プレフィックスを付けた **we20** トランザクション コード (T コード) を使用します。

1. **[Partner Profiles]\(パートナー プロファイル\)** で、 **[Partner Type LS]\(パートナー タイプ LS\)**  >  **[Create]\(作成\)** の順に選択します。

1. 次の設定を使用して新しいパートナー プロファイルを作成します。

    * **[Partner No.]\(パートナー番号\)** に、[論理システム パートナーの識別子](#create-logical-system-partner)を入力します。

    * **[Partn.Type]\(パートナー タイプ\)** に、「**LS**」と入力します。

    * **[Agent]\(エージェント\)** に、Azure Logic Apps または SAP ではない他のシステムのプログラム識別子を登録するときに使用する SAP ユーザー アカウントの識別子を入力します。

1. 変更を保存します。 [論理システム パートナーを作成](#create-logical-system-partner)していない場合、 **[Enter a valid partner number]\(有効なパートナー番号を入力してください\)** というエラーが表示されます。

1. パートナー プロファイルの設定の **[Outbound parmtrs.]\(送信パラメーター\)** で、 **[Create outbound parameter]\(送信パラメーターの作成\)** を選択します。

1. 次の設定を使用して新しい送信パラメーターを作成します。

    * **メッセージの種類** を入力します (例: **CREMAS**)。

    * [受信側ポートの識別子](#create-receiver-port)を入力します。

    * **[Pack. Size]\(パッケージ サイズ\)** に IDoc のサイズを入力します。 [SAP から IDoc を 1 つずつ送信する](#receive-idoc-packets-from-sap)場合は、 **[Pass IDoc Immediately]\(IDoc をすぐに渡す\)** を選択します。

1. 変更を保存します。

#### <a name="test-sending-messages"></a>メッセージの送信をテストする

1. **[Test Tool for IDoc Processing (IDoc 処理のテスト ツール)]** 設定を開くには、SAP インターフェイスで、 **/n** プレフィックスを付けた **we19** トランザクション コード (T コード) を使用します。

1. **[Template for test]\(テスト用テンプレート\)** で、 **[Via message type]\(メッセージの種類を使用\)** を選択し、メッセージの種類 (例: **CREMAS**) を入力します。 **［作成］** を選択します

1. **[Continue]\(続行\)** を選択して、 **[Which IDoc type?]\(どの IDoc タイプですか?\)** というメッセージを確認します。

1. **EDIDC** ノードを選択します。 受信側と送信側のポートの適切な値を入力します。 **[続行]** をクリックします。

1. **[Standard Outbound Processing]\(標準の送信処理\)** を選択します。

1. 送信 IDoc 処理を開始するには、 **[Continue]\(続行\)** を選択します。 処理が完了すると、 **"IDoc sent to SAP system or external program" (SAP システムまたは外部プログラムに IDoc が送信されました)** というメッセージが表示されます。

1. 処理エラーがないかどうかを確認するには、 **/n** プレフィックスを付けた **sm58** トランザクション コード (T コード) を使用します。

## <a name="receive-idoc-packets-from-sap"></a>SAP から IDoc パケットを受信する

[IDoc をパケットで送信する](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/4ab38886549a6d8ce10000000a42189c.html)ように SAP を設定することができます。パケットは、IDoc のバッチまたはグループです。 IDoc パケットを受信するには、SAP コネクタ、特にトリガーでは、追加の構成は必要ありません。 ただし、トリガーがパケットを受信した後に IDoc パケット内の各項目を処理するには、パケットを個々の IDoc に分割するためにいくつかの追加の手順が必要になります。

次の例は、[`xpath()` 関数](./workflow-definition-language-functions-reference.md#xpath)を使用してパケットから個々の IDoc を抽出する方法を示しています。

1. 開始する前に、SAP トリガーを使用したロジック アプリ ワークフローが必要です。 ロジック アプリ ワークフローにこのトリガーがまだない場合は、このトピックの前の手順に従って、[SAP トリガーを使用したロジック アプリ ワークフローを設定](#receive-message-from-sap)します。

   > [!IMPORTANT]
   > SAP の **プログラム ID** では大文字と小文字が区別されます。 ロジック アプリ ワークフローと SAP サーバーを構成するときに、**プログラム ID** に同じ大文字と小文字の形式を一貫して使用していることを確認してください。 そうしないと、IDoc を SAP に送信しようとしたときに、tRFC Monitor (T コード SM58) に次のエラーが表示されることがあります。
   >
   > * **関数 IDOC_INBOUND_ASYNCHRONOUS が見つかりません**
   > * **非 ABAP RFC クライアント (パートナー タイプ) はサポートされていません**
   >
   > SAP からの詳しい情報は、以下のノート (要ログイン) [https://launchpad.support.sap.com/#/notes/2399329](https://launchpad.support.sap.com/#/notes/2399329) と [https://launchpad.support.sap.com/#/notes/353597](https://launchpad.support.sap.com/#/notes/353597) を確認してください。

   次に例を示します。

   ![SAP トリガーをロジック アプリ ワークフローに追加する方法を示すスクリーンショット。](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. SAP 要求の状態をすぐに返信する[応答アクションをロジック アプリ ワークフローに追加](../connectors/connectors-native-reqres.md#add-a-response-action)します。 SAP サーバーとの通信チャネルを解放するために、このアクションはトリガーの直後に追加することをお勧めします。 次の状態コード (`statusCode`) のうち応答アクションで使用するものを選択します。

    * **202 Accepted**。これは、要求が処理に対して承認されたものの、処理はまだ完了していないことを意味します。

    * **204 No Content**。これは、サーバーで要求が正常に処理され、送信する追加のコンテンツが応答ペイロード本文にないことを意味します。 

    * **200 OK**。 サーバーで長さゼロのペイロード本文が生成された場合でも、この状態コードには常にペイロードが含まれます。 

1. ご自身のロジック アプリ ワークフローで SAP から受け取る XML IDoc からルート名前空間を取得します。 この名前空間を XML ドキュメントから抽出するには、`xpath()` 式を使用して、ローカル文字列変数を作成してその名前空間を格納するステップを追加します。

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![IDoc からルート名前空間を取得する方法を示すスクリーンショット。](./media/logic-apps-using-sap-connector/get-namespace.png)

1. 個々の IDoc を抽出するには、別の `xpath()` 式を使用して、配列変数を作成して IDoc コレクションを格納するステップを追加します。

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![項目の配列を取得する方法を示すスクリーンショット。](./media/logic-apps-using-sap-connector/get-array.png)

   配列変数を使用すると、コレクションを列挙することによって、各 IDoc をご自身のロジック アプリ ワークフローで個別に処理できるようになります。 この例では、ループを使用して、ロジック アプリ ワークフローで各 IDoc を SFTP サーバーに転送します。

   ![IDoc を SFTP サーバーに送信する方法を示すスクリーンショット。](./media/logic-apps-using-sap-connector/loop-batch.png)

   各 IDoc にはルート名前空間が含まれている必要があります。これは、IDoc をダウンストリーム アプリ (この例では SFTP サーバー) に送信する前に、ファイルの内容がルート名前空間と共に `<Receive></Receive>` 要素内にラップされる理由です。

新しいロジック アプリ ワークフローを作成するときに、ワークフロー デザイナーでこのテンプレートを選択することで、このパターンのクイックスタート テンプレートを使用できます。

![バッチ ロジック アプリ テンプレートの選択を示すスクリーンショット。](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>SAP でアーティファクトのスキーマを生成する

この例では、HTTP 要求でトリガーすることのできるロジック アプリ ワークフローを使用します。 指定された IDoc および BAPI のスキーマを生成するために、"**スキーマを生成する**" SAP アクションによって要求が SAP システムに送信されます。

この SAP アクションでは、XML ドキュメント自体のコンテンツやデータではなく、[XML スキーマ](#sample-xml-schemas)が返されます。 応答で返されたスキーマは、Azure Resource Manager コネクタを使用して統合アカウントにアップロードされます。 スキーマには次の部分が含まれます。

* 要求メッセージの構造。 この情報を使用して、BAPI `get` リストを作成します。

* 応答メッセージの構造。 この情報を使用して応答を解析します。

要求メッセージを送信するには、汎用 SAP アクションである **[SAP にメッセージを送信する]** 、またはターゲットとなる **[\[BAPI] SAP でのメソッドの呼び出し]** アクションを使用します。

### <a name="sample-xml-schemas"></a>XML スキーマのサンプル

サンプル ドキュメントの作成で使用する XML スキーマの生成方法を学習する場合は、次のサンプルを参照してください。 これらの例では、次のようなさまざまな種類のペイロードを操作する方法を示します。

* [RFC 要求](#xml-samples-for-rfc-requests)

* [BAPI 要求](#xml-samples-for-bapi-requests)

* [IDoc 要求](#xml-samples-for-idoc-requests)

* 単純または複合の XML スキーマ データ型

* テーブル パラメーター

* オプションの XML 動作

省略可能な XML プロローグで XML スキーマを始めることができます。 SAP コネクタは、XML プロローグの有無に関係なく動作します。

```xml
<?xml version="1.0" encoding="utf-8">
```

#### <a name="xml-samples-for-rfc-requests"></a>RFC 要求の XML サンプル

次に示すのは、基本的な RFC 呼び出しの例です。 RFC 名は `STFC_CONNECTION` です。 この要求では既定の名前空間 `xmlns=` を使用しますが、`xmmlns:exampleAlias=` のような名前空間の別名を割り当てて使用することもできます。 この名前空間の値は、SAP における Microsoft サービス用のすべての RFC に対する名前空間です。 要求には、簡単な入力パラメーター `<REQUTEXT>` があります。

```xml
<STFC_CONNECTION xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
   <REQUTEXT>exampleInput</REQUTEXT>
</STFC_CONNECTION>
```

次に示すのは、テーブル パラメーターを使用した RFC 呼び出しの例です。 この例の呼び出しと、テスト RFC のグループは、すべての SAP システムの一部として利用できます。 テーブル パラメーターの名前は `TCPICDAT` です。 テーブルの行の種類は `ABAPTEXT` であり、テーブルの行ごとにこの要素を繰り返します。 この例には、`LINE` という名前の 1 つの行が含まれています。 テーブル パラメーターを使用する要求には、任意の数のフィールドを含めることができ、値は正の整数 (*n*) です。 

```xml
<STFC_WRITE_TO_TCPIC xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
   <RESTART_QNAME>exampleQName</RESTART_QNAME>
   <TCPICDAT>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
         <LINE>exampleFieldInput1</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
         <LINE>exampleFieldInput2</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
         <LINE>exampleFieldInput3</LINE>
      </ABAPTEXT>
   </TCPICDAT>
</STFC_WRITE_TO_TCPIC>
```

> [!NOTE]
> SAP ログオンのデータ ブラウザーを使用して、RFC **STFC_WRITE_TO_TCPIC** の結果を確認します (T-Code SE16)。テーブル名 **TCPIC** を使用します。

次に示すのは、匿名フィールドが存在するテーブル パラメーターを使用した RFC 呼び出しの例です。 匿名フィールドは、フィールドに名前が割り当てられていない場合です。 複合型は別の名前空間で宣言され、その宣言では、現在のノードとそのすべての子要素に対する新しい既定値を設定します。 この例では、記号 */* に対するエスケープ文字として 16 進コード `x002F` が使用されています。これは、この記号が SAP フィールド名で予約されているためです。

```xml
<RFC_XML_TEST_1 xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
   <IM_XML_TABLE>
      <RFC_XMLCNT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
         <_x002F_AnonymousField>exampleFieldInput</_x002F_AnonymousField>
      </RFC_XMLCNT>
   </IM_XML_TABLE>
</RFC_XML_TEST_1>
```

次の例には、名前空間に対するプレフィックスが含まれています。 すべてのプレフィックスを一度に宣言することも、ノードの属性として任意の数のプレフィックスを宣言することもできます。 RFC 名前空間の別名 `ns0` は、基本型のルートおよびパラメーターとして使用されます。

> [!NOTE]
> 複合型は、別名が `ns0` である通常の RFC 名前空間ではなく、別名が `ns3` である RFC 型用の別の名前空間で宣言されます。

```xml
<ns0:BBP_RFC_READ_TABLE xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Rfc/" xmlns:ns3="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc/">
   <ns0:DELIMITER>0</ns0:DELIMITER>
   <ns0:QUERY_TABLE>KNA1</ns0:QUERY_TABLE>
   <ns0:ROWCOUNT>250</ns0:ROWCOUNT>
   <ns0:ROWSKIPS>0</ns0:ROWSKIPS>
   <ns0:FIELDS>
      <ns3:RFC_DB_FLD>
         <ns3:FIELDNAME>KUNNR</ns3:FIELDNAME>
      </ns3:RFC_DB_FLD>
   </ns0:FIELDS>
</ns0:BBP_RFC_READ_TABLE>
```

#### <a name="xml-samples-for-bapi-requests"></a>BAPI 要求の XML サンプル

次の XML サンプルは、[BAPI メソッドを呼び出す](#actions)要求の例です。

> [!NOTE]
> SAP では、Azure Logic Apps によって入力フィルターなしで発行される RFC `RPY_BOR_TREE_INIT` に対する応答においてビジネス オブジェクトを記述することにより、外部システムでそれを使用できるようになります。 Logic Apps により、出力テーブル `BOR_TREE` が検査されます。 `SHORT_TEXT` フィールドは、ビジネス オブジェクトの名前に使用されます。 出力テーブルで SAP から返されないビジネス オブジェクトでは、Azure Logic Apps にアクセスできません。
> 
> カスタム ビジネス オブジェクトを使用する場合は、SAP でこれらのビジネス オブジェクトを公開してリリースする必要があります。 そうしないと、SAP の出力テーブル `BOR_TREE` にカスタム ビジネス オブジェクトが表記されません。 SAP からビジネス オブジェクトを公開するまで、Logic Apps でカスタム ビジネス オブジェクトにアクセスすることはできません。 

次の例では、BAPI メソッド `GETLIST` を使用して、銀行の一覧を取得します。 このサンプルには、銀行のビジネス オブジェクト `BUS1011` が含まれています。

```xml
<GETLIST xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
   <BANK_CTRY>US</BANK_CTRY>
   <MAX_ROWS>10</MAX_ROWS>
</GETLIST>
```

次の例では、`CREATE` メソッドを使用して銀行オブジェクトを作成します。 この例では、前の例と同じビジネス オブジェクト `BUS1011` を使用します。 `CREATE` メソッドを使用して銀行を作成するとき、このメソッドではコミットは既定では行われないため、変更を必ずコミットしてください。

> [!TIP]
> SAP システムで構成されている検証規則に XML ドキュメントが従っていることを確認します。 たとえば、このサンプル ドキュメントの銀行キー (`<BANK_KEY>`) には、米国では ABA 番号とも呼ばれる銀行支店コードを使用する必要があります。

```xml
<CREATE xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_ADDRESS>
    <BANK_NAME xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleBankName</BANK_NAME>
    <REGION xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleRegionName</REGION>
    <STREET xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleStreetAddress</STREET>
    <CITY xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">Redmond</CITY>
  </BANK_ADDRESS>
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</CREATE>
```

次の例では、銀行支店コード (`<BANK_KEY>` の値) を使用して、銀行の詳細を取得します。 

```xml
<GETDETAIL xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</GETDETAIL>
```

#### <a name="xml-samples-for-idoc-requests"></a>IDoc 要求の XML サンプル

単純な SAP IDoc XML スキーマを生成するには、**SAP Logon** アプリケーションと T コード `WE-60` を使用します。 GUI を使用して SAP ドキュメントにアクセスし、IDoc の種類と拡張機能に対する XML スキーマを XSD 形式で生成します。 汎用 SAP 形式とペイロードと、それらの組み込みダイアログの詳細については、[SAP のドキュメント](https://help.sap.com/viewer/index)を参照してください。

この例では、ルート ノードと名前空間を宣言します。 サンプル コードの URI `http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send` では、次の構成が宣言されています。

* `/IDoc` は、すべての IDoc に対するルート ノードです。

* `/3` は、共通セグメント定義に対するレコードの種類のバージョンです。

* `/ORDERS05` は、IDoc の種類です。

* `//` は、IDoc 拡張機能がないため、空のセグメントです。

* `/700` は、SAP のバージョンです。

* `/Send` は、情報を SAP に送信するためのアクションです。

```xml
<ns0:Send xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send" xmlns:ns3="http://schemas.microsoft.com/2003/10/Serialization" xmlns:ns1="http://Microsoft.LobServices.Sap/2007/03/Types/Idoc/Common/" xmlns:ns2="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700">
   <ns0:idocData>
```

`idocData` ノードを繰り返して、1 回の呼び出しで IDoc のバッチを送信できます。 次の例には、1 つのコントロール レコード `EDI_DC40` と、複数のデータ レコードがあります。

```xml
<...>
  <ns0:idocData>
    <ns2:EDI_DC40>
      <ns1:TABNAM>EDI_DC40</ns1:TABNAM>
      <...>
      <ns1:ARCKEY>Cor1908207-5</ns1:ARCKEY>
    </ns2:EDI_DC40>
    <ns2:E2EDK01005>
      <ns2:DATAHEADERCOLUMN_SEGNAM>E23DK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:CURCY>USD</ns2:CURCY>
    </ns2:E2EDK01005>
    <ns2:E2EDK03>
    <...>
  </ns0:idocData>
```

次の例は IDoc コントロール レコードのサンプルであり、プレフィックス `EDI_DC` が使用されています。 SAP の実際のインストールと IDoc の種類に合わせて、値を更新する必要があります。 たとえば、IDoc クライアント コードは `800` ではない可能性があります。 SAP チームに連絡し、お使いの SAP インストールに適した値を使用していることを確認してください。

```xml
<ns2:EDI_DC40>
  <ns:TABNAM>EDI_DC40</ns1:TABNAM>
  <ns:MANDT>800</ns1:MANDT>
  <ns:DIRECT>2</ns1:DIRECT>
  <ns:IDOCTYP>ORDERS05</ns1:IDOCTYP>
  <ns:CIMTYP></ns1:CIMTYP>
  <ns:MESTYP>ORDERS</ns1:MESTYP>
  <ns:STD>X</ns1:STD>
  <ns:STDVRS>004010</ns1:STDVRS>
  <ns:STDMES></ns1:STDMES>
  <ns:SNDPOR>SAPENI</ns1:SNDPOR>
  <ns:SNDPRT>LS</ns1:SNDPRT>
  <ns:SNDPFC>AG</ns1:SNDPFC>
  <ns:SNDPRN>ABAP1PXP1</ns1:SNDPRN>
  <ns:SNDLAD></ns1:SNDLAD>
  <ns:RCVPOR>BTSFILE</ns1:RCVPOR>
  <ns:RCVPRT>LI</ns1:RCVPRT>
```

次の例は、単純なセグメントを含むサンプル データ レコードです。 この例では、SAP 日付形式が使用されています。 厳密に型指定されたドキュメントでは、`2020-12-31 23:59:59` などのネイティブな XML 日付形式を使用できます。

```xml
<ns2:E2EDK01005>
  <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
    <ns2:CURCY>USD</ns2:CURCY>
    <ns2:BSART>OR</ns2:BSART>
    <ns2:BELNR>1908207-5</ns2:BELNR>
    <ns2:ABLAD>CC</ns2:ABLAD>
  </ns2>
  <ns2:E2EDK03>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK03</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:IDDAT>002</ns2:IDDAT>
      <ns2:DATUM>20160611</ns2:DATUM>
  </ns2:E2EDK03>
```

次の例は、グループ化されたセグメントを含むデータ レコードです。 このレコードには、グループの親ノード `E2EDKT1002GRP` と、`E2EDKT1002` や `E2EDKT2001` などの複数の子ノードが含まれます。 

```xml
<ns2:E2EDKT1002GRP>
  <ns2:E2EDKT1002>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT1002</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDID>ZONE</ns2:TDID>
  </ns2:E2EDKT1002>
  <ns2:E2EDKT2001>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT2001</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDLINE>CRSD</ns2:TDLINE>
  </ns2:E2EDKT2001>
</ns2:E2EDKT1002GRP>
```

推奨される方法は、tRFC で使用する IDoc 識別子を作成することです。 SAP コネクタ API の [Send IDoc 操作](/connectors/sap/#send-idoc)を使用して、このトランザクション識別子 `tid` を設定できます。

次の例は、トランザクション識別子 `tid` を設定する別の方法です。 この例では、最後のデータ レコード セグメント ノードと IDoc データ ノードが閉じられます。 その後、GUID `guid` が、重複を検出するための tRFC 識別子として使用されます。

```xml
     </E2STZUM002GRP>
  </idocData>
  <guid>8820ea40-5825-4b2f-ac3c-b83adc34321c</guid>
</Send>
```

### <a name="add-the-request-trigger"></a>要求トリガーを追加する

1. Azure portal で空のロジック アプリを作成し、ワークフロー デザイナーを開きます。

1. 検索ボックスに、フィルターとして「`http request`」と入力します。 **[トリガー]** の一覧から、 **[HTTP 要求の受信時]** を選択します。

   ![要求トリガーの追加を示すスクリーンショット。](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. ロジック アプリ ワークフローに使用するエンドポイントの URL を生成するために、ここでロジック アプリを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

   次のように、エンドポイントの URL がトリガーに表示されます。

   ![エンドポイント URL の生成を示すスクリーンショット。](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>スキーマを生成する SAP アクションを追加する

1. ワークフロー デザイナーのトリガーで、 **[New step (新しいステップ)]** を選択します。

   ![ロジック アプリ ワークフローへの新しいステップの追加を示すスクリーンショット。](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. 検索ボックスに、フィルターとして「`generate schemas sap`」と入力します。 **[アクション]** の一覧から **[スキーマの生成]** を選択します。
  
   ![ワークフローへの「スキーマの生成」アクションの追加を示すスクリーンショット。](./media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   または、 **[Enterprise]** タブを選択し、SAP アクションを選択します。

   ![[Enterprise] タブから「スキーマの生成」アクションを選択するスクリーンショット。](./media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. 接続が既に存在する場合は、次の手順に進んで SAP アクションを設定します。 ただし、接続の詳細を確認するメッセージが表示されたら、オンプレミスの SAP サーバーへの接続を今すぐ作成できるように情報を提供します。

   1. 接続の名前を入力します。

   1. **[データ ゲートウェイ]** セクションの **[サブスクリプション]** で、まず、データ ゲートウェイのインストールのために Azure portal で作成したデータ ゲートウェイ リソースの Azure サブスクリプションを選択します。 

   1. **[接続ゲートウェイ]** で、Azure のデータ ゲートウェイ リソースを選択します。

   1. 接続に関する情報を引き続き入力します。 **[ログオンの種類]** プロパティで、プロパティが **[アプリケーション サーバー]** または **[グループ]** のどちらに設定されているかに基づいて、手順に従います。

      * **[アプリケーション サーバー]** の場合、通常は任意として表示される次のプロパティが必須になります。

        ![SAP アプリケーション サーバーでの接続の作成を示すスクリーンショット](./media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * **[グループ]** の場合、通常は任意として表示される次のプロパティが必須になります。

        ![SAP メッセージ サーバーでの接続の作成を示すスクリーンショット](./media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

   1. 完了したら、 **[作成]** をクリックします。

      Azure Logic Apps により、接続の設定とテストが行われ、適切に機能していることが確認されます。

1. スキーマを生成するアーティファクトのパスを指定します。

   SAP アクションはファイル ピッカーから選択できます。

   ![SAP アクションの選択を示すスクリーンショット。](./media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   あるいは、アクションを手動で入力できます。

   ![SAP アクションの手動での入力を示すスクリーンショット。](./media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   複数のアーティファクトにスキーマを生成するには、次のようにアーティファクトごとに SAP アクション詳細を指定します。

   ![[Add new item (新規項目の追加)] の選択を示すスクリーンショット。](./media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![2 つの項目を示すスクリーンショット。](./media/logic-apps-using-sap-connector/schema-generator-example.png)

   SAP アクションの詳細については、「[IDoc 操作のメッセージ スキーマ](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)」を参照してください。

1. ロジック アプリ ワークフローを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

既定では、厳密な型指定は、スキーマに照らした XML 検証を実行することによって無効な値をチェックするために使用します。 この動作により、問題を初期段階で検出できます。 **[安全な型指定]** オプションは、旧バージョンとの互換性のために使用でき、文字列の長さのみをチェックします。 詳しくは、[[安全な型指定] オプション](#safe-typing)に関する記事をご覧ください。

### <a name="test-your-workflow"></a>ワークフローのテスト

1. デザイナーのツール バーで **[実行]** を選択し、ロジック アプリ ワークフローの実行をトリガーします。

1. 実行を開き、 **[スキーマの生成]** アクションの出力を確認します。

   出力には、指定のメッセージ リストに対して生成されたスキーマが表示されます。

### <a name="upload-schemas-to-an-integration-account"></a>統合アカウントにスキーマをアップロードする

任意で、BLOB、ストレージ、統合アカウントなど、リポジトリにある生成済みのスキーマをダウンロードしたり、保存したりできます。 統合アカウントの場合、他の XML アクションでこの上ない操作性が与えられます。そこで、この例では、Azure Resource Manager コネクタを使用し、同じロジック アプリ ワークフロー用の統合アカウントにスキーマをアップロードする方法を示します。

1. ワークフロー デザイナーのトリガーで、 **[New step (新しいステップ)]** を選択します。

1. 検索ボックスに、フィルターとして「`resource manager`」と入力します。 **[Create or update a resource]\(リソースの作成または更新\)** を選択します。

   ![Azure Resource Manager アクションの選択を示すスクリーンショット。](./media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Azure サブスクリプション、Azure リソース グループ、統合アカウントなど、アクションの詳細を入力します。 フィールドに SAP トークンを追加するには、これらのフィールドのボックス内をクリックし、表示される動的コンテンツ リストから選択します。

   1. **[新しいパラメーターの追加]** リストを開き、 **[Location]\(場所\)** および **[Properties]\(プロパティ\)** フィールドを選択します。

   1. 次の例で示すように、これらの新しいフィールドに詳細情報を指定します。

      ![Azure Resource Manager アクションの詳細な入力を示すスクリーンショット。](./media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   SAP の **スキーマ生成** アクションにより、スキーマがコレクションとして生成されます。そのため、デザイナーによって **For each** ループがアクションに自動的に追加されます。 このアクションは次の例のように表示されます。

   ![「for each」ループを含む Azure Resource Manager アクションを示すスクリーンショット。](./media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > スキーマでは base64 でコード化された形式が使用されます。 統合アカウントにスキーマをアップロードするには、`base64ToString()` 関数で複合する必要があります。 `"properties"` 要素のコードは次の例のようになります。
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. ロジック アプリ ワークフローを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

### <a name="test-your-workflow"></a>ワークフローのテスト

1. デザイナーのツール バーで **[実行]** を選択し、ロジック アプリ ワークフローを手動でトリガーします。

1. 実行が成功したら、統合アカウントに移動し、生成されたスキーマが存在することを確認します。

<a name="enable-secure-network-communications"></a>

## <a name="enable-secure-network-communications-snc"></a>Secure Network Communications (SNC) を有効にする

開始する前に、前述の[前提条件](#prerequisites)を満たしていることを確認してください。これは、データ ゲートウェイを使用していて、ロジック アプリ ワークフローがマルチテナント Azure で実行されている場合にのみ適用されます。

* オンプレミス データ ゲートウェイが、ご利用の SAP システムと同じネットワーク内にあるコンピューターにインストールされていること。

* SSO のために、データ ゲートウェイが、SAP ユーザーにマップされたユーザーとして実行されている。

* 追加のセキュリティ機能を提供する SNC ライブラリが、データ ゲートウェイと同じコンピューターにインストールされている。 例をいくつか挙げると、[sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)、Kerberos、NTLM などがあります。

   SAP システムとの間でやりとりされる要求に対して SNC を有効にするには、SAP 接続の **[SNC を使用する]** チェックボックスを選択し、次のプロパティを指定します。

   ![SAP 接続での SNC の設定を示すスクリーンショット。](./media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | プロパティ | 説明 |
   |----------| ------------|
   | **SNC Library Path (SNC ライブラリ パス)** | SNC ライブラリ名、または NCo インストール場所の相対パス、または絶対パス 例として、`sapsnc.dll`、`.\security\sapsnc.dll`、または `c:\security\sapsnc.dll` があります。 |
   | **SNC SSO** | SNC 経由で接続する場合、SNC ID は、通常、呼び出し元を認証するために使用されます。 もう 1 つのオプションは、ユーザーおよびパスワード情報を使用して呼び出し元を認証できるようにオーバーライドすることです。ただし、行は引き続き暗号化されます。 |
   | **SNC の自分の名前** | ほとんどの場合、このプロパティは省略できます。 インストールされている SNC ソリューションは、通常、独自の SNC 名を認識しています。 複数の ID をサポートするソリューションの場合のみ、この特定の宛先またはサーバーに使用される ID の指定が必要になる場合があります。 |
   | **SNC パートナー名** | バックエンド SNC の名前。 |
   | **保護の SNC 品質** | この特定の宛先またはサーバーの SNC 通信に使用されるサービスの品質。 既定値は、バックエンド システムによって定義されます。 最大値は、SNC のために使用されるセキュリティ製品によって定義されます。 |
   |||

   > [!NOTE]
   > データ ゲートウェイと SNC ライブラリが置かれているコンピューター上で、環境変数 SNC_LIB および SNC_LIB_64 は設定しないでください。 設定した場合、それらが、コネクタを介して渡される SNC ライブラリ値よりも優先されます。

## <a name="safe-typing"></a>安全な型指定

既定では、SAP 接続を作成するときに、スキーマに照らした XML 検証を実行することによって無効な値をチェックするには、厳密な型指定を使用します。 この動作により、問題を初期段階で検出できます。 **[安全な型指定]** オプションは、旧バージョンとの互換性のために使用でき、文字列の長さのみをチェックします。 **[安全な型指定]** を選択すると、SAP の DATS 型と TIMS 型は、対応する XML の `xs:date` と `xs:time` (`xmlns:xs="http://www.w3.org/2001/XMLSchema"`) ではなく、文字列として扱われます。 安全な型指定は、すべてのスキーマ生成、"送信済み" ペイロードと "受信済み" 応答の両方のメッセージ送信、およびトリガーの動作に影響します。

厳密な型指定を使用すると ( **[安全な型指定]** が有効になっていない場合)、スキーマにより DATS 型と TIMS 型がよりわかりやすい XML 型にマップされます。

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

厳密な型指定を使用してメッセージを送信する場合、DATS と TIMS 応答は一致する XML 型の形式に準拠します。

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

**[安全な型指定]** が有効になっている場合は、スキーマにより、DATS 型と TIMS 型は、長さだけ制限された XML 文字列フィールドにマップされます。次に例を示します。

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

**[安全な型指定]** が有効になっている状態でメッセージを送信した場合、DATS と TIMS の応答は次の例のようになります。

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="send-sap-telemetry-foron-premises-data-gateway-to-azure-application-insights"></a>オンプレミス データ ゲートウェイの SAP テレメトリを Azure Application Insights に送信する

オンプレミス データ ゲートウェイの 2021 年 8 月の更新により、SAP コネクタの操作で、SAP クライアント ライブラリからテレメトリ データを送信し、Microsoft SAP アダプターから [Application Insights](../azure-monitor/app/app-insights-overview.md) にトレースすることができます。これは Azure Monitor の機能です。 このテレメトリには主に、以下のデータが含まれています。

* SAP NCo のメトリックとモニターに基づくメトリックとトレース。

* Microsoft SAP アダプターからのトレース。

### <a name="metrics-and-traces-from-sap-client-library"></a>SAP クライアント ライブラリからのメトリックとトレース

"*メトリック*" は、オンプレミス データ ゲートウェイ上のリソースの使用状況と可用性に基づいて、一定期間にわたって変化する場合も変化しない場合もある数値です。 システムの正常性について理解を深め、以下のアクティビティに関するアラートを作成するために、これらのメトリックを利用できます。

* システムの正常性が悪化しつつあるかどうか。

* 通常とは異なるイベント。

* システムに対する高い負荷。

この情報は Application Insights のテーブル `customMetrics` に送信されます。 既定では、メトリックは 30 秒間隔で送信されます。

SAP NCo のメトリックとトレースは、SAP NCo のメトリック、特に以下の NCo クラスに基づいています。

* RfcDestinationMonitor。

* RfcConnectionMonitor。

* RfcServerMonitor。

* RfcRepositoryMonitor。

各クラスで提供されるメトリックの詳細については、[SAP NCo のドキュメント (サインインが必要)](https://support.sap.com/en/product/connectors/msnet.html#section_512604546) を確認してください。

"*トレース*" には、メトリックと共に使用されるテキスト情報が含まれています。 この情報は、`traces` という名前の Application Insights のテーブルに送信されます。 既定では、トレースは 10 分間隔で送信されます。

### <a name="set-up-sap-telemetry-for-application-insights"></a>Application Insights 用の SAP テレメトリを設定する

ゲートウェイ インストールについての SAP テレメトリを Application Insights に送信する前に、Application Insights リソースを作成して設定する必要があります。 詳細については、次のドキュメントを確認してください。

* [Application Insights リソースを作成する (クラシック)](../azure-monitor/app/create-new-resource.md)

* [ワークスペース ベースの Application Insights リソース](../azure-monitor/app/create-workspace-resource.md)

SAP テレメトリを Application insights に送信できるようにするには、以下の手順に従います。

1. **Microsoft.ApplicationInsights.EventSourceListener.dll** 用の NuGet パッケージを次の場所からダウンロードします: [https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/2.14.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/2.14.0)。

1. ダウンロードしたファイルをオンプレミス データ ゲートウェイのインストール ディレクトリに追加します。

1. オンプレミス データ ゲートウェイのインストール ディレクトリで、**Microsoft.ApplicationInsights.dll** ファイルのバージョン番号が、追加した **Microsoft.ApplicationInsights.EventSourceListener.dll** ファイルと同じであることを確認します。 ゲートウェイでは現在、バージョン 2.14.0 が使用されています。

1. **ApplicationInsights.config** ファイルで、`<InstrumentationKey></Instrumentation>` 要素が含まれる行をコメント解除して、[Application Insights インストルメンテーション キー](../azure-monitor/app/app-insights-overview.md#how-does-application-insights-work)を追加します。 プレースホルダーの <*your-Application-Insights-instrumentation-key*> を、実際のキーに置き換えます。以下に例を示します。

      ```xml
      <?xml version="1.0" encoding="utf-8"?>
      <ApplicationInsights schemaVersion="2014-05-30" xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
         <!-- Uncomment this element and insert your Application Insights key to receive ETW telemetry about your gateway <InstrumentationKey>*your-instrumentation-key-placeholder*</InstrumentationKey> -->
         <TelemetryModules>
            <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule, Microsoft.ApplicationInsights">
               <IsHeartbeatEnabled>false</IsHeartbeatEnabled>
            </Add>
            <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
               <Sources>
                  <Add Name="Microsoft-LobAdapter" Level="Verbose" />
               </Sources>
            </Add>
         </TelemetryModules>
      </ApplicationInsights>
      ```

1. **ApplicationInsights.config** ファイルでは、要件に応じて、SAP コネクタ操作の必須トレース `Level` の値を変更できます。以下に例を示します。

   ```xml
   <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
         <Add Name="Microsoft-LobAdapter" Level="Verbose" />
      </Sources>
   </Add>
   ```

   詳細については、次のドキュメントを確認してください。

   * `Level` の値: [EventLevel Enum](/dotnet/api/system.diagnostics.tracing.eventlevel)

   * [EventSource の追跡](../azure-monitor/app/configuration-with-applicationinsights-config.md#eventsource-tracking)

   * [EventSource イベント](../azure-monitor/app/asp-net-trace-logs.md#use-eventsource-events)

1. 変更を適用した後で、オンプレミス データ ゲートウェイ サービスを再起動します。

### <a name="review-metrics-in-application-insights"></a>Application Insights でメトリックを確認する

ロジック アプリ ワークフローで SAP 操作を実行したら、Application Insights に送信されたテレメトリを確認できます。

1. Azure portal で Application Insights のリソースを開きます。

1. リソース メニューで、 **[監視]** の下の **[ログ]** を選択します。

   次のスクリーンショットは、 **[ログ]** ペインが開いている Application Insights が表示された Azure portal を示しています。

   [![クエリを作成するための [ログ] ペインが開いている Application Insights が表示された Azure portal を示しているスクリーンショット。](./media/logic-apps-using-sap-connector/application-insights-query-panel.png)](./media/logic-apps-using-sap-connector/application-insights-query-panel.png#lightbox)

1. **[ログ]** ペインでは、[Kusto クエリ言語 (KQL)](/azure/data-explorer/kusto/concepts/) を使用して、特定の要件に基づく [クエリ](/azure/data-explorer/kusto/query/)を作成できます。

   次のクエリ例のようなクエリ パターンを使用できます。

   ```Kusto
   customMetrics
   | extend DestinationName = tostring(customDimensions["DestinationName"])
   | extend MetricType = tostring(customDimensions["MetricType"])
   | where customDimensions contains "RfcDestinationMonitor"
   | where name contains "MaxUsedCount"
   ```

1. クエリを実行した後は、結果を確認します。

   次のスクリーンショットは、クエリ例のメトリック結果テーブルを示しています。

   [![メトリック結果テーブルが表示された Application Insights を示すスクリーンショット。](./media/logic-apps-using-sap-connector/application-insights-metrics.png)](./media/logic-apps-using-sap-connector/application-insights-metrics.png#lightbox)

   * **MaxUsedCount** は、"監視対象によって同時に使用されたクライアント接続の最大数。" であると、 [SAP NCo のドキュメント (サインインが必要)](https://support.sap.com/en/product/connectors/msnet.html#section_512604546) で説明されています。 この値を使用して、同時に開いている接続の数を把握できます。

   * メトリックは 30 秒間隔で生成されるため、 **[valueCount]** 列には読み取りごとに "**2**" と表示されます。そしてこれらのメトリックは、Application Insights によって分単位で集計されます。

   * **[DestinationName]** 列には、Microsoft SAP アダプターの内部名である文字列が含まれています。

     このリモート関数呼び出し (RFC) の対象についてより深く理解するには、この値を `traces` と共に使用します。以下に例を示します。

     ```Kusto
     customMetrics
     | extend DestinationName = tostring(customDimensions["DestinationName"])
     | join kind=inner (traces
        | extend DestinationName = tostring(customDimensions["DestinationName"]),
        AppServerHost = tostring(customDimensions["AppServerHost"]),
        SncMode = tostring(customDimensions["SncMode"]),
        SapClient = tostring(customDimensions["Client"])
        | where customDimensions contains "RfcDestinationMonitor"
        )
        on DestinationName , $left.DestinationName == $right.DestinationName
     | where customDimensions contains "RfcDestinationMonitor"
     | where name contains "MaxUsedCount"
     | project AppServerHost, SncMode, SapClient, name, valueCount, valueSum, valueMin, valueMax
     ```

それらの Application Insights の機能を使用してメトリック グラフやアラートを作成することもできます。以下に例を示します。

[![グラフ形式で結果が表示されている Application Insights を示すスクリーンショット。](./media/logic-apps-using-sap-connector/application-insights-metrics-chart.png)](./media/logic-apps-using-sap-connector/application-insights-metrics-chart.png#lightbox)

### <a name="traces-from-microsoft-sap-adapter"></a>Microsoft SAP アダプターからのトレース

Microsoft SAP アダプターから送信されたトレースは、問題の事後分析のために利用し、SAP コネクタの操作から発生したりしなかったりする場合がある既存の内部システム エラーを見つけることができます。 これらのトレースは、Application Insights に先立つ古いイベント ソース フレームワークからのものであるため、`message` が `"n\a"` に設定されています。以下に例を示します。

```Kusto
traces
| where message == "n/a"
| where severityLevel > 0
| extend ActivityId = tostring(customDimensions["ActivityId"])
| extend fullMessage = tostring(customDimensions["fullMessage"])
| extend shortMessage = tostring(customDimensions["shortMessage"])
| where ActivityId contains "8ad5952b-371e-4d80-b355-34e28df9b5d1"
```

次のスクリーンショットは、クエリ例のトレース結果テーブルを示しています。

[![トレース結果テーブルが表示された Application Insights を示すスクリーンショット。](./media/logic-apps-using-sap-connector/application-insights-traces.png)](./media/logic-apps-using-sap-connector/application-insights-traces.png#lightbox)

## <a name="advanced-scenarios"></a>高度なシナリオ

### <a name="change-language-headers"></a>言語ヘッダーを変更する

Logic Apps から SAP に接続する場合、接続の既定の言語は英語です。 受信要求で[標準 HTTP ヘッダーの `Accept-Language`](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4) を使用して、接続の言語を設定できます。

> [!TIP]
> ほとんどの Web ブラウザーでは、ユーザーの設定に基づいて `Accept-Language` ヘッダーが追加されます。 Web ブラウザーは、ワークフロー デザイナーで新しい SAP 接続を作成するときにこのヘッダーを適用します。Web ブラウザーの設定を更新して、好みの言語を使用するか、ワークフロー デザイナーではなく Azure Resource Manager を使用して SAP 接続を作成します。

たとえば、**HTTP 要求** トリガーを使用して、`Accept-Language` ヘッダーを含む要求をロジック アプリ ワークフローに送信できます。 ロジック アプリ ワークフローのすべてのアクションがこのヘッダーを受け取ります。 次に、SAP では、BAPI エラー メッセージなどのシステム メッセージで指定された言語が使用されます。

ロジック アプリ ワークフローの SAP 接続パラメーターには言語プロパティがありません。 そのため、`Accept-Language` ヘッダーを使用すると、次のエラーが発生する場合があります。 **"Please check your account info and/or permissions and try again" (お使いのアカウントの情報やアクセス許可を確認し、もう一度お試しください)** 。 この場合、代わりに SAP コンポーネントのエラー ログを確認してください。 エラーは、実際にはこのヘッダーを使用する SAP コンポーネントで発生するため、これらのいずれかのエラー メッセージが表示される可能性があります。

* `"SAP.Middleware.Connector.RfcLogonException: Select one of the installed languages"`

* `"SAP.Middleware.Connector.RfcAbapMessageException: Select one of the installed languages"`

### <a name="confirm-transaction-explicitly"></a>トランザクションを明示的に確認する

Logic Apps から SAP にトランザクションを送信する場合、この交換は SAP ドキュメントの「[Transactional RFC Server Programs](https://help.sap.com/doc/saphelp_nwpi71/7.1/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true)」 (トランザクション RFC サーバー プログラム) で説明されている 2 つの手順で行われます。 既定では、 **[SAP に送信する]** アクションで、関数転送とトランザクション確認の両方の手順が 1 回の呼び出しで処理されます。 SAP コネクタには、これらの手順を分離するオプションが用意されています。 IDoc を送信することができます。さらに、トランザクションを自動的に確認するのではなく、明示的な **[\[IDOC] Confirm transaction ID]\([IDOC] トランザクション ID を確認する\)** アクションを使用することができます。

トランザクション ID の確認を分離するこの機能は、SAP でトランザクションを複製したくない場合に役立ちます。たとえば、ネットワークの問題などが原因でエラーが発生する可能性のあるシナリオの場合です。 トランザクション ID を個別に確認することで、トランザクションは SAP システム内で 1 回だけ完了します。

このパターンを示す例を次に示します。

1. 空のロジック アプリを作成し、要求トリガーを追加します。

1. SAP コネクタから、 **[\[IDOC] Send document to SAP]\([IDOC] SAP にドキュメントを送信する\)** アクションを追加します。 SAP システムに送信する IDoc の詳細を入力します。

1. トランザクション ID を別の手順で明示的に確認するには、 **[TID の確認]** フィールドで **[いいえ]** を選択します。 省略可能な **[トランザクション ID の GUID]** フィールドについては、手動で値を指定するか、またはコネクタで自動的に生成し、 **\[[IDOC] SAP にドキュメントを送信する** アクションからの応答でこの GUID が返されるようにできます。

   ![「[IDOC] SAP にドキュメントを送信する」アクションのプロパティを示すスクリーンショット](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. トランザクション ID を明示的に確認するには、 **[\[IDOC] Confirm transaction ID]\([IDOC] トランザクション ID を確認する\)** アクションを追加し、[重複する IDoc が SAP に送信されるのを確実に回避](#avoid-sending-duplicate-idocs)します。 **[Transaction ID]\(トランザクション ID\)** ボックス内をクリックし、動的コンテンツ リストが表示されるようにします。 そのリストから、 **[\[IDOC] Send document to SAP]\([IDOC] SAP にドキュメントを送信する\)** アクションから返された **[Transaction ID]\(トランザクション ID\)** の値を選択します。

   ![「トランザクション ID の確認」アクションを示すスクリーンショット](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   この手順が行われた後、現在のトランザクションは、SAP コネクタ側と SAP システム側の両端で完了としてマークされます。

#### <a name="avoid-sending-duplicate-idocs"></a>重複する IDoc が送信されないようにする

ロジック アプリ ワークフローから SAP に重複する IDoc が送信される問題が発生する場合は、次の手順に従って、IDoc トランザクション識別子として機能する文字列変数を作成します。 このトランザクション識別子を作成すると、一時的な停止、ネットワークの問題、受信確認の喪失などの問題が発生した場合に、重複するネットワーク転送を防ぐことができます。

> [!NOTE]
> SAP システムでは、指定された時間 (既定では 24 時間) が経過すると、トランザクション識別子がわからなくなります。 その結果、ID または GUID が不明の場合、SAP はトランザクション識別子を確認できません。
> トランザクション ID の確認が失敗する場合、このエラーは、SAP が確認を応答する前に SAP システムとの通信が失敗したことを示します。

1. ワークフロー デザイナーで、**変数を初期化する** アクションをロジック アプリ ワークフローに追加します。

1. **[変数を初期化する]** アクションのエディターで、次の設定を構成します。 そして、変更を保存します。

   1. **[名前]** に、変数の名前を入力します。 たとえば、「 `IDOCtransferID` 」のように入力します。

   1. **[型]** では、変数の型として **[文字列]** を選択します。

   1. **[値]** では、 **[初期値を入力]** テキスト ボックスを選択して、動的コンテンツ メニューを開きます。

   1. **[式]** タブを選択します。関数の一覧で、関数 `guid()` を入力します。

   1. **[OK]** を選択して変更を保存します。 **[値]** フィールドに、GUID を生成する `guid()` 関数が設定されます。

1. **[変数を初期化する]** アクションの後に、 **[\[IDOC] Send document to SAP]\([IDOC] SAP にドキュメントを送信する\)** を追加します。

1. **[\[IDOC] Send document to SAP]\([IDOC] SAP にドキュメントを送信する\)** アクションのエディターで、次の設定を構成します。 そして、変更を保存します。

   1. **[IDOC type]\(IDOC の種類\)** でメッセージの種類を選択し、 **[Input IDOC message]\(IDOC メッセージを入力する\)** でメッセージを指定します。

   1. **[SAP release version]\(SAP リリース バージョン\)** では、お使いの SAP 構成の値を選択します。

   1. **[Record types version]\(レコード タイプ バージョン\)** では、お使いの SAP 構成の値を選択します。

   1. **[Confirm TID]\(TID を確認する\)** では、 **[No]\(いいえ\)** を選択します。

   1. **[Add new parameter list]\(新しいパラメーター リストの追加\)**  >  **[Transaction ID GUID]\(トランザクション ID GUID\)** を選択します。

   1. テキスト ボックスを選択して、動的コンテンツ メニューを開きます。 **[変数]** タブで、例えば `IDOCtransferID` など、作成した変数の名前を選択します。

1. **[\[IDOC] Send document to SAP]\([IDOC] SAP にドキュメントを送信する\)** アクションのタイトル バーで、 **[...]**  >  **[設定]** の順に選択します。

   **[再試行ポリシー]** の場合は、 **[既定]** &gt; **[完了]** を選択することをお勧めします。 ただし、代わりに、特定のニーズに合わせてカスタム ポリシーを構成することもできます。 カスタム ポリシーでは、一時的なネットワーク障害を克服するために、少なくとも 1 回の再試行を構成することをお勧めします。

1. **[\[IDOC] Send document to SAP]\([IDOC] SAP にドキュメントを送信する\)** アクションの後に、 **[\[IDOC] Confirm transaction ID]\(トランザクション ID を確認する\)** アクションを追加します。

1. **[\[IDOC] Confirm transaction ID]\(トランザクション ID を確認する\)** アクションのエディターで、次の設定を構成します。 そして、変更を保存します。

1. **[トランザクション ID]** に、変数の名前をもう一度入力します。 たとえば、「 `IDOCtransferID` 」のように入力します。

1. 必要に応じて、テスト環境で重複除去を検証します。

    1. 前の手順で使用したのと同じ **[トランザクション ID]** GUID を使用して **[\[IDOC] Send document to SAP]\([IDOC] SAP にドキュメントを送信する\)** アクションを繰り返します。
    
    1. **\[[IDOC] SAP にドキュメントを送信する]** アクションを呼び出すたびに、どの IDoc 番号が割り当てられたかを確認するには、 **\[[IDOC] トランザクションの IDOC リストを取得する]** アクションを、同じ **[トランザクション ID]** と **[受信]** 方向で使用します。
    
       両方の呼び出しで同じ 1 つの IDoc 番号が返された場合、その IDoc の重複は除去されています。

   同じ IDoc を 2 回送信すると、SAP が tRFC 呼び出しの重複を識別し、1 つの受信 IDoc メッセージに対する 2 つの呼び出しを解決できることを検証できます。

## <a name="known-issues-and-limitations"></a>既知の問題と制限事項

マネージド (ISE 以外の) SAP コネクタに関して現在知られている問題と制限は次のようになります。

* 一般に、SAP トリガーでデータ ゲートウェイ クラスターがサポートされない。 フェールオーバーで、SAP システムと通信するデータ ゲートウェイ ノードがアクティブなノードと異なる場合があり、結果として、予想外の動作が発生します。

  * 送信シナリオの場合、フェールオーバー モードのデータ ゲートウェイ クラスターがサポートされます。

  * 負荷分散モードのデータ ゲートウェイ クラスターは、ステートフル [SAP アクション](#actions)ではサポートされません。 このようなアクションとしては、 **[\[BAPI - RFC] ステートフル セッションの作成]** 、 **[\[BAPI] トランザクションのコミット]** 、 **[\[BAPI] トランザクションのロールバック]** 、 **[\[BAPI - RFC] ステートフル セッションの終了]** に加えて、 **[セッション ID]** 値を指定するすべてのアクションが挙げられます。 ステートフル通信は、同じデータ ゲートウェイ クラスター ノードに保持する必要があります。

  * ステートフル SAP アクションの場合は、非クラスター モードで、またはフェールオーバー用に設定されているクラスターで、データゲート ウェイを使用します。

* 現在、SAP コネクタは SAP ルーター文字列をサポートしていない。 オンプレミス データ ゲートウェイは、接続する SAP システムと同じ LAN 上に存在する必要があります。

* **\[BAPI] SAP でのメソッドの呼び出し** アクションでは、アクションから返される **CallBapiResponse** オブジェクトに少なくとも 1 つの警告が存在する場合、自動コミット機能によって BAPI の変更はコミットされません。 警告を無視して BAPI の変更をコミットするには、 **\[BAPI - RFC] ステートフル セッションの作成** アクションで明示的にセッションを作成し、 **\[BAPI] SAP でのメソッドの呼び出し** アクションの自動コミット機能を無効にして、代わりに **\[BAPI] トランザクションのコミット** アクションを呼び出します。

* [ISE のロジック アプリ](connect-virtual-network-vnet-isolated-environment-overview.md)の場合、このコネクタの ISE のラベルが付いたバージョンでは、代わりに [ISE メッセージ制限](logic-apps-limits-and-config.md#message-size-limits)が使用されます。

## <a name="connector-reference"></a>コネクタのレファレンス

SAP コネクタの詳細については、[コネクタのリファレンス](/connectors/sap/)を参照してください。 SAP コネクタ、トリガー、およびアクションの制限、パラメーター、および戻り値については詳細を確認できます。

### <a name="triggers"></a>トリガー

:::row:::
    :::column span="1":::
        [**SAP からメッセージを受信したとき**](/connectors/sap/#when-a-message-is-received)
    :::column-end:::
    :::column span="3":::
        SAP からメッセージを受信したときは、何らかの処理を行ってください。
    :::column-end:::
:::row-end:::

### <a name="actions"></a>アクション

:::row:::
    :::column span="1":::
        [ **[BAPI - RFC] ステートフル セッションの終了**](/connectors/sap/#[bapi---rfc]-close-stateful-session-(preview))
    :::column-end:::
    :::column span="3":::
        SAP システムへの既存のステートフル接続セッションを終了します。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[BAPI - RFC] ステートフル セッションの作成**](/connectors/sap/#[bapi---rfc]-create-stateful-session-(preview))
    :::column-end:::
    :::column span="3":::
        SAP システムへのステートフル接続セッションを作成します。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[BAPI] SAP でのメソッドの呼び出し**](/connectors/sap/#[bapi]-call-method-in-sap-(preview))
    :::column-end:::
    :::column span="3":::
        ご利用の SAP システムで BAPI メソッドを呼び出します。
        \
        \
        呼び出しには、次のパラメーターを使用する必要があります:     \
        \
        **Business Object** (`businessObject`)。検索可能なドロップダウン メニューです。
        \
        \
        **Method** (`method`)。**Business Object** を選択した後に使用可能なメソッドを設定します。 使用できるメソッドは、選択した **Business Object** によって異なります。
        \
        \
        **Input BAPI parameters** (`body`)。呼び出しの BAPI メソッド入力パラメーター値を含む XML ドキュメント、または BAPI パラメーターを含むストレージ BLOB の URI を呼び出します。
        \
        \
        **[BAPI] SAP でのメソッドの呼び出し** アクションを使用する方法の詳細な例については、[BAPI 要求の XML サンプル](#xml-samples-for-bapi-requests)を参照してください。
        \
        ワークフロー デザイナーを使用して BAPI 要求を編集している場合は、次の検索関数を使用できます:     \
        \
        デザイナーでオブジェクトを選択すると、使用可能なメソッドのドロップダウン メニューが表示されます。
        \
        \
        BAPI API の呼び出しによって提供される検索可能なリストを使用して、ビジネス オブジェクトの種類をキーワードでフィルター処理します。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[BAPI] トランザクションのコミット**](/connectors/sap/#[bapi]-commit-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        セッションの BAPI トランザクションをコミットします。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[BAPI] トランザクションのロールバック**](/connectors/sap/#[bapi]-roll-back-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        セッションの BAPI トランザクションをロールバックします。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[IDOC - RFC] トランザクション ID を確認する**](/connectors/sap/#[idoc---rfc]-confirm-transaction-id-(preview))
    :::column-end:::
    :::column span="3":::
        トランザクション識別子の確認を SAP に送信します。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[IDOC] トランザクションの IDOC リストを取得する**](/connectors/sap/#[idoc]-get-idoc-list-for-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        セッション識別子またはトランザクション識別子で、トランザクションの IDoc のリストを取得します。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[IDOC] IDOC の状態を取得する**](/connectors/sap/#[idoc]-get-idoc-status-(preview))
    :::column-end:::
    :::column span="3":::
        IDoc の状態を取得します。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[IDOC] SAP にドキュメントを送信する**](/connectors/sap/#[idoc]-send-document-to-sap-(preview))
    :::column-end:::
    :::column span="3":::
        ご利用の SAP サーバーに IDoc メッセージを送信します。
        \
        \
        呼び出しには、次のパラメーターを使用する必要があります:     \
        \
        **IDOC type with optional extension** (`idocType`)。検索可能なドロップダウン メニューです。
        \
        \
        **Input IDOC message** (`body`)。IDoc ペイロードを含む XML ドキュメント、または IDoc XML ドキュメントを含むストレージ BLOB の URI を呼び出します。 このドキュメントは、WE60 IDoc のドキュメントに従った SAP IDoc XML スキーマ、または一致する SAP IDoc アクション URI 用に生成されたスキーマのいずれかに準拠している必要があります。
        \
        \
        省略可能なパラメーター **SAP release version** (`releaseVersion`) を使用すると、IDoc の種類を選択した後に値が設定されます。これは、選択した IDoc の種類によって異なります。
        \
        \
        IDoc の送信アクションに関するより詳細な例については、「[SAP サーバへの IDoc メッセージの送信に関するチュートリアル](#send-idoc-messages-to-sap-server)」を参照してください。
        \
        \
        省略可能なパラメーター **Confirm TID** (`confirmTid`) を使用する方法については、「[トランザクションを明示的に確認する方法に関するチュートリアル](#confirm-transaction-explicitly)」を参照してください。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[RFC] RFC をトランザクションに追加する**](/connectors/sap/#[rfc]-add-rfc-to-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        ご利用のトランザクションに RFC 呼び出しを追加します。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[RFC] SAP での関数の呼び出し**](/connectors/sap/#[rfc]-call-function-in-sap-(preview))
    :::column-end:::
    :::column span="3":::
        ご利用の SAP システム上で、RFC 操作 (sRFC、tRFC、または qRFC) を呼び出します。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[RFC] トランザクションのコミット**](/connectors/sap/#[rfc]-commit-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        セッションおよびキューの RFC トランザクションをコミットします。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[RFC] トランザクションの作成**](/connectors/sap/#[rfc]-create-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        識別子およびキュー名で新しいトランザクションを作成します。 トランザクションが存在する場合は、その詳細を取得します。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[RFC] トランザクションの取得**](/connectors/sap/#[rfc]-get-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        識別子およびキュー名でトランザクションの詳細を取得します。 新しいトランザクションが存在しない場合は作成します。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [**[スキーマの生成]**](/connectors/sap/#generate-schemas)
    :::column-end:::
    :::column span="3":::
        IDoc、BAPI、RFC 用に SAP 成果物のスキーマを生成します。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [**SAP テーブルの読み取り**](/connectors/sap/#read-sap-table-(preview))
    :::column-end:::
    :::column span="3":::
        SAP テーブルを読み取ります。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [**SAP にメッセージを送信する**](/connectors/sap/#send-message-to-sap)
    :::column-end:::
    :::column span="3":::
        メッセージの種類 (RFC、BAPI、IDoc) を SAP に送信します。
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>次のステップ

* Azure Logic Apps から[オンプレミス システムに接続します](logic-apps-gateway-connection.md)
* [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md) を使用して、検証、変換、およびその他のメッセージ操作を使用する方法を確認します。
* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
