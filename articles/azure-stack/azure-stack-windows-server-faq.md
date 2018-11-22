---
title: Azure Stack の Windows Server に関する FAQ | Microsoft Docs
description: Azure Stack Marketplace の Windows Server に関する FAQ 一覧
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2018
ms.author: sethm
ms.reviewer: avishwan
ms.openlocfilehash: bf70f9a74b58758e03800d7f6fb92a8f8754828a
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613097"
---
# <a name="windows-server-in-azure-stack-marketplace-faq"></a>Azure Stack Marketplace の Windows Server に関する FAQ

この記事では、[Azure Stack Marketplace](azure-stack-marketplace.md) の Windows Server イメージについてよく寄せられるいくつかの質問に回答します。

## <a name="marketplace-items"></a>Marketplace アイテム

### <a name="how-do-i-update-to-a-newer-windows-image"></a>より新しい Windows イメージに更新するにはどうすればよいですか?

まず、Azure Resource Manager テンプレートが特定のバージョンを参照しているかどうかを確認します。 該当する場合は、それらのテンプレートを更新するか、以前のバージョンのイメージを維持します。 **version: latest** を使用することをお勧めします。

次に、Virtual Machine Scale Sets が特定のバージョンを参照している場合、これらを後でスケーリングするかどうかを検討し、さらに以前のバージョンを維持するかどうかを決める必要があります。 どの条件も該当しない場合は、新しいイメージをダウンロードする前に、以前のイメージを Marketplace で削除してください。 元のイメージのダウンロードに [Marketplace Management]\(Marketplace の管理\) を使用した場合は、それを使って行ってください。 そのうえで、新しいバージョンをダウンロードします。

### <a name="what-are-the-licensing-options-for-windows-server-marketplace-images-on-azure-stack"></a>Azure Stack における Windows Server Marketplace イメージのライセンス オプションを教えてください。

Microsoft は、次の 2 つのバージョンの Windows Server イメージを Azure Stack Marketplace で提供しています。

- **従量課金制**: これらのイメージでは、正規の価格の Windows メーターが実行されます。 
   "*使用量課金モデル*" を利用するマイクロソフト エンタープライズ契約 (EA) のお客様や、SPLA ライセンスの使用を希望しない CSP にお勧めします。
- **ライセンス持ち込み (BYOL)**: これらのイメージでは、基本メーターが実行されます。
   Windows Server ライセンスを所有する EA のお客様や、SPLA ライセンスを使用する CSP にお勧めします。

Azure Stack では、Azure ハイブリッド使用特典 (AHUB) はサポートされません。 "容量" モデルを通じてライセンスを受けるお客様は、BYOL イメージを使用する必要があります。 Azure Stack Development Kit (ASDK) を使ってテストを行う場合は、どちらのオプションを使用してもかまいません。

### <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>テナント/ユーザーに提供する場合に間違ったバージョンをダウンロードした場合はどうすればよいですか?

まず、[Marketplace Management]\(Marketplace の管理\) から、間違ったバージョンを削除します。 完了するまでお待ちください ([Marketplace Management]\(Marketplace の管理\) ブレードではなく、通知を見て完了を確認してください)。 そのうえで、正しいバージョンをダウンロードします。

### <a name="what-if-my-user-incorrectly-checked-the-i-have-a-license-box-in-previous-windows-builds-and-they-dont-have-a-license"></a>実際にはライセンスを持っていないにもかかわらず、ユーザーが間違って以前の Windows ビルドの [I have a license]\(ライセンスを持っています\) チェック ボックスをオンにしてしまった場合はどうすればよいですか?

