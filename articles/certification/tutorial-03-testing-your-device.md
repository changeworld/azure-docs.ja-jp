---
title: Azure Certified Device プログラム - チュートリアル - デバイスをテストする
description: Azure Certified Device ポータルで AICS サービスを使用してデバイスをテストするためのステップ バイ ステップ ガイド
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: ef82d44ef44189c0430ba9789baf3279fbe49a9c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310956"
---
# <a name="tutorial-test-and-submit-your-device"></a>チュートリアル: デバイスをテストして提出する

認定プロセスにおける次の主要フェーズとして、デバイスをテストする必要があります (これはデバイスの詳細を追加する前に行うこともできます)。 ポータルから Azure IoT Certification Service (AICS) を使用し、Microsoft の認定要件に従って、デバイスのパフォーマンスを証明します。 無事テスト フェーズを通過したら、Azure 認定チームによる最終的なレビューと承認を受けるためにデバイスを提出します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Device Provisioning Service (DPS) を使用してデバイスを IoT Hub に接続する
> * 選択した認定プログラムに従ってデバイスをテストする
> * Azure 認定チームのレビューを受けるためにデバイスを提出する

## <a name="prerequisites"></a>前提条件

- [Azure Certified Device ポータル](https://certify.azure.com)にサインインして、そこでデバイス用のプロジェクトを作成する必要があります。 詳細については、[チュートリアル](tutorial-01-creating-your-project.md)を参照してください。
- (省略可) デバイスを準備し、そのパフォーマンスを認定要件に従って手動で検証することをお勧めします。 これは、別のデバイス コードまたは認定プログラムを使用して再テストを実施したい場合、新しいプロジェクトを作成する必要があるためです。

## <a name="connecting-your-device-using-dps"></a>DPS を使用してデバイスを接続する

すべての認定デバイスについて、DPS を使用して IoT Hub に接続できることを実証する必要があります。 次の手順は、ポータルからテスト対象のデバイスを正しく接続する方法を示したものです。

1. テスト フェーズを開始するには、プロジェクトの概要ページの [`Connect & test`] リンクを選択します。  

    ![接続とテストのリンク](./media/images/connect-and-test-link.png)

1. 選択した証明書に応じて、[接続とテスト] ページに必要なテストが表示されます。 これらを見て適切な認定プログラムに申請していることを確認してください。  

    ![接続とテストのページ](./media/images/connect-and-test.png)

1. Device Provisioning Service (DPS) を使用してデバイスを IoT Hub に接続します。 DPS は、対称キー、X.509 証明書、トラステッド プラットフォーム モジュール (TPM) の各接続オプションをサポートしています。 これはすべての認定で必要になります。

    - "*DPS を使用して Azure IoT Hub にデバイスを接続する方法について詳しくは、[デバイスのプロビジョニングの概要](../iot-dps/about-iot-dps.md &quot;Device Provisioning Service の概要")に関するページを参照してください。* "
    
1. 対称キーを使用する場合、指定された DPS ID スコープ、デバイス ID、認証キー、DPS エンドポイントを使用して DPS を構成するように求められます。 それ以外の場合、X.509 証明書または保証キーを入力するように求められます。

1. DPS を使用してデバイスを構成したら、ページ下部の [`Connect`] ボタンをクリックして接続を確認します。 接続に成功したら、[`Next`] ボタンをクリックしてテスト フェーズに進むことができます。  

    ![接続と接続テスト](./media/images/connected.png)

## <a name="testing-your-device"></a>デバイスをテストする

AICS へのデバイスの接続に成功したら、申請する認定プログラムに固有の認定テストをいつでも実行することができます。

1. **Azure Certified Device 認定の場合**: [Select device capability]\(デバイス機能の選択\) タブで、デバイスに対して実行したいテストを確認して選択します。
1. **IoT Plug and Play 認定の場合**: デバイス モデルで宣言したテスト中にチェックされるパラメーターを慎重に確認します。
1. **Edge Managed 認定の場合**: 接続を実証する以外に別途手順は必要ありません。
1. 特定の認定プログラムに必要な準備が完了したら、[`Next`] を選択して "テスト" フェーズに進みます。
1. ページ上の [`Run tests`] を選択して、デバイスに対する AICS の実行を開始します。
1. テストに合格したという通知が届いたら、[`Finish`] を選択して概要ページに戻ります。

![テスト成功](./media/images/test-pass.png)

7. その他、AICS について質問がある場合やトラブルシューティングのサポートが必要な場合は、トラブルシューティング ガイドを参照してください。

> [!NOTE]
> IoT Plug and Play と Edge Managed のオンライン認定プロセスは、デバイスを提出して手動でのレビューを受けなくても完了できますが、自動サービスによるテスト範囲を越えるデバイス検証を目的に、Azure Certified Device チームのメンバーから問い合わせがある場合があります。

## <a name="submitting-your-device-for-review"></a>レビューを受けるためにデバイスを提出する

[Device details]\(デバイスの詳細\) セクションですべての必須フィールドに情報を入力し、"接続とテスト" プロセスで自動テストに合格したら、認定レビューを受ける準備が整った旨を Azure Certified Device チームに伝えることができます。

1. プロジェクトの概要ページで [`Submit for review`] を選択します。  

    ![[Review and Certify]\(レビューと認定\) リンク](./media/images/review-and-certify.png)

1. ポップアップ ウィンドウで申請を確認します。 デバイスの提出後は、編集がリクエストされるまで、デバイスの詳細はすべて読み取り専用となります。 ([発行後にデバイス情報を編集する方法](./how-to-edit-published-device.md)に関するページを参照してください。)  

    ![[認定レビューの開始] ダイアログ](./media/images/start-certification-review.png)

1. プロジェクトが提出されると、プロジェクトが Azure 認定チームによるレビュー中 (`Under Certification Review`) である旨が、プロジェクトの概要ページに表示されます。  

    ![レビュー中](./media/images/review-and-certify-under-review.png)

1. 5 から 7 営業日以内に、デバイスの申請ステータスをお知らせするメールが、Azure 認定チームから、企業情報に入力されたアドレス宛てに届きます。

    - 申請承認済み  
        プロジェクトがレビューされて承認されると、メールが届きます。 このメールには、Azure Certified Device バッジや、バッジの使用ガイドラインなど、デバイスが認定されたというメッセージを広める方法についての情報を含んだ一連のファイルが添付されます。 おめでとうございます。

    - 申請待ち  
        プロジェクトが承認されなかった場合、プロジェクトの詳細に変更を加え、準備が整った段階で、認定を受けるためにデバイスを再提出することができます。 プロジェクトが承認されなかった理由と認定のために再提出する手順が記載されたメールが届きます。

## <a name="next-steps"></a>次のステップ

お疲れさまでした。 デバイスは、すべてのテストに無事合格し、Azure Certified Device プログラムを通じて承認されました。 Azure Certified Device カタログにデバイスを発行できるようになりました。顧客は、そのカタログを通じて Azure における貴社の製品のパフォーマンスを信頼し、それを購入することができます。
> [!div class="nextstepaction"]
> [チュートリアル: デバイスを発行する](tutorial-04-publishing-your-device.md)

