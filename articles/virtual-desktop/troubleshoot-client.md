---
title: リモート デスクトップ クライアントの Windows Virtual Desktop のトラブルシューティング - Azure
description: Windows Virtual Desktop テナント環境でクライアント接続を設定するときの問題を解決する方法です。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
ms.openlocfilehash: bcf22ec1eaf05f5dda6396cca017fa21fd6ddbf5
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "75968191"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>リモート デスクトップ クライアントのトラブルシューティング

この記事では、リモート デスクトップ クライアントに関する一般的な問題とその修正方法について説明します。

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Windows 7 または Windows 10 用のリモート デスクトップ クライアントが応答を停止する、または開くことができない

次の PowerShell コマンドレットを使って、帯域外 (OOB) のクライアントのレジストリをクリーンアップします。

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

**%AppData%\RdClientRadc** に移動して、すべての内容を削除します。

Windows 7 および Windows 10 用のリモート デスクトップ クライアントをアンインストールしてから再インストールします。

## <a name="web-client-wont-open"></a>Web クライアントが開かない

まず、ブラウザーで別の Web サイト ([www.bing.com](https://www.bing.com) など) を開き、インターネット接続をテストします。

**nslookup** を使って、DNS で FQDN を解決できることを確認します。

```cmd
nslookup rdweb.wvd.microsoft.com
```

別のクライアント (Windows 7 または Windows 10 用のリモート デスクトップ クライアントなど) で接続してみて、Web クライアントを開くことができるかどうかを確認します。

### <a name="opening-another-site-fails"></a>別のサイトを開くことができない

これは、通常、ネットワーク接続の問題またはネットワークの停止が原因で発生します。 ネットワーク サポートに問い合わせることをお勧めします。

### <a name="nslookup-cannot-resolve-the-name"></a>nslookup で名前を解決できない

これは、通常、ネットワーク接続の問題またはネットワークの停止が原因で発生します。 ネットワーク サポートに問い合わせることをお勧めします。

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>自分のクライアントは接続できないが、ネットワーク上の他のクライアントは接続できる

Web クライアントを使用しているときにブラウザーの動作が開始されたり動作が停止したりした場合は、次の手順に従ってトラブルシューティングを行います。

1. ブラウザーを再起動します。
2. ブラウザーの Cookie をクリアします。 「[Internet Explorer で cookie ファイルを削除する方法](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)」をご覧ください。
3. ブラウザーのキャッシュをクリアします。 [お使いのブラウザーのブラウザー キャッシュをクリアする方法](https://binged.it/2RKyfdU)をご覧ください。
4. プライベート モードでブラウザーを開きます。

## <a name="web-client-stops-responding-or-disconnects"></a>Web クライアントが応答を停止するか切断する

別のブラウザーまたはクライアントを使って接続してみてください。

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>他のブラウザーやクライアントも正常に機能しないか、開けない

ブラウザーを切り替えた後も問題が解決しない場合は、ブラウザーではなく、ネットワークで問題が発生している可能性があります。 ネットワーク サポートに問い合わせることをお勧めします。

## <a name="web-client-keeps-prompting-for-credentials"></a>Web クライアントが資格情報の入力を求め続ける

Web クライアントで資格情報の入力の要求が出続ける場合は、次の手順に従います。

1. Web クライアントの URL が正しいことを確認します。
2. 使用している資格情報が、URL に関連付けられた Windows Virtual Desktop 環境に対するものであることを確認します。
3. ブラウザーの Cookie をクリアします。 詳細については、「[Internet Explorer で cookie ファイルを削除する方法](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)」をご覧ください。
4. ブラウザーのキャッシュをクリアします。 詳細については、[お使いのブラウザーのブラウザー キャッシュをクリアする方法](https://binged.it/2RKyfdU)をご覧ください。
5. プライベート モードでブラウザーを開きます。

## <a name="next-steps"></a>次のステップ

- Windows Virtual Desktop トラブルシューティングの概要とエスカレーション トラックについては、「[トラブルシューティングの概要、フィードバック、サポート](troubleshoot-set-up-overview.md)」を参照してください。
- Windows Virtual Desktop 環境でテナント/ホスト プールを作成しているときに発生した問題を解決するには、「[Tenant and host pool creation](troubleshoot-set-up-issues.md)」 (テナントとホスト プールの作成) を参照してください。
- Windows Virtual Desktop で仮想マシン (VM) の構成中に発生した問題を解決するには、[Session host virtual machine configuration (セッション ホスト仮想マシンの構成)](troubleshoot-vm-configuration.md) に関する記事を参照してください。
- Windows Virtual Desktop で PowerShell を使用しているときに発生した問題を解決するには、「[Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照してください。
- トラブルシューティング チュートリアルについては、「[Tutorial:Resource Manager テンプレート デプロイのトラブルシューティング](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)」を参照してください。