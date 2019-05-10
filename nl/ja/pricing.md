---

copyright:
  years:  2018, 2019
lastupdated: "2019-03-06"

keywords: Sysdig, IBM Cloud, monitoring, pricing

subcollection: Sysdig

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:download: .download}
{:important: .important}
{:note: .note}


# 料金
{: #pricing_plans}

{{site.data.keyword.mon_full_notm}} インスタンスには、さまざまな価格プランを使用できます。
{:shortdesc}
 

| プラン            | 層         | データ収集  |
|------------------|--------------|------------------|
| `Trial`          |              | ノード当たり最大 20 個のコンテナー、またはノード当たり 200 個のカスタム・メトリックに対して 30 日間だけデータが収集されます。 |
| `Graduated tier` | `Basic`      | ノード当たり最大 20 個のコンテナー、またはノード当たり 200 個のカスタム・メトリックに対してデータが収集されます。 |
| `Graduated tier` | `Pro`        | ノード当たり最大 50 個のコンテナー、またはノード当たり 500 個のカスタム・メトリックに対してデータが収集されます。 |
| `Graduated tier` | `Advanced`   | ノード当たり最大 110 個のコンテナー、またはノード当たり 1000 個のカスタム・メトリックに対してデータが収集されます。 |
{: caption="表 1. サービス・プランのリスト" caption-side="top"} 


**注:** ノードは、Sysdig エージェントをインストールしたホスト、コンテナー、仮想マシン、ベアメタル、または任意のメトリック・ソースにすることができます。

ノード当たりのコンテナー数またはメトリック数が一定期間にわたって累進階層プランのしきい値制限を超えると、自動層の検出が適用されます。 アラート **[{{site.data.keyword.IBM_notm}}]: Usage Tier Change** を有効にしている場合、請求使用量の通知構成に従って、アラート通知がトリガーされます。

[IBM Cloud サポート ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://cloud.ibm.com/unifiedsupport/supportcenter){:new_window} でチケットをオープンし、*拡張累進階層で支払われた価格プラン* の上限を超えたものについて、**カスタム価格見積もり**を要求できます。
{: tip}

データは、すべてのプランにわたり、標準ガイドラインに従って収集および保存されます。 詳しくは、[データ収集](/docs/services/Monitoring-with-Sysdig?topic=Sysdig-about#overview_collection)および[データ保存](/docs/services/Monitoring-with-Sysdig?topic=Sysdig-about#overview_retention)を参照してください。


## 使用量の確認
{: #pricing_usage}

{{site.data.keyword.mon_full_notm}} サービスの使用方法およびその使用法に関連付けられるコストをモニターするには、[使用量の表示](/docs/billing-usage?topic=billing-usage-viewingusage#viewingusage)を参照してください。



## 層の変更について通知するアラートの有効化
{: #pricing_alert}

層の変更があった場合に通知されるようにするには、アラート **[{{site.data.keyword.IBM_notm}}]: Usage Tier Change** を有効にする必要があります。

アラートを有効にするには、以下のステップを実行します。

1. Web UI を起動します。 Web UI の起動方法について詳しくは、[Web UI へのナビゲート](/docs/services/Monitoring-with-Sysdig?topic=Sysdig-launch#launch)を参照してください。 
2. 通知チャネルを作成します。 詳しくは、[通知チャネルの構成](/docs/services/Monitoring-with-Sysdig?topic=Sysdig-notifications#notifications_create)を参照してください。 
3. **「アラート (ALERTS)」**をクリックして、*「アラート (Alerts)」*セクションにナビゲートします。
2. **[IBM]: Usage Tier Change** を検索します。
3. アラートを編集して、通知チャネルを追加します。
4. **「保存」**をクリックします。



## サービス・プラン・アラートの生成方法
{: #pricing_how}

層の変更があった場合に通知されるようにするには、アラート **[{{site.data.keyword.IBM_notm}}]: Usage Tier Change** を有効にする必要があります。

**注:** このアラートを有効にする場合、通知を受信する通知チャネルを指定する必要があります。

使用量は、ノード数の平均および過去 1 時間にわたり 10 秒ごとにサンプリングされたメトリックに応じて計算されます。 一時的な小さい変動は含まれません。 過去の使用量と現在の使用量の差異は、1 時間に 1 回計算されます。

定義されているしきい値は、次のように設定されます。

``` 
usageTiers {
  containerDensity {
    Basic = [0, 20]
    Pro = [21, 50]
    Advanced = [51, 100]
  }
  metricDensity {
    Basic = [0, 200]
    Pro = [201, 500]
    Advanced = [501, 1000]
}
```
{: screen}

アラート通知は、以下のように生成されます。
1. 層内のノードごとのコンテナー数が増加する場合、カスタム・イベントは毎時生成されます。
2. アラート条件により、ノードごとのコンテナー数における変化について通知するカスタム・イベントの有無が検査されます。 最後に使用量が計算されてから層内のコンテナー数が増加したイベントを検出した場合、通知が送信されます。

アラートの頻度は、1 時間に 1 回です。 変動するノードの場合、アラートの頻度は最大で 2 時間ごとです。

アラートは、ノードが*基本* 層から *Pro* 層または*拡張* 層に移行した場合にのみ生成されることに注意してください。 



### 例
{: #pricing_examples}

**サンプル 1** 

平均コンテナー数に応じて次のようになります。 

| 時間     | コンテナーの数 | 説明                                                                   | アラートが生成されるかどうか |
|----------|----------------------|-------------------------------------------------------------------------------|------------------------|
| 10:00    | 18                   | 層は**「基本」**に設定されます。                                                     | いいえ                     |
| 11:00    | 21                   | コンテナーの数は*基本* 層を超えます。 層は**「Pro (Pro)」**に移行します。            | はい                    |
| 12:00    | 19                   | コンテナーの数は*基本* 層を下回ります。 層は**「基本」**に戻ります。     | いいえ                    |
| 13:00    | 20                   | 層は変更されません。 層は**「基本」**に設定されます。                                     | いいえ                     |
{: caption="表 2. サンプル 1" caption-side="top"} 


**サンプル 2**

平均コンテナー数に応じて次のようになります。 

| 時間     | コンテナーの数 | 説明                                                                   | アラートが生成されるかどうか |
|----------|----------------------|-------------------------------------------------------------------------------|------------------------|
| 10:00    | 15                   | 層は**「基本」**に設定されます。                                                     | いいえ                     |
| 11:00    | 19                   | 層は**「基本」**に設定されます。                                                     | いいえ                     |
| 12:00    | 20                   | 層は**「基本」**に設定されます。                                                     | いいえ                    |
| 13:00    | 21                   | コンテナーの数は*基本* 層を超えます。 層は**「Pro (Pro)」**に移行します。            | はい                     |
{: caption="表 3. サンプル 2" caption-side="top"}


**サンプル 3**

平均コンテナー数に応じて次のようになります。 

| 時間     | コンテナーの数 | 説明                                                                   | アラートが生成されるかどうか |
|----------|----------------------|-------------------------------------------------------------------------------|------------------------|
| 10:00    | 15                   | 層は**「基本」**に設定されます。                                                     | いいえ                     |
| 11:00    | 20                   | 層は**「基本」**に設定されます。                                                     | いいえ                    |
| 12:00    | 21                   | 層は**「基本」**に設定されます。                                                     | はい                    |
| 13:00    | 20                   | コンテナーの数は*基本* 層に戻ります。 層は**「Pro (Pro)」**に移行します。          | いいえ                     |
{: caption="表 3. サンプル 3" caption-side="top"}


