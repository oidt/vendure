---
title: "HistoryEntryConfig"
weight: 10
date: 2023-07-20T13:56:17.953Z
showtoc: true
generated: true
---
<!-- This file was generated from the Vendure source. Do not modify. Instead, re-run the "docs:build" script -->
import MemberInfo from '@site/src/components/MemberInfo';
import GenerationInfo from '@site/src/components/GenerationInfo';
import MemberDescription from '@site/src/components/MemberDescription';


## HistoryEntryConfig

<GenerationInfo sourceFile="packages/admin-ui/src/lib/core/src/providers/custom-history-entry-component/history-entry-component-types.ts" sourceLine="75" packageName="@vendure/admin-ui" since="1.9.0" />

Configuration for registering a custom <a href='/admin-ui-api/custom-history-entry-components/history-entry-component#historyentrycomponent'>HistoryEntryComponent</a>.

```ts title="Signature"
interface HistoryEntryConfig {
  type: string;
  component: Type<HistoryEntryComponent>;
}
```

### type

<MemberInfo kind="property" type="string"   />

The type should correspond to the custom HistoryEntryType string.
### component

<MemberInfo kind="property" type="Type&#60;<a href='/admin-ui-api/custom-history-entry-components/history-entry-component#historyentrycomponent'>HistoryEntryComponent</a>&#62;"   />

The component to be rendered for this history entry type.