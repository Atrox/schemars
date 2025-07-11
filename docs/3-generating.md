---
title: Generating Schemas
nav_order: 5
permalink: /generating/
---

# Generating Schemas

The easiest way to generate a schema for a type that implements is to use the [`schema_for!` macro](https://docs.rs/schemars/latest/schemars/macro.schema_for.html), like so:

```rust
let my_schema = schema_for!(MyStruct);
```

This will create a schema that conforms to [JSON Schema 2020-12](https://json-schema.org/specification-links#2020-12), but this is liable to change in a future version of Schemars if support for other JSON Schema versions is added.

If you want more control over how the schema is generated, you can use the [`generate` module](https://docs.rs/schemars/latest/schemars/generate/). There are two main types in this module:

- [`SchemaSettings`](https://docs.rs/schemars/latest/schemars/generate/struct.SchemaSettings.html), which defines what JSON Schema features should be used when generating schemas (for example, how `Option`s should be represented).
- [`SchemaGenerator`](https://docs.rs/schemars/latest/schemars/generate/struct.SchemaGenerator.html), which manages the generation of a schema document.

For example, to generate a schema that conforms to [JSON Schema Draft 7](https://json-schema.org/specification-links.html#draft-7):

```rust
let generator = SchemaSettings::draft07().into_generator();
let my_schema = generator.into_root_schema_for::<MyStruct>();
```

See the API documentation for more info on how to use those types for custom schema generation.

### Serialize vs. Deserialize contract

Of particular note is the `contract` setting, which controls whether the generated schemas should describe how types are serialized or how they're *de*serialized. By default, this is set to `Deserialize`. If you instead want your schema to describe the serialization behaviour, modify the `contract` field of `SchemaSettings` or use the `for_serialize()` helper method:

{% include example.md name="serialize_contract" %}

## Schema from Example Value

If you want a schema for a type that can't/doesn't implement `JsonSchema`, but does implement `serde::Serialize`, then you can generate a JSON schema from a value of that type using the [`schema_for_value!` macro](https://docs.rs/schemars/latest/schemars/macro.schema_for_value.html). However, this schema will generally be less precise than if the type implemented `JsonSchema` - particularly when it involves enums, since schemars will not make any assumptions about the structure of an enum based on a single variant.

{% include example.md name="from_value" %}
