---
title: Azure Backup - Archive レベルの概要
description: Azure Backup の Archive レベルのサポートについて説明します。
ms.topic: overview
ms.date: 10/23/2021
ms.custom: references_regions
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: cedc8dbcaaf05c54ae624b4c1ab548a900231cf1
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132718490"
---
# <a name="overview-of-archive-tier-in-azure-backup"></a>Azure Backup の Archive レベルの概要

お客様は、組織のコンプライアンス ルールによって定義されたデータ保有ニーズに従って長期保有 (LTR) バックアップ データを含むバックアップ データを保存するために Azure Backup を使用しています。 ほとんどの場合、古いバックアップ データにアクセスすることはめったになく、コンプライアンスのニーズのためだけに保存されています。

Azure Backup では、スナップショットと Standard レベルに加えて、Archive レベルでの長期保有ポイントのバックアップがサポートされています。

## <a name="supported-workloads"></a>サポートされるワークロード

Archive レベルでは、次のワークロードがサポートされます。

| ワークロード | Operations |
| --- | --- |
| Azure の仮想マシン | <ul><li>月単位および年単位の復旧ポイントトのみ。 日単位および週単位の復旧ポイントはサポートされていません。  </li><li>期間 >= 3 か月 (Vault-Standard 層) </li><li>保有期間 >= 6 か月 </li><li>アクティブな日単位および週単位の依存関係なし。 </li></ul> |
| Azure 仮想マシン内の SQL Server または Azure 仮想マシン内の SAP HANA | <ul><li>完全復旧ポイントのみ。 ログと差分はサポートされていません。 </li><li>期間 >= 45 日 (Vault-Standard レベル)。 </li><li>保有期間 >= 6 か月 </li><li>依存関係なし </li></ul> |

