次の表は、IoT Hub Device Provisioning Service のリソースに適用される制限の一覧です。

| リソース | 制限 |
| --- | --- |
| Azure サブスクリプションあたりの Device Provisioning Services の最大数 | 10 |
| 加入の最大数 | 500,000 |
| 登録の最大数 | 500,000 |
| 加入グループの最大数 | 100 |
| CA の最大数 | 25 |

> [!NOTE]
> [Microsoft サポート](https://azure.microsoft.com/support/options/)に連絡して、サブスクリプションのインスタンス数を増やすことができます。

> [!NOTE]
> [Microsoft サポート](https://azure.microsoft.com/support/options/)に連絡して、プロビジョニング サービスの加入数と登録数を増やすことができます。

Device Provisioning Service は、次のクォータを超えた場合に、要求を調整します。

| スロットル | ユニットあたりの値 |
| --- | --- |
| 操作 | 200/分/サービス |
| デバイス登録 | 200/分/サービス |
| デバイスのポーリング操作 | 5/10 秒/デバイス |
