# <a name="frequently-asked-questions-about-classic-to-azure-resource-manager-migration"></a>クラシックから Azure Resource Manager への移行に関してよく寄せられる質問

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>この移行計画は Azure 仮想マシン上で実行されている既存のサービスやアプリケーションに影響しますか? 

いいえ。 VM (クラシック) は、一般公開で完全にサポートされるサービスです。 引き続きこうしたリソースを利用して、Microsoft Azure のフットプリントを拡大できます。

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>近日中に移行する予定がない場合、VM はどうなりますか? 

既存のクラシック API やリソース モデルが廃止されることはありません。 Resource Manager デプロイメント モデルで使用できる高度な機能により、移行が簡単になります。 Resource Manager の IaaS に含まれる [機能強化をいくつか](../articles/azure-resource-manager/resource-manager-deployment-model.md) 確認することを強くお勧めします。

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>既存のツールにとって、この移行計画はどのような意味がありますか? 

Resource Manager デプロイメント モデルへのツールの更新は、移行計画で考慮する必要がある最も重要な変更の 1 つです。

## <a name="how-long-will-the-management-plane-downtime-be"></a>管理プレーンのダウンタイムはどれくらいですか? 

移行対象のリソースの数によって異なります。 小規模なデプロイ (VM が数十台) の場合、移行に要する時間は 1 時間未満です。 大規模なデプロイ (VM が数百台) の場合は、移行に数時間かかることがあります。

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>Resource Manager で移行対象のリソースがコミットされた後で、ロールバックすることはできますか? 

リソースが準備完了状態の場合は、移行を中止できます。 リソースがコミット操作によって正常に移行されたら、ロールバックできません。

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>コミット操作が失敗した場合、移行をロールバックすることはできますか? 

コミット操作が失敗した場合は、移行を中止できません。 コミット操作を含むすべての移行操作がべき等です。 したがって、しばらくしてから操作を再試行することをお勧めします。 エラーが続く場合は、サポート チケットを作成するか、 [VM フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows)でフォーラム投稿を作成し、ClassicIaaSMigration タグを付けてください。

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>Resource Manager で IaaS を使用する必要がある場合、別の Express Route 回線を購入する必要はありますか? 

いいえ。 最近、 [クラシック デプロイ モデルから Resource Manager デプロイ モデルへの ExpressRoute 回線の移行](../articles/expressroute/expressroute-move.md)が可能になりました。 ExpressRoute 回線が既にある場合は、新しいものを購入する必要はありません。

## <a name="what-if-i-had-configured-role-based-access-control-policies-for-my-classic-iaas-resources"></a>クラシック IaaS リソース用にロールベースの Access Control ポリシーを構成した場合はどうすればよいですか? 

移行中に、リソースはクラシックから Resource Manager に変換されます。 したがって、移行後に必要になる RBAC ポリシーの更新を計画しておくことをお勧めします。

## <a name="what-if-im-using-azure-site-recovery-or-azure-backup-today"></a>現在、Azure Site Recovery または Azure Backup サービスを使用している場合はどうすればよいですか? 

バックアップが有効になっている仮想マシンを移行する場合は、「[私はクラシック VM を Backup コンテナーにバックアップしてあります」をご覧ください。VM をクラシック モードから Resource Manager モードに移行したいのですが、Recovery Services コンテナーにバックアップするにはどうすればよいですか。](../articles/backup/backup-azure-backup-ibiza-faq.md)私はクラシック VM を Backup コンテナーにバックアップしてあります。 VM をクラシック モードから Resource Manager モードに移行したいのですが、  Recovery Services コンテナーにバックアップするにはどうすればよいですか。

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>サブスクリプションまたはリソースを検証し、移行が可能かどうかを確認できますか? 

はい。 プラットフォームでサポートされる移行オプションでは、移行準備の最初の手順として、リソースを移行できるかどうかを検証します。 検証操作が失敗した場合、移行を完了できないすべての理由についてメッセージを受信します。

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>IaaS リソースの移行準備中にクォータ エラーが発生した場合はどうなりますか? 

移行を中止することをお勧めします。その後、VM を移行するリージョンのクォータを増やすサポート要求を記録します。 クォータ要求が承認されたら、移行手順を再開できます。

## <a name="how-do-i-report-an-issue"></a>問題はどのようにレポートすればよいですか? 

移行の問題や質問については、ClassicIaaSMigration というキーワードを付けて、 [VM フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows)に投稿してください。 質問はすべてのこのフォーラムに投稿することをお勧めします。 サポート契約がある場合は、サポート チケットを記録してもかまいません。

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>移行中にプラットフォームで選択されたリソース名が気に入らない場合はどうすればよいですか? 

クラシック デプロイメント モデルで明示的に名前を指定したリソースはすべて、移行中は保持されます。 場合によっては、新しいリソースが作成されます。 たとえば、各 VM に対してネットワーク インターフェイスが作成されます。 現時点では、移行中に作成されるこれらの新しいリソース名を制御することはできません。 [Azure フィードバック フォーラム](http://feedback.azure.com)で、この機能の投票を記録してください。

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>リンクの承認を受けた複数のサブスクリプションによって使用されている ExpressRoute 回線は移行できますか。 

サブスクリプション間の承認リンクが使用されている ExpressRoute 回線は、ダウンタイムなしで自動的に移行することができません。 Microsoft は、手動の手順でこれらの回線を移行する方法についてのガイダンスを用意しています。 手順と詳細については、「[クラシックから Resource Manager デプロイメント モデルへの ExpressRoute 回線および関連する仮想ネットワークの移行](../articles/expressroute/expressroute-migration-classic-resource-manager.md)」を参照してください。

## <a name="i-got-a-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated-"></a>次のようなメッセージを受信しました。*"VM のエージェントの全般的な状態が、準備が整っていないことを示しています。そのため、VM を移行することはできません。VM エージェントから、エージェントの全般的な状態が準備完了として報告されるようにしてください"* または *"VM には、VM から状態が報告されない拡張機能が含まれています。 そのため、この VM は移行できません。" *

このメッセージを受信するのは、VM がインターネットに送信接続されていない場合です。 VM エージェントでは、送信接続を使用して、5 分ごとにエージェントの状態を更新するために Azure ストレージ アカウントにアクセスします。
