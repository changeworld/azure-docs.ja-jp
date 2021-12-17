---
title: SNMP MIB の監視を設定する
description: SNMP を使用して、センサーの稼働状況の監視を実行できます。 センサーは、承認済みの監視サーバーから送信された SNMP クエリに応答します。
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: dc9baa68c39ec049f1ca717f1bb34a364d6bad10
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278723"
---
# <a name="set-up-snmp-mib-monitoring"></a>SNMP MIB の監視を設定する

簡易ネットワーク管理プロトコル (SNMP) を使用して、センサーの稼働状況の監視を実行できます。 センサーは、承認済みの監視サーバーから送信された SNMP クエリに応答します。 SNMP モニターは、センサーの OID を定期的にポーリングします (1 秒間に最大 50 回)。

サポートされている SNMP のバージョンは、SNMP v2 または SNMP v3 です。 SNMP は、ポート 161 (SNMP) で UDP をトランスポート プロトコルとして使用します。

SNMP 監視の構成を開始する前に、ファイアウォールでポート UDP 161 を開く必要があります。

## <a name="oids"></a>OID

| 管理コンソールとセンサー | OID | Format | 説明 |
|--|--|--|--|
| アプリケーション名 | 1.3.6.1.2.1.1.5.0 | STRING | オンプレミス管理コンソールのアプリケーション名 |
| ベンダー | 1.3.6.1.2.1.1.4.0 | STRING | Microsoft サポート (support.microsoft.com) |
| プラットフォーム | 1.3.6.1.2.1.1.1.0 | STRING | センサーまたはオンプレミス管理コンソール |
| シリアル番号 | 1.3.6.1.4.1.53313.1 |STRING | ライセンスが使用する文字列 |
| ソフトウェア バージョン | 1.3.6.1.4.1.53313.2 | STRING | Xsense のフルバージョン文字列と管理のフルバージョン文字列 |
| CPU 使用率 | 1.3.6.1.4.1.53313.3.1 | GAUGE32 | 0 から 100 で表示 |
| CPU 温度 | 1.3.6.1.4.1.53313.3.2 | STRING | Linux 入力に基づいて 0 から 100 で示される摂氏 マシンに物理的な温度センサーがない (VM など) 場合には、"センサーが見つかりません" が返されます。|
| メモリ使用量 | 1.3.6.1.4.1.53313.3.3 | GAUGE32 | 0 から 100 で表示 |
| ディスク使用量 | 1.3.6.1.4.1.53313.3.4 | GAUGE32 | 0 から 100 で表示 |
| サービスの状態 | 1.3.6.1.4.1.53313.5  |STRING | オンラインまたはオフライン (4 つの重要コンポーネントのいずれかが停止している場合) |
| サービスの状態 | 1.3.6.1.4.1.53313.5  |STRING | オンラインまたはオフライン (4 つの重要コンポーネントのいずれかが停止している場合) |
| ローカル/クラウド接続 | 1.3.6.1.4.1.53313.6   |STRING | センサーが Azure の Defender for IoT に接続されているか、オンプレミスでのみ管理されているかを示します |
| ライセンスの状態 | 1.3.6.1.4.1.53313.5  |STRING | アクティブ化ファイルの有効期限が切れているかどうかを示します |

   - 存在しないキーは、[スタック オーバーフロー](https://stackoverflow.com/questions/51419026/querying-for-non-existing-record-returns-null-with-http-200)に基づいて、null の HTTP 200 で応答します。
    
   - ハードウェア関連の MIB (CPU 使用率、CPU 温度、メモリ使用量、ディスク使用量) は、すべてのアーキテクチャと物理センサーでテストする必要があります。 仮想マシンの CPU 温度は対象ではありません。

センサーが受信するすべての SNMP クエリを含むログはダウンロードできます。これには、接続データやパケットからの生データも含まれます。

SNMP v2 の稼働状況の監視を定義するには:

1. サイド メニューで、 **[システム設定]** を選択します。

2. **[Active Discovery]\(アクティブ検出\)** ウィンドウで、 **[SNMP MIB Monitoring]\(SNMP MIB 監視\)** :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: を選択します。

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="SNMP ウィンドウを編集します。":::

3. **[Allowed Hosts]\(許可されるホスト\)** セクションで **[Add host]\(ホストの追加\)** を選択し、システムの稼働状況の監視を実行するサーバーの IP アドレスを入力します。

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="許可されるホストの IP アドレスを入力します。":::

4. **[認証]** セクションの **[SNMP v2 Community String]\(SNMP v2 コミュニティ文字列\)** ボックスに文字列を入力します。 SNMP コミュニティ文字列には、任意の英数字 (大文字、小文字、数字) の組み合わせを最大 32 文字含めることができます。 スペースは使用できません。

5. **[保存]** を選択します。

SNMP v3 の稼働状況の監視を定義するには:

1. サイド メニューで、 **[システム設定]** を選択します。

2. **[Active Discovery]\(アクティブ検出\)** ウィンドウで、 **[SNMP MIB Monitoring]\(SNMP MIB 監視\)** :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: を選択します。

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="SNMP ウィンドウを編集します。":::

3. **[Allowed Hosts]\(許可されるホスト\)** セクションで **[Add host]\(ホストの追加\)** を選択し、システムの稼働状況の監視を実行するサーバーの IP アドレスを入力します。

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="許可されるホストの IP アドレスを入力します。":::

4. **[認証]** セクションで、次のパラメーターを設定します。

    | パラメーター | 説明 |
    |--|--|
    | **ユーザー名** | SNMP ユーザー名には、任意の英数字 (大文字、小文字、数字) の組み合わせを最大 32 文字含めることができます。 スペースは使用できません。 <br /> <br />SNMP v3 認証のユーザー名は、システムと SNMP サーバーで構成する必要があります。 |
    | **パスワード** | 大文字と小文字が区別される認証パスワードを入力します。 認証パスワードには、8 文字から 12 文字までの任意の英数字 (大文字、小文字、数字) の組み合わせを含めることができます。 <br /> <br/>SNMP v3 認証のユーザー名は、システムと SNMP サーバーで構成する必要があります。 |
    | **認証タイプ** | MD5 または SHA を選択します。 |
    | **暗号化** | DES または AES を選択します。 |
    | **秘密鍵** | この鍵には、任意の英数字 (大文字、小文字、数字) の組み合わせを 8 文字で指定します。 |

5. **[保存]** を選択します。

## <a name="see-also"></a>関連項目

[トラブルシューティング ログのエクスポート](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
