---
title: ネットワークの分離
description: Azure DevTest Labs でのネットワークの分離について説明します。
ms.topic: how-to
ms.date: 08/25/2020
ms.openlocfilehash: 555c6b13a46ba6cd70ef136d9a6a4cf88d14c4f9
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397720"
---
# <a name="network-isolation-in-devtest-labs"></a>DevTest Labs でのネットワークの分離

[Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md)は、パブリック インターネットから Azure リソースを分離するセキュリティ境界として機能します。 オンプレミスのリソースに安全に接続できるように、Azure 仮想ネットワークをオンプレミス ネットワークに参加させることもできます。 DevTest Labs では、ラボ リソースが組織のネットワーク ポリシーに従うようにするために、[すべてのラボ仮想マシン](devtest-lab-configure-vnet.md)と[環境をネットワーク](connect-environment-lab-virtual-network.md)に分離することを選択できます。 

ラボの所有者が望めば、ラボを完全に分離することもできます。 選択したネットワークに仮想マシンと環境を分離します。 サブスクリプションに作成したラボのストレージ アカウントとキー コンテナーを分離することもできます。 この記事では、ネットワーク分離ラボを作成する手順について説明します。 

以下の記事も確認してください。

- [DevTest Labs がラボ ストレージ アカウントを使用する方法](encrypt-storage.md)
- [DevTest Labs がキー コンテナーを使用する方法](devtest-lab-store-secrets-in-key-vault.md)
 
> [!NOTE]
> ネットワークの分離は、現在新しいラボの作成でのみサポートされています。

## <a name="steps-to-enable-network-isolation-during-lab-creation"></a>ラボの作成時にネットワークの分離を有効にする手順

1. ラボの作成時に、 **[ネットワーク]** タブにアクセスします。
1. ラボによって作成される **既定** のネットワークを選択するか、ドロップダウンから既存のネットワークを選択することができます。 選択できるのは、ラボと同じリージョンおよびサブスクリプション内にあるネットワークのみです。 

    > [!div class="mx-imgBorder"]
    > ![ラボの作成を示すスクリーンショット。](./media/network-isolation/create-lab.png)
1. サブネットを選択します。

    > [!div class="mx-imgBorder"]
    > ![サブネットの作成を示すスクリーンショット。](./media/network-isolation/create-lab-subnet.png)
1. ラボのストレージ アカウントとキー コンテナーを既定のネットワークに分離する場合は、これ以上のアクションは不要です。 以後、リソースはラボによって処理されます。
 
    > [!div class="mx-imgBorder"]
    > ![ネットワークの分離を示すスクリーンショット。](./media/network-isolation/isolate-lab-resources.png)
1. ラボのストレージ アカウントとキー コンテナーを、選択した既存のネットワークに分離する場合は、ラボの作成後、以降の手順を実行してください。 これらの手順により、ラボが分離モードで確実に機能し続けます。 
 
    > [!div class="mx-imgBorder"]
    > ![リソースの分離を示すスクリーンショット。](./media/network-isolation/isolate-my-vnet.png)

    > [!IMPORTANT]
    > ラボの所有者は、ラボでリソースを構成または作成する前に、これらの手順を完了する必要があります。

### <a name="steps-to-follow-post-lab-creation"></a>ラボの作成後に従う手順

1. ラボのホーム ページで、 **[概要]** ページの **リソース グループ** を選択します。 そのラボが含まれているリソース グループの **[リソースグループ]** ページが表示されます。 
 
   > [!div class="mx-imgBorder"]
   > ![Contoso ラボを示すスクリーンショット。](./media/network-isolation/contoso-lab.png)
1. ラボの Azure ストレージ アカウントを選択します。 ラボ ストレージ アカウントの名前付け規則は、\<*labNameWithoutInvalidCharacters*>\<*4-digit number*> です。 たとえば、ラボ名が contosolab である場合、ストレージ アカウント名は acontosolab1234 になります。
 
   > [!div class="mx-imgBorder"]
   > ![Contoso テストを示すスクリーンショット。](./media/network-isolation/contoso-test.png)
