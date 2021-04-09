---
title: Common Event Format (CEF) キーおよび CommonSecurityLog フィールドのマッピング
description: この記事では、CEF キーを、Azure Sentinel 内の CommonSecurityLog の対応するフィールド名にマップします。
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.date: 03/16/2021
ms.openlocfilehash: 6c23fe86af030d371e12914062bb9558e8db3484
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104776094"
---
# <a name="cef-and-commonsecuritylog-field-mapping"></a>CEF および CommonSecurityLog フィールドのマッピング

下記の表では、Common Event Format (CEF) のフィールド名を、Azure Sentinel の CommonSecurityLog で使用する名前にマップしています。これらの表は、Azure Sentinel で CEF データ ソースを操作するときに役立つ場合があります。

詳細については、「[Common Event Format を使用して外部ソリューションを接続する](connect-common-event-format.md)」を参照してください。

## <a name="a---c"></a>A から C

|CEF キー名  |CommonSecurityLog でのフィールド名  |説明  |
|---------|---------|---------|
| act    |    <a name="deviceaction"></a> DeviceAction     |  イベントで触れられているアクション。       |
|   app  |    ApplicationProtocol     |  アプリケーションで使用される、HTTP、HTTPS、SSHv2、Telnet、POP、IMPA、IMAPS などのプロトコル。   |
| cnt    |    EventCount     |  イベントに関連付けられているカウントで、同じイベントが観察された回数を示します。       |
| | | |

## <a name="d"></a>D

|CEF キー名  |CommonSecurityLog での名前  |説明  |
|---------|---------|---------|
|Device Vendor     |  DeviceVendor       | デバイスの製品とバージョンの定義と共に、送信元デバイスの種類を一意に識別する文字列。       |
|Device Product     |   DeviceProduct      |   デバイスのベンダーとバージョンの定義と共に、送信元デバイスの種類を一意に識別する文字列。        |
|デバイス バージョン     |   DeviceVersion      |      デバイスの製品とベンダーの定義と共に、送信元デバイスの種類を一意に識別する文字列。     |
|DeviceEventClassID     |   DeviceEventClassID     |   イベントの種類ごとの一意の識別子として機能する文字列または整数。      |
| destinationDnsDomain    | destinationDnsDomain        |   完全修飾ドメイン名 (FQDN) の DNS の部分。      |
| destinationServiceName | DestinationServiceName | イベントによりターゲットとされるサービス。 たとえば、「 `sshd` 」のように入力します。|
| destinationTranslatedAddress | DestinationTranslatedAddress | IP ネットワーク内でイベントによって参照されている、変換後の送信先を IPv4 IP アドレスとして識別します。 |
| destinationTranslatedPort | DestinationTranslatedPort | 変換後の、ファイアウォールなどのポート。 <br>有効なポート番号: `0` - `65535` |
| deviceDirection | <a name="communicationdirection"></a> CommunicationDirection | 観察された通信が行われた方向に関する何らかの情報。 有効な値: <br>- `0` = 受信 <br>- `1` = 送信 |
| deviceDnsDomain | DeviceDnsDomain | 完全修飾ドメイン名 (FQDN) の DNS ドメインの部分 |
| deviceExternalID | DeviceExternalID | イベントを生成しているデバイスを一意に識別する名前。 |
| deviceFacility | DeviceFacility | イベントを生成している機能。|
| deviceInboundInterface | DeviceInboundInterface |デバイスにパケットまたはデータが入力されたインターフェイス。  |
| deviceNtDomain | DeviceNtDomain | デバイス アドレスの Windows ドメイン |
| deviceOutboundInterface | DeviceOutboundInterface |デバイスにパケットまたはデータが残されたインターフェイス。 |
| devicePayloadId |DevicePayloadId |イベントに関連付けられているペイロードの一意の識別子。 |
| deviceProcessName | ProcessName | イベントに関連付けられているプロセス名。 <br><br>たとえば UNIX では、syslog エントリを生成しているプロセスです。 |
| deviceTranslatedAddress | DeviceTranslatedAddress | IP ネットワーク内でイベントによって参照されている、変換後のデバイス アドレスを識別します。 <br><br>形式は Ipv4 アドレスです。 |
| dhost |DestinationHostName | IP ネットワーク内でイベントによって参照されている発信元。  <br>ノードを使用できる場合は、その形式を、送信先ノードに関連付けられている FQDN にする必要があります。 たとえば、`host.domain.com` または `host` です。 |
| dmac | DestinationMacAddress | 宛先の MAC アドレス (FQDN) |
| dntdom | DestinationNTDomain | 宛先アドレスの Windows ドメイン名。|
| dpid | DestinationProcessId |イベントに関連付けられている送信先プロセスの ID。|
| dpriv | DestinationUserPrivileges | 宛先で使用する特権を定義します。 <br>有効な値: `Admninistrator`、`User`、`Guest` |
| dproc | DestinationProcessName | イベントの送信先プロセスの名前 (`telnetd` や `sshd.` など)。 |
| dpt | DestinationPort | 送信先ポート。 <br>有効な値: `*0` - `65535` |
| dst | DestinationIP | IP ネットワーク内でイベントによって参照されている送信先の IpV4 アドレス。 |
| dtz | DeviceTimeZon | イベントを生成しているデバイスのタイムゾーン |
| duid |DestinationUserId | ID で宛先ユーザーを識別します。 |
| duser | DestinationUserName |名前で宛先ユーザーを識別します。|
| dvc | DeviceAddress | イベントを生成しているデバイスの IPv4 アドレス。 |
| dvchost | DeviceName | ノードを使用できる場合は、デバイス ノードに関連付けられている FQDN。 たとえば、`host.domain.com` または `host` です。| 
| dvcmac | DeviceMacAddress | イベントを生成しているデバイスの MAC アドレス。 |
| dvcpid | プロセス ID | イベントを生成しているデバイス上のプロセスの ID を定義します。 |

