---
title: "OrderHistoryEntry"
weight: 10
date: 2023-07-20T13:56:15.172Z
showtoc: true
generated: true
---
<!-- This file was generated from the Vendure source. Do not modify. Instead, re-run the "docs:build" script -->
import MemberInfo from '@site/src/components/MemberInfo';
import GenerationInfo from '@site/src/components/GenerationInfo';
import MemberDescription from '@site/src/components/MemberDescription';


## OrderHistoryEntry

<GenerationInfo sourceFile="packages/core/src/entity/history-entry/order-history-entry.entity.ts" sourceLine="14" packageName="@vendure/core" />

Represents an event in the history of a particular <a href='/typescript-api/entities/order#order'>Order</a>.

```ts title="Signature"
class OrderHistoryEntry extends HistoryEntry {
  constructor(input: DeepPartial<OrderHistoryEntry>)
  @Index() @ManyToOne(type => Order, { onDelete: 'CASCADE' }) @Index()
    @ManyToOne(type => Order, { onDelete: 'CASCADE' })
    order: Order;
}
```
Extends

 * <a href='/typescript-api/entities/history-entry#historyentry'>HistoryEntry</a>



### constructor

<MemberInfo kind="method" type="(input: DeepPartial&#60;<a href='/typescript-api/entities/order-history-entry#orderhistoryentry'>OrderHistoryEntry</a>&#62;) => OrderHistoryEntry"   />


### order

<MemberInfo kind="property" type="<a href='/typescript-api/entities/order#order'>Order</a>"   />

