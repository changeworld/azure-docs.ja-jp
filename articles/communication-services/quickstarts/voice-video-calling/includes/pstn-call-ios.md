---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/11/2020
ms.author: nikuklic
ms.openlocfilehash: 80146b4da376f3a1d43bcecc89a4a335263ac5ad
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102193830"
---
[!INCLUDE [Emergency Calling Notice](../../../includes/emergency-calling-notice-include.md)]
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- デプロイ済みの Communication Services リソース。 [Communication Services のリソースを作成する](../../create-communication-resource.md)。
- Communication Services リソースで取得した電話番号。 [電話番号の取得方法](../../telephony-sms/get-phone-number.md)。
- 通話クライアントを有効にするための `User Access Token`。 [`User Access Token` を取得する方法](../../access-tokens.md)についての詳細
- [アプリケーションへの通話の追加の概要](../getting-started-with-calling.md)に関するクイックスタートを完了します

### <a name="prerequisite-check"></a>前提条件のチェック

- Communication Services リソースに関連付けられている電話番号を表示するには、[Azure portal](https://portal.azure.com/) にサインインし、Communication Services リソースを見つけて、左側のナビゲーション ペインから **[電話番号]** タブを開きます。
- アプリの作成と実行には、iOS 用の Azure Communication Services 通話クライアント ライブラリを使用できます。

## <a name="setting-up"></a>設定

## <a name="start-a-call-to-phone"></a>電話の呼び出しを開始する

Communication Services リソースで取得した電話番号を指定してください。通話を開始する際に使用します。
> [!WARNING]
> 電話番号は、E.164 国際標準形式になっている必要があります (例: +12223334444)。

*[Start Call]\(通話を開始\)* ボタンをタップしたときに実行される `startCall` イベント ハンドラーに変更を加えます。

```swift
func startCall() {
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            let startCallOptions = ACSStartCallOptions()
            startCallOptions!.alternateCallerID = PhoneNumber(phoneNumber: "+12223334444")
            self.call = self.callAgent!.startCall([PhoneNumber(phoneNumber: self.callee)], options: startCallOptions)
            self.callDelegate = CallDelegate(self)
            self.call!.delegate = self.callDelegate
        }
    }
}
```

## <a name="run-the-code"></a>コードの実行

iOS シミュレーターでアプリをビルドして実行するには、 **[製品]**  >  **[実行]** の順に選択するか、(&#8984;-R) キーボード ショートカットを使用します。

![クイック スタート アプリの最終的な外観](../media/ios/quick-start-make-call.png)

追加したテキスト フィールドに電話番号を入力し、 **[Start Call]\(通話を開始\)** ボタンをクリックすることによって、電話を呼び出すことができます。
> [!WARNING]
> 電話番号は、E.164 国際標準形式になっている必要があります (例: +12223334444)。

> [!NOTE]
> 初めて通話を行うときに、マイクへのアクセスを求めるメッセージが表示されます。 実稼働アプリケーションでは、`AVAudioSession` API を使用して[アクセス許可の状態を確認し](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources)、アクセス許可が付与されていない場合はアプリケーションの動作を適切に更新する必要があります。
