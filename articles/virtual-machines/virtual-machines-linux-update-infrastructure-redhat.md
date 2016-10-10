<properties
   pageTitle="Red Hat Update Infrastructure (RHUI) | Microsoft Azure"
   description="Microsoft Azure のオンデマンド Red Hat Enterprise Linux インスタンス用の Red Hat Update Infrastructure (RHUI) について説明します。"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="BorisB2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="borisb"/>

# Azure のオンデマンド Red Hat Enterprise Linux VM 用 Red Hat Update Infrastructure (RHUI)

Azure Marketplace から利用できるオンデマンドの Red Hat Enterprise Linux (RHEL) イメージから作成した仮想マシンは、Azure にデプロイされた Red Hat Update Infrastructure (RHUI) にアクセスするよう登録されています。オンデマンド RHEL インスタンスは、リージョンの yum リポジトリへのアクセス権を持ち、増分更新を受信することができます。

RHUI によって管理される yum リポジトリの一覧は、プロビジョニング中に RHEL インスタンスで構成されます。特別な構成を行う必要はありません。RHEL インスタンスが最新の更新を取得できる状態になった後で `yum update` を実行してください。

> [AZURE.NOTE] Azure RHUI インフラストラクチャが最近 (2016 年 9 月) 更新されたため、Azure RHUI へのアクセスが中断されないよう、既存の RHEL インスタンスの構成を変更する必要があります。詳細については、「RHUI Azure インフラストラクチャの更新」のセクションを参照してください。


## RHUI Azure インフラストラクチャの更新
2016 年 9 月より、Azure では Red Hat Update Infrastructure (RHUI) サーバーの新しいセットが提供されています。これらのサーバーは [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) と共にデプロイされるため、単一のエンドポイント (rhui-1.micrsoft.com) をリージョンに関係なく任意の VM で使用できます。また、よく知られた証明機関 (Baltimore Root) にチェーンされている SSL 証明書を使用します。RHUI 更新サーバー用に ACL やカスタムのルーティング テーブルを使用する一部のお客様の場合は、この更新を自動化すると危険があるため、この更新は "オプトイン" となっています。 このページには、これらの新しいサーバーに手動でオンボードする手順と、(個々の手順が検証されたときに) 自動化された方式でオンボードするための完全なスクリプトが記載されています。Azure Marketplace にある新しい RHEL PAYG イメージ (日付が 2016 年 9 月以降のバージョン) では、自動的に新しい Azure RHUI サーバーがポイントされるため、追加アクションは不要です。

### 新しい Azure RHUI インフラストラクチャのオンボード タイムライン

| Date | 注 |
| --- | --- |
|2016 年 9 月 22 日 | RHUI サーバーとインストールの手順が使用可能になります。新しい (2016年 9 月の日付の) RHEL PAYG Marketplace イメージを使用してデプロイされた VM では新しい RHUI サーバーが自動的に使用されますが、既存の VM では "オプトイン" になります。
|2016 年 11 月 1 日 | 従来の RHEL PAYG VM イメージ (以前の Azure RHUI サーバーを使用する) が Azure Marketplace ギャラリーから削除されます。
|2017 年 1 月 16 日 | 以前の Azure RHUI サーバーは廃止されます。Azure RHUI へのアクセスを維持するには、影響を受けるすべての PAYG RHEL VM をこのときまでに更新してください。

### 新しい RHUI サーバーの IP アドレス

```
13.91.45.82
52.187.72.244
52.174.166.207
40.112.59.164
```

### 新しい Azure RHUI サーバーを使用するための手動更新の手順

(Curl を使用して) 公開キー署名をダウンロードします。

```
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
```

ダウンロードしたキーを検証します。

```
gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
```

出力を確認し、`keyid` と `user ID packet` を検証します。

```
Version: GnuPG v1.4.7 (GNU/Linux)
:public key packet:
        version 4, algo 1, created 1446074508, expires 0
        pkey[0]: [2048 bits]
        pkey[1]: [17 bits]
        keyid: EB3E94ADBE1229CF
:user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
:signature packet: algo 1, keyid EB3E94ADBE1229CF
        version 4, created 1446074508, md5len 0, sigclass 0x13
        digest algo 2, begin of digest 1a 9b
        hashed subpkt 2 len 4 (sig created 2015-10-28)
        hashed subpkt 27 len 1 (key flags: 03)
        hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
        hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
        hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
        hashed subpkt 30 len 1 (features: 01)
        hashed subpkt 23 len 1 (key server preferences: 80)
        subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
        data: [2047 bits]
```

公開キーをインストールします。

```
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
```

クライアント RPM をダウンロードして検証し、インストールします。

RHEL 6 の場合は次のようにダウンロードします。

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.0-2.noarch.rpm 
```

RHEL 7 の場合は次のようにします。

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.0-2.noarch.rpm  
```

次のように検証します。

```
rpm -Kv azureclient.rpm
```

出力で、パッケージの署名が正しいことを確認します。

