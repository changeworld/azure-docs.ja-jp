---
title: Azure Lab Services で VirtualBox にエシカル ハッキング ラボを設定する | Microsoft Docs
description: Azure Lab Services を使用してラボを設定し、VirtualBox でエシカル ハッキングを教える方法について説明します。
ms.topic: article
ms.date: 06/11/2021
ms.openlocfilehash: 49db4562a7d541a4c3409a5956a67e3bd0aadef4
ms.sourcegitcommit: 40dfa64d5e220882450d16dcc2ebef186df1699f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2021
ms.locfileid: "113040791"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class-with-virtualbox"></a>VirtualBox でエシカル ハッキング クラスを教えるためのラボを設定する

この記事では、エシカル ハッキングのフォレンジクス面に焦点を当てたクラスを設定する方法について説明します。 侵入テストは、悪意のある攻撃者が利用する可能性のある脆弱性を実証するためにシステムまたはネットワークへの侵入を試みる訓練で、エシカル ハッキング コミュニティで使用します。

エシカル ハッキング クラスの学生は、脆弱性に対する防御のための最新の手法を学習できます。 各学生は、3 つの仮想マシンが入れ子になったホスト仮想マシンを用意します。そのうち 2 つの仮想マシンには [Seed](https://seedsecuritylabs.org/lab_env.html) イメージを、もう 1 つのマシンには [Kali Linux](https://www.kali.org/) イメージを使用します。 Seed 仮想マシンは悪用目的に使用し、Kali 仮想マシンでは、フォレンジック タスクの実行に必要なツールを利用できます。

この記事は 2 つの主要なセクションから成ります。 最初のセクションでは、クラスルーム ラボを作成する方法について説明します。 2 番目のセクションでは、入れ子になった仮想化を有効にし、必要なツールとイメージを使用して、テンプレート マシンを作成する方法について説明します。 ここでは、イメージのホストとして [VirtualBox](https://www.virtualbox.org/) を有効にしたマシンで、2 つの Seed イメージと 1 つの Kali Linux イメージをホストします。

## <a name="lab-configuration"></a>ラボの構成

このラボを設定するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 Azure サブスクリプションを取得したら、Azure Lab Services で新しいラボ アカウントを作成するか、既存のアカウントを使用することができます。 新しいラボ アカウントを作成するには、次のチュートリアルを参照してください: 「[チュートリアル: Azure Lab Services でラボ アカウントを設定する](tutorial-setup-lab-account.md)」。

[こちらのチュートリアル](tutorial-setup-classroom-lab.md)に従い、新しいラボを作成して次の設定を適用します。

| 仮想マシンのサイズ | Image |
| -------------------- | ----- |
| 中 (入れ子になった仮想化) | Windows Server 2019 Datacenter |
| 中 (入れ子になった仮想化) | Windows 10 |

## <a name="template-machine"></a>テンプレート マシン

テンプレート マシンを作成したら、マシンを起動して接続し、次の 3 つの主要なタスクを実行します。

1. [VirtualBox](https://www.virtualbox.org/) を使用して仮想マシンを入れ子にするようマシンを設定します。
2. [Kali](https://www.kali.org/) Linux イメージを設定します。 Kali は、侵入テストとセキュリティ監査用のツールが含まれる Linux ディストリビューションです。
3. Seed イメージを設定します。 この例では、[Seed](https://seedsecuritylabs.org/lab_env.html) イメージを使用します。 このイメージは、セキュリティ トレーニング専用に作成されます。

この記事の残りの部分では、上記のタスクを手動で実行する手順について説明します。

### <a name="installing-virtualbox"></a>VirtualBox をインストールする

1. Windows ホスト オプションを選択して [VirtualBox プラットフォーム パッケージ](https://www.virtualbox.org/wiki/Downloads)をダウンロードします。
2. インストールの実行可能ファイルを実行し、既定のオプションを使用してインストールを完了します。

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Kali Linux イメージを使用して入れ子になった仮想マシンを設定する

Kali は、侵入テストとセキュリティ監査用のツールが含まれる Linux ディストリビューションです。

1. [Kali Linux VM VirtualBox イメージ](https://www.kali.org/get-kali/#kali-virtual-machines)から ova イメージをダウンロードします。  32bit バージョンをお勧めします。64bit バージョンでは、読み込み時にエラーが発生します。  ダウンロード ページに記載されている既定のユーザー名とパスワードを覚えておいてください。
2. VirtualBox Manager を開いて [.ova イメージをインポート](https://docs.oracle.com/cd/E26217_01/E26796/html/qs-import-vm.html)します。  続行するには、Kali ライセンス契約を確認し、同意する必要があります。

>[!Note]
>- Kali VM に使用される VirtualBox の既定の RAM は 2 GB (2,048) です。ニーズに応じて最低でも 4 GB (4,096) に RAM を増やすことをお勧めします。  これは、学生が各自の VM で変更できます。  VirtualBox 内の RAM サイズを変更しても、ラボの VM サイズは変わりません。
>- 既定では、ハード ディスクが 80 GB 上限に設定されていますが、動的に割り当てが行われます。  ラボ サービス マシンのハード ドライブ領域は 128 GB に制限されています。このディスク サイズを超えないようご注意ください。
>- Kali イメージは USB 2.0 対応ですが、[Oracle VM VirtualBox Extension Pack](https://www.virtualbox.org/wiki/Downloads) が必要です。ない場合は、[USB] タブで USB コントローラーを 1.0 に設定してください。

### <a name="setup-seed-lab-images"></a>Seed ラボ イメージを設定する

1. [SEED Labs VM イメージ](https://seedsecuritylabs.org/labsetup.html)をダウンロードして展開します。
2. 指示に従って [VirtualBox に VM を作成](https://github.com/seed-labs/seed-labs/blob/master/manuals/vm/seedvm-manual.md)します。
   複数の SEED VM が必要な場合は、各マシンについて、同じ .iso を使用して .iso のコピーを作成します。異なるマシンでは、適切に機能しません。

>[!IMPORTANT] 
>テンプレートを発行する前に、入れ子になったすべての仮想マシンの電源がオフになっていることを確認してください。  電源オンのままにすると、仮想マシンの破損を含め、予期しない副作用が生じます。

## <a name="cost"></a>コスト  

このラボのコストを見積もるには、次の例を使用できます。

20 時間の授業が予定されていて、宿題または課題のために 10 時間のクォータが用意されている、25 人の学生のクラスの場合、ラボの価格は次のようになります:

学生 25 人 \* (20 + 10) 時間 \* 55 ラボ ユニット \* 0.01 USD/時間 = 412.50 USD

>[!IMPORTANT]
>コストの見積もりはあくまでも例です。 価格について詳しくは、「[Azure Lab Services の価格](https://azure.microsoft.com/pricing/details/lab-services/)」をご覧ください。

## <a name="conclusion"></a>まとめ

この記事では、エシカル ハッキング クラスのラボを作成する手順について説明しました。 侵入テストを行えるように、入れ子になった仮想化を設定して、ホスト仮想マシン内に 2 つの仮想マシンを作成する手順が含まれます。

## <a name="next-steps"></a>次のステップ

次の手順は、どのラボの設定でも同じです。

- [ユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [クォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)
- [スケジュールを設定する](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [登録リンクを学生に電子メールで送る](how-to-configure-student-usage.md#send-invitations-to-users)。
