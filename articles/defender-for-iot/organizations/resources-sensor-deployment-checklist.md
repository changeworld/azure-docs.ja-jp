---
title: Azure Defender for IoT のデプロイ前のチェックリスト
description: この記事では、サイトを準備するときに、デプロイ前に使用する必要がある情報とチェックリストを提供します。
ms.date: 07/18/2021
ms.topic: checklist
ms.openlocfilehash: ac60e574a3d61bfa0c3106375d0606b7de6d9494
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2021
ms.locfileid: "114676952"
---
# <a name="pre-deployment-checklist-overview"></a>デプロイ前のチェックリストの概要

この記事では、オンボードが正常に行われるようにサイトを準備するときに、デプロイ前に使用する必要がある情報とチェックリストを提供します。

- Defender for IoT 物理センサーは、レイヤー 1 と 2 (場合によってはレイヤー 3 も) 間の産業用通信を監視する、マネージド スイッチに接続する必要があります。
- このセンサーは、スイッチ ミラー ポート (SPAN ポート) または TAP でリッスンします。
- 管理ポートは、SSL を使用してビジネスまたは企業のネットワークに接続されています。

## <a name="checklist"></a>チェック リスト

産業用ネットワーク図の概要を把握することで、サイト エンジニアは Azure Defender for IoT 機器の適切な場所を定義できます。

### <a name="1-global-network-diagram"></a>1. グローバル ネットワーク図

グローバル ネットワーク図は、産業用 OT 環境の図を提供します

:::image type="content" source="media/resources-sensor-deployment-checklist/purdue-model.png" alt-text="これは、セットアップで Azure Defender for IoT センサーが当てはまる場所です。":::

:::image type="content" source="media/resources-sensor-deployment-checklist/backbone-switch.png" alt-text="これは、Purdue モデルで Azure Defender for IoT センサーが当てはまる場所です。":::

> [!Note] 
> Defender for IoT アプライアンスは、スイッチのポート間のトラフィックを認識する下位レベルのスイッチに接続する必要があります。 

### <a name="2-committed-devices"></a>2. コミット済みデバイス

監視するネットワーク デバイスの概数を指定します。 この情報は、Azure Defender for IoT ポータルへのサブスクリプションをオンボードするときに必要になります。 オンボード プロセス中に、デバイス数を 1,000 単位で入力するように求められます。

### <a name="3-optional-subnet-list"></a>3. (省略可能) サブネット リスト 

運用ネットワークのサブネット リストを提供します。

| **#** | **サブネット名** | **説明** |
|--|--|--|
| 1 |  |  |
| 2 |  |  |
| 3 |  |  |
| 4 |  |  |

### <a name="4-vlans"></a>4. VLAN

運用ネットワークの VLAN リストを用意します。

| **#** | **VLAN 名** | **説明** |
|--|--|--|
| 1 |  |  |
| 2 |  |  |
| 3 |  |  |
| 4 |  |  |

### <a name="5-switch-models-and-mirroring-support"></a>5. スイッチのモデルとミラーリングのサポート

スイッチにポート ミラーリング機能があることを確認するには、Defender for IoT プラットフォームで接続する必要があるスイッチのモデル番号を指定します。

| **#** | **スイッチ** | **Model** | **トラフィックのミラーリングのサポート (SPAN、RSPAN、またはなし)** |
|--|--|--|--|
| 1 |  |  |
| 2 |  |  |
| 3 |  |  |
| 4 |  |  |

### <a name="6-third-party-switch-management"></a>6. サードパーティ製スイッチの管理

サード パーティではスイッチを管理していますか? Y または N 

「はい」の場合、だれですか? __________________________________ 

そのポリシーは何ですか? __________________________________ 

### <a name="7-serial-connection"></a>7. シリアル接続

ネットワークのシリアル接続を介して通信するデバイスはありますか? はい、いいえ 

「はい」の場合、そのシリアル通信プロトコルか記入してください: ________________ 

