---
title: インクルード ファイル
description: インクルード ファイル
documentationcenter: partner-center-commercial-marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 08/16/2019
ms.author: dsindona
ms.custom: include file
ms.openlocfilehash: cf6a5730f92eba8f0a4e63bf40a4d2b50f76eb3a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131087"
---
パートナー センターを通じてオファーをマーケットプレースに公開する場合、オファーを CRM システムに接続する必要があります。 この方法により、顧客が関心を持ったり、製品をデプロイしたりしたときに、すぐに顧客の連絡先情報を受け取ることができます。

1. 顧客リードの送信先となるリードのターゲットを選択します。 次の CRM システムがサポートされています。

    * [Dynamics 365](../commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    * [Marketo](../commercial-marketplace-lead-management-instructions-marketo.md)
    * [Salesforce](../commercial-marketplace-lead-management-instructions-salesforce.md)

    ご使用の CRM システムがこの一覧で明示的にサポートされていない場合は、次のオプションを使用して顧客のリード データを格納できます。 その後、このデータを CRM システムにエクスポートまたはインポートできます。

    * [Azure テーブル](../commercial-marketplace-lead-management-instructions-azure-table.md)
    * [HTTPS エンドポイント](../commercial-marketplace-lead-management-instructions-https.md)

2. 選択されたリードのターゲットに関するこのリンクされたドキュメントを読み、マーケットプレース オファーからリードを受け取るようにリードのターゲットを設定する方法を確認してください。
3. パートナー センター内でオファーをマーケットプレースに公開するときに、オファーをリードのターゲットに接続します。 これを行う方法の詳細については、リンクされたドキュメントを参照してください。
4. リードのターゲットへの接続が適切に設定されていることを確認します。 リーダーのターゲットを適切に構成したら、パートナー センターでオファーに対して **[発行]** を選択します。 次に、接続を検証し、テスト リードを送信します。 公開前にオファーを確認したときに、プレビュー環境でオファーを自分で入手してみることで、リードの接続をテストすることもできます。
5. リードが失われないように、リード ターゲットへの接続が最新の状態に保たれていることを確認します。 ご自身で何かを変更したときは、必ずこれらの接続を更新してください。
