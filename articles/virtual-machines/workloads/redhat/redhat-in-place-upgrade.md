---
title: Azure 上の Red Hat Enterprise Linux イメージのインプレース アップグレード
description: Red Hat Enterprise 7.x イメージから最新の 8.x バージョンへのインプレース アップグレードを実行する手順について説明します
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: beede74134affeb3ee0d4bdd20d5da3b4c5e6eda
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566624"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>インプレース アップグレードの Red Hat Enterprise Linux

この記事では、Azure で 'Leapp' ユーティリティを使用して Red Hat Enterprise Linux 7 から Red Hat Enterprise Linux 8 へのインプレース アップグレードを実行する手順について説明します。 インプレース アップグレード中に、既存の RHEL 7 オペレーティング システムが RHEL 8 バージョンに置き換えられます。

>[!Note] 
> Red Hat Enterprise Linux プランの SQL Server では、Azure でのインプレース アップグレードはサポートされていません。

## <a name="what-to-expect-during-the-upgrade"></a>アップグレード中の予期される動作
アップグレード中にシステムが数回再起動しますが、これは正常です。 最後の再起動により、VM が RHEL 8 最新のマイナー リリースにアップグレードされます。 

アップグレード プロセスには 20 分から数時間かかることがあります。これは、VM サイズやシステムにインストールされているパッケージの数など、いくつかの要因によって異なります。

## <a name="preparations-for-the-upgrade"></a>アップグレードの準備
インプレース アップグレードは、お客様がシステムを次のメジャー バージョンにアップグレードできるようにするための、Red Hat と Azure によって公式に推奨されている方法です。 ここでアップグレードを実行する前に、次の点に注意して考慮してください。 

>[!Important] 
> アップグレードを実行する前に、イメージのスナップショットを作成してください。

>[!NOTE]
> この記事のコマンドは、ルート アカウントを使用して実行する必要があります

* 最新の RHEL 7 バージョン (現時点では RHEL 7.9) を使用していることを確認してください。 ロックされたバージョンを使用していて、RHEL 7.9 にアップグレードできない場合は、[こちらの手順を使用して EUS 以外のリポジトリに切り替える](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/redhat-rhui#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock)ことができます。

* 次のコマンドを実行して、アップグレードの進行状況と完了したかどうかを確認します。 このコマンドにより、プロセスと実行されている内容、およびアップグレードが可能かどうかを説明するファイルが '/var/log/leapp/leapp-report.txt' の下に生成されます
    ```bash
    leapp preupgrade --no-rhsm
    ```
* シリアル コンソールが確実に機能することで、アップグレード プロセス中の監視が可能になります。

* `/etc/ssh/sshd_config` で SSH ルート アクセスを有効にします
    1. `/etc/ssh/sshd_config` ファイルを開きます
    1. '#PermitRootLogin yes' を検索します
    1. '#' を削除してコメントを解除します

## <a name="steps-for-performing-the-upgrade"></a>アップグレードの実行手順

これらの手順は、慎重に実行してください。 間違いなく、実稼働インスタンスより前のテスト マシンでアップグレードを試すことが推奨されます。

1. yum update を実行して、最新のクライアント パッケージを取得します。
    ```bash
    yum update -y
    ```

1. leapp-client-package をインストールします。
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. [RedHat ポータル](https://access.redhat.com/articles/3664871)にある、repomap.csv と pes-events.json を含む leapp-data.tar.gz ファイルを使用して、それらを抽出します。 
    1. ファイルをダウンロードします。
    1. 次のコマンドを使用して、内容を抽出し、ファイルを削除します。
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. 'Leapp' に 'answers' ファイルを追加します。
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. 'Leapp' アップグレードを実行します。
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  `leapp upgrade` コマンドが正常に完了したら、手動でシステムを再起動してプロセスを完了します。 システムは数回再起動され、その間は使用できなくなります。 シリアル コンソールを使用してプロセスを監視します。

1.  アップグレードが正常に完了したことを確認します。
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. アップグレードが完了したら、root ssh アクセスを削除します。
    1. `/etc/ssh/sshd_config` ファイルを開きます
    1. '#PermitRootLogin yes' を検索します
    1. コメントに '#' を追加します

1. sshd サービスを再起動して、変更を反映させます
    ```bash
    systemctl restart sshd
    ```

## <a name="common-issues"></a>一般的な問題
これらは、`leapp preupgrade` または `leapp upgrade` のいずれかのプロセスが失敗するおそれがある一般的なインスタンスの一部です。

**エラー:次の無効なプラグイン パターンに一致するものが見つかりませんでした**
```plaintext
STDERR:
No matches found for the following disabled plugin patterns: subscription-manager
Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
```
**解決策**\
ファイル `/etc/yum/pluginconf.d/subscription-manager.conf` を編集し、enabled を `enabled=0` に変更して、subscription-manager プラグインを無効にします。

これは、PAYG VM に使用されていない subscription-manager yum プラグインが有効になっていることが原因で発生します。

**エラー:ルートを使用したリモート ログインで発生するおそれのある問題** `leapp preupgrade` は次のエラーで失敗する場合があります。
```structured-text
============================================================
                     UPGRADE INHIBITED
============================================================

Upgrade has been inhibited due to the following problems:
    1. Inhibitor: Possible problems with remote login using root account
Consult the pre-upgrade report for details and possible remediation.

============================================================
                     UPGRADE INHIBITED
============================================================
```
**解決策**\
`/etc/sshd_conf` でルート アクセスを有効にします。
これは、「[アップグレードの準備](#preparations-for-the-upgrade)」セクションのとおりに `/etc/sshd_conf` で root ssh アクセスを有効にしていないことが原因で発生します。 

## <a name="next-steps"></a>次のステップ
* [Azure の Red Hat イメージ](./redhat-images.md)の詳細を確認します。
* [Red Hat Update Infrastructure](./redhat-rhui.md) の詳細を確認します。
* [RHEL BYOS プラン](./byos.md)の詳細を確認します。
* Red Hat のインプレース アップグレード プロセスに関する情報については、Red Hat のドキュメント、「[RHEL 7 から RHEL 8 へのアップグレード](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index)」を参照してください
* すべてのバージョンの RHEL に対する Red Hat のサポート ポリシーに関する情報は、「[Red Hat Enterprise Linux Life Cycle \(Red Hat Enterprise Linux のライフ サイクル\)](https://access.redhat.com/support/policy/updates/errata)」ページに記載されています。