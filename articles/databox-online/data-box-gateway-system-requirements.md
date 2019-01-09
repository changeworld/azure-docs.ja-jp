---
title: Microsoft Azure Data Box Gateway のシステム要件| Microsoft Docs
description: Azure Data Box Gateway のソフトウェア要件とネットワーキング要件について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/17/2018
ms.author: alkohli
ms.openlocfilehash: da22c09a227069af0eeb42ab67a59189ae494185
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256674"
---
# <a name="azure-data-box-gateway-system-requirements-preview"></a>Azure Data Box Gateway のシステム要件 (プレビュー)

この記事では、Microsoft Azure Data Box Gateway ソリューション、および Azure Data Box Gateway に接続するクライアントのシステム要件のうち、重要なものについて説明します。 この情報は、Data Box Gateway をデプロイする前によく確認し、デプロイ時やそれ以降の操作時にも、必要に応じて繰り返し参照することをお勧めします。

Data Box Gateway 仮想デバイスのシステム要件は次のとおりです。

- **ホストのソフトウェア要件** - サポートされているプラットフォーム、ローカル構成 UI 用のブラウザー、SMB クライアント、およびデバイスに接続するホストのその他の要件について説明します。
- **デバイスのネットワーク要件** - 仮想デバイスの操作のためのネットワーク要件について説明します。

