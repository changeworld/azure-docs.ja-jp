<properties
    pageTitle="Azure ポータルを使用した Linux VM の作成 | Microsoft Azure"
    description="Azure ポータルを使用して Linux VM を作成します。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="04/29/2016"
    ms.author="v-livech"
/>

# ポータルを使用して Azure に Linux VM を作成する

この記事では、[Azure ポータル](https://portal.azure.com/)を使用して Linux 仮想マシンを簡単に作成する方法を示します。唯一の要件は、[Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)および [SSH 公開キーと秘密キーのファイル](virtual-machines-linux-mac-create-ssh-keys.md)です。

> [AZURE.NOTE] VM へのアクセスをセキュリティで保護するためにパスワードを使用する場合、パスワードは 13 文字以上にし、1 つ以上の大文字、1 つ以上の小文字、1 つ以上の特殊文字、および 1 つ以上の数字を含める必要があります。


1. Azure アカウント ID で Azure ポータルにサインインし、左上隅にある **[+ 新規]** をクリックします。

    ![screen1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

2. **[Marketplace]** で **[Virtual Machines]** をクリックし、**[おすすめアプリ]** イメージ リストから **[Ubuntu Server 14.04 LTS]** をクリックします。下部でデプロイ モデルとして `Resource Manager` が選択されていることを確認してから、**[作成]** をクリックします。

    ![screen2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

3. **[基本]** ページで、以下の項目を入力します。
    - VM の名前
    - 管理者ユーザーのユーザー名
    - 認証の種類 (**[SSH 公開キー]** に設定)
    - 文字列で表された SSH 公開キー (既定では、`~/.ssh/` ディレクトリから取得)
    - リソース グループ名 (新しいデプロイ グループを作成する場合)。または既存のグループを選択して、

    **[OK]** をクリックして続行し、VM サイズを選択すると、以下のように表示されます。

    ![screen3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

4. **[DS1]** サイズを選び (Premium SSD に Ubuntu をインストールする場合)、**[選択]** をクリックして設定を構成します。

    ![screen4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

5. **[設定]** の [ストレージ] と [ネットワーク] の値は既定値のままにし、**[OK]** をクリックして概要を表示します。DS1 を選択するとディスクの種類が Premium SSD に設定されることに注意してください。**S** は SSD を示します。

    ![screen5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

6. 新しい Ubuntu VM の設定を確認し、**[OK]** をクリックします。

    ![screen6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

7. ポータルのダッシュボードを開き、**[ネットワーク インターフェイス]** で NIC を選択します。

    ![screen7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

8. NIC 設定でパブリック IP アドレスのメニューを開きます。

    ![screen8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

9. SSH 公開キーを使用して、パブリック IP に SSH 接続します。

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## 次のステップ

テストまたはデモンストレーション用の Linux VM を迅速に作成しました。インフラストラクチャに合わせてカスタマイズした Linux VM を作成する方法については、次の記事を参照してください。

- [テンプレートを使用して Azure に Linux VM を作成する](virtual-machines-linux-cli-deploy-templates.md)
- [Azure テンプレートを使用して安全な Linux VM を作成する](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Azure CLI を使用して新しく Linux VM を作成する](virtual-machines-linux-create-cli-complete.md)

これらの記事を読むと、Azure インフラストラクチャの構築を始めることができ、独自またはオープン ソースのインフラストラクチャ デプロイ、構成、オーケストレーション ツールを使用できます。

<!---HONumber=AcomDC_0615_2016-->