「はい」の場合、ネットワーク図に、どのデバイスがシリアル プロトコルと通信し、それらがどこにあるかを示してください。

*マークされたシリアル接続を含むネットワーク図を追加します。*

### <a name="8-vendors-and-protocols-industrial-equipment"></a>8. ベンダーとプロトコル (産業機器)

産業機器の製造元とプロトコルの一覧を用意する。 (オプション)。

| **#** | **ベンダー名** | **通信プロトコル** |
|--|--|--|
| 1 |  |  |
| 2 |  |  |
| 3 |  |  |
| 4 |  |  |

次に例を示します。

- Siemens

- Rockwell Automation - イーサネットまたは IP

- Emerson – DeltaV、Ovation

### <a name="9-qos"></a>9. QoS

QoS では、センサーの既定の設定は 1.5 Mbps です。 これを変更する場合は記入してください: ________________ 

   事業単位 (BU): ________________

### <a name="10-sensor"></a>10. センサー  

センサー アプライアンスは、ネットワーク アダプターを介してスイッチの SPAN ポートに接続されています。 これは、もう 1 つの専用ネットワーク アダプターを介して管理するために、顧客の企業のネットワークに接続されています。

企業のネットワークに接続されるセンサー NIC のアドレスの詳細を記入してください。 

| 項目 | アプライアンス 1 | アプライアンス 2 | アプライアンス 3 |
|--|--|--|--|
| アプライアンスの IP アドレス |  |  |  |
| Subnet |  |  |  |
| デフォルト ゲートウェイ |  |  |  |
| DNS |  |  |  |
| ホスト名 |  |  |  |

### <a name="11-idraciloserver-management"></a>11. iDRAC/iLO/サーバー管理

| 項目 | アプライアンス 1 | アプライアンス 2 | アプライアンス 3 |
|--|--|--|--|
| アプライアンスの IP アドレス |  |  |  |
| Subnet |  |  |  |
| デフォルト ゲートウェイ |  |  |  |
| DNS |  |  |  |

### <a name="12-on-premises-management-console"></a>12. オンプレミスの管理コンソール  

| 項目 | アクティブ | パッシブ (HA を使用する場合) |
|--|--|--|
| IP アドレス |  |  |
| Subnet |  |  |
| デフォルト ゲートウェイ |  |  |
| DNS |  |  |

### <a name="13-snmp"></a>13. SNMP  

| 項目 | 詳細 |
|--|--|
| IP |  |
| IP アドレス |  |
| ユーザー名 |  |
| Password |  |
| 認証の種類 | MD5 または SHA |
| 暗号化 | DES または AES |
| 秘密鍵 |  |
| SNMP v2 コミュニティ文字列 |

### <a name="14-ssl-certificate"></a>14. SSL 証明書

VPN デバイスを使用する予定はありますか? はい、いいえ

「はい」の場合は、どのサービスを使用して生成しますか? 証明書にはどの属性を含めますか (ドメインや IP アドレスなど)?

### <a name="15-smtp-authentication"></a>15. SMTP 認証

電子メール サーバーにアラートを転送するために SMTP を使用する予定ですか? はい、いいえ

「はい」の場合は、どの認証方法を使用しますか?  

### <a name="16-active-directory-or-local-users"></a>16. Active Directory またはローカル ユーザー

Active Directory 管理者に連絡して、Active Directory サイト ユーザー グループを作成するか、ローカル ユーザーを作成してください。 ユーザーにデプロイ日の準備ができていることを確認してください。

### <a name="17-iot-device-types-in-the-network"></a>17. ネットワーク内の IoT デバイスの種類

| デバイスの種類 | ネットワークブ内のデバイスの数 | 平均帯域幅 |
|--|--|--|
| 例: カメラ |  |
| 例: X 線機器 |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |

## <a name="next-steps"></a>次のステップ

[Azure Defender for IoT のネットワーク設定について](how-to-set-up-your-network.md)

[Defender for IoT のインストールについて](how-to-install-software.md)
