

SQL Server や Oracle などの一部のデータベース ワークロードでは、メモリ、記憶域、I/O 帯域幅は大量に必要ですが、コアの数は多くなくてもかまいません。 多くのデータベース ワークロードは、CPU 集中型ではありません。 Azure で提供される一部の VM サイズでは、VM の vCPU の数を制限してフトウェア ライセンスのコストを抑えながら、同じメモリ、記憶域、I/O 帯域幅を維持できます。

vCPU の数を、元の VM サイズの半分または 4 分の 1 に制限することができます。 これらの新しい VM サイズでは、識別しやすいように、アクティブな vCPU の数を指定するサフィックスが付加されています。

たとえば、現在の VM サイズ Standard_GS5 の仕様は、32 vCPU、448 GB RAM、64 ディスク (最大 256 TB)、80,000 IOP または 2 GB/秒の I/O 帯域幅です。 新しい VM サイズ Standard_GS5-16 および Standard_GS5-8 の仕様は、アクティブな vCPU はそれぞれ 16 個および 8 個ですが、メモリ、記憶域、I/O 帯域幅は Standard_GS5 と同じです。

SQL Server または Oracle のライセンス料金は新しい vCPU の数に制限され、他の製品は新しい vCPU の数に基づいて課金されます。 その結果、アクティブな (課金対象の) vCPU に対する VM の仕様の比は 50 ～ 75% 高くなります。 Azure でのみ使用可能なこれらの新しい VM サイズでは、ワークロードは何分の 1 かの (コアごとの) ライセンス コストで、より高い CPU 使用率を実現できます。 現時点では、OS のライセンスを含むコンピューティング コストは元のサイズと同じです。 詳しくは、[コスト効率の高いデータベース ワークロードのための新しい VM サイズ](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/)に関するページをご覧ください。


| 名前                | vCPU | 仕様           |
|---------------------|------|-----------------|
| Standard_M64-32ms   | 32   | M64ms と同じ   |
| Standard_M64-16ms   | 16   | M64ms と同じ   |
| Standard_M128-64ms  | 64   | M128ms と同じ  |
| Standard_M128-32ms  | 32   | M128ms と同じ  |
| Standard_E32-16_v3  | 16   | E32s_v3 と同じ |
| Standard_E32-8s_v3  | 8    | E32s_v3 と同じ |
| Standard_E64-32s_v3 | 32   | E64s_v3 と同じ |
| Standard_E64-16s_v3 | 16   | E64s_v3 と同じ |
| Standard_GS4-8      | 8    | GS4 と同じ     |
| Standard_GS4-4      | 4    | GS4 と同じ     |
| Standard_GS5-16     | 16   | GS5 と同じ     |
| Standard_GS5-8      | 8    | GS5 と同じ     |
| Standard_DS13-4_v2  | 4    | DS13_v2 と同じ |
| Standard_DS13-2_v2  | 2    | DS13_v2 と同じ |
| Standard_DS14-8_v2  | 8    | DS14_v2 と同じ |
| Standard_DS14-4_v2  | 4    | DS14_v2 と同じ |