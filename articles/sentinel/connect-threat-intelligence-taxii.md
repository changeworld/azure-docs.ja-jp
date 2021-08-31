---
title: Azure Sentinel を STIX/TAXII 脅威インテリジェンス フィードに接続する | Microsoft Docs
description: 脅威インジケーターをインポートするために、Azure Sentinel を業界標準の脅威インテリジェンス フィードに接続する方法について説明します。
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2021
ms.author: yelevin
ms.openlocfilehash: ce1d2fafd330848c19dc5386fad11d87bfe2670a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732530"
---
# <a name="connect-azure-sentinel-to-stixtaxii-threat-intelligence-feeds"></a>Azure Sentinel を STIX/TAXII 脅威インテリジェンス フィードに接続する

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

「[脅威インテリジェンス プラットフォーム (TIP) を Azure Sentinel に接続する](connect-threat-intelligence-tip.md)」**も参照してください**

脅威インテリジェンスの送信に最も広く採用されている業界標準は、[STIX データ形式と TAXII プロトコルの組み合わせ](https://oasis-open.github.io/cti-documentation/)です。 組織が現在の STIX/TAXII バージョン (2.0 または 2.1) をサポートするソリューションから脅威インジケーターを受け取る場合、**脅威インテリジェンス - TAXII データ コネクタ** を使用して、脅威インジケーターを Azure Sentinel に取り込むことができます。 このコネクタを使用すると、Azure Sentinel の組み込み TAXII クライアントで、TAXII 2.x サーバーから脅威インテリジェンスをインポートできます。

:::image type="content" source="media/connect-threat-intelligence-taxii/threat-intel-taxii-import-path.png" alt-text="TAXII のインポート パス":::

Azure Sentinel の[脅威インテリジェンス](understand-threat-intelligence.md)の詳細、特に、Azure Sentinel と統合できる [TAXII 脅威インテリジェンス フィード](threat-intelligence-integration.md#taxii-threat-intelligence-feeds)について確認してください。

## <a name="prerequisites"></a>前提条件  

- 脅威インジケーターを格納する Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。
- TAXII 2.0 または TAXII 2.1 の **API ルート URI** と **コレクション ID** が必要です。

## <a name="instructions"></a>Instructions

TAXII サーバーから Azure Sentinel に STIX 形式の脅威インジケーターをインポートするには、次の手順に従います。

1. TAXII サーバー API ルートとコレクション ID を取得する

1. Azure Sentinel で脅威インテリジェンス - TAXII データ コネクタを有効にする

### <a name="get-the-taxii-server-api-root-and-collection-id"></a>TAXII サーバー API ルートとコレクション ID を取得する

TAXII 2.x サーバーによって、脅威インテリジェンスのコレクションをホストする URL である API ルートが公開されています。 通常、API ルートとコレクション ID は、TAXII サーバーをホスティングしている脅威インテリジェンス プロバイダーのドキュメント ページで確認できます。 

> [!NOTE]
> 場合によっては、プロバイダーは検出エンドポイントと呼ばれる URL のみを公開します。 [以下で詳しく説明](#find-the-api-root)されているように、cURL ユーティリティを使用して、検出エンドポイントを参照し、API ルートを要求できます。

### <a name="enable-the-threat-intelligence---taxii-data-connector-in-azure-sentinel"></a>Azure Sentinel で脅威インテリジェンス - TAXII データ コネクタを有効にする

TAXII サーバーから Azure Sentinel に脅威インジケーターをインポートするには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) で **Azure Sentinel** サービスに移動します。

1. TAXII サーバーからの脅威インジケーターのインポート先となる **ワークスペース** を選択します。

1. メニューから **[データ コネクタ]** を選択し、コネクタ ギャラリーから **[脅威インテリジェンス - TAXII]** を選択して、 **[Open connector page]\(コネクタ ページを開く\)** ボタンをクリックします。

1. この TAXII サーバー コレクションの **フレンドリ名** と、**API ルート URL**、**コレクション ID**、**ユーザー名** (必要な場合)、**パスワード** (必要な場合) を入力し、インジケーターのグループと必要なポーリング頻度を選択します。 **[追加]** をクリックします。

    :::image type="content" source="media/connect-threat-intelligence-taxii/threat-intel-configure-taxii-servers.png" alt-text="TAXII サーバーを構成する":::
 
TAXII サーバーへの接続が正常に確立されたことを示す確認メッセージが表示されます。1 つ以上の TAXII サーバーから複数のコレクションに接続するために、必要な回数だけ上記の最後の手順を繰り返すことができます。

数分以内に、脅威インジケーターが Azure Sentinel ワークスペースに送られるようになります。 新しいインジケーターは、Azure Sentinel ナビゲーション メニューからアクセスできる **[脅威インテリジェンス]** ブレードで確認できます。

### <a name="find-the-api-root"></a>API ルートを見つける

以下に、Windows およびほとんどの Linux ディストリビューションで提供されている [cURL](https://en.wikipedia.org/wiki/CURL) コマンド ライン ユーティリティを使用して、検出エンドポイントのみを指定して API ルートの検出と TAXII サーバーのコレクションの参照を行う方法の例を示します。 [Anomali Limo](https://www.anomali.com/community/limo) ThreatStream TAXII 2.0 サーバーの検出エンドポイントを使用すると、API ルート URI を要求し、その後、コレクションを要求できます。

1.  API ルートを取得するには、ブラウザーで ThreatStream TAXII 2.0 サーバーの検出エンドポイント (https://limo.anomali.com/taxii ) に移動します。 ユーザー名とパスワード `guest` を使用して認証します。

    次の応答が表示されます。

    ```json
    {
        "api_roots":
        [
            "https://limo.anomali.com/api/v1/taxii2/feeds/",
            "https://limo.anomali.com/api/v1/taxii2/trusted_circles/",
            "https://limo.anomali.com/api/v1/taxii2/search_filters/"
        ],
        "contact": "info@anomali.com",
        "default": "https://limo.anomali.com/api/v1/taxii2/feeds/",
        "description": "TAXII 2.0 Server (guest)",
        "title": "ThreatStream Taxii 2.0 Server"
    }
    ```

2.  API ルートでホスティングされているコレクション ID の一覧を参照するには、cURL ユーティリティと前の応答の API ルート (https://limo.anomali.com/api/v1/taxii2/feeds/) ) を使用します。ただし、この API ルートに「`collections/` 」を追加します。

    ```json
    curl -u guest https://limo.anomali.com/api/v1/taxii2/feeds/collections/
    ```
    パスワード「guest」を使用して再度認証した後、次の応答が表示されます。

    ```json
    {
        "collections":
        [
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "107",
                "title": "Phish Tank"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "135",
                "title": "Abuse.ch Ransomware IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "136",
                "title": "Abuse.ch Ransomware Domains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "150",
                "title": "DShield Scanning IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "200",
                "title": "Malware Domain List - Hotlist"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "209",
                "title": "Blutmagie TOR Nodes"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "31",
                "title": "Emerging Threats C&C Server"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "33",
                "title": "Lehigh Malwaredomains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "41",
                "title": "CyberCrime"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "68",
                "title": "Emerging Threats - Compromised"
            }
        ]
    }
    ```

これで、Anomali Limo によって提供される 1 つ以上の TAXII サーバー コレクションに Azure Sentinel を接続するために必要なすべての情報が得られました。

| **API ルート** (https://limo.anomali.com/api/v1/taxii2/feeds/) | コレクション ID |
| ------------------------------------------------------------ | ------------: |
| **Phish Tank**                                               | 107           |
| **Abuse.ch Ransomware IPs**                                  | 135           |
| **Abuse.ch Ransomware Domains**                              | 136           |
| **DShield Scanning IPs**                                     | 150           |
| **Malware Domain List - Hotlist**                            | 200           |
| **Blutmagie TOR Nodes**                                      | 209           |
| **Emerging Threats C&C Server**                              |  31           |
| **Lehigh Malwaredomains**                                    |  33           |
| **CyberCrime**                                               |  41           |
| **Emerging Threats - Compromised**                           |  68           |
|

## <a name="next-steps"></a>次のステップ

このドキュメントでは、TAXII プロトコルを使用して Azure Sentinel を脅威インテリジェンス フィードに接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](./detect-threats-built-in.md)の概要。
