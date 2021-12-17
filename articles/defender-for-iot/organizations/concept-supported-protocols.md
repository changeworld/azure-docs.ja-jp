---
title: Microsoft Defender for IoT によってサポートされているプロトコル
description: Microsoft Defender for IoT によってサポートされているプロトコルについて説明します。
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: ebfd983baf2e925fda66c5fc23edc380420db591
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305962"
---
# <a name="support-for-iot-ot-ics-and-scada-protocols"></a>IoT、OT、ICS、SCADA プロトコルのサポート

Microsoft Defender for IoT によって、オープンで相互運用可能な運用テクノロジ (OT) サイバーセキュリティ プラットフォームが提供されます。 Defender for IoT は、さまざまな場所にデプロイされ、あらゆる業界や地域で、要求の厳しい複雑な OT 環境へのデプロイにより、IoT、IT、および ICS のリスクが軽減されます。

## <a name="supported-protocols"></a>サポートされるプロトコル

Microsoft Defender for IoT では、多様なエンタープライズで幅広いプロトコルがサポートされており、すべての産業部門、エンタープライズ ネットワーク、およびビル管理システム (BMS) 環境にわたる工業オートメーション装置が含まれます。 カスタム プロトコルまたは独自プロトコルの場合、Microsoft では、カスタム プロトコル dissector をプラグインとして簡単に開発、テスト、およびデプロイするための SDK を提供しています。 この SDK では、プロトコルの設計方法や、機密情報を含む可能性のある PCAP の共有によってなど、独自の情報を危険にさらすことなく、このすべてが実行されます。 サポートされているプロトコルを次に示します。

### <a name="supported-protocols-passive-monitoring"></a>サポートされているプロトコル (パッシブ監視)

このセクションでは、パッシブ監視を使用して検出されるプロトコルの一覧を示します。

**ABB:** IEC61850 MMS (ABB 拡張機能を含む)

**ASHRAE:** BACnet、BACnet BACapp、BACnet BVLC

**Beckhoff:** AMS (ADS)、Twincat 

**Cisco:** CAPWAP コントロール、CAPWAP データ、CDP、LWAPP

**DNP. org:** DNP3

**Emerson:** DeltaV、Emerson OpenBSI/BSAP、Ovation DCS ADMD、Ovation DCS DPUSTAT、Ovation DCS SSRPC

**Emerson Fischer:** ROC

**Eurocontrol:** ASTERIX

**GE:** Bentley Nevada (System 1)、EGD、GSM (GE MarkVI and MarkVIe)、SRTP (GE)

**Honeywell:** ENAP、Experion DCS CDA、Experion DCS FDA

**IEC:** Codesys V3、ICCP TASE.2/IEC-60870、IEC60870-5 (IEC104/101)、IEC60870-5-103 (encapsulated serial)、IEC61850 GOOSE、IEC61850 MMS, IEC61850 SMV (SAMPLED-VALUES)、LonTalk (LonWorks)

**IEEE**: LLC、STP、VLAN

**IETF:** ARP、DCE RPC、DNS、FTP (FTP_ADAT、FTP_DATA)、GSSAPI (RFC2743)、HTTP、ICMP、IPv4、IPv6、LLDP、MDNS、NBNS、NTLM (NTLMSSP Auth Protocol)、RPC、SMB/Browse/NBDGM、SMB/CIFS、SNMP、SPNEGO (RFC4178)、SSH、Syslog、Telnet、TFTP、TPKT、UDP

**ISO:** CLNP (ISO 8473)、COTP (ISO 8073)、ISO Industrial Protocol、MQTT (IEC 20922)

**Medical:** ASTM、HL7

**Microsoft:** Horizon コミュニティ dissector、Horizon の独自 dissector (顧客によって開発)。 詳細については、「[Horizon の独自プロトコル dissector](references-horizon-sdk.md)」を参照してください。

**Mitsubishi:** Melsoft/Melsec (Mitsubishi Electric)

**Omron:** FINS

**Oracle:** TDS、TNS

**Rockwell Automation:** ENIP、EtherNet/IP CIP (Rockwell 拡張機能を含む)、EtherNet/IP CIP FW バージョン 27 以上

