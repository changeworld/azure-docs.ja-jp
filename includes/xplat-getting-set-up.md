<properties services="virtual-machines" title="Setting up xplat-cli for service management" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## xplat-cli の使用

次の手順を実行すると、適切なサブスクリプションで xplat-cli の最新のバージョンを簡単に使用できます。まず xplat-cli をインストールしてアカウントに接続する必要があります。[Azure コマンド ライン インターフェイス (xplat-cli)](xplat-cli.md) に関するページを参照してください。

### 手順 1. xplat-cli のバージョンを更新する

サービス管理モードで命令型コマンドに xplat-cli を使用するには、可能な限り最新バージョンを使用する必要があります。バージョンを確認するには、「`azure --version`」と入力します。次のような結果が表示されます。

    $ azure --version
    0.8.17 (node: 0.10.25)
    
xplat-cli のバージョンを更新する場合は、[xplat-cli](https://github.com/Azure/azure-xplat-cli) を参照してください。

### 手順 2. Azure アカウントとサブスクリプションを設定する

使用するアカウントに xplat-cli を接続すると、複数のサブスクリプションが存在する場合があります。この場合は「`azure account list`」と入力して、アカウントで使用可能なサブスクリプションを確認してから、「`azure account set <subscription id or name> true`」と入力して使用するサブスクリプションを選択します。ここで _subscription id or name_ は、現在のセッションで使用するサブスクリプション id またはサブスクリプション名です。次のように表示されます。

    $ azure account set "Visual Studio Ultimate with MSDN" true
    info:    Executing command account set
    info:    Setting subscription to "Visual Studio Ultimate with MSDN" with id "0e220bf6-5caa-4e9f-8383-51f16b6c109f".
    info:    Changes saved
    info:    account set command OK
    
> [AZURE.NOTE]Azure アカウントはまだ取得しておらず、MSDN サブスクリプションのサブスクリプションのみがある場合は、[ここから MSDN サブスクリプション会員の特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)をアクティブ化して無料の Azure クレジットを取得するか、無料のアカウントを使用できます。どちらを使用しても Azure にアクセスできます。

<!--HONumber=52-->