1. ストレージ アカウントで、[ファイアウォールと仮想ネットワーク] にアクセスし、[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します] のチェックボックスをオンにします。 [DevTest Labs は信頼された Microsoft サービスである](../storage/common/storage-network-security.md#trusted-microsoft-services)ため、このオプションを使用すると、ラボをネットワーク分離モードで正常に動作させることができます。 

   > [!div class="mx-imgBorder"]
   > ![Contoso ラボのファイアウォールを示すスクリーンショット。](./media/network-isolation/contoso-lab-firewalls-vnets.png)
1. 次に、 **[既存の仮想ネットワークを追加]** をクリックし、ラボの作成時に選択した仮想ネットワークとサブネットを選択し、 **[有効化]** をクリックします。 

   > [!div class="mx-imgBorder"]
   > ![Contoso の my vnet を示すスクリーンショット。](./media/network-isolation/contoso-lab-my-vnet.png)
5.  選択した仮想ネットワークのサービス エンドポイントが正常に有効になったら、 **[追加]** クリックします。 

   > [!div class="mx-imgBorder"]
   > ![[追加] を示すスクリーンショット。](./media/network-isolation/contoso-firewall-add.png)
 
これにより、Azure Storage は追加された仮想ネットワークからの着信接続を許可し、ラボがネットワーク分離モードで正常に動作できるようにします。 

また、これらの手順を自動化して、複数のラボに対してこの設定を構成することもできます。 

[PowerShell と CLI を使用して Azure Storage の既定のネットワーク アクセス ルールを管理する方法の詳細を確認する](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#powershell)

## <a name="network-isolation-for-an-existing-lab"></a>既存のラボのネットワーク分離

ラボの所有者は、既存のラボのネットワークを分離することを選択できます。 [このサンプルスクリプト](https://github.com/Azure/azure-devtestlab/blob/master/Tools/ConvertDtlLabToIsolatedNetwork/Convert-DtlLabToIsolatedNetwork.ps1)では、既存のラボおよび関連するラボ リソースを分離されたネットワーク モードに変換する方法を示します。 

## <a name="things-to-remember-while-using-a-lab-in-a-network-isolated-mode"></a>ネットワーク分離モードでラボを使用するときに覚えておくべきこと

### <a name="accessing-labs-storage-account-outside-the-lab"></a>ラボ外のラボのストレージ アカウントへのアクセス 

ネットワーク分離されたラボのストレージ アカウントについては、許可されたエンドポイントからのアクセスをラボの所有者が明示的に有効にする必要があります。 カスタム イメージを作成するための VHD をストレージ アカウントにアップロードする操作などで、このアクセスが必要になります。 アクセスを有効にするためには、仮想マシンを作成し、その仮想マシンからラボのストレージ アカウントに安全にアクセスできるようにします。 

[仮想マシンから非公開でストレージ アカウントにアクセスする方法の詳細を確認する](../private-link/tutorial-private-endpoint-storage-portal.md)

### <a name="exporting-usage-data-from-the-lab"></a>ラボからの使用状況データのエクスポート 

[ネットワーク分離ラボされたラボの個人の使用状況データをエクスポートする](personal-data-delete-export.md)には、ラボの所有者が明示的にストレージ アカウントを指定し、データを格納するための BLOB をアカウント内に生成する必要があります。 

ラボのストレージ アカウントが指定されていないと、ネットワーク分離モードでは、この操作は失敗します。 お客様によってストレージ アカウントが指定されなかった場合、ラボがそのストレージ アカウントにアクセスして使用することができません。 

[指定されたストレージ アカウントでラボの使用状況データをエクスポートする方法の詳細を確認する](personal-data-delete-export.md#azure-powershell)

## <a name="next-steps"></a>次のステップ

[Azure Resource Manager テンプレートと PowerShell を使ってラボを自動的に作成または変更する](devtest-lab-use-arm-and-powershell-for-lab-resources.md)
