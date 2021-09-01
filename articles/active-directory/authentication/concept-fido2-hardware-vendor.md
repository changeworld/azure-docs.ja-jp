---
title: Azure AD へのサインインで Microsoft と互換性のある FIDO2 セキュリティ キー ベンダーになる
description: FIDO2 ハードウェア パートナーになるプロセスについて説明します
ms.date: 08/02/2021
services: active-directory
ms.service: active-directory
ms.subservice: authentication
author: knicholasa
ms.author: nichola
ms.topic: conceptual
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50d21b99fd1def56b60896d157c8a35b9381a003
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779471"
---
# <a name="become-a-microsoft-compatible-fido2-security-key-vendor"></a>Microsoft と互換性のある FIDO2 セキュリティ キー ベンダーになる

ほとんどのハッキング関連の侵害では、盗まれたまたは脆弱なパスワードが使用されます。 多くの場合、IT では、セキュリティ インシデントのリスクを軽減するために、より強力なパスワードの複雑さや、パスワードの頻繁な変更を強制します。 ただし、これによりヘルプ デスクのコストが増加し、ユーザーが新しい複雑なパスワードを記憶または格納する必要があるためユーザー エクスペリエンスが低下します。

FIDO2 セキュリティ キーを使用すると、代替手段を利用できます。 FIDO2 セキュリティ キーは、脆弱な資格情報を、サービス間で再利用、再生、共有できない、強力なハードウェアを基盤にした公開または秘密キー資格情報に置き換えることができます。 セキュリティ キーは共有デバイスのシナリオをサポートしており、自分の資格情報を持ち運び、組織に属する Azure Active Directory 参加済み Windows 10 デバイスの認証を安全に受けられます。

Microsoft は FIDO2 セキュリティ キー ベンダーと提携して、セキュリティ デバイスが Windows、Microsoft Edge ブラウザー、オンライン Microsoft アカウントで動作することを確認し、強力なパスワードレス認証を有効にします。

Microsoft と互換性のある FIDO2 セキュリティ キー ベンダーになるには、次のプロセスを実行します。  Microsoft は、パートナーとの市場投入活動にコミットしておらず、顧客の需要に基づいてパートナーの優先順位を評価します。

1. まず、認証側に FIDO2 認定資格が必要です。 FIDO2 認定資格を取得していないプロバイダーと連携することはできません。 認定資格の詳細については、Web サイト [https://fidoalliance.org/certification/](https://fidoalliance.org/certification/) をご覧ください。
2. FIDO2 認定資格を取得した後、フォーム [https://forms.office.com/r/NfmQpuS9hF](https://forms.office.com/r/NfmQpuS9hF) にリクエストを入力してください。 エンジニアリング チームは、FIDO2 デバイスの互換性のみをテストします。 ソリューションのセキュリティはテストされません。
3. テスト フェーズに進んだことを確認した後、通常、プロセスが完了するまでに約 3 から 6 か月かかります。 通常、この手順には次のことが含まれます。
    - Microsoft とチームの間で最初のディスカッションを行います。
        - FIDO アライアンス認定資格または認定資格へのパス (完了していない場合) を確認します
        - ベンダーからデバイスの概要を受け取ります
    - Microsoft からテスト スクリプトが共有されます。 特定のニーズがある場合、Microsoft のエンジニアリング チームは質問に答えることができます。
    - 完了し、成功したすべての結果を Microsoft エンジニアリング チームに送信します
    - Microsoft による確認が完了したら、各デバイスの複数のハードウェアまたはソリューションのサンプルを Microsoft エンジニアリング チームに送信します
        - 受信後、Microsoft エンジニアリング チームはテスト スクリプトの検証とユーザー エクスペリエンスのフローを行います
4. Microsoft エンジニアリング チームによってすべてのテストが成功したら、Microsoft はベンダーのデバイスが [FIDO MDS](https://fidoalliance.org/metadata/) に表示されていることを確認します。
5. Microsoft が御社の FIDO2 セキュリティキーを Azure AD バックエンドと承認された FIDO2 ベンダーの一覧に追加します。

## <a name="current-partners"></a>現在のパートナー

次の表では、Microsoft と互換性のある FIDO2 セキュリテ ィキー ベンダーであるパートナーを示します。

| **プロバイダー** | **リンク** |
| --- | --- |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| Excelsecu | [https://www.excelsecu.com/productdetail/esecufido2secu.html](https://www.excelsecu.com/productdetail/esecufido2secu.html) |
| Feitian | [https://ftsafe.us/pages/microsoft](https://ftsafe.us/pages/microsoft) |
| Go-Trust ID | [https://www.gotrustid.com/](https://www.gotrustid.com/idem-key) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Hypersecu | [https://www.hypersecu.com/hyperfido](https://www.hypersecu.com/hyperfido) |
| IDmelon Technologies Inc. | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) |
| Kensington  | [https://www.kensington.com/solutions/product-category/why-biometrics/](https://www.kensington.com/solutions/product-category/why-biometrics/) |
| KONA I | [https://konai.com/business/security/fido](https://konai.com/business/security/fido) |
| Nymi   | [https://www.nymi.com/product](https://www.nymi.com/product) |
| OneSpan Inc. | [https://www.onespan.com/products/fido](https://www.onespan.com/products/fido) |
| Thales | [https://cpl.thalesgroup.com/access-management/authenticators/fido-devices](https://cpl.thalesgroup.com/access-management/authenticators/fido-devices) |
| Thetis | [https://thetis.io/collections/fido2](https://thetis.io/collections/fido2) |
| Token2 スイス | [https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key](https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key) |
| TrustKey Solutions | [https://www.trustkeysolutions.com/security-keys/](https://www.trustkeysolutions.com/security-keys/) |
| VinCSS | [https://passwordless.vincss.net](https://passwordless.vincss.net/) |
| Yubico | [https://www.yubico.com/solutions/passwordless/](https://www.yubico.com/solutions/passwordless/) |

## <a name="next-steps"></a>次のステップ

[FIDO2 の互換性](fido2-compatibility.md)

