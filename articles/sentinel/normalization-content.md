---
title: Advanced SIEM Information Model (ASIM) コンテンツ | Microsoft Docs
description: この記事では、Advanced SIEM Information Model (ASIM) を使用した Microsoft Sentinel コンテンツについて説明します。
services: sentinel
cloud: na
documentationcenter: na
author: oshezaf
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: ofshezaf
ms.custom: ignite-fall-2021
ms.openlocfilehash: fae60a1a6375190f995ee3b1cc7c6639b9f6dd3b
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712872"
---
# <a name="advanced-siem-information-model-asim-security-content--public-preview"></a>Advanced SIEM Information Model (ASIM) セキュリティ コンテンツ (パブリック プレビュー)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Sentinel の正規化されたセキュリティ コンテンツには、ソースに依存しない正規化パーサーで使用できる分析ルール、検索クエリ、ブックが含まれています。

<a name="builtin"></a>正規化された組み込みのコンテンツを Microsoft Sentinel のギャラリーと[ソリューション](sentinel-solutions-catalog.md)で見つける、独自の正規化されたコンテンツを作成する、既存のコンテンツを変更して正規化されたデータを使用することができます。

この記事では、ASIM をサポートするように構成された組み込みの Microsoft Sentinel コンテンツを紹介します。  Microsoft Sentinel GitHub リポジトリへのリンクは下に参照として掲載していますが、これらのルールは [Microsoft Sentinel Analytics ルール ギャラリー](detect-threats-built-in.md)でも見つけることができます。 ルールに関連するハンティング クエリをコピーするには、リンクされた GitHub ページを使用してください。

