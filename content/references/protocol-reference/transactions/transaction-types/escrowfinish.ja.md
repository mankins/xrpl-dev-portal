---
html: escrowfinish.html
parent: transaction-types.html
blurb: 保留中の支払いから受取人へXRPを送金します。
labels:
  - Escrow
---
# EscrowFinish

[[ソース]](https://github.com/ripple/rippled/blob/master/src/ripple/app/tx/impl/Escrow.cpp "Source")

_[Escrow Amendment][]が必要です。_

保留中の支払いから受取人へXRPを送金します。

## {{currentpage.name}} JSONの例

```json
{
    "Account": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
    "TransactionType": "EscrowFinish",
    "Owner": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
    "OfferSequence": 7,
    "Condition": "A0258020E3B0C44298FC1C149AFBF4C8996FB92427AE41E4649B934CA495991B7852B855810100",
    "Fulfillment": "A0028000"
}
```

{% include '_snippets/tx-fields-intro.ja.md' %}
<!--{# fix md highlighting_ #}-->


| フィールド       | JSONの型         | [内部の型][]       | 説明                |
|:----------------|:-----------------|:------------------|:--------------------|
| `Owner` | 文字列 | AccountID | 保留中の支払いに資金を供給した支払元アカウントのアドレス。 |
| `OfferSequence` | 符号なし整数 | UInt32 | 終了する保留中の支払いを作成した[EscrowCreateトランザクション][]のトランザクションシーケンス。 |
| `Condition` | 文字列 | Blob | _（省略可）_ 以前に指定された保留中の支払いの[PREIMAGE-SHA-256 Crypto-condition](https://tools.ietf.org/html/draft-thomas-crypto-conditions-02#section-8.1)に一致する16進数値。 |
| `Fulfillment` | 文字列 | Blob | _（省略可）_ 保留中の支払いの`Condition`に一致する[PREIMAGE-SHA-256 Crypto-condition fulfillment](https://tools.ietf.org/html/draft-thomas-crypto-conditions-02#section-8.1.4)の16進数値。 |

すべてのアカウントがEscrowFinishトランザクションを送信できます。

- 保留中の支払いに`FinishAfter`時刻が設定されている場合、この時刻よりも前にはこれを実行できません。具体的には、対応する[EscrowCreateトランザクション][]で指定されている`FinishAfter`時刻が、最後に閉鎖されたレジャーの閉鎖時刻よりも後の場合、EscrowFinishトランザクションは失敗します。
- 保留中の支払いに`Condition`が指定されている場合に、その条件に対応する`Fulfillment`を指定しないと、この支払いを実行できません。
- 有効期限切れの保留中の支払いは実行できません。具体的には、対応する[EscrowCreateトランザクション][]で指定されている`CancelAfter`時刻が、最後に閉鎖されたレジャーの閉鎖時刻よりも前の場合、EscrowFinishトランザクションは失敗します。

**注記:** EscrowFinishトランザクションにフルフィルメントが含まれている場合、このトランザクションを送信するための最小[トランザクションコスト](transaction-cost.html)が増加します。トランザクションにフルフィルメントが含まれていない場合、トランザクションコストは標準の10 dropです。トランザクションにフルフィルメントが含まれている場合、トランザクションコストは330 [XRP drop][XRPのdrop数]に加えて、プリイメージサイズの16バイトごとに更に10 dropです。

[本番環境以外のネットワーク](parallel-networks.html)では、保留中のescrowの送金先アカウントを[削除](accounts.html#アカウントの削除)できる場合があります。この場合、 escrowを終了しようとしても結果`tecNO_TARGET`で失敗しますが、 escrowオブジェクトは通常期限切れになる場合を除き、維持されます。別の支払いで送金先アカウントが再作成された場合、 escrowは正常に終了できます。escrowの送金先アカウントは、[fix1523 Amendment](known-amendments.html#fix1523)が有効になる前にescrowが作成された場合にのみ削除できます。本番環境の XRP Ledgerにはそのようなescrowは存在しないため、本番環境のXRP Ledgerではこのようなエッジケースには対応できません。また、このエッジケースは、fix1523とescrowのAmendmentを同時に有効にするテストネットワークでも不可能です。これは、[新しいジェネシスレジャーを開始](start-a-new-genesis-ledger-in-stand-alone-mode.html)するときのデフォルトです。

<!--{# common link defs #}-->
{% include '_snippets/rippled-api-links.md' %}
{% include '_snippets/tx-type-links.md' %}
{% include '_snippets/rippled_versions.md' %}