> [!IMPORTANT]
> Data Box Gateway はプレビュー段階にあります。 このソリューションをデプロイする前に、「[プレビューの使用条件に関するページ](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を確認してください。 

## <a name="specifications-for-the-virtual-device"></a>仮想デバイスの仕様

Data Box Gateway の基になっているホスト システムでは、仮想デバイスのプロビジョニング専用に次のリソースを使用できます。

| 仕様                                          | 説明              |
|---------------------------------------------------------|--------------------------|
| 仮想プロセッサ (コア)   | 最低 4 |            
| メモリ  | 8 GB 以上|
| 可用性|単一ノード|
| ディスク| OS ディスク:250 GB <br> データ ディスク:最小 2 TB、シン プロビジョニング、SSD を使用する必要があります|
| ネットワーク インターフェイス|1 つ以上の仮想ネットワーク インターフェイス|


## <a name="supported-os-for-clients-connected-to-device"></a>デバイスに接続されるクライアントでサポートされている OS

Data Box Gateway に接続されるクライアントまたはホストでサポートされているオペレーティング システムの一覧を次に示します。

| **オペレーティング システム/プラットフォーム** | **バージョン** |
| --- | --- |
| Windows Server |2012 R2 <br> 2016 |
| Windows |8、10 |
| SUSE Linux |Enterprise Server 12 (x86_64)|
| Ubuntu |16.04.3 LTS|
| CentOS | 7.0 |

## <a name="supported-protocols-for-clients-accessing-device"></a>デバイスにアクセスするクライアントでサポートされるプロトコル

|**プロトコル** |**バージョン**   |**メモ**  |
|---------|---------|---------|
|SMB    | 2.X、3.X      | SMB 1 はサポートされていません。|
|NFS     | V3 と V4        |         |

## <a name="supported-virtualization-platforms-for-device"></a>デバイスでサポートされている仮想化プラットフォーム

| **オペレーティング システム/プラットフォーム**  |**バージョン**   |**メモ**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016  |         |
|VMware ESXi     | 6.0 <br> 6.5        |VMware ツールはサポートされていません。         |


## <a name="supported-storage-accounts"></a>サポートされるストレージ アカウント

Data Box Gateway でサポートされるストレージ アカウントの一覧を次に示します。

| **ストレージ アカウント** | **メモ** |
| --- | --- |
| クラシック | 標準 |
| 汎用  |標準。V1 と V2 の両方がサポートされます。 また、ホット層とクール層の両方がサポートされます。 |


## <a name="supported-storage-types"></a>サポートされるストレージの種類

Data Box Gateway でサポートされるストレージの種類の一覧を次に示します。

| **ファイル形式** | **メモ** |
| --- | --- |
| Azure ブロック BLOB | |
| Azure ページ BLOB  | |
| Azure Files | |

## <a name="supported-browsers-for-local-web-ui"></a>ローカル Web UI 用にサポートされているブラウザー

仮想デバイスのローカル Web UI 用にサポートされているブラウザーの一覧を次に示します。

|ブラウザー  |バージョン  |その他の要件/注意事項  |
|---------|---------|---------|
|Google Chrome   |最新バージョン         |         |
|Microsoft Edge    | 最新バージョン        |         |
|Internet Explorer     | 最新バージョン        |         |
|FireFox    |最新バージョン         |         |


## <a name="networking-requirements"></a>ネットワーク要件

SMB、クラウド、または管理トラフィックを許可するためにファイアウォールで開く必要があるポートを次の表に示します。 この表では、"*イン*" ("*受信*") は、着信クライアント要求がデバイスにアクセスする方向を意味します。 "*アウト*" ("*送信*") は Data Box Gateway デバイスがデプロイを超えて外部に (たとえば、インターネットに) データを送信する方向を意味します。

| ポート番号| インまたはアウト | ポート範囲| 必須|   メモ                                                             |                                                                                     |
|--------|---------|----------|--------------|----------------------|---------------|
| TCP 80 (HTTP)|アウト|WAN |いいえ |送信ポートは、更新プログラムを取得するためのインターネット アクセスに使用します。 <br>送信 Web プロキシは、ユーザーが構成できます。 |                          
| TCP 443 (HTTPS)|アウト|WAN|はい|送信ポートは、クラウドのデータへのアクセスに使用します。<br>送信 Web プロキシは、ユーザーが構成できます。|   
| UDP 53 (DNS)|アウト|WAN|場合により必須<br>メモを参照|このポートは、インターネット ベースの DNS サーバーを使用する場合にのみ必要です。<br>ローカル DNS サーバーを使用することをお勧めします。 |
| UDP 123 (NTP)|アウト|WAN|場合により必須<br>メモを参照|このポートは、インターネット ベースの NTP サーバーを使用する場合にのみ必要です。  |
| UDP 67 (DHCP)|アウト|WAN|場合により必須<br>メモを参照|このポートは、DHCP サーバーを使用する場合にのみ必要です。  |
| TCP 80 (HTTP)|イン|LAN|はい|これは、ローカル管理に使用するデバイスのローカル UI 用の受信ポートです。 <br>HTTP 経由でのローカル UI へのアクセスは、自動的に HTTPS にリダイレクトされます。  | 
| TCP 443 (HTTPS)|イン|LAN|はい|これは、ローカル管理に使用するデバイスのローカル UI 用の受信ポートです。 | 

## <a name="url-patterns-for-firewall-rules"></a>ファイアウォール ルールの URL パターン

多くの場合、ネットワーク管理者は、受信トラフィックと送信トラフィックをフィルターする URL パターンに基づいて、高度なファイアウォール ルールを構成できます。 Data Box Gateway デバイスと Data Box Gateway サービスは、Azure Service Bus、Azure Active Directory Access Control、ストレージ アカウント、Microsoft Update サーバーなど、他の Microsoft アプリケーションに依存しています。 その Microsoft アプリケーションと関連付けられた URL パターンを使用してファイアウォール ルールを構成できます。 Microsoft アプリケーションに関連付けられた URL パターンは変化する可能性がある点を理解することが重要です。 このため、ネットワーク管理者は必要に応じて Data Box Gateway のファイアウォール ルールを監視し更新する必要があります。

ほとんどの場合、送信トラフィックのファイアウォール ルールは Data Box Gateway 固定 IP アドレスに基づいて設定することが推奨されます。 ただし、次の情報を使用して、セキュリティで保護された環境を作成するのにために必要な高度なファイアウォール ルールを設定することもできます。

> [!NOTE]
> - デバイスの (送信元) IP は、常にすべてのクラウド対応ネットワーク インターフェイスに合わせて設定します。
> - 宛先 IP は、[Azure データセンターの IP 範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)に合わせて設定するようにします。

|     URL パターン                                                                                                                                                                                                                                                                                                                                                                                                                                       |     コンポーネント/機能                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
|    https://*.databoxedge.azure.com/*<br>https://*.servicebus.windows.net/*<br>https://login.windows.net                                                                                                                                                                                                                                                                                                        |    Azure Data Box Gateway サービス<br>Azure Service Bus<br>認証サービス    |
|    http://*.backup.windowsazure.com                                                                                                                                                                                                                                                                                                                                                                                                                   |    デバイスのアクティブ化                                                                                    |
|    http://crl.microsoft.com/pki/*   http://www.microsoft.com/pki/*                                                                                                                                                                                                                                                                                                                                                                                    |    証明書の失効                                                                               |
|    https://*.core.windows.net/*   https://*.data.microsoft.com   http://*.msftncsi.com                                                                                                                                                                                                                                                                                                                                                                |    Azure ストレージ アカウントと監視                                                                |
|    http://windowsupdate.microsoft.com<br>http://*.windowsupdate.microsoft.com<br>https://*.windowsupdate.microsoft.com<br>http://*.update.microsoft.com<br>https://*.update.microsoft.com<br>http://*.windowsupdate.com<br>http://download.microsoft.com<br>http://*.download.windowsupdate.com<br>http://wustat.windows.com<br>http://ntservicepack.microsoft.com<br>http://*.ws.microsoft.com<br>https://*.ws.microsoft.com<br>http://*.mp.microsoft.com        |    Microsoft Update サーバー                                                                             |
|    http://*.deploy.akamaitechnologies.com                                                                                                                                                                                                                                                                                                                                                                                                             |    Akamai CDN                                                                                           |
|    https://*.partners.extranet.microsoft.com/*                                                                                                                                                                                                                                                                                                                                                                                                        |    サポート パッケージ                                                                                      |
|    http://*.data.microsoft.com                                                                                                                                                                                                                                                                                                                                                                                                                        |    Windows のテレメトリ サービス (「顧客満足度及び診断テレメトリのための更新プログラム」を参照)      |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                       |                                                                                                         |



## <a name="internet-bandwidth"></a>インターネット帯域幅

次の要件は、Data Box Gateway デバイスで使用できるインターネットの最小帯域幅に適用されます。

- Data Box Gateway で 20 Mbps 以上の専用インターネット帯域幅が常に利用できること。 この帯域幅はその他のアプリケーションと共有できません。 
- 帯域幅調整を使用した場合、Data Box Gateway で専用の 32 Mbps インターネット帯域幅 (またはそれ以上) が利用できること。

## <a name="next-step"></a>次のステップ

* [Azure Data Box Gateway をデプロイする](data-box-gateway-deploy-prep.md)