> [!TIP]
> また、Microsoft Sentinel の正規化パーサーと正規化されたコンテンツに関する詳しいウェビナーを[こちら](https://www.youtube.com/watch?v=zaqblyjQW6k)でご視聴いただけます。スライドは[こちら](https://1drv.ms/b/s!AnEPjr8tHcNmjGtoRPQ2XYe3wQDz?e=R3dWeM)でご確認いただけます。 詳細については、「[次のステップ](#next-steps)」を参照してください。
>

> [!IMPORTANT]
> 現在、ASIM はプレビュー段階です。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>

## <a name="authentication-security-content"></a>認証のセキュリティ コンテンツ

ASIM の正規化では、次の組み込みの認証コンテンツがサポートされています。

### <a name="analytics-rules"></a>分析ルール

 - [潜在的なパスワード スプレー攻撃 (認証正規化を使用)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthPasswordSpray.yaml)
 - [Brute force attack against user credentials (Uses Authentication Normalization) (ユーザー資格情報に対するブルート フォース攻撃 (認証正規化を使用))](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthBruteForce.yaml)
 - [User login from different countries within 3 hours (Uses Authentication Normalization) (さまざまな国からの 3 時間以内のユーザー ログイン (認証正規化を使用))](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthSigninsMultipleCountries.yaml)
 - [Sign-ins from IPs that attempt sign-ins to disabled accounts (Uses Authentication Normalization) (無効なアカウントへのサインインを試行する IP からのサインイン (認証正規化を使用))](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imSigninAttemptsByIPviaDisabledAccounts.yaml)


## <a name="dns-query-security-content"></a>DNS クエリのセキュリティ コンテンツ

ASIM の正規化では、次の組み込みの DNS クエリ コンテンツがサポートされています。

### <a name="analytics-rules"></a>分析ルール

 - (プレビュー) TI のドメイン エンティティの DNS イベントへのマッピング (正規化された DNS)
 - (プレビュー) TI の IP エンティティの DNS イベントへのマッピング (正規化された DNS)
 - [潜在的 DGA の検出 (ASimDNS)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDns_HighNXDomainCount_detection.yaml)
  - [Excessive NXDOMAIN DNS Queries (Normalized DNS) (過剰な NXDOMAIN DNS クエリ (正規化された DNS))](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDns_ExcessiveNXDOMAINDNSQueries.yaml)
 - [DNS events related to mining pools (Normalized DNS) (マイニング プールに関連する DNS イベント (正規化された DNS))](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDNS_Miners.yaml)
 - [DNS events related to ToR proxies (Normalized DNS) (ToR プロキシに関連する DNS イベント (正規化された DNS))](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDNS_TorProxies.yaml)
 - [Known Barium domains (既知の Barium ドメイン)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/BariumDomainIOC112020.yaml)
 - [Known Barium IP addresses (既知の Barium IP アドレス)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/BariumIPIOC112020.yaml) 
 - [Exchange Server Vulnerabilities Disclosed March 2021 IoC Match (2021 年 3 月に公開された Exchange Server の脆弱性の IoC 一致)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ExchangeServerVulnerabilitiesMarch2021IoCs.yaml)
 - [既知の GALLIUM ドメインとハッシュ](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
 - [Known IRIDIUM IP (既知の IRIDIUM IP)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
 - [NOBELIUM - ドメインおよび IP IOC - 2021 年 3 月](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)
 - [既知の Phosphorus グループ ドメイン/IP](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
 - [Known STRONTIUM group domains - July 2019 (既知の STRONTIUM グループ ドメイン - 2019 年 7 月)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
 - [Solorigate ネットワーク ビーコン](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
 - [DCU 削除に含まれる THALLIUM ドメイン](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
 - [Known ZINC Comebacker and Klackring malware hashes (既知の ZINC Comebacker および Klackring マルウェア ハッシュ)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)

## <a name="file-activity-security-content"></a>ファイル アクティビティのセキュリティ コンテンツ

ASIM の正規化では、次の組み込みのファイル アクティビティ コンテンツがサポートされています。

### <a name="analytic-rules"></a>分析ルール

- [SUNBURST and SUPERNOVA backdoor hashes (Normalized File Events) (SUNBURST と SUPERNOVA のバックドア ハッシュ (正規化されたファイル イベント))](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimFileEvent/imFileESolarWindsSunburstSupernova.yaml)
- [Exchange Server Vulnerabilities Disclosed March 2021 IoC Match (2021 年 3 月に公開された Exchange Server の脆弱性の IoC 一致)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ExchangeServerVulnerabilitiesMarch2021IoCs.yaml)
- [HAFNIUM UM Service writing suspicious file (疑わしいファイルを書き込む HAFNIUM UM サービス)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/HAFNIUMUmServiceSuspiciousFile.yaml)
- [NOBELIUM - Domain, Hash and IP IOCs - May 2021 (NOBELIUM - ドメイン、ハッシュ、IP IOC - 2021 年 3 月)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_IOCsMay2021.yaml)
- [SUNSPOT log file creation (SUNSPOT ログ ファイルの作成)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/SUNSPOTLogFile.yaml)
- [Known ZINC Comebacker and Klackring malware hashes (既知の ZINC Comebacker および Klackring マルウェア ハッシュ)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)

## <a name="process-activity-security-content"></a>プロセス アクティビティのセキュリティ コンテンツ

ASIM の正規化では、次の組み込みのプロセス アクティビティ コンテンツがサポートされています。

### <a name="analytics-rules"></a>分析ルール

 - [Probable AdFind Recon Tool の使用 (正規化されたプロセス イベント)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_AdFind_Usage.yaml)
 - [Base64 でエンコードされた Windows プロセスのコマンド ライン (正規化されたプロセス イベント)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_base64_encoded_pefile.yaml)
 - [ごみ箱内のマルウェア (正規化されたプロセス イベント)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_malware_in_recyclebin.yaml)
 - [NOBELIUM - vbscript の疑わしい rundll32.exe の実行 (正規化されたプロセス イベント)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_NOBELIUM_SuspiciousRundll32Exec.yaml)
 - [SUNBURST の疑わしい SolarWinds 子プロセス (正規化されたプロセス イベント)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_SolarWinds_SUNBURST_Process-IOCs.yaml)

### <a name="hunting-queries"></a>ハンティング クエリ

 - [Cscript スクリプトの毎日の概要の内訳 (正規化されたプロセス イベント)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_cscript_summary.yaml)
 - [ユーザーとグループの列挙 (正規化されたプロセス イベント)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_enumeration_user_and_group.yaml)
 - [Exchange PowerShell スナップインの追加 (正規化されたプロセス イベント)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_ExchangePowerShellSnapin.yaml)
 - [メールボックスのエクスポートとエクスポートの削除を行うホスト (正規化されたプロセス イベント)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_HostExportingMailboxAndRemovingExport.yaml)
 - [Invoke-PowerShellTcpOneLine の使用 (正規化されたプロセス イベント)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Invoke-PowerShellTcpOneLine.yaml)
 - [Base64 の Nishang リバース TCP シェル (正規化されたプロセス イベント)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_NishangReverseTCPShellBase64.yaml)
 - [一般的でない/ドキュメントに記載されていないコマンド ライン スイッチを使用して作成されたユーザーの概要 (正規化されたプロセス イベント)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_persistence_create_account.yaml)
 - [Powercat のダウンロード (正規化されたプロセス イベント)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_PowerCatDownload.yaml)
 - [PowerShell のダウンロード (正規化されたプロセス イベント)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_powershell_downloads.yaml)
 - [特定のホストのプロセスのエントロピ (正規化されたプロセス イベント)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_ProcessEntropy.yaml)
 - [SolarWinds インベントリ (正規化されたプロセス イベント)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_SolarWindsInventory.yaml)
 - [Adfind ツールを使用した疑わしい列挙型 (正規化されたプロセス イベント)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Suspicious_enumeration_using_adfind.yaml)
 - [Windows システムのシャットダウン/再起動 (正規化されたプロセス イベント)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Windows%20System%20Shutdown-Reboot(T1529).yaml)
 - [Certutil (LOLBins と LOLScripts、正規化されたプロセス イベント)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Certutil-LOLBins.yaml)
 - [Rundll32 (LOLBins と LOLScripts、正規化されたプロセス イベント)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/inProcess_SignedBinaryProxyExecutionRundll32.yaml)
 - [一般的でないプロセス - 下位 5% (正規化されたプロセス イベント)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_uncommon_processes.yaml)
 - [Unicode Obfuscation in Command Line (コマンドラインでの Unicode の難読化)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/UnicodeObfuscationInCommandLine.yaml)

## <a name="registry-activity-security-content"></a>レジストリ アクティビティのセキュリティ コンテンツ

ASIM の正規化では、次の組み込みのレジストリ アクティビティ コンテンツがサポートされています。

### <a name="hunting-queries"></a>ハンティング クエリ

- [Persisting Via IFEO Registry Key (IFEO レジストリ キーを使用した永続化)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/PersistViaIFEORegistryKey.yaml)

## <a name="modify-your-content-to-use-normalized-data"></a><a name="modify"></a>正規化されたデータを使用するためにコンテンツを変更する

カスタム コンテンツで正規化を使用するには:

- クエリに関連するソースに依存しないパーサーを使用するには、クエリを変更します。
- 正規化されたスキーマ フィールド名を使用するために、クエリのフィールド名を変更します。
- 該当する場合は、クエリのフィールドの正規化された値を使用する条件を変更します。

たとえば、Infoblox DNS サーバーから送信された DNS イベントで動作する **逆引き検索回数が多いと観測されたレア クライアント** DNS 分析ルールを考えてみましょう。

```kusto
let threshold = 200;
InfobloxNIOS
| where ProcessName =~ "named" and Log_Type =~ "client"
| where isnotempty(ResponseCode)
| where ResponseCode =~ "NXDOMAIN"
| summarize count() by Client_IP, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (InfobloxNIOS
    | where ProcessName =~ "named" and Log_Type =~ "client"
    | where isnotempty(ResponseCode)
    | where ResponseCode =~ "NXDOMAIN"
    ) on Client_IP
| extend timestamp = TimeGenerated, IPCustomEntity = Client_IP
```

次のコードはソースに依存しないバージョンです。正規化を使用して、DNS クエリ イベントを提供するソースに対して同じ検出を提供します。

```kusto
imDns(responsecodename='NXDOMAIN')
| summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (imDns(responsecodename='NXDOMAIN')) on SrcIpAddr
| extend timestamp = TimeGenerated, IPCustomEntity = SrcIpAddr```
```

正規化されたソースに依存しないバージョンには、次の違いがあります。

- `imDns` 正規化されたパーサーは、Infoblox パーサーの代わりに使用されます。

- `imDns` は DNS クエリ イベントのみをフェッチするため、Infoblox バージョンの `where ProcessName =~ "named" and Log_Type =~ "client"` で行われるようなイベント種類の確認は必要ありません。

- `Client_IP` の代わりに `SrcIpAddr` フィールドを使用します。
 
- パーサー パラメーターのフィルター処理は ResponseCodeName に使用され、明示的な where 句は不要です。


正規化された DNS ソースのサポートとは別に、正規化されたバージョンは短く、理解しやすくなります。 

スキーマまたはパーサーでフィルター処理パラメーターがサポートされていない場合、最後の変更を除き、変更は似ています。 代わりに、フィルター条件は、次のように元のクエリから保持されます。

```kusto
let threshold = 200;
imDns
| where isnotempty(ResponseCodeName)
| where ResponseCodeName =~ "NXDOMAIN"
| summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (imDns
    | where isnotempty(ResponseCodeName)
    | where ResponseCodeName =~ "NXDOMAIN"
    ) on SrcIpAddr
| extend timestamp = TimeGenerated, IPCustomEntity = SrcIpAddr
```

## <a name="next-steps"></a><a name="next-steps"></a>次のステップ

この記事では、Advanced SIEM Information Model (ASIM) コンテンツについて説明します。

詳細については、次を参照してください。

- Microsoft Sentinel の正規化パーサーと正規化されたコンテンツに関する詳しいウェビナーを[こちら](https://www.youtube.com/watch?v=zaqblyjQW6k)でご視聴いただけます。スライドは[こちら](https://1drv.ms/b/s!AnEPjr8tHcNmjGtoRPQ2XYe3wQDz?e=R3dWeM)でご確認いただけます。
- [Advanced SIEM Information Model の概要](normalization.md)
- [Advanced SIEM Information Model のスキーマ](normalization-about-schemas.md)
- [Advanced SIEM Information Model のパーサー](normalization-about-parsers.md)