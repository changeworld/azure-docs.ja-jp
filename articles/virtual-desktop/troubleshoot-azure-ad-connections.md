---
title: Azure AD 参加済み VM への接続 Azure Virtual Desktop - Azure
description: Azure Virtual Desktop で Azure AD 参加済み VM に接続しているときの問題の解決方法。
services: virtual-desktop
author: Heidilohr
manager: lizross
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 08/20/2021
ms.author: helohr
ms.openlocfilehash: f168c05e5df3421126c94bea7160896fb1b75363
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122662274"
---
# <a name="connections-to-azure-ad-joined-vms"></a>Azure AD 参加済み VM への接続

>[!IMPORTANT]
>この内容は、Azure Resource Manager Azure Virtual Desktop オブジェクトを含む Azure Virtual Desktop に適用されます。

この記事では、Azure Virtual Desktop で Azure AD 参加済み VM への接続に関する問題を解決する方法について説明します。

## <a name="provide-feedback"></a>フィードバックの提供

[Azure Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/bd-p/AzureVirtualDesktopForum) にアクセスし、Azure Virtual Desktop サービスに関して製品チームや活発なコミュニティのメンバーと意見を交わしてください。

## <a name="all-clients"></a>すべてのクライアント

### <a name="your-account-is-configured-to-prevent-you-from-using-this-device"></a>このデバイスを使用できないようにアカウントが構成されています

**アカウントがこのデバイスを使用できないように構成されているため詳細については、システム管理者に連絡する** ように示すエラーが発生した場合は、そのユーザー アカウントに VM 上で[Virtual Machine User Login ロール](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#azure-role-not-assigned)が与えられていることを確認してください。 

## <a name="windows-desktop-client"></a>Windows デスクトップクライアント

### <a name="the-logon-attempt-failed"></a>ログインに失敗しました

Windows セキュリティ資格情報のプロンプトで **ログオン試行が失敗した** ことを示すエラーが発生した場合は、次のことを確認してください。

- セッション ホストと同じ Azure AD テナントに Azure AD 参加またはハイブリッド Azure AD 参加しているデバイスを使用している
- セッション ホストと同じ Azure AD テナントに Azure AD が登録された、Windows 10 2004 以降を搭載したデバイスを使用している
- [PKU2U プロトコルがローカル PC とセッション ホストの両方で有効](/windows/security/threat-protection/security-policy-settings/network-security-allow-pku2u-authentication-requests-to-this-computer-to-use-online-identities)になっていること
- Azure AD 参加済み VM ではサポートされていないので、ユーザー アカウントでの[ユーザーごとの MFA は無効になります](deploy-azure-ad-joined-vm.md#enabling-mfa-for-azure-ad-joined-vms)。

### <a name="the-sign-in-method-youre-trying-to-use-isnt-allowed"></a>使用しようとしているサインイン方法は許可されていません

**使用しようとしているサインイン方法が許可されいないため、別のサインイン方法を試すか、システム管理者に連絡する** ように示すエラーが表示されたら、アクセスを制限する条件付きアクセス ポリシーがあることを確認してください。 「[多要素認証を有効にする](deploy-azure-ad-joined-vm.md#enabling-mfa-for-azure-ad-joined-vms)」の手順に従って、Azure AD 参加済み VM に対して多要素認証を有効にします。

## <a name="web-client"></a>Web クライアント

### <a name="sign-in-failed-please-check-your-username-and-password-and-try-again"></a>サイン インできませんでした。 ユーザー名とパスワードを確認してから、もう一度お試しください

「**名前に接続できませんでした。サインインできませんでした。ユーザー名とパスワードを確認してから、もう一度お試しください。** 」というエラーが発生した場合。 Web クライアントを使用する場合は、[他のクライアントからの接続が有効になっていることを確認します](deploy-azure-ad-joined-vm.md#connect-using-the-other-clients)。

### <a name="we-couldnt-connect-to-the-remote-pc-because-of-a-security-error"></a>We couldn't connect to the remote PC because of a security error (セキュリティ エラーのため、リモート PC に接続できませんでした)

「**Oops, we couldn't connect to NAME. We couldn't connect to the remote PC because of a security error. If this keeps happening, ask your admin or tech support for help. (NAME に接続できません。セキュリティ エラーのため、リモート PC に接続できませんでした。この問題が引き続き発生する場合は、管理者またはテクニカル サポートにお問い合わせください)** 」というメッセージが表示される場合は、アクセスを制限する条件付きアクセス ポリシーがあります。 「[多要素認証を有効にする](deploy-azure-ad-joined-vm.md#enabling-mfa-for-azure-ad-joined-vms)」の手順に従って、Azure AD 参加済み VM に対して多要素認証を有効にします。

## <a name="android-client"></a>Android クライアント

### <a name="error-code-2607---we-couldnt-connect-to-the-remote-pc-because-your-credentials-did-not-work"></a>エラーコード 2607 - 資格情報が機能しなかったため、リモート PC に接続できませんでした

「**資格情報が機能しなかったため、リモート PC に接続できませんでした。リモート コンピューターは、AADJ に結合されています。** 」というエラーが発生した場合。 Android クライアントを使用する場合でエラー コード 2607 であれば、[他のクライアントからの接続が有効になっていることを確認します](deploy-azure-ad-joined-vm.md#connect-using-the-other-clients)。

## <a name="next-steps"></a>次のステップ

- Azure Virtual Desktop トラブルシューティングの概要とエスカレーション トラックについては、「[トラブルシューティングの概要、フィードバック、サポート](troubleshoot-set-up-overview.md)」を参照してください。
- Azure Virtual Desktop 環境を作成しているときや、Azure Virtual Desktop 環境でホスト プールを作成しているときに発生した問題を解決するには、[環境とホスト プールの作成](troubleshoot-set-up-issues.md)に関するページを参照してください。
- Azure Virtual Desktop で仮想マシン (VM) の構成中に発生した問題を解決するには、[セッション ホスト仮想マシンの構成](troubleshoot-vm-configuration.md) に関する記事を参照してください。
- Azure Virtual Desktop エージェントまたはセッション接続に関連する問題のトラブルシューティングについては、「[Azure Virtual Desktop エージェントに関する一般的な問題をトラブルシューティングする](troubleshoot-agent.md)」を参照してください。
- Azure Virtual Desktop で PowerShell を使用しているときに発生した問題を解決するには、「[Azure Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照してください。
- トラブルシューティング チュートリアルについては、「[Tutorial:Resource Manager テンプレート デプロイのトラブルシューティング](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)」を参照してください。