**Schneider Electric:** Modbus/TCP、Modbus TCP-Schneider Unity Extensions、OASYS (Schneider Electric Telvant) 

**Schneider Electric/Invensys:** Foxboro Evo、Foxboro I/A、Trident、TriGP、TriStation

**Schneider Electric/Modicon:** Modbus RTU

**Schneider Electric/Wonderware:** Wonderware Suitelink

**Siemens:** CAMP、PCS7、PCS7 WinCC - Historian、Profinet DCP、Profinet Realtime、Siemens PHD、Siemens S7、Siemens S7-Plus、Siemens S7-Plus、Siemens SICAM、Siemens WinCC

**Toshiba:** Toshiba Computer Link

**Yokogawa:** Centum ODEQ (Centum/ProSafe DCS)、HIS Equalize、Vnet/IP

### <a name="supported-protocols-active-monitoring"></a>サポートされているプロトコル (アクティブ監視)

このセクションでは、ping スイープやクエリなど、アクティブなプローブを使用して検出されるプロトコルの一覧を示します。

**IETF:** ping スイープ、SNMP ネットワーク レイアウト クエリ、SNMP クエリ

**Microsoft:** Windows WMI クエリ (WMI/WinRM が必要): ハードウェア、BIOS、バージョン、ソフトウェア、パッチ

**Rockwell Automation:** ENIP クエリ、ENIP スキャン、EtherNet/IP CIP (CIP クエリ)

**Siemens:** Siemens S7

## <a name="quickly-add-support-for-proprietary-restricted-protocols"></a>独自の制限付きプロトコルのサポートを迅速に追加する

デジタル化により、数十億の IoT デバイスのデプロイが推進され、続いて、IT と OT のネットワーク間の接続が大幅に増加しています。 これは、産業制御システムに対する危険なサイバー攻撃のリスクの拡大など、攻撃対象領域が拡大していることを意味します。

攻撃対象領域を減らすために、Horizon Protocol SDK により、IoT および ICS 環境で使用されるすべてのプロトコルを迅速かつ安全にサポートできます。

Horizon により、お客様とテクノロジ パートナー向けに次のソリューションが提供されます。

- 一般的、独自、カスタムのプロトコル、またはすべての標準に逸脱したプロトコルの無制限で完全なサポート。

- DPI 開発における新しいレベルの柔軟性と範囲。

- Defender for IoT プラットフォームのバージョンをアップグレードすることなく、OT の可視性と制御を指数的に拡張するツール。

- 機密情報を危険にさらすことなく、独自の開発を可能にするセキュリティ。

Horizon SDK を使用して、トラフィックのディープ パケット インスペクション (DPI) を有効にし、リアルタイムで脅威を検出するプラグインを作成できます。 Horizon SDK により、追加のカスタマイズも可能になります。 たとえば、Horizon SDK では、資産のベンダー、パートナー、またはプラットフォーム所有者が、アラート、イベント、およびプロトコル パラメーターのテキストをローカライズおよびカスタマイズできます。

[![Horizon SDK により、IoT および ICS 環境で使用されている 100% のプロトコルを迅速にサポートできます。](media/concept-supported-protocols/sdk-horizon.png)](media/concept-supported-protocols/sdk-horizon-expanded.png#lightbox)

## <a name="collaborate-with-the-horizon-community"></a>Horizon コミュニティとの共同作業

デジタル変革や、プロトコルのサポートに対する業界全体のコラボレーションに向けて先導するコミュニティにご参加ください。 Horizon ICS コミュニティでは、重要なインフラストラクチャ、ビル管理、生産ライン、輸送システムにおける分野の専門家やその他の産業リーダーが知識を共有できます。

コミュニティでは、チュートリアル、ディスカッション フォーラム、インストラクター主導のトレーニング、教育用ホワイト ペーパー、ウェビナーなどが提供されています。

ここから、ぜひコミュニティにご参加ください。<horizon-community@microsoft.com>

## <a name="next-steps"></a>次のステップ

[Horizon の独自プロトコル dissector](references-horizon-sdk.md) に関する詳細を確認する。

[Horizon API](references-horizon-api.md) を確認する。