```
azureclient.rpm:
    Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
    Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
    V3 RSA/SHA256 Signature, key ID be1229cf: OK
    MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
```

次のように RPM をインストールします。

```
sudo rpm -U azureclient.rpm
```

完了したら、VM から Azure RHUI にアクセスできることを確認します。

### 上記のタスクを自動化するオールインワンのスクリプト
必要な場合は次のスクリプトを使用して、影響を受ける VM を新しい Azure RHUI サーバーに更新するタスクを自動化できます。

```
# Download key
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 

# Validate key
if ! gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release | grep -q "keyid: EB3E94ADBE1229CF"; then
    echo "Keyfile azure.asc NOT valid. Exiting."
    exit 1
fi

# Install Key
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release

# Download RPM package
if grep -q "release 7" /etc/redhat-release; then
    ver=7
elif  grep -q "release 6" /etc/redhat-release; then
    ver=6
else
    echo "Version not supported, exiting"
    exit 1
fi

url=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel$ver/rhui-azure-rhel$ver-2.0-2.noarch.rpm
curl -o azureclient.rpm "$url"

# Verify package
if ! rpm -Kv azureclient.rpm | grep -q "key ID be1229cf: OK"; then
    echo "RPM failed validation ($url)"
    exit 1
fi

# Install package
sudo rpm -U azureclient.rpm
```

## RHUI の概要
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) では、Red Hat 認定クラウド プロバイダーがホストしている Red Hat Enterprise Linux クラウド インスタンス用に yum リポジトリ コンテンツを管理するために、拡張性の高いソリューションを提供します。アップストリームの Pulp プロジェクトに基づいて、RHUI では、クラウド プロバイダーが Red Hat でホストされるリポジトリ コンテンツをローカルでミラーし、独自のコンテンツを使ったカスタムのリポジトリを作成し、これらのリポジトリを負荷分散されたコンテンツ配信システムを介してエンド ユーザーの大規模グループが利用できるようにします。

## RHUI を使用できるリージョン
RHUI は、RHEL のオンデマンド イメージが提供されているすべてのリージョンで利用できます。これには、現在 [Azure の状態ダッシュボード](https://azure.microsoft.com/status/) ページに掲載されているすべてのパブリック リージョンが含まれます。RHEL のオンデマンド イメージからプロビジョニングされた VM の料金には、RHUI の利用料が含まれています。地域/国ごとのクラウド製品リリースは、RHEL のオンデマンド提供が今後拡大されていくにつれて変化します。

> [AZURE.NOTE] Azure でホストされている RHUI の利用は、[Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)内の VM に限定されます。

## 更新プログラムを他の更新リポジトリから取得する

更新プログラムを別の (Azure でホストされている RHUI 以外の) 更新リポジトリから取得する必要がある場合、RHUI からインスタンスの登録をいったん解除した後、必要な更新インフラストラクチャ (Red Hat Satellite、Red Hat カスタマー ポータル CDN など) に登録し直す必要があります。そうしたサービスと [Azure の Red Hat Cloud Access](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure) の登録には、適切な Red Hat サブスクリプションが必要となります。

RHUI の登録を解除し、目的の更新インフラストラクチャに登録し直すには、次の手順に従います。

1.	/etc/yum.repos.d/rh-cloud.repo を編集し、`enabled=1` をすべて `enabled=0` に変更します。次に例を示します。

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.	/etc/yum/pluginconf.d/rhnplugin.conf を編集し、`enabled=0` を `enabled=1` に変更します。
3.	次に、目的のインフラストラクチャ (Red Hat カスタマー ポータルなど) に登録します。[Red Hat カスタマー ポータルにシステムを登録してサブスクライブする方法](https://access.redhat.com/solutions/253273)については、Red Hat ソリューション ガイドに従ってください。

> [AZURE.NOTE] RHEL Pay-As-You-Go (PAYG: 従量課金) イメージの料金には、Azure でホストされる RHUI の利用料が含まれています。Azure でホストされている RHUI から PAYG RHEL VM の登録を解除しても、仮想マシンが BYOL (Bring-Your-Own-License: ライセンス持ち込み) タイプの VM に変換されるわけではありません。そのため同じ VM を別の更新ソースに登録した場合、二重に料金が発生する可能性があります。
> 
> Azure でホストされている RHUI 以外の更新インフラストラクチャを常に使用する必要がある場合は、[Azure 用の Red Hat ベースの仮想マシンを作成してアップロードする方法](virtual-machines-linux-redhat-create-upload-vhd.md)についての記事を参照して、独自の (BYOL タイプの) イメージを作成し、デプロイすることを検討してください。

## 次のステップ
Azure Marketplace の従量課金イメージから Red Hat Enterprise Linux VM を作成し、Azure でホストされる RHUI を利用するには、[Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/) にアクセスしてください。特別な設定を行わなくても、RHEL インスタンスで `yum update` を使用することができます。

<!---HONumber=AcomDC_0928_2016-->