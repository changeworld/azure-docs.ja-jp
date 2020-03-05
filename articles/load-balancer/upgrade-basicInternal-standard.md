---
title: Basic Internal から Standard Internal へのアップグレード - Azure Load Balancer
description: この記事では、Basic SKU から Standard SKU に Azure Internal Load Balancer をアップグレードする方法について説明します
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 02/23/2020
ms.author: irenehua
ms.openlocfilehash: 8d3f4294a5c8b09a132d56cd72ccb36ce766e0dd
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616720"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Azure Internal Load Balancer のアップグレード - 送信接続は不要
[Azure Standard Load Balancer](load-balancer-overview.md) では、豊富な機能とゾーンの冗長性による高可用性が提供されます。 Load Balancer SKU の詳細については、[比較表](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus)を参照してください。

アップグレードには、次の 2 つの段階があります。

1. 構成の移行
2. Standard Load Balancer のバックエンド プールに VM を追加する

この記事では、構成の移行について説明します。 バックエンド プールへの VM の追加は、お客様固有の環境によって異なる場合があります。 ただし、一般的な推奨事項を簡単に[説明しています](#add-vms-to-backend-pools-of-standard-load-balancer)。

## <a name="upgrade-overview"></a>アップグレードの概要

次の処理を行う Azure PowerShell スクリプトが用意されています。

* 指定した場所に Standard Internal SKU Load Balancer を作成します。 Standard Internal Load Balancer によって[送信接続](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)が提供されないことに注意してください。
* 新しく作成した Standard Load Balancer に Basic SKU Load Balancer の構成をシームレスにコピーします。

### <a name="caveatslimitations"></a>注意事項と制限事項

* スクリプトでは、送信接続の必要がない Internal Load Balancer のアップグレードのみをサポートしています。 一部の VM に[送信接続](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)が必要な場合は、この[ページ](upgrade-InternalBasic-To-PublicStandard.md)の説明をご覧ください。 
* Standard Load Balancer には新しいパブリック アドレスがあります。 既存の Basic Load Balancer に関連付けられている IP アドレスを Standard Load Balancer にシームレスに移動することはできません。それぞれの SKU が異なるためです。
* Standard Load Balancer が別のリージョンに作成されている場合、以前のリージョンに存在する VM を新しく作成した Standard Load Balancer に関連付けることはできません。 この制限を回避するには、必ず新しいリージョンに新しい VM を作成してください。
* Load Balancer にフロントエンド IP 構成またはバックエンド プールがない場合は、スクリプトの実行中にエラーが発生する可能性があります。 それらが空でないことを確認してください。

## <a name="download-the-script"></a>スクリプトのダウンロード

[PowerShell ギャラリー](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0)から移行スクリプトをダウンロードします。
## <a name="use-the-script"></a>スクリプトの使用

ローカルの PowerShell 環境のセットアップと設定に応じて、次の 2 つのオプションがあります。

* Azure Az モジュールがインストールされていない場合、または Azure Az モジュールをアンインストールしてもかまわない場合、最善の方法は `Install-Script` オプションを使用してスクリプトを実行することです。
* Azure Az モジュールを保持する必要がある場合は、スクリプトをダウンロードして直接実行するのが最善の方法です。

Azure Az モジュールがインストールされているかどうかを確認するには、`Get-InstalledModule -Name az` を実行します。 インストールされている Az モジュールが見つからなかった場合は、`Install-Script` メソッドを使用できます。

### <a name="install-using-the-install-script-method"></a>Install-Script メソッドを使用してインストールする

このオプションを使用するには、コンピューターに Azure Az モジュールがインストールされていないことが必要です。 インストールされている場合、次のコマンドにはエラーが表示されます。 Azure Az モジュールをアンインストールするか、もう 1 つのオプションであるスクリプトを手動でダウンロードして実行する方法を使用します。
  
次のコマンドを使用してこのスクリプトを実行します。

`Install-Script -Name AzureILBUpgrade`

このコマンドでは、必要な Az モジュールもインストールされます。  

### <a name="install-using-the-script-directly"></a>スクリプトを使用して直接インストールする

Azure Az モジュールがインストールされていて、それらをアンインストールできない (またはそれらをアンインストールしたくない) 場合は、スクリプトのダウンロード リンクの **[Manual Download]\(手動ダウンロード\)** タブを使用して、手動でスクリプトをダウンロードすることができます。 スクリプトは、生の nupkg ファイルとしてダウンロードされます。 この nupkg ファイルからスクリプトをインストールするには、「[パッケージの手動ダウンロード](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)」を参照してください。

スクリプトを実行するには、次の手順を実行します。

1. `Connect-AzAccount` を使用して Azure に接続します。

1. `Import-Module Az` を使用して Az モジュールをインポートします。

1. 必要なパラメーターを確認します。

   * **rgName: [文字列]:必須** – 既存の Basic Load Balancer と新しい Standard Load Balancer のリソース グループです。 この文字列値を検索するには、Azure portal に移動し、Basic Load Balancer ソースを選択して、ロードバランサーの **[概要]** をクリックします。 そのページにリソース グループがあります。
   * **oldLBName: [文字列]:必須** – アップグレードする既存の Basic Balancer の名前です。 
   * **newlocation: [文字列]:必須** – Standard Load Balancer が作成される場所です。 他の既存のリソースとの関連付けを強化するために、選択した Basic Load Balancer の同じ場所を Standard Load Balancer に継承することをお勧めします。
   * **newLBName: [文字列]:必須** – 作成される Standard Load Balancer の名前です。
1. 適切なパラメーターを使用してスクリプトを実行します。 完了するまで 5 から 7 分かかることがあります。

    **例**

   ```azurepowershell
   ./AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>Standard Load Balancer のバックエンド プールに VM を追加する

まず、スクリプトによって、Basic Internal Load Balancer から移行された正確な構成で新しい Standard Internal Load Balancer が正常に作成されたことを再確認します。 この確認は Azure portal から行うことができます。

手動テストとして、必ず少量のトラフィックを Standard Load Balancer 経由で送信してください。
  
新しく作成された Standard Internal Load Balancer のバックエンド プールに VM を追加する方法に関するいくつかのシナリオと、それぞれについての推奨事項を次に示します。

* **以前の Basic Internal Load Balancer のバックエンド プールから、新しく作成された Standard Internal Load Balancer のバックエンド プールに既存の VM を移動する。**
    1. このクイック スタートのタスクを実行するには、[Azure portal](https://portal.azure.com) にサインインする必要があります。
 
    1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧から**新しく作成された Standard Load Balancer** を選択します。
   
    1. **[設定]** で、 **[バックエンド プール]** を選択します。
   
    1. Basic Load Balancer のバックエンド プールと一致するバックエンド プールを選択し、次の値を選択します。 
      - **仮想マシン**: ドロップダウンして、Basic Load Balancer の一致するバックエンド プールから VM を選択します。
    1. **[保存]** を選択します。
    >[!NOTE]
    >パブリック IP を持つ VM の場合は、同じ IP アドレスが保証されていない標準 IP アドレスを最初に作成する必要があります。 基本 IP から VM の関連付けを解除し、新しく作成した標準 IP アドレスに関連付けます。 次に、手順に従って、Standard Load Balancer のバックエンド プールに VM を追加できます。 

* **新しく作成された Standard Internal Load Balancer のバックエンド プールに追加する新しい VM を作成する。**
    * VM を作成して Standard Load Balancer に関連付ける方法の詳細については、[こちら](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal#create-virtual-machines)参照してください。

## <a name="common-questions"></a>一般的な質問

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Azure PowerShell スクリプトで v1 から v2 に構成を移行するにあたり、何か制限事項はありますか?

はい。 「[注意事項と制限事項](#caveatslimitations)」をご覧ください。

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Azure PowerShell スクリプトでは、Basic Load Balancer から新しく作成した Standard Load Balancer にトラフィックを切り替えることもできますか?

いいえ。 Azure PowerShell スクリプトで移行されるのは構成のみです。 実際のトラフィックの移行は、お客様ご自身の責任において行っていただく必要があります。

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>このスクリプトの使用中に問題が発生しました。 どこに問い合わせればよいですか?
  
slbupgradesupport@microsoft.com 宛に電子メールを送信する方法と、Azure サポートでサポート ケースを開く方法があります (両方利用できます)。

## <a name="next-steps"></a>次のステップ

[Standard Load Balancer の詳細を確認する](load-balancer-overview.md)
