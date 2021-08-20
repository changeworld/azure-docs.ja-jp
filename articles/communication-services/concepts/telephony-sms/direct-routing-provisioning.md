---
title: Azure ダイレクト ルーティングのプロビジョニングと構成 - Azure Communication Services
description: セッション ボーダー コントローラーを追加し、Azure Communication Services のダイレクト ルーティング用に音声ルーティングを構成する方法について説明します
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 15e6c2383539a1b7c119346ea0624a01964f2364
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2021
ms.locfileid: "113762756"
---
# <a name="session-border-controllers-and-voice-routing"></a>セッション ボーダー コントローラーと音声ルーティング
Azure Communication Services のダイレクト ルーティングを使用すると、既存のテレフォニー インフラストラクチャを Azure に接続できます。 この記事では、サポートされているセッション ボーダー コントローラー (SBC) をダイレクト ルーティングに接続するために必要な手順の概要と、対応する通信リソースで音声ルーティングがどのように機能するかについて説明します。 

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]
 
Azure Communication Services のダイレクト ルーティングが実際の組織に適したソリューションであるかどうかについては、[Azure のテレフォニーの概念](./telephony-concept.md)に関する記事を参照してください。 デプロイの前提条件と計画の詳細については、[Communication Services のダイレクト ルーティング インフラストラクチャの要件](./sip-interface-infrastructure.md)に関する記事を参照してください。

## <a name="connect-the-sbc-with-azure-communication-services"></a>SBC を Azure Communication Services に接続する

### <a name="configure-using-azure-portal"></a>Azure portal を使用して構成する 
1. 左側のナビゲーションで、[Voice Calling - PSTN]\(音声通話 - PSTN\) の下にある [直接ルーティング] を選択し、[Session Border Controller]\(セッション ボーダー コントローラー\) タブで [構成] を選択します。
1. SBC の完全修飾ドメイン名とシグナリング ポートを入力します。
 
Office 365 を使用する場合は、SBC の FQDN のドメイン部分が、Office 365 管理ポータルの [ドメイン] に登録したドメインと異なっていることを確認してください。 
- たとえば、`contoso.com` が O365 での登録済みドメインである場合、Communication Services には `sbc.contoso.com` を使用できません。 ただし、上位レベルのドメインは、それが O365 に存在しなければ使用できます。`acs.contoso.com` ドメインを作成し、SBC 名として FQDN `sbc.acs.contoso.com` を使用することができます。
- SBC の証明書は、その名前と一致している必要があります。ワイルドカードの証明書はサポートされています。
- *.onmicrosoft.com ドメインは、SBC のFQDN には使用できません。
すべての要件の一覧については、「[Azure ダイレクト ルーティング インフラストラクチャの要件](./sip-interface-infrastructure.md)」を参照してください。

   :::image type="content" source="../media/direct-routing-provisioning/add-session-border-controller.png" alt-text="セッション ボーダー コントローラーの追加。":::
- 終了したら [次へ] をクリックします。
すべてが正しく設定されたら、Microsoft とセッション ボーダー コントローラーの間での OPTIONS メッセージの交換が確認されるはずです。接続を検証するには SBC 監視/ログを使用します。

## <a name="voice-routing-considerations"></a>音声ルーティングに関する考慮事項

Azure Communication Services のダイレクト ルーティングには、呼び出された数値パターンに基づいて呼び出しが特定のセッション ボーダー コントローラー (SBC) に送信されるようにするというルーティング メカニズムがあります。
リソースにダイレクト ルーティング構成を追加すると、このリソースのインスタンス (ID) から行われたすべての呼び出しでは、ダイレクト ルーティング トランクが最初に試みられます。 このルーティングは、ダイヤル番号と、リソース用に構成された音声ルート内の一致に基づきます。 一致がある場合、呼び出しはダイレクト ルーティング トランク内を通過します。 一致がない場合、次の手順として、callAgent.startCall メソッドの alternateCallerId パラメーターが処理されます。 リソースが音声通話 (PSTN) に対応していて、Microsoft から購入した番号を少なくとも 1 つ持っている場合、また、alternateCallerId がリソース用に購入された番号の 1 つと一致している場合、呼び出しは、Microsoft インフラストラクチャを使用して音声通話 (PSTN) を経由してルーティングされます。 購入した番号のどれにも alternateCallerId パラメーターが一致しない場合、呼び出しは失敗します。 次の図は、Azure Communication Services の音声ルーティングのロジックを示しています。

:::image type="content" source="../media/direct-routing-provisioning/voice-routing-diagram.png" alt-text="Communication Services の発信音声ルーティング。":::

## <a name="voice-routing-examples"></a>音声ルーティングの例
次の例では、呼び出しフローでの音声ルーティングを示します。

### <a name="one-route-example"></a>1 つのルートの例:
パターン `^\+1(425|206)(\d{7})$` を使用して 1 つの音声ルートを作成し、それに `sbc1.contoso.biz` と `sbc2.contoso.biz` を追加した場合、ユーザーが `+1 425 XXX XX XX` または `+1 206 XXX XX XX` に対して呼び出しを行うと、その呼び出しは最初に SBC `sbc1.contoso.biz` または `sbc2.contoso.biz` にルーティングされます。 どちらの SBC も使用できない場合、呼び出しは削除されます。

