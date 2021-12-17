---
title: コストと所有権の管理
description: この記事では、コストを最適化し、環境全体で所有権を整合させるのに役立つ情報を提供します。
ms.topic: how-to
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: a62ba95203ffea3162f7e79360bd36eb0f8f9733
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397605"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Azure DevTest Labs インフラストラクチャのガバナンス - コストと所有権の管理
コストと所有権は、開発およびテスト用の環境の構築を検討するときの主な懸案事項です。 このセクションでは、コストを最適化し、環境全体で所有権を整合させるのに役立つ情報を提供します。

## <a name="optimize-for-cost"></a>コストを最適化する

### <a name="question"></a>Question
DevTest Labs 環境内でコストを最適化にはどうすればよいですか?

### <a name="answer"></a>Answer
コストの最適化には、DevTest Labs のいくつかの組み込み機能が利用できます。 ユーザーのアクティビティを制限するには、[コスト管理としきい値](devtest-lab-configure-cost-management.md)と[ポリシー](devtest-lab-set-lab-policy.md)に関する記事をご覧ください。 

開発とテストのワークロードに DevTest Labs を利用するときは、Enterprise Agreement の一部として [Enterprise Dev/Test サブスクリプションのベネフィット](https://azure.microsoft.com/offers/ms-azr-0148p/)の利用を検討してください。 または、従量課金制をご利用の場合は、[従量課金制の DevTest プラン](https://azure.microsoft.com/offers/ms-azr-0023p/)を検討してください。

このアプローチには、いくつかの利点があります。

- Windows 仮想マシン、クラウド サービス、HDInsight、App Service、および Logic Apps について特別な低料金の Dev/Test 価格
- 他の Azure サービスについての魅力的な Enterprise Agreement (EA) 価格
- ギャラリー内の専用の Dev/Test イメージ (Windows 8.1 と Windows 10 を含む) へのアクセス
 
Enterprise Dev/Test サブスクリプション内で実行されている Azure リソースを利用できるのは、アクティブな Visual Studio サブスクライバーのみ (標準サブスクリプション、年次クラウド サブスクリプション、月次クラウド サブスクリプション) です。 ただし、エンド ユーザーはアプリケーションにアクセスして、フィードバックを提供したり、承認テストを実行したりできます。 このサブスクリプション内のリソースは、アプリケーションの開発とテストにのみ使用できます。 アップタイムの保証はありません。

DevTest プランを使用する場合、このベネフィットの利用はアプリケーションの開発とテストだけが対象となります。 Azure DevOps および HockeyApp の使用を除いて、このサブスクリプションでの使用には、金銭的な補償を伴う SLA は付随しません。

## <a name="define-role-based-access-across-your-organization"></a>組織全体でロールベースのアクセスを定義する
### <a name="question"></a>Question
IT 部門が管理作業を行い開発者/テスト担当者がそれぞれの作業を行うことができるように、DevTest Labs 環境に対して Azure ロールベースのアクセス制御を定義するにはどうすればよいですか。 

### <a name="answer"></a>Answer
大まかなパターンはありますが、詳細は組織によって異なります。

全社的 IT 部門は必要なもののみを所有し、プロジェクト チームおよびアプリケーション チームが必要なレベルの制御を行えるようにする必要があります。 一般にこれは、全社的 IT 部門はサブスクリプションを所有し、ネットワークの構成などの中核的 IT 機能を扱うことを意味します。 サブスクリプションの **所有者** のセットは小規模にする必要があります。 これらの所有者は、必要に応じて他の所有者を指名したり、"パブリック IP なし" などのサブスクリプションレベルのポリシーを適用したりできます。

レベル 1 やレベル 2 のサポートなど、サブスクリプション全体へのアクセスを必要とするユーザーのサブセットが存在する場合があります。 その場合は、このようなユーザーに **共同作成者** のアクセス権を付与してリソースを管理できるようにしながら、ポリシーへのアクセスやその変更は許可しないことをお勧めします。

DevTest Labs リソースの所有者は、プロジェクト チームまたはアプリケーション チームに近い方である必要があります。 そうした所有者は、マシンとソフトウェアの要件を理解しています。 ほとんどの組織では、プロジェクトまたは開発のリーダーが DevTest Labs リソースの所有者です。 この所有者は、ラボ環境内のユーザーとポリシーを管理でき、DevTest Labs 環境にあるすべての仮想マシンを管理できます。

プロジェクト チームとアプリケーション チームのメンバーは、DevTest Labs ユーザー ロールに追加します。 これらのユーザーは、ラボおよびサブスクリプションレベルのポリシーに従って仮想マシンを作成できます。 ユーザーは各自の仮想マシンを管理できますが、他のユーザーに属する仮想マシンを管理することはできません。

詳細については、[Azure エンタープライズ スキャフォールディングのサブスクリプションの規範的なガバナンス](/azure/architecture/cloud-adoption/appendix/azure-scaffold)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ
「[Corporate policy and compliance](devtest-lab-guidance-governance-policy-compliance.md)」(会社のポリシーとコンプライアンス) をご覧ください。
