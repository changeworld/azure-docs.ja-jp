| リソース | 基本 | Standard | プレミアム |
|---|---|---|---|---|
| ストレージ | 10 GiB | 100 GiB| 500 GiB |
| 1 分あたりの ReadOps<sup>1、2</sup> | 1k | 300k | 10,000k |
| 1 分あたりの WriteOps<sup>1、3</sup> | 100 | 500 | 2k |
| ダウンロード帯域幅 MBps<sup>1</sup> | 30 | 60 | 100 |
| アップロード帯域幅 MBps<sup>1</sup> | 10 | 20 | 50 |
| Webhook | 2 | 10 | 100 |
| geo レプリケーション | 該当なし | 該当なし | [サポート *(プレビュー)*](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> *ReadOps*、*WriteOps*、および*帯域幅*は最小推定値です。 ACR は、使用状況で必要とするパフォーマンスの向上に努めます。

<sup>2</sup> [Docker プル](https://docs.docker.com/registry/spec/api/#pulling-an-image)は、イメージ内のレイヤー数とマニフェストの取得に基づいて、複数の読み取り操作に変換します。

<sup>3</sup> [Docker プッシュ](https://docs.docker.com/registry/spec/api/#pushing-an-image)は、プッシュする必要があるレイヤーの数に基づいて、複数の書き込み操作に変換します。 `docker push` には、*ReadOps* が含まれ、既存のイメージのマニフェストを取得します。