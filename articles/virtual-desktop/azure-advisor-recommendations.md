---
title: Azure Advisor Windows Virtual Desktop のチュートリアル - Azure
description: Windows Virtual Desktop の Azure Advisor の推奨事項を解決する方法。
author: Heidilohr
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 666f882dec6c00b9fe5421485afded1fbd57d961
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448306"
---
# <a name="how-to-resolve-azure-advisor-recommendations"></a>Azure Advisor の推奨事項を解決する方法

この記事では、Windows Virtual Desktop の Azure Advisor に表示される推奨事項を解決する方法について説明します。

## <a name="no-validation-environment-enabled"></a>"検証環境が有効になっていません"

>[!div class="mx-imgBorder"]
>![Azure Advisor オペレーショナル エクセレンス ページのスクリーンショット。 "検証環境が有効になっていません" という推奨事項が赤色で強調表示されている。](media/no-validation-environment.png)

この推奨事項は、[オペレーショナル エクセレンス] に表示されます。 推奨事項には、次のような警告メッセージも表示されます。

"このサブスクリプションで検証環境が有効になっていません。 お使いのホスト プールの作成時に、[プロパティ] タブの [検証環境] で **[いいえ]** が選択されています。Windows Virtual Desktop サービスのデプロイを通じてビジネスの継続性を確保するには、潜在的な問題をテストできる検証環境を持つホスト プールが少なくとも 1 つあることを確認してください。"

この警告メッセージが表示されないようにするには、いずれかのホスト プールで検証環境を有効にします。

検証環境を有効にするには:

1. Azure portal のホーム ページに移動し、変更するホスト プールを選択します。

2. 次に、運用環境から検証環境に変更するホスト プールを選択します。

3. ホスト プールの左列で **[プロパティ]** を選択します。 次に、[検証環境] が表示されるまで下にスクロールします。 **[はい]** を選択してから、 **[適用]** を選択します。

>[!div class="mx-imgBorder"]
>![[プロパティ] メニューのスクリーンショット。 [検証環境] が赤色で強調表示され、[はい] ボタンが選択されている。](media/validation-yes.png)

これらの変更によって警告がすぐに消えることはありませんが、最終的には表示されなくなります。 Azure Advisor は 1 日に 2 回更新されます。 それまでは、推奨事項を手動で延期または無視することができます。 推奨事項が自動的に消去されるようにすることをお勧めします。 こうすることで、設定の変更によって問題が発生したかどうかを Azure Advisor が通知できます。

## <a name="not-enough-production-non-validation-environments-enabled"></a>"十分な運用 (検証ではない) 環境が有効になっていません"

この推奨事項は、[オペレーショナル エクセレンス] に表示されます。

この推奨事項の場合、次のいずれかの理由で警告メッセージが表示されます。

- 検証環境内のホスト プールが多すぎる。
- 運用ホスト プールがない。

検証環境にはホスト プールの半数未満を配置することをお勧めします。

この警告を解決するには:

1. Azure portal のホーム ページにアクセスします。

2. 検証から運用に変更するホスト プールを選択します。

3. ホスト プールで、画面右側の列にある **[プロパティ]** タブを選択します。 次に、[検証環境] が表示されるまで下にスクロールします。 **[いいえ]** を選択してから、 **[適用]** を選択します。

>[!div class="mx-imgBorder"]
>![[プロパティ] メニューのスクリーンショット。 [検証環境] が赤色で強調表示され、[いいえ] ボタンが選択されている。](media/validation-no.png)

これらの変更によって警告がすぐに消えることはありませんが、最終的には表示されなくなります。 Azure Advisor は 1 日に 2 回更新されます。 それまでは、推奨事項を手動で延期または無視することができます。 推奨事項が自動的に消去されるようにすることをお勧めします。 こうすることで、設定の変更によって問題が発生したかどうかを Azure Advisor が通知できます。

## <a name="not-enough-links-are-unblocked-to-successfully-implement-your-vm"></a>"十分なリンクがブロック解除されていないため、VM を正常に実装できません"

この推奨事項は、[オペレーショナル エクセレンス] に表示されます。

仮想マシン (VM) が正しく機能するようにするには、特定の URL のブロックを解除する必要があります。 「[安全な URL リスト](safe-url-list.md)」でその一覧を確認できます。 URL がブロック解除されていない場合、VM は正常に機能しません。

この推奨事項を解決するには、「[安全な URL リスト](safe-url-list.md)」のすべての URL のブロックを解除してください。 サービス タグまたは FQDN タグを使用して、URL のブロックを解除することもできます。

## <a name="next-steps"></a>次のステップ

一般的な問題の解決方法について詳しく説明しているガイドが必要な場合は、「[Windows Virtual Desktop のトラブルシューティングの概要、フィードバック、およびサポート](troubleshoot-set-up-overview.md)」を参照してください。