## <a name="e---i"></a>E から I

|CEF キー名  |CommonSecurityLog での名前  |説明  |
|---------|---------|---------|
|end     |  EndTime       | イベントに関連するアクティビティが終了した時刻。        |
|externalId    |   ExternalID      | 発信元デバイスによって使用されている ID。 一般に、これらの値は、イベントにそれぞれが関連付けられている、増加してゆく値です。        |
|fileCreateTime     |  FileCreateTime      | ファイルが作成された日時。        |
|fileHash     |   FileHash      |   ファイルのハッシュ。      |
|fileId     |   FileID      |ファイルに関連付けられている ID (inode など)。         |
| fileModificationTime | FileModificationTime |ファイルが最後に変更された日時。 |
| filePath | FilePath | ファイル名を含む、ファイルへの完全なパス。 たとえば、`C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` や `/usr/bin/zip` などです。|
| filePermission |FilePermission |ファイルのアクセス許可。 |
| fileType | FileType | パイプやソケットなどのファイルの種類。|
|fname | FileName| パスを含まないファイルの名前。 |
| fsize | FileSize | ファイルのサイズ。 |
|Host    |  Computer       | Syslog に記載されているホスト        |
|in     |  ReceivedBytes      |内側へ転送されたバイト数。         |
| | | |

## <a name="m---p"></a>M から P

|CEF キー名  |CommonSecurityLog での名前  |説明  |
|---------|---------|---------|
|msg   |  Message       | イベントに関する詳細情報を示すメッセージ。        |
|名前     |  アクティビティ       |   人間が判読でき、理解できるイベントの説明を表している文字列。     |
|oldFileCreateTime     |  OldFileCreateTime       | 古いファイルが作成された日時。        |
|oldFileHash     |   OldFileHash      |   古いファイルのハッシュ。      |
|oldFileId     |   OldFileId     |   古いファイルに関連付けられている、inode などの ID。      |
| oldFileModificationTime | OldFileModificationTime |古いファイルが最後に変更された日時。 |
| oldFileName |  OldFileName |古いファイルの名前。 |
| oldFilePath | OldFilePath | ファイル名を含む、古いファイルへの完全なパス。 <br>たとえば、`C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` または `/usr/bin/zip` です。|
| oldFilePermission | OldFilePermission |古いファイルのアクセス許可。 |
|oldFileSize | OldFileSize | 古いファイルのサイズ。|
| oldFileType | OldFileType | パイプやソケットなど、古いファイルのファイルの種類。|
| out | SentBytes | 外側へ転送されたバイト数。 |
| 結果 | 結果 | イベントの結果 (`success` や `failure` など)。|
|proto    |  Protocol       | 使用されているレイヤー 4 プロトコルを識別するトランスポート プロトコル。 <br><br>ありえる値として、`TCP` や `UDP` などのプロトコル名が含まれます。        |
| | | |

## <a name="r---t"></a>R から T

