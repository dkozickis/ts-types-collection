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

### Deep Paths

[Playground](https://www.typescriptlang.org/play?ts=4.1.0-pr-40336-88#code/MYewdgzgLgBFBOBDSAbRUCW4YF4YG8AoGGAIgAsQBbAU1IC4DiSzybEATDMAcwbIAqATwAONDjABSiAG6IAysHgYRsdDACCYITEWIUNAHSkANMxKkONCEpWZw-UsLGLlqmDQAeUGmA4QpWQVbdwAjHU4uXjhRa2NmAF8zCw4QYAh+IhYWCnYovkYnWNc7GAARNIBXWjAodCwwUkTCBMJCKFiYAAV0clxdGmV9DAAvcR6ocgB5ADMAHg6xEBm4JFR68AA+AG42xZpu3tm5gQ9vX38YEFCAKxpgKE3+rJgAbQBpGG4YAGsaIWWMAEAF1GAIPsCzj4-AFrncHjAAPxvd4mGCGDETabzcHvYGbSGMCEtV5-AErEG7dqdeSDDDDMYcLHHU5eaGXOH3R79SQgbhzACi3iQDzmzJxmzR0GUvFe+LRAHJDAqdntOrz+azzjCYNLuDw5WiylCLgE9bwnjhzECTTq5UiYAqFTB6NateyAq9KmAfmAQAB3MCQ5EAAwAJPhwQAGYEJEMut22y5en1+wNojGGbgzQYwACqwZg4cjrxjCQjZXL+A1YDmeaNmzjCey5p4VKswDQ8AOM29DwaMBEvROSdhty5mwAFBAQJV4MAaGC0UPJoxxSdNgBKRit9v3Ls9vv2MAwJTsHwCEds01XccPKcIZAQNDHsHbmCTlfkNdHCUwAA+AxDCgozjL+G6brgTy7m0oCQLAsB4Ge6A0AIk6Pusx6blSUCTq8CqULQCqKmwnD6gqwLYTAAD01EwGGQpiA8zgHK27R4QR1A0MRjqALwbgCVOxRVHZDRdEMZ4TFQAK8DwCA8B7JOnG0IYpH5AqwksLR9GMVyLG6gg+rsUpRgCep2wiSJWniZJ0myfJhBAA)

```ts
const translation = {
  "home": {
    "heading": "Typed JavaScript at Any Scale.",
    "description": "TypeScript extends JavaScript by adding types."
  },
  "docs": {
      "heading": "TypeScript Documentation"
  }
}

type Path = SerializedPathOf<typeof translation>;

type PathOf<T extends object> = {
  [K in keyof T]: T[K] extends object ? [K, ...PathOf<T[K]>] : [K]
}[keyof T];

type SerializedPathOf<T extends object> = Join<Extract<PathOf<T>, string[]>, '.'>;

type Join<T extends string[], D extends string> =
    T extends [] ? '' :
    T extends [unknown] ? `${T[0]}` :
    T extends [unknown, ...infer U] ? `${T[0]}${D}${Join<U, D>}` :
    string;

declare function path<T extends object>(source: T, path: PathOf<T>): string;

declare function createT<T extends object>(translation: T): (path: PathOf<T> | SerializedPathOf<T>) => string;

const t = createT(translation);

t(['home', 'heading']); // $ExpectType string
t(['home', '💩']);      // $ExpectError

t('home.heading');     // $ExpectType string
t('home.💩');          // $ExpectError

```
