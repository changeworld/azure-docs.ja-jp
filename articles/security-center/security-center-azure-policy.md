---
title: "Azure Security Center のセキュリティ ポリシーを Azure Policy に統合する | Microsoft Docs"
description: "このドキュメントでは、Azure Security Center のセキュリティ ポリシーを Azure Policy に統合するための構成方法について説明します。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: c359b77efbfdbcf7d2c0193dc015ce418a3d997d
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2017
---
# <a name="integrate-security-center-security-policies-with-azure-policy"></a>Security Center のセキュリティ ポリシーを Azure Policy に統合する
この記事では、Azure Policy を利用する Azure Security Center のセキュリティ ポリシーの構成方法について説明します。 

## <a name="how-security-policies-work"></a>セキュリティ ポリシーのしくみ
Security Center では、Azure サブスクリプションごとに自動で既定のセキュリティ ポリシーが作成されます。 Security Center でポリシーを編集するか、[Azure Policy](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) を使用して、以下を行うことができます。
* 新しいポリシー定義を作成する。
* 組織全体または組織内の部署を表す、管理グループにまたがってポリシーを割り当てる。
* ポリシーのコンプライアンスを監視する。

> [!NOTE]
> Azure Policy は現在、制限付きのプレビュー段階です。 参加するには、「[Sign up for Azure Policy (Azure Policy へのサインアップ)](https://aka.ms/getpolicy)」に移動します。 Azure Policy の詳細については、「[コンプライアンスを強制するポリシーの作成と管理](http://docs.microsoft.com/azure/azure-policy/create-manage-policy)」を参照してください。

## <a name="edit-security-policies"></a>セキュリティ ポリシーの編集
Security Center では、各 Azure サブスクリプションの既定のセキュリティ ポリシーを編集できます。 セキュリティ ポリシーを変更するには、サブスクリプションまたはそれが含まれる管理グループの所有者、共同作成者、セキュリティ管理者のいずれかである必要があります。 Security Center でセキュリティ ポリシーを表示するには、次の操作を行います。

1. Azure ポータルにサインインします。

2. **Security Center** ダッシュボードの **[全般]** で、**[セキュリティ ポリシー]** を選択します。

    ![[ポリシー管理] ウィンドウ](./media/security-center-policies/security-center-policies-fig10.png)

3. セキュリティ ポリシーを有効にするサブスクリプションを選択します。  

4. **[ポリシー コンポーネント]** セクションで、**[セキュリティ ポリシー]** を選択します。  
    **[基本]** ウィンドウが開きます。

    ![ポリシー コンポーネント](./media/security-center-policies/security-center-policies-fig12.png)

5. ポリシー定義を削除するには、**[Policies and parameters]\(ポリシーとパラメーター\)** で、削除する定義の横にある **[削除]** を選択します。

6. **[Save]** をクリックします。  
    **[Available Definitions]\(使用できる定義\)** ウィンドウが開き、Azure Policy を介して Security Center に割り当てられている既定のポリシーが表示されます。 

7. (省略可能) **[Available Definitions]\(使用できる定義\)** ウィンドウで、次のいずれかの操作を実行します。

    * ポリシー定義を追加するには、定義の横にあるプラス記号 (+) を選択します。

    ![使用できるポリシー定義](./media/security-center-policies/security-center-policies-fig11.png)

    * ポリシーの詳細を確認するには、該当するポリシーを選択します。  
    定義の **[プレビュー]** ウィンドウが開きます。 定義の説明と、[ポリシー定義](../azure-policy/policy-definition.md)の構造を提供する JSON コードへのリンクが表示されます。

    ![定義の [プレビュー] ウィンドウ](./media/security-center-policies/security-center-policies-fig14.png)

7. 編集が終了したら、**[保存]** を選択します。

## <a name="available-security-policy-definitions"></a>利用可能なセキュリティ ポリシーの定義

既定のセキュリティ ポリシーで利用可能なポリシー定義については、次の表を参照してください。 

| [ポリシー] | 有効なポリシーによって実行される内容 |
| --- | --- |
| システムの更新プログラム |Windows Update または Windows Server Update Services から、利用できる毎日のセキュリティ更新プログラムと重要な更新プログラムの一覧を取得します。 取得される一覧は、その仮想マシンに対して構成されているサービスによって異なります。この一覧によって、不足している更新プログラムを適用するよう推奨されます。 Linux システムの場合、ポリシーはディストリビューション提供のパッケージ管理システムを利用し、使用できる更新プログラムが含まれているパッケージを特定します。 また、[Azure Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md) 仮想マシンのセキュリティ更新プログラムと重要な更新プログラムについても確認します。 |
| OS の脆弱性 |オペレーティング システム構成を毎日分析し、攻撃に対する仮想マシンの脆弱性を引き起こすおそれのある問題を特定します。 また、このポリシーによって、これらの脆弱性に対応するための構成の変更が推奨されます。 監視対象の具体的な構成の詳細については、[推奨されるベースラインの一覧](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)を参照してください。 (現時点では、Windows Server 2016 は完全にはサポートされていません。) |
| エンドポイント保護 |ウイルス、スパイウェア、およびその他の悪意のあるソフトウェアを特定して削除するため、すべての Windows 仮想マシン (VM) にエンドポイント保護を設定することをお勧めします。 |
| ディスクの暗号化 |静止データの保護を強化するために、ディスク暗号化はすべての仮想マシンで有効にすることをお勧めします。 |
| ネットワーク セキュリティ グループ |パブリック エンドポイントがある VM への受信トラフィックと送信トラフィックを制御するように、 [ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md) を構成することを推奨します。 サブネットで構成されたネットワーク セキュリティ グループは、他に指定しない限り、すべての仮想マシンのネットワーク インターフェイスによって継承されます。 このポリシーは、ネットワーク セキュリティ グループが構成されているかどうかをチェックするほか、受信セキュリティ規則を評価して、受信トラフィックを許可する規則を特定します。 |
| Web アプリケーション ファイアウォール |次のいずれかにあてはまる場合に、Web アプリケーション ファイアウォールを仮想マシンに設定することを推奨します。 <ul><li>[インスタンスレベル パブリック IP](../virtual-network/virtual-networks-instance-level-public-ip.md) が使用されていて、関連するネットワーク セキュリティ グループの受信セキュリティ規則が、ポート 80/443 にアクセスできるように構成されている。</li><li>負荷分散された IP が使用されていて、関連する負荷分散規則と受信ネットワーク アドレス変換 (NAT) 規則が、ポート 80/443 にアクセスできるように構成されている。 詳細については、「 [Azure Resource Manager によるロード バランサーのサポート](../load-balancer/load-balancer-arm.md)」を参照してください。</li> |
| 次世代のファイアウォール |Azure に組み込まれているネットワーク セキュリティ グループの適用範囲外までネットワーク保護を拡張します。 次世代ファイアウォールの利用が推奨されるデプロイが Security Center によって検出されると、仮想アプライアンスを設定できるようになります。 |
| SQL の監査と脅威の検出 |コンプライアンスのために、また高度な脅威検出の調査のために、Azure データベースへのアクセスの監査を有効にすることを推奨します。 |
| SQL の暗号化 |Azure SQL データベース、関連付けられたバックアップ、トランザクション ログ ファイルに対し、保存中の暗号化を有効にすることを推奨します。 セキュリティ侵害を受けた場合でも、データが読み取られることはありません。 |
| 脆弱性評価 |VM に脆弱性評価ソリューションをインストールすることをお勧めします。 |
| ストレージ暗号化 |現在この機能は、Azure Blob Storage と Azure Files で利用できます。 [Storage サービスの暗号化] を有効にした後、暗号化されるのは新しいデータのみであり、このストレージ アカウントにある既存のファイルは暗号化されません。 |
| JIT ネットワーク アクセス |ジャスト イン タイム ネットワーク アクセスが有効になっている場合、Security Center ではネットワーク セキュリティ グループの規則の作成により、Azure VM への受信トラフィックがロックダウンされます。 ユーザーは VM 上の受信トラフィックをロックダウンする必要があるポートを選択します。 詳細については、「[Manage virtual machine access using just in time (ジャスト イン タイムを使用して仮想マシンへのアクセスを管理する)](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)」を参照してください。 |


## <a name="next-steps"></a>次のステップ
この記事では、Security Center でのセキュリティ ポリシーの構成方法について説明しました。 Security Center の詳細については、次の記事を参照してください。

* [Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md): Azure Security Center に関する設計上の考慮事項を計画および理解する方法について説明します。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md): Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md): セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md): パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md): このサービスの使用に関してよく寄せられる質問に対する回答を確認します。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/): Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