|CEF キー名  |CommonSecurityLog での名前  |説明  |
|---------|---------|---------|
|原因     |  理由      |監査イベントが生成された理由。 <br><br>たとえば、`Bad password` または `Unknown user` です。         |
|要求     |   RequestURL      | HTTP 要求のためにアクセスされる URL (プロトコルを含む)。 たとえば、`http://www/secure.com` のように指定します。        |
|requestClientApplication     |   RequestClientApplication      |   要求に関連付けられているユーザー エージェント。      |
| requestContext | RequestContext | HTTP 参照元など、要求送信元のコンテンツについて説明します。 |
| requestCookies | RequestCookies |要求に関連付けられている Cookie。 |
| requestMethod | RequestMethod | URL にアクセスするために使用されるメソッド。 <br><br>有効な値には、`POST` や `GET` などのメソッドが含まれます。 |
| rt | ReceiptTime | アクティビティに関連するイベントが受信された時刻。 |
|重大度     |  <a name="logseverity"></a> LogSeverity       |  イベントの重要度を記述する文字列または整数。<br><br> 有効な文字列値: `Unknown`、`Low`、`Medium`、`High`、`Very-High` <br><br>有効な整数値: `0`-`3` = 低、`4`-`6` = 中、`7`-`8` = 高、`9`-`10` = 非常に高い |
| shost    | SourceHostName        |IP ネットワーク内でイベントによって参照されている発信元を識別します。 ノードを使用できる場合は、形式を、ソース ノードに関連付けられている完全修飾ドメイン名 (DQDN) にする必要があります。 たとえば、`host` または `host.domain.com` です。 |
| smac | SourceMacAddress | 発信元 MAC アドレス。 |
| sntdom | SourceNTDomain | 発信元アドレスの Windows ドメイン名。 |
| sourceDnsDomain | SourceDnsDomain | 完全な FQDN の DNS ドメインの部分。 |
| sourceServiceName | SourceServiceName | イベントの生成を担っているサービス。 |
| sourceTranslatedAddress | SourceTranslatedAddress | IP ネットワーク内でイベントによって参照されている、変換後の発信元を識別します。 |
| sourceTranslatedPort | SourceTranslatedPort | 変換後の、ファイアウォールなどの発信元ポート。 <br>有効なポート番号は `0` - `65535` です。 |
| spid | SourceProcessId | イベントに関連付けられている発信元プロセスの ID。|
| spriv | SourceUserPrivileges | 発信元ユーザーの特権。 <br><br>有効な値には `Administrator`、`User`、`Guest` が含まれます。 |
| sproc | SourceProcessName | イベントの発信元プロセスの名前。|
| spt | SourcePort | 発信元ポート番号。 <br>有効なポート番号は `0` - `65535` です。 |
| src | SourceIP |IP ネットワーク内でイベントによって参照されている、IPv4 アドレスとしての発信元。 |
| start | StartTime | イベントによって参照されているアクティビティが開始された時刻。 |
| suid | SourceUserID | ID で発信元ユーザーを識別します。 |
| type | EventType | イベントの種類。 値は次のとおりです。 <br>- `0`: 基本イベント <br>- `1`: 集計済み <br>- `2`: 相関イベント <br>- `3`: アクション イベント <br><br>**注**: 基本イベントについてはこのイベントを省略できます。 |
| | | |

## <a name="unmapped-fields"></a>マップされていないフィールド

以下の **CommonSecurityLog** フィールド名には、CEF キーでのマッピングがありません。


|CommonSecurityLog でのフィールド名  |説明  |
|---------|---------|
|**OriginalLogSeverity**     |  常に空で、CiscoASA との統合でサポートされます。 <br>ログ重大度の値の詳細については、[LogSeverity](#logseverity) フィールドを参照してください。       |
|**RemoteIP**     |     リモート IP アドレス。 <br>この値は、可能な場合は [CommunicationDirection](#communicationdirection) フィールドに基づきます。     |
|**RemotePort**     |   リモート ポート。 <br>この値は、可能な場合は [CommunicationDirection](#communicationdirection) フィールドに基づきます。      |
|**SimplifiedDeviceAction**     |   [DeviceAction](#deviceaction) の値を、静的な値のセットに単純化しますが、[DeviceAction](#deviceaction) フィールドの元の値は保持します。 <br>例: `Denied` > `Deny`。      |
|     |         |


## <a name="next-steps"></a>次のステップ

詳細については、「[Common Event Format を使用して外部ソリューションを接続する](connect-common-event-format.md)」を参照してください。
