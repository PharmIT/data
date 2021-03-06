## About Features

Please read the [Feature Flag Guide](http://emberjs.com/guides/configuring-ember/feature-flags/)
for a detailed explanation.

To add a new feature flag or change an existing one, you can add an
entry in `config/features.json`.

**Ember Data features flags must begin with `ds-`, such as
`ds-new-coalescing`.**

## Feature Flags

- `ds-boolean-transform-allow-null` [#4022](https://github.com/emberjs/data/pull/4022)

  Allow `null`/`undefined` values for `boolean` attributes via `DS.attr('boolean', { allowNull: true })`

- `ds-improved-ajax` [#3099](https://github.com/emberjs/data/pull/3099)

  This feature allows to customize how a request is formed by overwriting
  `methodForRequest`, `urlForRequest`, `headersForRequest` and `bodyForRequest`
  in the `DS.RESTAdapter`.

- `ds-pushpayload-return` [#4110](https://github.com/emberjs/data/pull/4110)

  Enables `pushPayload` to return the model(s) that are created or
  updated via the internal `store.push`.

- `ds-serialize-ids-and-types` [#3848](https://github.com/emberjs/data/pull/3848)

  Enables a new `ids-and-type` strategy (in addition to the already existing `ids` and `records`) for
  serializing has many relationships using the `DS.EmbeddedRecordsMixin` that  will include both
  `id` and `type` of each model as an object.

  For instance, if a use has many pets, which is a polymorphic relationship, the generated payload would be:

  ```js
  {
    "user": {
      "id": "1"
      "name": "Bertin Osborne",
      "pets": [
        { "id": "1", "type": "Cat" },
        { "id": "2", "type": "Parrot"}
      ]
    }
  }
  ```

  This is particularly useful for polymorphic relationships not backed by STI when just including the id
  of the records is not enough.

- `ds-extended-errors` [#3586](https://github.com/emberjs/data/pull/3586) [#4287](https://github.com/emberjs/data/pull/4287)

  Enables `extend` method on errors. It means you can extend from `DS.AdapterError`.

  ```js
    const MyCustomError = DS.AdapterError.extend({ message: "My custom error." });
  ```

  It will also add a few new errors to rest adapter based on http status.

  * [401] `DS.UnauthorizedError`
  * [403] `DS.ForbiddenError`
  * [404] `DS.NotFoundError`
  * [409] `DS.ConflictError`
  * [500] `DS.ServerError`

- `ds-payload-type-hooks` [#4318](https://github.com/emberjs/data/pull/4318)

  Adds two new hooks `modelNameFromPayloadType` and `payloadTypeFromModelName`
  hooks to the serializers. They are used to map a custom type in the payload
  to the Ember-Data model name and vice versa.

  It also deprecates `modelNameFromPayloadKey` and `payloadKeyFromModelName`
  for the JSONSerializer and JSONAPISerializer: those payloads don't have
  _keys_ which represent a model name. Only the keys in the payload for a
  RESTSerializer represent model names, so the `payloadKeyFromModelName` and
  `modelNameFromPayloadKey` are available in that serializer.

  ```js
  // rest reponse
  {
    "blog/post": {
      "id": 1,
      "user": 1,
      "userType": "api::v1::administrator"
    }
  }

  // RESTSerializer invokes the following hooks
  restSerializer.modelNameFromPayloadKey("blog/post");
  restSerializer.modelNameFromPayloadType("api::v1::administrator");
  ```

  ```js
  // json-api reponse
  {
    "data": {
      "id": 1,
      "type": "api::v1::administrator",
      "relationships": {
        "supervisor": {
          "data": {
            "id": 1,
            "type": "api::v1::super-user"
          }
        }
      }
    }
  }

  // JSONAPISerializer invokes the following hooks
  jsonApiSerializer.modelNameFromPayloadType("api::v1::administrator");
  jsonApiSerializer.modelNameFromPayloadType("api::v1::super-user");
  ```

- `ds-overhaul-references` [#4398](https://github.com/emberjs/data/pull/4398)

  This tackles some inconsistencies within `push()` on references. It should
  only be used to push a JSON-API payload. The following use cases are
  addressed and deprecated:

  - `BelongsToReference#push()` accepts a `DS.Model`
  - `HasManyReference#push()` accepts a plain array
  - `HasManyReference#push()` accepts a pseudo-JSON-API format:

      ```js
      {
        data: [
          { data: { type: 'model', id: 1 } }
        ]
      }
      ```

- `ds-check-should-serialize-relationships`

Adds public method for `shouldSerializeHasMany`, used to determine if a
`hasMany` relationship can be serialized.
