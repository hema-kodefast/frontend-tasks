## Dynamic form implementation task (based on fields_data.js)

### Scope
- Build a reusable dynamic form renderer that consumes the array exported in `fields_data.js`.
- For each element, render a component by `fieldType` and hydrate it with its `properties`.
- Persist user input back into the same shape (respecting the type of `properties.value`).
- No hardcoding of types or labels; everything should be data-driven from the schema.

### Input schema (shape)
```json
{
  "fieldName": "<string>",
  "fieldType": "<string>",
  "fieldId": "<string>",
  "fieldUniqueKey": "<string>",
  "properties": {
    "placeholder": "<string>",
    "required": "<boolean>",
    "label": "<string>",
    "value": "<any>",
    "fieldLabelProperties": {
      "fieldLabel": "<string>",
      "showFieldLabel": "<boolean>",
      "color": "<hex>",
      "fontsize": "<css-size>",
      "fontFamily": "<string>",
      "textAlign": "left|center|right",
      "labelDisplayMode": "hover|always|hidden"
    }
  }
}
```

### Rendering rules
- Pick component by `fieldType` (see summary table).
- Bind props directly from `properties` with safe defaults if optional fields are absent.
- Respect `required`, min/max constraints, formats, and ranges where applicable.
- Text/number/date split rendering: if `splitMode` is true, render `splitBoxes.count|width|height|spacing` boxes.
- Options-based fields use `properties.options` or `properties.items`.
- `formula` evaluates live based on `selectedFields` and writes output to `value`.
- Date/time variants honor `dateFormat`, `timeFormat`, `availableDate`.
- Upload fields keep `value` as an array if provided that way.
- Labels come from `properties.fieldLabelProperties` when present.

### Edge cases in dataset
- Signature-linked date: `dateSigned.linkedSignatureField`.
- Checkbox groups: `checkbox.checkboxGroup.{enabled, groupName, minSelections, maxSelections, required}`.
- Fixed-step time: `fixed_time.timeStep`, `fixed_time.timeRange.{start,end}`.
- Date/Time ranges: `*_range` variants include both format(s) and range semantics.

### Deliverables
- `DynamicForm` component (framework of choice) that renders the provided array.
- A registry mapping `fieldType` -> component.
- Validation driven entirely by `properties`.
- Serializer that returns the array with updated `properties.value` per field.
- Demo/story that renders the provided dataset.

### Acceptance criteria
- All listed field types render without console errors.
- Each field respects `required` and its constraints.
- Options, ranges, and formats behave as defined by the schema.
- Labels styled by `fieldLabelProperties`.
- `formula` recomputes when dependencies change.
- File/image fields preserve their value shape.
- Implementation is fully data-driven; no hardcoded labels/options.

### Field types and properties summary
- Unless noted, all types support: `placeholder`, `required`, `label`, `value`, `fieldLabelProperties`, `fieldUniqueKey`.

| Field type | Key properties (union observed) | Notes |
|---|---|---|
| signature | printSign | Signature capture field |
| initial | — | Initial capture field |
| dateSigned | dateFormat, timeFormat, displayType, linkedSignatureField | Auto date linked to a signature |
| checkbox | value (boolean-like), useCrossmark, checkboxGroup.{enabled,groupName,minSelections,maxSelections,required} | Single checkbox or grouped selection rules |
| text | splitMode, color, textAlign, fontFamily, fontWeight, fontStyle, fontsize, splitBoxes.{count,width,spacing,height}, maskFieldValue | Single-line text input |
| radio | options[], value | Single choice |
| dropdown | options[], value | Single select dropdown |
| date | dateFormat, availableDate, splitMode, splitBoxes.{…}, displayValue, fieldId, timestamp, lastUpdate | Date picker |
| file | value[], documentNames[], documentCount | File upload; `value` is typically an array |
| stamp | value | Stamp widget |
| number | splitMode, textAlign, fontFamily, fontWeight, fontStyle, fontsize, color, splitBoxes.{…}, maskFieldValue | Numeric input |
| formula | formula, selectedFields[], decimalPlaces, useThousandsSeparator, showCalculation, dateOperationType, timeOperationType, fieldMapping, dateFormatType, explanation, lastCalculation | Computed field; writes to `value` |
| list | items[{name,value}], value | Static list selector |
| multiline | value, fontFamily, fontWeight, fontStyle, fontsize, color | Multi-line text area |
| multiple | options[], minSelect, maxSelect, value | Multi-select input |
| daterange | dateFormat, availableDate, value | Date range picker |
| time | timeFormat, value | Time picker |
| currency | currencySymbol, fontWeight, fontStyle, fontsize, color, useThousandsSeparator | Currency input |
| date_time | dateFormat, availableDate, timeFormat, value | Date-time picker |
| weekdays | value | Weekday selector |
| fixed_time | timeStep, timeFormat, timeRange.{start,end}, value | Fixed-step time selection |
| time_range | timeFormat, value | Time range picker |
| date_time_range | dateFormat, timeFormat, availableDate, value | Date-time range picker |
| heading | value, headingLevel, color, fontFamily, textAlign | Display-only heading |
| paragraph | value, fontsize, color, fontFamily, textAlign | Display-only paragraph |
| image | (uploader dependent) | Image upload/display |
| horizontal | — | Horizontal rule/separator |
| table | (dataset dependent) | Static table layout if provided |
| my_signature | printSign, value | Sender’s signature (auto) |
| my_initial | value | Sender’s initial (auto) |


### Notes
- Preserve `properties.value` types when reading/writing (string vs number vs array).
- When `fieldLabelProperties.showFieldLabel` is false, hide labels accordingly.
- Default styles can fall back to sensible UI defaults if the given font/color values are absent.