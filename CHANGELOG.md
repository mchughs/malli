# Malli CHANGELOG

We use [Break Versioning][breakver]. The version numbers follow a `<major>.<minor>.<patch>` scheme with the following intent:

| Bump    | Intent                                                     |
| ------- | ---------------------------------------------------------- |
| `major` | Major breaking changes -- check the changelog for details. |
| `minor` | Minor breaking changes -- check the changelog for details. |
| `patch` | No breaking changes, ever!!                                |

`-SNAPSHOT` versions are preview versions for upcoming releases.

[breakver]: https://github.com/ptaoussanis/encore/blob/master/BREAK-VERSIONING.md

Malli is in [alpha](README.md#alpha).

## 0.2.1 (2020-10-22)

* fix `:sequential` decoding with empty sequence under `mt/json-transformer`, fixes [#288](https://github.com/metosin/malli/issues/288)
  * removed broken `mt/-sequential->seq`

## 0.2.0 (2020-10-18)

* **BREAKING (MINOR)**: `m/deref` returns original schema, does not throw, fixes [#284](https://github.com/metosin/malli/issues/284).
* **BREAKING (MINOR)**: the following utilities in `malli.util` deref top-level refs recursively: `merge`, `union`, `transform-entries`, `optional-keys`, `required-keys`, `select-keys` and `dissoc`.
* `m/deref-all` derefs all top-level references recursively, e.g.

```clj
(m/deref-all [:schema [:schema int?]])
; => int?
```

* `:ref`, `:schema`, `::m/schema` have now generators, JSON Schema and Swagger support
* `mu/subschemas` walks over top-level `:ref` and all `:schema`s.
* `m/walk` can walk over `:ref` and `:schema` reference schemas. Walking can be enabled using options `:malli.core/walk-refs` and `:malli.core/walk-schema-refs`.
* Welcome declarative schema transformations!

There are also declarative versions of schema transforming utilities in `malli.util/schemas`. These include `:merge`, `:union` and `:select-keys`:

```clj
(def registry (merge (m/default-schemas) (mu/schemas)))

(def Merged
  (m/schema
    [:merge
     [:map [:x :string]]
     [:map [:y :int]]]
    {:registry registry}))

Merged
;[:merge
; [:map [:x :string]]
; [:map [:y :int]]]

(m/deref Merged)
;[:map 
; [:x :string] 
; [:y :int]]

(m/validate Merged {:x "kikka", :y 6})
; => true
```

* New options for SCI:
  * `:malli.core/disable-sci` for explicitly disabling `sci`, fixes [#276](https://github.com/metosin/malli/issues/276)
  * `:malli.core/sci-options` for configuring `sci`

* `malli.transform/default-value-transformer` accepts options `:key` and `:defaults`:

```clj
(m/decode
  [:map
   [:user [:map
           [:name :string]
           [:description {:ui/default "-"} :string]]]]
  nil
  (mt/default-value-transformer
    {:key :ui/default
     :defaults {:map (constantly {})
                :string (constantly "")}}))
; => {:user {:name "", :description "-"}}
```
* Support microsecond precision when parsing datetime strings. [#280](https://github.com/metosin/malli/pull/280)

## 0.1.0 (2020-10-08)

First stable release.

## Breaking Changes in pre-alpha:

* 8.10.2020
  * removed `:list` schema
  * removed `malli.error/SchemaError` protocol in favor of using `m/type-properties` for custom errors
* 20.9.2020
  * removed `m/-predicate-schema`, `m/-partial-predicate-schema` and `m/-leaf-schema`
* 19.9.2020
  * new mandatory Protocol method in `m/Schema`: `-type-properties`
* 1.9.2020
  * `m/children` returns 3-tuple (key, properties, schema) for `MapSchema`s
  * `m/map-entries` is removed, `m/entries` returns a `MapEntry` of key & `m/-val-schema`  
* 4.8.2020
  * `:path` in explain is re-implemented: map keys by value, others by child index
  * `m/-walk` and `m/Walker` uses `:path`, not `:in`
  * `m/-outer` has new parameter order: `walker schema path children options`
  * `malli.util/path-schemas` replaced with `malli.util/subschemas` & `malli.util/distict-by`
  * `LensSchema` has a new `-key` method
  * renamed some non-user apis in `malli.core` & `malli.util`
  * moved map-syntax helpers from `malli.core` to `malli.util`
  * dynaload `com.gfredericks/test.chuck`
* 23.7.2020
  * `sci` is not a default dependency. Enabling sci-support:
    * **Clojure**: add a dependency to `borkdude/sci`
    * **ClojureScript**: also require `sci.core` (directly or via `:preloads`)
* 18.7.2020
  * big cleanup of `malli.transform` internals.
* 12.7.2020
  * `malli.mermaid` is removed (in favor of `malli.dot`)  
* 10.7.2020
  * `[metosin/malli "0.0.1-20200710.075225-19"]`
  * Visitor is implemented using a Walker.
    * `m/accept` -> `m/walk`
    * `m/schema-visitor` -> `m/schema-walker`
    * `m/map-syntax-visitor` -> `m/map-syntax-walker`
* 31.6.2020
  * new `-children` method in `Schema`, to return child schemas as instances (instead of just AST)
* 17.6.2020
  * change all `malli.core/*-registry` defs into `malli.core/*-schemas` defns to enable DCE for clojurescript
* 9.6.2020 
  * `malli.core/name` & `malli.core/-name` renamed to `malli.core/type` & `malli.core/-type`
  * `malli.generator/-generator` is renamed to `malli.generator/-schema-generator`