「[特典付きの Windows Server VM を従量課金制に戻す](../virtual-machines/windows/hybrid-use-benefit-licensing.md#powershell-1)」を参照してください。

### <a name="what-if-i-have-an-older-image-and-my-user-forgot-to-check-the-i-have-a-license-box-or-we-use-our-own-images-and-we-do-have-enterprise-agreement-entitlement"></a>私が以前のイメージを所有しているにもかかわらず、私のユーザーが [I have a license]\(ライセンスを持っています\) チェック ボックスをオンにするのを忘れてしまった場合は、どうすればよいですか。または、私たちが独自のイメージを使用していて、なおかつマイクロソフト エンタープライズ契約の権利がある場合はどうすればよいですか?

「[既存の VM を Windows Server 向け Azure ハイブリッド特典を使用するように変換する](../virtual-machines/windows/hybrid-use-benefit-licensing.md#convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server)」を参照してください。 Azure ハイブリッド特典は Azure Stack には適用されませんが、この設定の効果は適用されることに注意してください。

### <a name="what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server"></a>Windows Server を使用する他の VM (SQL Server、Machine Learning Server など) についてはどうでしょうか?

これらのイメージは、**licenseType** パラメーターが適用されるため、従量課金制となります。 このパラメーターは自分で設定することができます (前出の FAQ の回答を参照)。 これが適用されるのは、Windows Server ソフトウェアだけです。自分のライセンスの持ち込みが必要となる階層化された製品 (SQL など) には適用されません。 従量課金制ライセンスは、階層化されたソフトウェア製品には適用されません。

### <a name="i-have-an-enterprise-agreement-ea-and-will-be-using-my-ea-windows-server-license-how-do-i-make-sure-images-are-billed-correctly"></a>私はマイクロソフト エンタープライズ契約 (EA) を所有しており、EA Windows Server ライセンスを使う予定です。イメージに対して正しく課金されるようにするには、どうすればよいですか?

Azure Resource Manager テンプレートに **licenseType: Windows_Server** を追加してください。 この設定は、個々の仮想マシンのリソース ブロックに追加する必要があります。

## <a name="activation"></a>アクティブ化

Azure Stack で Windows Server 仮想マシンのライセンス認証を行うには、次の条件が満たされている必要があります。

- Azure Stack のすべてのホスト システムに対し、OEM によって適切な BIOS マーカーが設定されていること。
- Windows Server 2012 R2 および Windows Server 2016 で[仮想マシンの自動ライセンス認証](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11))が使用されていること。 キー管理サービス (KMS) などのライセンス認証サービスは、Azure Stack ではサポートされていません。

### <a name="how-can-i-verify-that-my-virtual-machine-is-activated"></a>自分の仮想マシンがライセンス認証されたことは、どのようにして確認できますか?

管理者特権でのコマンド プロンプトで、次のコマンドを実行します。 

```shell
slmgr /dlv
``` 

正しくライセンス認証されていれば、`slmgr` の出力にその旨が明記され、ホスト名が表示されます。 ディスプレイ上の透かしに頼らないようにしてください。これらは最新ではない可能性があるほか、ご利用のものとは異なる仮想マシンからの表示である可能性もあります。

### <a name="my-vm-is-not-set-up-to-use-avma-how-can-i-fix-it"></a>私の VM は AVMA を使用するように設定されていません。解決方法を教えてください。

管理者特権でのコマンド プロンプトで、次のコマンドを実行します。 

```shell
slmgr /ipk <AVMA key> 
```

イメージに使用するキーについては、記事「[Automatic Virtual Machine Activation (仮想マシンの自動ライセンス認証)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11))」を参照してください。

### <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>独自の Windows Server イメージを作成しています。確実に AVMA が使用されるようにするには、どうすればよいですか?

`sysprep` コマンドを実行する前に、適切なキーを指定して `slmgr /ipk` コマンド ラインを実行することをお勧めします。 または、Unattend.exe セットアップ ファイルに AVMA キーを含めます。

### <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-it-is-not-activating-or-using-kms-activation"></a>Azure 上で作成した Windows Server 2016 イメージを使おうとしているのですが、ライセンス認証が実行されません (または KMS ライセンス認証が使用されません)。

`slmgr /ipk` コマンドを実行します。 Azure のイメージが正しく AVMA にフォールバックされないことがありますが、Azure KMS システムに到達できれば、ライセンス認証が行われます。 これらの VM が AVMA を使用する設定になっていることを確認するようお勧めします。

### <a name="i-have-performed-all-of-these-steps-but-my-virtual-machines-are-still-not-activating"></a>これらの手順はすべて実行しましたが、まだ仮想マシンがライセンス認証されません。

ハードウェア サプライヤーに問い合わせて、正しい BIOS マーカーがインストールされていることを確認してください。

### <a name="what-about-earlier-versions-of-windows-server"></a>以前のバージョンの Windows Server についてはどうでしょうか?

以前のバージョンの Windows Server では、[仮想マシンの自動ライセンス認証](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11))はサポートされていません。 VM のライセンス認証を手動で行う必要があります。

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。

- [Azure Stack Marketplace の概要](azure-stack-marketplace.md)
- [Azure から Azure Stack に Marketplace の項目をダウンロードする](azure-stack-download-azure-marketplace-item.md)
