

## <a name="what-is-happening"></a>状況

業界全体に及ぶハードウェアベースのセキュリティ脆弱性が [1 月 3 日に明らかになりました](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html)。 お客様のセキュリティを維持することは、常に最優先事項であり、Azure のお客様がこうした脆弱性に晒されないように積極的な手段を講じています。

セキュリティの脆弱性の公表と共に、[計画メンテナンスのタイミングを早め](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/)、まだ更新が必要な VM の自動再起動を開始しました。


## <a name="how-can-i-see-which-of-my-vms-are-already-updated"></a>どの VM が既に更新済みかはどのように確認できますか。 

VM の状態と、再起動が完了したかどうかは、[Azure Portal の VM リスト](https://aka.ms/T08tdc)で確認できます。 VM は、更新プログラムが適用された場合は "更新済み"、更新がまだ必要な場合は "スケジュール済み" と表示されます。 すべての VM を "スケジュール済み" にする場合は、[Azure Service Health](https://portal.azure.com/) を参照してください。

## <a name="can-i-find-out-exactly-when-my-vms-will-be-rebooted"></a>自分の VM がいつ再起動されるか正確にわかりますか。

再起動に関する通知を受け取る最適な方法は、[スケジュールされたイベント](https://docs.microsoft.com/azure/virtual-machines/windows/scheduled-events)を構成することです。 これにより、メンテナンスのために VM が停止する 15 分間前に通知を受け取ることができます。

## <a name="can-i-manually-redeploy-now-to-perform-the-required-maintenance"></a>今すぐ手動で再展開して、必要なメンテナンスを実行できますか。 

再展開された VM が更新済みのホストに割り当てられることは保証できません。 可能な限り、Azure ファブリックは既に更新されているホストに VM を割り当てようとします。 更新されていないホストに VM を再展開することは可能ですが、その場合は、スケジュールされたメンテナンスの一環として、2 回目の再起動が強制されることがあります。 そのため、手動の再配置は回避策として推奨されません。

## <a name="how-long-will-the-reboot-take"></a>再起動にはどのくらいかかりますか。 

ほとんどの再起動はおよそ **30 分**かかります。

## <a name="does-the-guest-os-need-to-be-updated"></a>ゲスト OS を更新する必要はありますか。 

この Azure インフラストラクチャの更新は、ハイパーバイザー レベルで明らかにされた脆弱性に対処し、Windows または Linux VM イメージを更新する必要はありません。 ただし、いつもと変わらず、VM イメージにセキュリティのベスト プラクティスを適用し続ける必要があります。 必要に応じて、オペレーティング システムのベンダーに、更新と手順についてお問い合わせください。 Windows Server VM をご利用の場合は、ガイダンスが公開されましたので、[こちら](../articles/virtual-machines/windows/mitigate-se.md)でご確認ください。

## <a name="will-there-be-a-performance-impact-as-a-result-of-resolving-this-update"></a>この更新を解決した結果、パフォーマンスに影響が及びますか。

Azure のお客様の大部分は、この更新によって著しいパフォーマンスの影響を受けることはありません。 CPU とディスク I/O パスの最適化に取り組んできましたが、修正プログラムを適用した後にパフォーマンスへの大きな影響は見られません。 ごく一部のお客様は、ネットワーク パフォーマンスへの影響を経験する可能性があります。 その場合、Azure のすべてのお客様が無料で利用できる機能である Azure 高速ネットワーク ([Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) または [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) 用) を使用して対処できます。

## <a name="i-follow-your-recommendations-for-high-availability-will-my-environment-remain-highly-available-during-the-reboot"></a>高可用性の推奨事項に従うと、再起動中に環境の高可用性は維持されますか。

はい。可用性セットまたは仮想マシン スケール セットにデプロイされた仮想マシンには、更新ドメイン (UD) コンストラクトがあります。 メンテナンスを実行するときに、Azure では UD の制約が遵守され、(同じ可用性セット内の) 別の UD の仮想マシンは再起動されません。 高可用性の詳細については、「[Azure での Windows 仮想マシンの可用性の管理](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)」または [Azure での Linux 仮想マシンの可用性管理](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)に関する記事をご覧ください。

## <a name="i-have-architected-my-business-continuitydisaster-recovery-plan-using-region-pairs-will-reboots-to-my-vms-occur-in-region-pairs-at-the-same-time"></a>リージョンのペアを使用して、ビジネス継続性/ディザスター リカバリー プランを設計しました。 VM の再起動は、リージョンのペアで同時に発生しますか。

通常は、Azure の計画済みのメンテナンス イベントは、ペアになるリージョンに 1 回に 1 つずつロールアウトされるため、両方のリージョンでの中断のリスクが最小限に抑えられます。 ただし、このセキュリティ更新プログラムは緊急を要するため、すべてのリージョンに同時に更新プログラムをロールアウトしています。

## <a name="what-about-paas-services-on-azure"></a>Azure での PaaS サービスはどうですか。  

Web とモバイル、データ サービス、IoT、サーバーレスなどの Azure プラットフォーム サービスの脆弱性が対処されました。 これらのサービスを使用している顧客がとる必要のある操作はありません。

## <a name="intel-released-additional-guidance-on-january-22-2018-related-to-the-security-vulnerabilities--will-this-guidance-cause-any-additional-maintenance-activities-by-azure"></a>Intel は、2018 年 1 月 22 日にセキュリティの脆弱性に関連する追加のガイダンスを公開しました。  このガイダンスにより、Azure による追加のメンテナンス アクティビティが発生しますか。  

2018 年 1 月 3 日に発表された Azure の軽減策は、Intel の[更新されたガイダンス](https://newsroom.intel.com/news/root-cause-of-reboot-issue-identified-updated-guidance-for-customers-and-partners/)に影響を受けません。 この新しい情報の結果として、お客様の VM に追加のメンテナンス アクティビティが発生することはありません。
 

## <a name="next-steps"></a>次の手順

詳しくは、[CPU の脆弱性からの Azure 顧客の保護](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/)に関する記事を参照してください。
