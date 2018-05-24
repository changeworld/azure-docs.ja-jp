---
title: Azure Security Center でのセキュリティ ポリシーの設定 | Microsoft Docs
description: この記事は、Azure Security Center でのセキュリティ ポリシーを構成する場合に役立ちます。
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2018
ms.author: yurid
ms.openlocfilehash: fed4a587d2258cdab35d09f5e3947a8cacade157
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364388"
---
# <a name="set-security-policies-in-azure-security-center"></a>Azure Security Center でのセキュリティ ポリシーの設定
この記事は、Security Center でのセキュリティ ポリシーを構成する場合に役立ちます。

## <a name="how-security-policies-work"></a>セキュリティ ポリシーのしくみ
Security Center では、Azure サブスクリプションごとに自動で既定のセキュリティ ポリシーが作成されます。 Security Center でポリシーを編集したり、ポリシーのコンプライアンスを監視したりすることができます。

> [!NOTE]
> Azure Policy (限定プレビュー) を使って Security Center のポリシーを拡張できるようになりました。 プレビューに参加するには、「[Sign up for Azure Policy (Azure Policy へのサインアップ)](https://aka.ms/getpolicy)」に移動します。 詳細については、「[Security Center のセキュリティ ポリシーを Azure Policy に統合する](security-center-azure-policy.md)」を参照してください。

開発やテストに使用されるリソースと運用アプリケーションで使用されるリソースとでは、セキュリティ要件が異なる場合があります。 個人情報のように規制されたデータが使用されるアプリケーションには、より高いレベルのセキュリティが必要と考えられます。 Azure Security Center で有効化されているセキュリティ ポリシーは、セキュリティに関する推奨事項と監視を促進して、潜在的な脆弱性を識別し、脅威を軽減します。 自分に合った選択肢を判断する方法の詳細については、「[Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md)」を参照してください。

## <a name="edit-security-policies"></a>セキュリティ ポリシーの編集
Security Center では、各 Azure サブスクリプションの既定のセキュリティ ポリシーを編集できます。 セキュリティ ポリシーを変更するには、そのサブスクリプションの所有者、共同作業者、またはセキュリティ管理者である必要があります。 Security Center でセキュリティ ポリシーを構成するには、次の操作を行います。

1. Azure ポータルにサインインします。

2. **Security Center** ダッシュボードの **[全般]** で、**[セキュリティ ポリシー]** を選択します。

3. セキュリティ ポリシーを有効にするサブスクリプションを選択します。

4. **[ポリシー コンポーネント]** セクションで、**[セキュリティ ポリシー]** を選択します。  
    これが、Security Center によって割り当てられる既定のポリシーとなります。 利用可能なセキュリティ推奨事項について、そのオンとオフを切り替えることができます。

5. 編集が終了したら、**[保存]** を選択します。

## <a name="available-security-policy-definitions"></a>利用可能なセキュリティ ポリシーの定義

既定のセキュリティ ポリシーで利用可能なポリシー定義については、次の表を参照してください。

| ポリシー | ポリシーによって実行される内容 |
| --- | --- |
| システムの更新プログラム |Windows Update または Windows Server Update Services から、利用できる毎日のセキュリティ更新プログラムと重要な更新プログラムの一覧を取得します。 取得される一覧は、その仮想マシンに対して構成されているサービスによって異なります。この一覧によって、不足している更新プログラムを適用するよう推奨されます。 Linux システムの場合、ポリシーはディストリビューション提供のパッケージ管理システムを利用し、使用できる更新プログラムが含まれているパッケージを特定します。 また、[Azure Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md) 仮想マシンのセキュリティ更新プログラムと重要な更新プログラムについても確認します。 |
| セキュリティ構成 |オペレーティング システム構成を毎日分析し、攻撃に対する仮想マシンの脆弱性を引き起こすおそれのある問題を特定します。 また、このポリシーによって、これらの脆弱性に対応するための構成の変更が推奨されます。 監視対象の具体的な構成の詳細については、[推奨されるベースラインの一覧](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)を参照してください。 (現時点では、Windows Server 2016 は完全にはサポートされていません。) |
| エンドポイント保護 |ウイルス、スパイウェア、およびその他の悪意のあるソフトウェアを特定して削除するため、すべての Windows 仮想マシン (VM) にエンドポイント保護を設定することをお勧めします。 |
| ディスクの暗号化 |静止データの保護を強化するために、ディスク暗号化はすべての仮想マシンで有効にすることをお勧めします。 |
| ネットワーク セキュリティ グループ |パブリック エンドポイントがある VM への受信トラフィックと送信トラフィックを制御するように、 [ネットワーク セキュリティ グループ](../virtual-network/security-overview.md) を構成することを推奨します。 サブネットで構成されたネットワーク セキュリティ グループは、他に指定しない限り、すべての仮想マシンのネットワーク インターフェイスによって継承されます。 このポリシーは、ネットワーク セキュリティ グループが構成されているかどうかをチェックするほか、受信セキュリティ規則を評価して、受信トラフィックを許可する規則を特定します。 |
| Web アプリケーション ファイアウォール |次のいずれかにあてはまる場合に、Web アプリケーション ファイアウォールを仮想マシンに設定することを推奨します。 <ul><li>[インスタンスレベル パブリック IP](../virtual-network/virtual-networks-instance-level-public-ip.md) が使用されていて、関連するネットワーク セキュリティ グループの受信セキュリティ規則が、ポート 80/443 にアクセスできるように構成されている。</li><li>負荷分散された IP が使用されていて、関連する負荷分散規則と受信ネットワーク アドレス変換 (NAT) 規則が、ポート 80/443 にアクセスできるように構成されている。 詳細については、「 [Azure Resource Manager によるロード バランサーのサポート](../load-balancer/load-balancer-arm.md)」を参照してください。</li> |
| 次世代のファイアウォール |Azure に組み込まれているネットワーク セキュリティ グループの適用範囲外までネットワーク保護を拡張します。 次世代ファイアウォールの利用が推奨されるデプロイが Security Center によって検出されると、仮想アプライアンスを設定できるようになります。 |
| SQL の監査と脅威の検出 |コンプライアンスのために、また高度な脅威検出の調査のために、SQL データベースへのアクセスの監査を有効にすることを推奨します。 |
| SQL の暗号化 |SQL データベース、関連付けられたバックアップ、トランザクション ログ ファイルに対し、保存中の暗号化を有効にすることを推奨します。 セキュリティ侵害を受けた場合でも、データが読み取られることはありません。 |
| 脆弱性評価 |VM に脆弱性評価ソリューションをインストールすることをお勧めします。 |
| ストレージ暗号化 |現在この機能は、BLOB と Azure Files で利用できます。 [Storage サービスの暗号化] を有効にした後、暗号化されるのは新しいデータのみであり、このストレージ アカウントにある既存のファイルは暗号化されません。 |
| JIT ネットワーク アクセス |ジャスト イン タイム ネットワーク アクセスが有効になっている場合、Security Center ではネットワーク セキュリティ グループの規則の作成により、Azure VM への受信トラフィックがロックダウンされます。 ユーザーは VM 上の受信トラフィックをロックダウンする必要があるポートを選択します。 詳細については、「[Manage virtual machine access using just in time (ジャスト イン タイムを使用して仮想マシンへのアクセスを管理する)](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)」を参照してください。 |


## <a name="next-steps"></a>次の手順
この記事では、Security Center でのセキュリティ ポリシーの構成方法について説明しました。 Security Center の詳細については、次の記事を参照してください。

* [Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md): Azure Security Center に関する設計上の考慮事項を計画および理解する方法について説明します。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md): Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md): セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md): パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md): このサービスの使用に関してよく寄せられる質問に対する回答を確認します。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/): Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
