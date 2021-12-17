---
title: AS2 MDN 受信確認
description: Azure Logic Apps 内での AS2 メッセージのメッセージ処理通知 (MDN) 受信確認について説明します。
services: logic-apps
ms.suite: integration
author: S-Jindal
ms.author: shivamjindal
ms.reviewer: estfan, azla
ms.topic: reference
ms.date: 08/12/2021
ms.openlocfilehash: 510be5ca1343f115bcdf13c2d67b598b4d410564
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862845"
---
# <a name="mdn-acknowledgments-for-as2-messages-in-azure-logic-apps"></a>Azure Logic Apps 内での AS2 メッセージ の MDN 受信確認

Azure Logic Apps では、**AS2** 操作を使用するときに、電子データ交換 (EDI) 通信用の AS2 メッセージを処理するワークフローを作成できます。 EDI メッセージングでは、受信確認によって EDI インターチェンジの処理の状態が示されます。 インターチェンジを受信すると、[**AS2 デコード** アクション](logic-apps-enterprise-integration-as2.md#decode)により、メッセージ処理通知 (MDN) または受信確認を送信者に返すことができます。 MDN により、次の項目が確認されます。

* 受信側のパートナーが元のメッセージを正常に受信した。

  送信側のパートナーは、`MessageID`最初に送信されたメッセージ`original-message-id`を、受信側によって MDN に含められるフィールドと比較します。

* 受信側のパートナーにより、交換されたデータの整合性が確認された。

  メッセージ整合性チェック (MIC) または MIC ダイジェストは、最初に送信されたメッセージのペイロードから計算されます。 送信側のパートナーは、受信者が受信したメッセージのペイロードから計算し、署名されている場合に MDN の `Received-content-MIC` フィールドに含めた MIC をこの MIC と比較します。

  > [!NOTE]
  > MDN に署名することはできますが、暗号化または圧縮することはできません。

* 受信の否認防止

  送信側のパートナーは、受信側のパートナーの公開キーを使用して署名付きの MDN を比較し、MDN 内の返された MIC の値が、否認防止データベースに格納された元のメッセージ ペイロードの MIC と同じであることを確認します。

> [!NOTE]
> 応答内で MDN の送信を有効にすると、処理中にエラーが発生した場合でも、ロジック アプリは AS2 メッセージの処理の状態を報告するために MDN を返そうとします。 送信者が MDN を受信して確認するまで、AS2 転送は完了しません。
> 同期 MDN は、HTTP 応答 (`200 OK` 状態など) としても機能します。

このトピックでは、受信確認の生成に使用されるプロパティ、使用する MDN ヘッダー、MIC など、AS2 MDN ACK に関する簡単な概要について説明します。 その他の関連情報については、次のドキュメントを確認してください。

* [Azure Logic Apps で B2B エンタープライズ統合用の AS2 メッセージを交換する](logic-apps-enterprise-integration-as2.md)
* [AS2 メッセージの設定](logic-apps-enterprise-integration-as2-message-settings.md)
* [Azure Logic Apps とは](logic-apps-overview.md)

## <a name="mdn-generation"></a>MDN の生成

[AS2 デコード] アクションでは、アグリーメントの **[受信設定]** に **[MDN を送信する]** オプションが選択されている場合、パートナーの AS2 アグリーメントのプロパティに基づいて MDN が生成されます。 この場合、MDN の生成時にメッセージ ヘッダーの **AS2-From** プロパティが使用されますが、他のプロパティとその値はパートナーの AS2 アグリーメント設定から取得されます。

既定では、受信 AS2 メッセージ ヘッダーは、検証と MDN の生成に使用されます。 アグリーメントの検証と MDN の設定を代わりに使用するには、アグリーメントの **[受信設定]** で、 **[メッセージ プロパティをオーバーライドします]** を選択します。 または、このオプションが選択されていないか、アグリーメントが使用できない場合、AS2 デコード アクションでは代わりに受信メッセージ ヘッダーが使用されます。

## <a name="mdn-headers"></a>MDN のヘッダー

MDN を応答として AS2 メッセージに関連付けるには、`AS2-From` ヘッダー、`AS2-To` ヘッダー、および `MessageID` コンテキスト プロパティが使用されます。 MDN では、`Original-Message-ID` ヘッダーは、MDN が応答である AS2 メッセージの `Message-ID` ヘッダーから取得されます。 MDN には次のヘッダーが含まれます。

| ヘッダー | 説明 |
|---------|-------------|
| HTTP および AS2 | 詳細については、[AS2 メッセージの設定](logic-apps-enterprise-integration-as2-message-settings.md)に関する記事を参照してください。
| 転送層 | このヘッダーには、`Content-Type` ヘッダー ("signed multipart message" を含む)、MIC のアルゴリズム、署名の書式設定プロトコル、および最も外側のマルチパート境界サブヘッダーが含まれます。 |
| 第 1 部 | 署名付きマルチパート メッセージの第 1 部は、埋め込まれた MDN です。 この部分は人間が判読できます。 |
| 第 2 部 | 署名付きマルチパート メッセージの第 2 部は、デジタル署名、元のメッセージへの参照、処理の種類と状態、および MIC の値で構成されます。 この部分はコンピューターで読み取ることができます。 |
|||

## <a name="mic-digest"></a>MIC ダイジェスト

MIC ダイジェストまたは MIC では、MDN が最初に送信されたメッセージのペイロードに関連付けられていることが確認されます。 この MIC は、`Received-Content-MIC` 拡張フィールドの署名付きマルチパート MDN メッセージの第 2 部に含まれています。

MIC は base64 でエンコードされ、 **[MIC アルゴリズム]** プロパティによって決定されます。これは、AS2 アグリーメントの **[受信設定]** ページで **[MDN を送信する]** および **[署名付き MDN を送信する]** プロパティが選択されている場合に有効になります。 MIC 生成では、次のサポートされているハッシュ アルゴリズムから選択できます。

* SHA1
* MD5
* SHA2-256
* SHA2-384
* SHA2-512

たとえば、次のスクリーンショットは、AS2 アグリーメントの **[受信設定]** ページの MDN プロパティを示しています。

![AS2 アグリーメントの [受信設定] と MDN 受信確認設定が表示された Azure portal を示すスクリーンショット。](./media/logic-apps-enterprise-integration-as2-mdn-acknowledgment/mdn-ack-settings.png)

## <a name="next-steps"></a>次のステップ

* [AS2 メッセージの設定](logic-apps-enterprise-integration-as2-message-settings.md)
