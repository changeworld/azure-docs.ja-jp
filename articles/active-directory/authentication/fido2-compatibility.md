---
title: FIDO2 パスワードレス認証のブラウザー サポート | Azure Active Directory
description: ブラウザーとオペレーティング システムの組み合わせにより、Azure Active Directory を使用したアプリの FIDO2 パスワードレス認証がサポートされます。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: nichola
author: knicholasa
manager: martinco
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0a8f96fabdff3543222077f5113b0bd602997b7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2021
ms.locfileid: "100416611"
---
# <a name="browser-support-of-fido2-passwordless-authentication"></a>FIDO2 パスワードレス認証のブラウザー サポート

Azure Active Directory では、[FIDO2 セキュリティ キー](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#fido2-security-keys)をパスワードレス デバイスとして使用できます。 Microsoft アカウントでの FIDO2 認証のサポートは、[2018 年に発表されました](https://techcommunity.microsoft.com/t5/identity-standards-blog/all-about-fido2-ctap2-and-webauthn/ba-p/288910)。 この発表で説明されているように、Microsoft アカウントおよび Azure Active Directory アカウントでのセキュリティで保護された認証をサポートするためには、FIDO2 CTAP 仕様の特定のオプション機能と拡張機能を実装する必要があります。 次の図は、Azure Active Directory で FIDO2 認証キーを使用したパスワードレス認証をサポートしているブラウザーとオペレーティング システムの組み合わせを示しています。

## <a name="supported-browsers"></a>サポートされているブラウザー

この表は、Azure Active Directory (Azure AD) および Microsoft アカウント (MSA) の認証のサポートを示しています。 Microsoft アカウントは、Xbox、Skype、Outlook.com などのサービスのコンシューマーによって作成されます。 サポートされているデバイスの種類には、**USB**、近距離無線通信 (**NFC**)、Bluetooth Low Energy (**BLE**) などがあります。

|  | Chrome |  |  | Edge |  |  | Firefox |  |  |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| | USB | NFC | BLE | USB | NFC | BLE | USB | NFC | BLE |
| **Windows**  | ![Chrome では、AAD アカウント用に Windows で USB がサポートされます。][y] | ![Chrome では、AAD アカウント用に Windows で NFC がサポートされます。][y] | ![Chrome では、AAD アカウント用に Windows で BLE がサポートされます。][y] | ![Edge では、AAD アカウント用に Windows で USB がサポートされます。][y] | ![Edge では、AAD アカウント用に Windows で NFC がサポートされます。][y] | ![Edge では、AAD アカウント用に Windows で BLE がサポートされます。][y] | ![Firefox では、AAD アカウント用に Windows で USB がサポートされます。][y] | ![Firefox では、AAD アカウント用に Windows で NFC がサポートされます。][y] | ![Firefox では、AAD アカウント用に Windows で BLE がサポートされます。][y] |
| **macOS**  | ![Chrome では、AAD アカウント用に macOS で USB がサポートされます。][y] | ![Chrome では、AAD アカウント用に macOS で NFC はサポートされません。][n] | ![Chrome では、AAD アカウント用に macOS で BLE はサポートされません。][n] | ![Edge では、AAD アカウント用に macOS で USB がサポートされます。][y] | ![Edge では、AAD アカウント用に macOS で NFC はサポートされません。][n] | ![Edge では、AAD アカウント用に macOS で BLE はサポートされません。][n] | ![Firefox では、AAD アカウント用に macOS で USB はサポートされません。][n] | ![Firefox では、AAD アカウント用に macOS で NFC はサポートされません。][n] | ![Firefox では、AAD アカウント用に macOS で BLE はサポートされません。][n] |
| **Linux**  | ![Chrome では、AAD アカウント用に Linux で USB がサポートされます。][y] | ![Chrome では、AAD アカウント用に Linux で NFC はサポートされません。][n] | ![Chrome では、AAD アカウント用に Linux で BLE はサポートされません。][n] | ![Edge では、AAD アカウント用に Linux で USB はサポートされません。][n] | ![Edge では、AAD アカウント用に Linux で NFC はサポートされません。][n] | ![Edge では、AAD アカウント用に Linux で BLE はサポートされません。][n] | ![Firefox では、AAD アカウント用に Linux で USB はサポートされません。][n] | ![Firefox では、AAD アカウント用に Linux で NFC はサポートされません。][n] | ![Firefox では、AAD アカウント用に Linux で BLE はサポートされません。][n] |

## <a name="operating-system-versions-tested"></a>テストされたオペレーティング システムのバージョン

上記の表の情報は、次のオペレーティング システムのバージョンでテストされています。

| オペレーティング システム | テスト済みの最新バージョン |
| --- | --- |
| Windows | Windows 10 20H2 1904 |
| macOS | OS X 11 Big Sur |
| Linux | Fedora 32 Workstation |

## <a name="next-steps"></a>次のステップ
[パスワードなしのセキュリティ キー サインインを有効にする (プレビュー)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key)

<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png
