---
sourcePath: core/yql/reference/yql-docs-core-2/syntax/_includes/pragma/yson.md
sourcePath: yql/reference/yql-docs-core-2/syntax/_includes/pragma/yson.md
---

## Yson

Управление поведением Yson UDF по умолчанию, подробнее см. в [документации по ней](../../../udf/list/yson.md) и в частности [Yson::Options](../../../udf/list/yson.md#ysonoptions).

### `yson.AutoConvert`

| Тип значения | По умолчанию |
| --- | --- |
| Флаг | false |

Автоматическое конвертация значений в требуемый тип данных во всех вызовах Yson UDF, в том числе и неявных.

### `yson.Strict`

| Тип значения | По умолчанию |
| --- | --- |
| Флаг | true |

Управление строгим режимом во всех вызовах Yson UDF, в том числе и неявных. Без значения или при значении `"true"` - включает строгий режим. Со значением `"false"` - отключает.

### `yson.DisableStrict`

| Тип значения | По умолчанию |
| --- | --- |
| Флаг | false |

Инвертированная версия `yson.Strict`. Без значения или при значении `"true"` - отключает строгий режим. Со значением `"false"` - включает.