## What focus on first when working on guidelines

- Root elements
    - Info (like versioning for example)
    - Servers
    - Channels
    - Operations
    - Components - Reusability
- Schemas - Schema Formats and [Schema Registries](https://www.asyncapi.com/docs/tutorials/kafka/managing-schemas-using-schema-registry)
- Messages - [CloudEvents](https://github.com/cloudevents/spec) and [xRegistry](https://github.com/xregistry/spec)
- Then other concepts (traits for example)

> and yeah, it is not only about content of AsyncAPI document - I recommend to also have `-asyncapi.yaml` in filenames [because of SchemaStore](https://github.com/asyncapi/spec/tree/master/examples#file-naming)

**Reusability always!**

## AsyncAPI and different schema formats

### Prerequisite

https://github.com/asyncapi/cli with multiple installation options https://www.asyncapi.com/docs/tools/cli/installation

### Validate

```bash
asyncapi validate iata-openair-asyncapi.yaml --watch
```

### Generate static docs

```bash
asyncapi generate fromTemplate iata-openair-asyncapi.yaml @asyncapi/html-template -o docs
open docs/index.html
```

Although I personally recommend a client-side approach: https://www.asyncapi.com/blog/event-driven-api-documentation-made-simple-clientside-rendering

### Referencing OpenAPI Schemas

It's complicated :)

#### Simple $ref works

```yaml
# notice it references official IATA file and some specific schema called AccessGate
components:
    schemas:
        AccessGate:
            $ref: 'https://api.media.atlassian.com/file/5f189836-fc68-4db7-b38b-9aa64a58b6a1/binary?client=dfbfc3b4-a00a-4cf2-94c9-61c34d2102ab&collection=contentId-607649825&dl=true&max-age=2592000&token=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJkZmJmYzNiNC1hMDBhLTRjZjItOTRjOS02MWMzNGQyMTAyYWIiLCJhY2Nlc3MiOnsidXJuOmZpbGVzdG9yZTpjb2xsZWN0aW9uOmNvbnRlbnRJZC02MDc2NDk4MjUiOlsicmVhZCJdfSwiZXhwIjoxNzExNDUzMzg3LCJuYmYiOjE3MTE0NTA1MDd9.Im61U3hH06uv9cq8ANJ9FlHQI0wknyfefNLFIlzFo-s#/components/schemas/AccessGate'
```

#### Defining `schemaFormat`

One of the great AsyncAPI features is that it is supporting different schema formats -> https://www.asyncapi.com/docs/reference/specification/v3.0.0#multiFormatSchemaObject
Like anything you want with some recommendations like JSON Schema or Avro Schema or even Protobuf.

The default is AsyncAPI Schema which is a superset of JSON Schema - similar to OpenAPI Schema - almost.

So to be on the save side, best is always to use other schemas this way:
```yaml
      schemaFormat: application/vnd.oai.openapi+json;version=3.0.0
      schema:
        $ref: './IATA_Open_Air_JSON_Library_23.3.json#/components/schemas/AccessGate'
```

As you noticed, `3.0.0` version is used as `3.0.3` is not supported. To fix it, contribute to https://github.com/asyncapi/openapi-schema-parser/blob/master/src/index.ts#L49

#### But there are 2 things to take into consideration

`JSON Reference` requires to ignore anything that is passed next to `$ref` so in AsyncAPI below `title` and `description` would be ignored.
```json
        "accessGateIdentifier": {
            "title": "Access Gate",
            "description": "The identifier of the gate. For example:. 'A5'.",
            "$ref": "#/components/schemas/AccessGateIdentifier"
        }
```

It is the same in OpenAPI `3.0.3` that you use -> https://github.com/OAI/OpenAPI-Specification/blob/3.0.3/versions/3.0.3.md#reference-object
The stuff you're doing was introduced in OpenAPI `3.1.0` -> https://github.com/OAI/OpenAPI-Specification/blob/3.1.0/versions/3.1.0.md#reference-object

## AsyncAPI maturity and adoption

- v3 released with maintainers from Vendors and End Users. Around [40ppl involved](https://www.asyncapi.com/blog/release-notes-3.0.0#acknowledgements)
- [release process defined](https://github.com/asyncapi/spec/blob/master/RELEASE_PROCESS.md) - we can do even 4 minor releases a year if needed
- [sponsoring companies](https://github.com/asyncapi/spec/tree/master?tab=readme-ov-file#our-sponsors) - IBM, Solace, Postman and many others
- [json schema that describes AsyncAPI spec](https://github.com/asyncapi/spec-json-schemas/) - 27M downloads so far (over 2M this year)
- [use cases](https://www.asyncapi.com/casestudies/adeogroup) in production

Also check out [these slides](https://docs.google.com/presentation/d/1PRE93NDEWQn5W38bxm8Il_YEqWSu3msQ1Bmr0z09c9w/edit?usp=sharing) for more details about adoption we are aware of. You can put comments there if you need some clarification