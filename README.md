### Extract Route params from Express (or React Router) like route

```ts
type ExtractRouteParams<T extends string> =
  string extends T
  ? Record<string, string>
  : T extends `${infer Start}:${infer Param}/${infer Rest}`
  ? {[k in Param | keyof ExtractRouteParams<Rest>]: string}
  : T extends `${infer Start}:${infer Param}`
  ? {[k in Param]: string}
  : {};

type P = ExtractRouteParams<'/posts/:postId'>;

type C = ExtractRouteParams<'/posts/:postId/:commentId'>;
```
