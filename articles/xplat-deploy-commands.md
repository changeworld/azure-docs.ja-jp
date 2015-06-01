<properties
   pageTitle="Mac、Linux、および Windows 用 Azure CLI を使用したテンプレートのデプロイ"
   description="任意のテンプレートをデプロイまたは更新する基本的な手順について説明します。"
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="infrastructure"
   ms.date="04/21/2015"
   ms.author="rasquill"/>

# Mac、Linux、および Windows 用 Azure CLI を使用したテンプレートのデプロイ

## curl、wget、またはその他のダウンロード ツールをインストールする
このトピックでは **curl** を使用します。オペレーティング システムのパッケージ マネージャーを使用するか、[ここ](http://curl.haxx.se/download.html)からダウンロードします。

## テンプレートのパラメーター ファイル (または必要に応じてテンプレート) をダウンロードする

次の手順を実行すると、Azure テンプレートを 1 つ (複雑なテンプレートでも) デプロイできるようになります。このトピックでは、例として、カスタム スクリプト VM の拡張機能がインストールされた、基本的な Ubuntu サーバーを作成するテンプレートを使用します。参考資料として、このトピックの最後にもファイルが追加されています。

### azuredeploy-parameters.json ファイルをダウンロードする

デプロイするテンプレートの azuredeploy-parameters.json ファイルが存在する場合は、そのファイルをダウンロードします。

    curl -O https://github.com/azure/azurermtemplates/raw/master/linux-virtual-machine-with-customdata/azuredeploy-parameters.json
    
## リソース グループのデプロイメント情報を入力する
    
このファイルを任意のエディターで開きます。ここでいくつかのキー、特に **adminUsername**、**adminPassword** (複雑さのルールにご注意ください) の値、ストレージ アカウント名、必要な DNS 名を指定する必要があることがわかります。
    
    {
      "newStorageAccountName": {
        "value": "uniquestorageaccountname"
      },
      "adminUsername": {
        "value": ""
      },
      "adminPassword": {
        "value": ""
      },
      "dnsNameForPublicIP": {
        "value": "uniquednsnameforpublicip"
      },
      "vmSourceImageName": {
        "value": "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_2_LTS-amd64-server-20150309-ja-jp-30GB"
      },
      "location": {
        "value": "West US"
      },
      "virtualNetworkName": {
        "value": "myVNET"
      },
      "vmSize": {
        "value": "Standard_A0"
      },
      "vmName": {
        "value": "myVM"
      },
      "publicIPAddressName": {
        "value": "myPublicIP"
      },
      "nicName": {
        "value": "myNic"
      }
    }
    
新しい値を追加するか (この場合、新しいストレージと DNS リソースを作成できる場合は、それらが Azure により作成されます)、作成済みのリソースを使用します。次の azuredeploy parameters.json ファイルを例として示します。




次の URL で "空の" azuredeploy-parameters.json からパラメーター ファイルを取得します。このファイルは対話型のメソッドを使用する場合に動作します。ダウンロードしてカスタマイズしたパラメーター ファイルを使用する場合は、代わりに --template-file <template-file> オプションを使用する必要があります。また、実行する内容に応じて、これらのファイルの任意の部分から個々のセクションを抽出するスクリプトも記述しています。Json 解析を行う場合は、jq: curl http://stedolan.github.io/jq/download/linux64/jq -o/usr/bin/jq が必要になることも示す必要があります。


### テンプレートおよびパラメーター ファイルをデプロイする


[AZURE.NOTE]一部のテンプレートには、対応する azuredeploy-parameters.json ファイルがない場合もあります。

使用するテンプレートに応じて、パラメーターを設定するか、パラメーターが既にテンプレートの一部になっている場合もあります。これらのケースでは、

テンプレートにそのパラメーターが直接含まれている場合、または自分でパラメーターのデータを抽出する場合。テンプレートの構造の詳細については、「[Azure リソース マネージャーのテンプレート言語](https://msdn.microsoft.com/library/azure/dn835138.aspx)」


https://github.com/azure/azurermtemplates/raw/master/linux-virtual-machine-with-customdata/azuredeploy.json (または、単に azuredeploy-parameters.json ファイル) を参照してください。テンプレートのパラメーター セクションを抽出するか (この場合、テンプレート自体の内部に保存し直す必要があります)、別の azuredeploy-parameters.json ファイルとして抽出できます。パラメーターに格納する値を取得する必要があります。

## azuredeploy-templates.json ファイルを変更する

必要な値を収集します。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ

Vestibul ante ipsum primis in faucibus orci luctus et ultrices posuere cubilia Curae; Nullam ultricies, ipsum vitae volutpat hendrerit, purus diam pretium eros, vitae tincidunt nulla lorem sed turpis: [Link 3 to another azure.microsoft.com documentation topic](storage-whatis-account.md).

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--HONumber=52-->
