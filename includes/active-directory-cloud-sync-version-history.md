---
ms.openlocfilehash: 77866fcb72515d5167d9f06b7d9ad91c59bc7945
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2020
ms.locfileid: "97700598"
---
この記事では、これまでにリリースされた Azure Active Directory Connect プロビジョニング エージェントのバージョンと機能を一覧表示します。 Azure AD チームは、プロビジョニング エージェントを新機能で定期的に更新しています。 プロビジョニング エージェントは、新しいバージョンがリリースされると自動的に更新されます。 

Microsoft では、エージェントの最新バージョンと 1 つ前のバージョンを直接サポートしています。

## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>リリースの状態

2020 年 11 月 23 日:ダウンロード対象としてリリース済み

### <a name="new-features-and-improvements"></a>新機能と機能強化

* [gMSA](../articles/active-directory/cloud-provisioning/how-to-prerequisites.md#group-managed-service-accounts) のサポート
* 増分または差分の同期サイクル中の、1500 未満までのメンバー サイズのグループのサポート。 これは、グループ スコープ フィルターの使用時に適用されます。
* メンバー サイズ 15,000 までの大規模なグループのサポート
* 初期同期の機能強化
* 高度な詳細ログ
* [AADCloudSyncTools PowerShell モジュール](../articles/active-directory/cloud-provisioning/reference-powershell.md)の紹介
* 英語以外のサーバーにエージェントをインストールできるようにするために修正された制限
* スコープ内のオブジェクトのみに対する PHS フィルター処理のサポート (最初は、すべてのオブジェクトのパスワード ハッシュを同期していました)
* エージェントのメモリ リークの問題を修正
* プロビジョニング ログの強化
* [LDAP 接続タイムアウト](../articles/active-directory/cloud-provisioning/how-to-manage-registry-options.md#configure-ldap-connection-timeout)を構成するためのサポート 
* [参照の追跡](../articles/active-directory/cloud-provisioning/how-to-manage-registry-options.md#configure-referral-chasing)を構成するためのサポート 


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>リリースの状態

2019 年 12 月 4 日:ダウンロード対象としてリリース済み

### <a name="new-features-and-improvements"></a>新機能と機能強化

* オンプレミスの Active Directory から Azure AD にユーザー、連絡先、グループ データを同期するための [Azure AD Connect クラウド プロビジョニング](../articles/active-directory/cloud-provisioning/what-is-cloud-provisioning.md)のサポートが含まれています


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>リリースの状態

2019 年 9 月 9 日: 自動更新向けにリリース済み

### <a name="new-features-and-improvements"></a>新機能と機能強化

* プロビジョニング エージェントに関する問題をデバッグするために追加のトレースとログ記録を構成する機能
* 同期のパフォーマンスを向上させるために、マッピングで構成されている Azure AD 属性のみを取り込む機能

### <a name="fixed-issues"></a>修正された問題

* Azure AD 接続エラーに関する問題が発生した場合に、エージェントを応答しない状態にしていたバグを修正しました
* バイナリ データを Azure Active Directory から読み取るときに問題を引き起こしていたバグを修正しました
* エージェントがクラウド ハイブリッド ID サービスとの信頼を更新するのを失敗させていたバグを修正しました

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>リリースの状態

2019 年 1 月 23 日:ダウンロード対象としてリリース済み

### <a name="new-features-and-improvements"></a>新機能と機能強化

* パフォーマンス、安定性、信頼性を向上させるためにプロビジョニング エージェントおよびコネクタのアーキテクチャが刷新されました 
* UI 駆動型インストール ウィザードを使用してプロビジョニング エージェントの構成が簡略化されました 
* エージェントの自動更新のサポートが追加されました


