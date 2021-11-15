---
title: Application Gateway との統合
description: このエンド ツー エンドのチュートリアルでは、ILB App Service Environment のアプリを Application Gateway と統合する方法について説明します。
author: madsd
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.topic: article
ms.date: 10/12/2021
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: 77e8ec3db7b66fe0c639bfd56942b171b9fc2129
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132294278"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>ILB App Service Environment を Azure Application Gateway と統合する

[App Service Environment][AppServiceEnvironmentoverview] は、ユーザーの Azure 仮想ネットワークのサブネットに Azure App Service をデプロイしたものです。 アプリへのアクセスには、外部エンドポイントまたは内部エンドポイントを使用してデプロイできます。 内部エンドポイントを使用した App Service 環境のデプロイは、内部ロード バランサー (ILB) App Service 環境 (ASE) と呼ばれます。

Web アプリケーション ファイアウォール は、着信する Web トラフィックを検査して、SQL インジェクション、クロスサイト スクリプティング、マルウェアのアップロード、アプリケーション DDoS、およびその他の攻撃をブロックすることにより、Web アプリケーションのセキュリティを確保するのに役立ちます。 Azure Marketplace から WAF デバイスを取得することも、[Azure Application Gateway][appgw] を使用することもできます。

Azure Application Gateway は、レイヤー 7 負荷分散、TLS/SSL オフロード、および Web アプリケーション ファイアウォール (WAF) 保護を提供する仮想アプライアンスです。 これは、パブリック IP アドレスでリッスンし、トラフィックをアプリケーション エンドポイントにルーティングします。 以下では、WAF で構成されたアプリケーション ゲートウェイを ILB App Service 環境のアプリと統合する方法について説明します。  

アプリケーション ゲートウェイと ILB App Service 環境の統合は、アプリ レベルで行われます。 アプリケーション ゲートウェイを ILB App Service 環境で構成する場合、ILB App Service 環境の特定のアプリに対して行うことになります。 この手法を使うと、単一の ILB App Service 環境で、セキュリティ保護されたマルチテナント アプリケーションをホストできます。  

:::image type="content" source="./media/integrate-with-application-gateway/appgw-highlevel.png" alt-text="高レベルの統合図のスクリーンショット":::

このチュートリアルでは次を行います。

* Azure Application Gateway を作成します。
* ILB App Service 環境上のアプリを指すように Application Gateway を構成します。
* アプリケーション ゲートウェイを指すパブリック DNS ホスト名を編集します。

## <a name="prerequisites"></a>前提条件

Application Gateway を ILB App Service 環境と統合するには、次のものが必要です。

* ILB App Service 環境。
* ILB App Service 環境のプライベート DNS ゾーン。
* ILB App Service 環境で実行するアプリ。
* 後でアプリケーション ゲートウェイを指すために使われるパブリック DNS 名。
* アプリケーション ゲートウェイに TLS/SSL 暗号化を使用する必要がある場合は、アプリケーション ゲートウェイにバインドするために使用される有効な公開証明書が必要です。

### <a name="ilb-app-service-environment"></a>ILB App Service 環境

ILB App Service 環境を作成する方法の詳細については、「[Azure portal で ASE を作成する][creation]」および「[ARM を使用して ASE を作成する][createfromtemplate]」を参照してください。

* ILB ASE が作成されると、既定のドメインは `<YourAseName>.appserviceenvironment.net` になります。

    :::image type="content" source="./media/integrate-with-application-gateway/ilb-ase.png" alt-text="ILB ASE の概要のスクリーンショット":::

* 内部ロードバランサーは、受信アクセス用にプロビジョニングされます。 [ASE 設定] の IP アドレスで受信アドレスを確認できます。 この IP アドレスにマップされたプライベート DNS ゾーンは、後で作成できます。

    :::image type="content" source="./media/integrate-with-application-gateway/ip-addresses.png" alt-text="ILB ASE IP アドレス設定から受信アドレスを取得するスクリーンショット。":::

### <a name="a-private-dns-zone"></a>プライベート DNS ゾーン

内部的な名前解決には、[プライベート DNS ゾーン][privatednszone]が必要です。 次の表に示すレコード セットを使用して ASE 名を使用して作成します (手順については、「[クイックスタート - Azure portal を使用して Azure プライベート DNS ゾーンを作成する][createprivatednszone]」を参照してください)。

| 名前  | Type | 値               |
| ----- | ---- | ------------------- |
| *     | A    | ASE 受信アドレス |
| @     | A    | ASE 受信アドレス |
| @     | SOA  | ASE DNS 名        |
| *.scm | A    | ASE 受信アドレス |