>[!Note]
>- Azure VM 内の SQL Server または Azure VM 内の SAP HANA に対する Archive レベルのサポートは、現在複数のリージョンで一般提供されています。 サポートされているリージョンの詳細な一覧については、[サポート マトリックス](#support-matrix)を参照してください。
>- Azure Virtual Machines のアーカイブ アクセス層のサポートも、限定パブリック プレビュー段階です。 制限付きのパブリック プレビューにサインアップするには、こちらの[フォーム](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR463S33c54tEiJLEM6Enqb9UNU5CVTlLVFlGUkNXWVlMNlRPM1lJWUxLRy4u)に入力してください。

## <a name="supported-clients"></a>サポートされるクライアント

Archive レベルでは、次のクライアントがサポートされます。

- [PowerShell](./use-archive-tier-support.md?pivots=client-powershelltier)
- [CLI](./use-archive-tier-support.md?pivots=client-clitier)
- [Azure Portal](./use-archive-tier-support.md?pivots=client-portaltier)

## <a name="how-azure-backup-moves-recovery-points-to-the-vault-archive-tier"></a>Azure Backup で復旧ポイントを Vault-Archive レベルに移動する方法

> [!VIDEO https://www.youtube.com/embed/nQnH5mpiz60?start=416]

## <a name="archive-recommendations-only-for-azure-virtual-machines"></a>アーカイブに関する推奨事項 (Azure 仮想マシンのみ)

Azure 仮想マシンの復旧ポイントは増分です。 復旧ポイントを Archive レベルに移動すると、復旧ポイントは完全復旧ポイントに変換されます。これは、Archive レベル内のすべての復旧ポイントが独立し、互いに分離されていることを保証するためです。 そのため、バックアップ ストレージ全体 (Vault-Standard + Vault-Archive) が増加する可能性があります。

ストレージの増加量は、仮想マシンのチャーン パターンによって異なります。

- 仮想マシンのチャーンが多いほど、復旧ポイントを Archive レベルに移動したときにバックアップ ストレージ全体が小さくなります。
- 仮想マシンのチャーンが少ない場合、アーカイブ層に移行すると、バックアップ ストレージが増加する可能性があります。これによって、Vault-Standard レベルと Vault-Archive レベルの価格差が相殺される可能性があります。 そのため、全体的なコストが増加する場合があります。

これを解決するために、Azure Backup には推奨設定が用意されています。 この推奨設定によって復旧ポイントの一覧が返されます。これをまとめて Archive レベルに移動すると、コストを削減することができます。

>[!Note]
>コストの節約額はさまざまな原因によって左右されるため、インスタンスごとに異なる場合があります。

## <a name="modify-protection"></a>保護を変更する

Azure Backup には、データソースの保護を変更する 2 つの方法が用意されています。

- 既存のポリシーの変更
- 新しいポリシーを使用したデータソースの保護

どちらのシナリオも、新しいポリシーは、Standard レベルと Archive レベルにあるすべての古い復旧ポイントに適用されます。 そのため、ポリシーに変更があった場合は、古い復旧ポイントが削除される可能性があります。

復旧ポイントをアーカイブに移動すると、180 日間の早期削除期間が適用されます。 料金は日割り計算されます。 アーカイブの期間が 180 日間を経過していない復旧ポイントを削除した場合は、180 から Standard レベルで経過した日数を差し引いた日数に相当するコストが発生します。

アーカイブの期間が最低 180 日間を経過していない復旧ポイントを削除すると、早期削除コストが発生します。

## <a name="stop-protection-and-delete-data"></a>保護を停止してデータを削除する

保護を停止してデータを削除すると、すべての復旧ポイントが削除されます。 Archive レベルで 180 日間経過していないアーカイブ内の復旧ポイントについては、復旧ポイントを削除すると早期削除コストが発生します。

## <a name="archive-tier-pricing"></a>Archive レベルの価格

Archive レベルの価格は、[価格ページ](azure-backup-pricing.md)で確認できます。

## <a name="support-matrix"></a>サポート マトリックス

| ワークロード | プレビュー | 一般公開 |
| --- | --- | --- |
| Azure 仮想マシン内の SQL Server または Azure 仮想マシン内の SAP HANA | なし | オーストラリア東部、インド中部、北ヨーロッパ、東南アジア、東アジア、オーストラリア東南部、カナダ中部、ブラジル南部、カナダ東部、フランス中部、フランス南部、東日本、西日本、韓国中部、韓国南部、インド南部、英国西部、英国南部、米国中部、米国東部 2、米国西部、米国西部 2、米国中西部、米国東部、米国中南部、米国中北部、西ヨーロッパ、US Gov バージニア、US Gov テキサス、US Gov アリゾナ。 |
| Azure Virtual Machines | 米国東部、米国東部 2、米国中部、米国中南部、米国西部、米国西部 2、米国中西部、米国中北部、ブラジル南部、カナダ東部、カナダ中部、西ヨーロッパ、英国南部、英国西部、東アジア、東日本、インド南部、東南アジア、オーストラリア東部、インド中部、北ヨーロッパ、オーストラリア東南部、フランス中部、フランス南部、西日本、韓国中部、韓国南部。 | なし |


## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="what-will-happen-to-archive-recovery-points-if-i-stop-protection-and-retain-data"></a>保護を停止してデータを保持すると、アーカイブの復旧ポイントはどうなりますか?

復旧ポイントは、いつまでもアーカイブされたままになります。 詳細については、[復旧ポイントに対する保護の停止の影響](manage-recovery-points.md#impact-of-stop-protection-on-recovery-points)に関するページをご覧ください。

### <a name="is-cross-region-restore-supported-from-archive-tier"></a>Archive レベルからのリージョンをまたがる復元はサポートされていますか

GRS コンテナー内のデータを Standard レベルから Archive レベルに移動すると、データは GRS アーカイブに移動されます。 リージョンをまたがる復元が有効になっている場合でも、そうなります。 バックアップ データが Archive レベルに移動された後は、ペア リージョンにデータを復元することはできません。 ただし、リージョンの障害時には、セカンダリ リージョンのバックアップ データを復元に使用できるようになります。 

プライマリ リージョンの Archive レベルの復旧ポイントから復元している間、復旧ポイントは Standard レベルにコピーされ、プライマリ リージョンとセカンダリ リージョンの両方でリハイドレート期間に従って保持されます。 これらのリハイドレート復旧ポイントから、リージョンをまたがる復元を実行できます。

### <a name="i-can-see-eligible-recovery-points-for-my-virtual-machine-but-i-cant-seeing-any-recommendation-what-can-be-the-reason"></a>仮想マシンの対象復旧ポイントは表示できますが、推奨事項が表示されません。 どのような理由が考えられますか。

仮想マシンの復旧ポイントは、対象となる条件を満たしています。 そのため、アーカイブ可能な復旧ポイントがあります。 ただし、仮想マシンのチャーンが少ない可能性があります。そのため、推奨事項はありません。 このシナリオでは、アーカイブ可能な復旧ポイントを Archive レベルに移動できますが、バックアップ ストレージの全体的なコストが増加する可能性があります。

### <a name="i-have-stopped-protection-and-retained-data-for-my-workload-can-i-move-the-recovery-points-to-archive-tier"></a>保護を停止し、ワークロードのデータを保持しました。 復旧ポイントを Archive レベル移動することは可能ですか。

いいえ。 特定のワークロードの保護を停止すると、対応する復旧ポイントを Archive レベルに移動することはできなくなります。 復旧ポイントを Archive レベルに移動するには、データ ソースの保護を再開する必要があります。

## <a name="next-steps"></a>次のステップ

- [Archive レベルを使用する](use-archive-tier-support.md)
- [Azure Backup の価格](azure-backup-pricing.md)