### <a name="two-routes-example"></a>2 つのルートの例:
パターン `^\+1(425|206)(\d{7})$` を使用して 1 つの音声ルートを作成し、それに `sbc1.contoso.biz` と `sbc2.contoso.biz` を追加し、さらに `sbc3.contoso.biz` と `sbc4.contoso.biz` で同じパターンを使用して 2 番目のルートを作成した場合です。 この場合、ユーザーが `+1 425 XXX XX XX` または `+1 206 XXX XX XX` に対して呼び出しを行うと、その呼び出しは最初に SBC `sbc1.contoso.biz` または `sbc2.contoso.biz` にルーティングされます。 sbc1 と sbc2 がどちらも使用できない場合、優先順位の低いルート (`sbc3.contoso.biz` と `sbc4.contoso.biz`) が試みられます。 2 番目のルートの SBC がどれも使用できない場合、呼び出しは削除されます。

### <a name="three-routes-example"></a>3 つのルートの例:
パターン `^\+1(425|206)(\d{7})$` を使用して 1 つの音声ルートを作成し、それに `sbc1.contoso.biz` と `sbc2.contoso.biz` を追加し、さらに `sbc3.contoso.biz` と `sbc4.contoso.biz` で同じパターンを使用して 2 番目のルートを作成し、`^+1(\d[10])$` と `sbc5.contoso.biz` で 3 番目のルートを作成した場合です。 この場合、ユーザーが `+1 425 XXX XX XX` または `+1 206 XXX XX XX` に対して呼び出しを行うと、その呼び出しは最初に SBC `sbc1.contoso.biz` または `sbc2.contoso.biz` にルーティングされます。 sbc1 と sbc2 がどちらも使用できない場合、優先順位の低いルート (`sbc3.contoso.biz` と `sbc4.contoso.biz`) が試みられます。 2 番目のルートの SBC がどれも使用できない場合は、3 番目のルートが試みられます。sbc5 も使用できない場合、呼び出しは削除されます。 また、ユーザーが `+1 321 XXX XX XX` をダイヤルすると、呼び出しが `sbc5.contoso.biz` に進み、それが使用できない場合、呼び出しは削除されます。

> [!NOTE]
> どの例でも、音声ルートが上位であるほど優先順位が高くなりますが、ルート内の SBC はランダムな順序で試みられます。

> [!NOTE]
> どの例でも、ダイヤル番号がパターンに一致しない場合、その呼び出しは削除されます。ただし、その通信リソース用に購入された番号が存在し、その番号がアプリケーションの `alternateCallerId` として使用されていた場合を除きます。 

## <a name="configure-voice-routing"></a>音声ルーティングを構成する 

### <a name="configure-using-azure-portal"></a>Azure portal を使用して構成する

:::image type="content" source="../media/direct-routing-provisioning/voice-routing-configuration.png" alt-text="Communication Services の発信音声ルーティングの構成。":::

音声ルートに名前を付け、正規表現を使用して数値パターンを指定し、そのパターンに対して SBC を選択します。 基本的な正規表現の例を次に示します。
- `^\+\d+$` - 先頭に正符号が付いた 1 桁以上の数字の電話番号と一致します
- `^+1(\d[10])$` - `+1` の後に 10 桁の数字がある電話番号と一致します
- `^\+1(425|206)(\d{7})$` - 先頭が `+1425` または `+1206` で、その後に 7 桁の数字が続く電話番号と一致します
- `^\+0?1234$` - `+01234` と `+1234` の両方の電話番号と一致します。

正規表現の詳細については、[.NET 正規表現の概要](/dotnet/standard/base-types/regular-expressions)に関する記事を参照してください。

1 つのパターンに対して複数の SBC を選択できます。 このような場合、ルーティング アルゴリズムではそれらがランダムな順序で選択されます。 正確な数値パターンを複数回指定することもできます。 上位の行ほど優先順位が高くなり、その行に関連付けられているすべての SBC が使用できない場合に次の行が選択されます。 このようにして、複雑なルーティング シナリオが作成されます。

## <a name="delete-direct-routing-configuration"></a>ダイレクト ルーティング構成を削除する

### <a name="delete-using-azure-portal"></a>Azure portal を使用した削除

#### <a name="to-delete-a-voice-route"></a>音声ルートを削除するには:
1. 左側のナビゲーションで、[Voice Calling - PSTN]\(音声通話 - PSTN\) の [直接ルーティング] に移動し、[Voice Routes]\(音声ルート\) タブを選択します。
1. チェック ボックスを使用して、削除するルートを 1 つ以上選択します。
1. [削除] を選択します。

#### <a name="to-delete-an-sbc"></a>SBC を削除するには:
1. 左側のナビゲーションで、[Voice Calling - PSTN]\(音声通話 - PSTN\) の [直接ルーティング] に移動します。
1. [Session Border Controllers]\(セッション ボーダー コントローラー\) タブで、[構成] を選択します。
1. 削除する SBC の FQDN とポートのフィールドをクリアし、[次へ] を選択します。
1. [Voice Routes]\(音声ルート\) タブで、音声ルーティングの構成を確認し、必要に応じて変更を行います。 [保存] を選択します。

> [!NOTE]
> 音声ルートに関連付けられている SBC を削除する場合、[Voice Routes]\(音声ルート\) タブでそのルートに別の SBC を選択できます。SBC が関連付けられていない音声ルートは削除されます。

## <a name="next-steps"></a>次の手順

### <a name="conceptual-documentation"></a>概念説明のドキュメント

- [Azure Communication Services のダイレクト ルーティングに対して認定されたセッション ボーダー コントローラー](./certified-session-border-controllers.md)
- [料金](../pricing.md)

### <a name="quickstarts"></a>クイックスタート

- [電話の呼び出し](../../quickstarts/voice-video-calling/pstn-call.md)