### <a name="app-service-on-ilb-ase"></a>ILB ASE の App Service

ILB ASE で App Service プランとアプリを作成する必要があります。 ポータルでアプリを作成するときに、**リージョン** として ILB ASE を選択します。

### <a name="a-public-dns-name-to-the-application-gateway"></a>アプリケーション ゲートウェイへのパブリック DNS 名

インターネットからアプリケーション ゲートウェイに接続するには、ルーティング可能なドメイン名が必要です。 この例では、ルーティング可能なドメイン名 `asabuludemo.com` を使用し、このドメイン名 `app.asabuludemo.com` を使用して App Service に接続する計画を立てました。 このアプリケーション ドメイン名にマップされた IP アドレスは、アプリケーション ゲートウェイの作成後にパブリック IP に設定する必要があります。
アプリケーション ゲートウェイにマップされたパブリック ドメインでは、App Service でカスタム ドメインを構成する必要はありません。 [App Service ドメイン](../manage-custom-dns-buy-domain.md#buy-an-app-service-domain)を使用してカスタム ドメイン名を購入できます。 

### <a name="a-valid-public-certificate"></a>有効なパブリック証明書

セキュリティを強化するために、セッションの暗号化に TLS/SSL 証明書をバインドすることをお勧めします。 TLS/SSL 証明書をアプリケーション ゲートウェイにバインドするには、次の情報を含む有効な公開証明書が必要です。 [App Service 証明書](../configure-ssl-certificate.md#start-certificate-order)を使用して、TLS/SSL 証明書を購入し、.pfx 形式でエクスポートできます。

| 名前  | 値               | 説明|
| ----- | ------------------- |------------|
| **共通名** |`<yourappname>.<yourdomainname>` (例: `app.asabuludemo.com`)  <br/> または `*.<yourdomainname>`、例: `*.asabuludemo.com` | アプリケーション ゲートウェイの標準証明書または[ワイルドカード証明書](https://wikipedia.org/wiki/Wildcard_certificate)|
| **サブジェクトの別名** | `<yourappname>.scm.<yourdomainname>` (例: `app.scm.asabuludemo.com`)  <br/>または `*.scm.<yourdomainname>`、例: `*.scm.asabuludemo.com` |App Service kudu Service に接続することを許可する SAN。 App Service kudu サービスをインターネットに発行しない場合は、省略可能な設定です。|

証明書ファイルには秘密キーがあり、.pfx 形式で保存する必要があります。後でアプリケーション ゲートウェイにインポートされます。

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

基本的なアプリケーション ゲートウェイの作成については、「[チュートリアル: Azure portal を使用して Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイを作成する][Tutorial: Create an application gateway with a Web Application Firewall using the Azure portal]」を参照してください。

このチュートリアルでは、Azure portal を使用して、ILB App Service 環境でアプリケーションゲートウェイを作成します。

Azure portal で、 **[新規]**  >  **[ネットワーク]**  >  **[アプリケーション ゲートウェイ]** を選択してアプリケーション ゲートウェイを作成します。

1. 基本設定

    **[レベル]** ドロップダウン リストで、 **[Standard V2]** または **[WAF V2]** を選択して、アプリケーション ゲートウェイで **WAF** 機能を有効にすることができます。 

2. フロントエンドの設定

    フロントエンド IP アドレスの種類に **[パブリック]** 、 **[プライベート]** 、または **[両方]** を選択します。 **[プライベート]** または **[両方]** に設定する場合、アプリケーション ゲートウェイ サブネットの範囲内に静的 IP アドレスを割り当てる必要があります。 この場合、パブリック エンドポイントにのみパブリック IP を設定します。
    
    * パブリック IP アドレス - アプリケーション ゲートウェイのパブリック アクセスにパブリック IP アドレスを関連付ける必要があります。 この IP アドレスを記録します。後で DNS サービスにレコードを追加する必要があります。
    
        :::image type="content" source="./media/integrate-with-application-gateway/frontends.png" alt-text="アプリケーション ゲートウェイ フロントエンド設定からパブリック IP を取得する場面のスクリーンショット。":::

3. バックエンド設定

    バックエンド プール名を入力し、 **[ターゲットの種類]** で **[App Services]** または **[IP アドレスまたは FQDN]** を選択します。 この場合、 **[App Services]** に設定し、ターゲット ドロップダウン リストから App Service 名を選択します。

    :::image type="content" source="./media/integrate-with-application-gateway/add-backend-pool.png" alt-text="バックエンド設定でバックエンド プール名を追加したスクリーンショット。":::

4. 構成設定

    **[構成]** 設定で、 **[ルーティング規則の追加]** アイコンをクリックして、ルーティング規則を追加する必要があります。

    :::image type="content" source="./media/integrate-with-application-gateway/configuration.png" alt-text="構成設定にルーティング規則を追加したスクリーンショット。":::

    ルーティング規則は、**リスナー** と **バックエンド ターゲット** を構成する必要があります。 HTTPS リスナーは、概念実証デプロイやセキュリティ強化のために追加できます。

    * HTTP プロトコルを使用してアプリケーション ゲートウェイに接続するには、次の設定を使用してリスナーを作成します。
    
        | パラメーター      | 値                             | 説明                                                  |
        | -------------- | --------------------------------- | ------------------------------------------------------------ |
        | 規則の名前      | 例: `http-routingrule`    | ルーティング名                                                 |
        | リスナー名  | 例: `http-listener`       | リスナー名                                                |
        | フロントエンド IP    | パブリック                            | インターネット アクセスの場合は、[パブリック] に設定します                           |
        | Protocol       | HTTP                             | TLS/SSL 暗号化を使用しない                                       |
        | Port           | 80                               | 既定の HTTP ポート                                           |
        | リスナーの種類  | マルチ サイト                        | アプリケーション ゲートウェイでマルチサイトのリッスンを許可する           |
        | ホストの種類      | 複数またはワイルドカード                 | リスナーの種類がマルチサイトに設定されている場合は、複数、またはワイルドカード Web サイト名に設定します。 |
        | ホスト名      | 次に例を示します。`app.asabuludemo.com` | App Service のルーティング可能なドメイン名に設定します。              |
        
        :::image type="content" source="./media/integrate-with-application-gateway/http-routing-rule.png" alt-text="アプリケーション ゲートウェイ ルーティング規則の HTTP リスナーのスクリーンショット。":::
    
    * TLS/SSL 暗号化を使用してアプリケーション ゲートウェイに接続するには、次の設定を使用してリスナーを作成します。
    
        | パラメーター      | 値                             | 説明                                                  |
        | -------------- | --------------------------------- | ------------------------------------------------------------ |
        | 規則の名前      | 例: `https-routingrule`    | ルーティング名                                                 |
        | リスナー名  | 例: `https-listener`       | リスナー名                                                |
        | フロントエンド IP    | パブリック                            | インターネット アクセスの場合は、[パブリック] に設定します                           |
        | Protocol       | HTTPS                             | TLS/SSL 暗号化を使用する                                       |
        | Port           | 443                               | 既定の HTTPS ポート                                           |
        | HTTPS 設定 | 証明書のアップロード              | CN と、.pfx 形式の秘密キーが含まれている証明書をアップロードします。 |
        | リスナーの種類  | マルチ サイト                        | アプリケーション ゲートウェイでマルチサイトのリッスンを許可する           |
        | ホストの種類      | 複数またはワイルドカード                 | リスナーの種類がマルチサイトに設定されている場合は、複数、またはワイルドカード Web サイト名に設定します。 |
        | ホスト名      | 次に例を示します。`app.asabuludemo.com` | App Service のルーティング可能なドメイン名に設定します。              |
        
        :::image type="content" source="./media/integrate-with-application-gateway/https-routing-rule.png" alt-text="アプリケーション ゲートウェイ ルーティング規則の HTTPS リスナー。":::
    
    * **[バックエンド ターゲット]** で **[バックエンド プール]** と **[HTTP 設定]** を構成する必要があります。 バックエンド プールは、前の手順で構成されました。 **[新規追加]** リンクをクリックし、HTTP 設定を追加します。
    
        :::image type="content" source="./media/integrate-with-application-gateway/add-new-http-setting.png" alt-text="HTTP 設定を追加するための新しいリンクを追加する画面のスクリーンショット。":::
    
    * 次のような HTTP 設定が表示されます。
    
        | パラメーター                     | 値                                                        | 説明                                                  |
        | ----------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
        | HTTP 設定名             | 例: `https-setting`                                   | HTTP 設定名                                            |
        | バックエンド プロトコル              | HTTPS                                                        | TLS/SSL 暗号化を使用する                                       |
        | バックエンド ポート                  | 443                                                          | 既定の HTTPS ポート                                           |
        | 既知の CA 証明書を使用する | Yes                                                          | ILB ASE の既定のドメイン名は `.appserviceenvironment.net` です。このドメインの証明書は、信頼された公開ルート機関によって発行されます。 [信頼されたルート証明書] 設定では、**既知の CA の信頼されたルート証明書** を使用するように設定できます。 |
        | 新しいホスト名でオーバーライドする   | Yes                                                          | ILB ASE 上のアプリに接続すると、ホスト名ヘッダーが上書きされます。 |
        | [ホスト名の上書き]            | バックエンド ターゲットからホスト名を選択する | バックエンド プールを App Service に設定する場合は、バックエンド ターゲットからホストを選択できます。 |
        | カスタム プローブを作成する | いいえ | 既定の正常性プローブを使用する|
        
        :::image type="content" source="./media/integrate-with-application-gateway/https-setting.png" alt-text="[HTTP 設定の追加] ダイアログのスクリーンショット。":::


## <a name="configure-an-application-gateway-integration-with-ilb-ase"></a>ILB ASE とアプリケーション ゲートウェイの統合を構成する

アプリケーション ゲートウェイから ILB ASE にアクセスするには、プライベート DNS ゾーンへの仮想ネットワーク リンクがあるかどうかを確認する必要があります。 アプリケーション ゲートウェイの VNet に関連付けられた仮想ネットワークがない場合は、次の手順で仮想ネットワーク リンクを追加します。

### <a name="configure-virtual-network-links-with-a-private-dns-zone"></a>プライベート DNS ゾーンを使用して仮想ネットワーク リンクを構成する

* プライベート DNS ゾーンとの仮想ネットワーク リンクを構成するには、プライベート DNS ゾーンの構成プレーンにアクセスします。 **[仮想ネットワークのリンク]**  >  **[追加]** を選択します 

:::image type="content" source="./media/integrate-with-application-gateway/add-vnet-link.png" alt-text="プライベート DNS ゾーンに仮想ネットワーク リンクを追加します。":::

* **[リンク名]** を入力し、アプリケーション ゲートウェイが存在する各サブスクリプションと仮想ネットワークを選択します。

:::image type="content" source="./media/integrate-with-application-gateway/vnet-link.png" alt-text="プライベート DNS ゾーンの仮想ネットワーク リンク設定に対する入力リンク名の詳細のスクリーンショット。":::

* バックエンドの正常性状態は、アプリケーション ゲートウェイプレーンで **バックエンドの正常性** 状態から確認できます。

:::image type="content" source="./media/integrate-with-application-gateway/backend-health.png" alt-text="バックエンドの正常性状態からバックエンドの正常性状態を確認するスクリーンショット。":::

### <a name="add-a-public-dns-record"></a>パブリック DNS レコードを追加する

インターネットからアプリケーション ゲートウェイにアクセスする場合は、適切な DNS マッピングを構成する必要があります。

* アプリケーション ゲートウェイのパブリック IP アドレスは、アプリケーション ゲートウェイ プレーンの **フロントエンド IP 構成** にあります。

:::image type="content" source="./media/integrate-with-application-gateway/frontend-ip.png" alt-text="アプリケーション ゲートウェイのフロントエンド IP アドレスは、フロントエンド IP 構成にあります。":::

* Azure DNS サービスを使用する例として、レコード セットを追加して、アプリケーションのドメイン名をアプリケーション ゲートウェイのパブリック IP アドレスにマップすることができます。

:::image type="content" source="./media/integrate-with-application-gateway/dns-service.png" alt-text="アプリのドメイン名をアプリケーション ゲートウェイのパブリック IP アドレスにマップするためのレコード セットを追加するスクリーンショット。":::

### <a name="validate-connection"></a>接続の検証

* インターネットからのコンピューター アクセスで、アプリケーション ドメイン名の名前解決をアプリケーション ゲートウェイのパブリック IP アドレスに対して確認できます。

:::image type="content" source="./media/integrate-with-application-gateway/name-resolution.png" alt-text="コマンド プロンプトから名前解決を検証します。":::

* インターネットからのコンピューター アクセスで、ブラウザーから Web アクセスをテストします。

:::image type="content" source="./media/integrate-with-application-gateway/access-web.png" alt-text="ブラウザーを開き、Web にアクセスするときのスクリーンショット。":::

<!--LINKS-->
[appgw]: ../../application-gateway/overview.md
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[creation]: ./creation.md
[createfromtemplate]: ./create-from-template.md
[createprivatednszone]: ../../dns/private-dns-getstarted-portal.md
[AppServiceEnvironmentoverview]: ./overview.md
[privatednszone]: ../../dns/private-dns-overview.md
[Tutorial: Create an application gateway with a Web Application Firewall using the Azure portal]: ../../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md
