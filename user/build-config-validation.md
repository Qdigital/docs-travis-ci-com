---
title: Build Config Validation
layout: en
---

<blockquote class="beta">
  <p>
    This page documents the new build config validation feature that is currently in beta.
  </p>
</blockquote>


## Beta opt-in

You can opt in using the repository setting "Build config validation" in the
Travis CI UI, or by specifying the `version` in your `.travis.yml` file:

```yaml
version: ~> 1.0
```
{: data-file=".travis.yml"}

## Build config validation

When active the [build config validation](https://github.com/travis-ci/travis-yml)
feature will validate and normalize any incoming build config sources (e.g.
your `.travis.yml` file and configs from build requests created via API).

It uses a formal [config format specification](https://github.com/travis-ci/travis-yml/blob/master/schema.json)
which comes in the well known and well-supported format [JSON Schema](https://json-schema.org/).
This feature replaces any previous linting and validation tooling.

Reference documentation autogenerated from this specification can be found
[here](http://config.travis-ci.com).

You can try out and check how YAML build config sources will be normalized
on the [Travis CI build config explorer](http://config.travis-ci.com/explore)
(experimental).

## Validation messages

Validating build configs produces validation messages that you can review
on the respective build's "View Config" tab in the Travis CI UI.

This gives you direct insights into how your build config has been processed,
what issues Travis CI might have found, and how to resolve them.

The messages have 4 severity levels:

* `alert` - The build config might be leaking secrets. You should pay special attention to these messages.
* `error` - The build config contains severe errors that our system cannot recover from, so it will ignore the respective config sections.
* `warn`  - The build config contains mistakes that our system can repair. It is safe to ignore these messages.
* `info`  - The build config parser has made changes to the config that you might want to be informed of. It is safe to ignore these messages.

## Validation message types

This table lists build config validation message types, and explains how to respond to these messages.

<div id="config-validation-message-types"></div>

| Key  | Meaning | Action |
| ---- | ------- | ------ |
| <a id="alias_key">`alias_key`</a>                 | The key is an alias for another key. The aliased key will be used. | None needed. In order to remove the message use the aliased key. |
| <a id="alias_value">`alias_value`</a>             | The value is an alias for another value. The aliased value will be used. | None needed. In order to remove the message use the aliased value. |
| <a id="clean_key">`clean_key`</a>                 | The key is unknown, and contains special characters. Removing the special characters results in a known key. The known key will be used. | Remove the special characters. |
| <a id="clean_value">`clean_value`</a>             | The value is unknown, and contains special characters. Removing the special characters results in a known value. The known value will be used. | Remove the special characters. |
| <a id="default">`default`</a>                     | The key has not been given, the default value for this key will be used. | None needed. In order to remove the message specify a value. |
| <a id="deprecated">`deprecated`</a>               | The feature has been deprecated. | Follow the instruction indicated by the message. |
| <a id="deprecated_key">`deprecated_key`</a>       | The key has been deprecated. | Use the key or resolution mentioned in the message. |
| <a id="deprecated_value">`deprecated_value`</a>   | The value has been deprecated. | Use the value or resolution mentioned in the message. |
| <a id="downcase">`downcase`</a>                   | The string contains uppercase letters. | The downcased version of the string will be used. |
| <a id="duplicate">`duplicate`</a>                 | The value is duplicated. | None needed. In order to remove the message use unique values.|
| <a id="edge">`edge`</a>                           | The feature is an edge feature. | None needed. Be aware that the behavior of this feature can change, or go away. |
| <a id="empty">`empty`</a>                         | The section was empty, and will be dropped. | None needed. In order to remove the message remove the key, or add content. |
| <a id="find_key">`find_key`</a>                   | The key is not known. Instead, another similar key is known, and this is assumed to be a typo. The other key will be used. | Fix the typo or remove the key. |
| <a id="find_value">`find_value`</a>               | The value is not known. Instead, another similar value is known, and this is assumed to be a typo. The other value will be used. | Fix the typo or remove the key. |
| <a id="invalid_condition">`invalid_condition`</a> | The condition could not be parsed. | Fix the condition. See [Testing Conditions](https://docs.travis-ci.com/user/conditions-testing) for how to validate conditions locally. |
| <a id="invalid_env_var">`invalid_env_var`</a>     | The env var could not be parsed. | Fix the env var. |
| <a id="invalid_format">`invalid_format`</a>       | The value has an invalid format. | Fix the format used.|
| <a id="invalid_type">`invalid_type`</a>           | The section has an invalid type, and cannot be used. | Check the documentation on this config section, and use the appropriate YAML type. |
| <a id="required">`required`</a>                   | The required key has not been given. | Specify the key. |
| <a id="secure">`secure`</a>                       | The key expects an encrypted value, but an unencrypted value was given. This might leak credentials. | Encrypt the given secret. |
| <a id="strip_key">`strip_key`</a>                 | The key contains whitespace. Removing the special characters results in a known key. The known key will be used. | Remove the whitespace. |
| <a id="underscore_key">`underscore_key`</a>       | The key contains dashes. Replacing the dashes results in a known key. The known key will be used. | Use underscores, not dashes. |
| <a id="unexpected_seq">`unexpected_seq`</a>       | The key does not expect a sequence (array, list). This is a common error when working with YAML, so it is assumed that the first entry in the sequence represents the intended configuration. The first value in the sequence will be used. | Remove the extra dash that turns the section into a YAML sequence. |
| <a id="unknown_key">`unknown_key`</a>             | The key is not known. | Remove the key, or use the equivalent, known key. |
| <a id="unknown_value">`unknown_value`</a>         | The value is not known. | Remove the value, or use the equivalent, known value. |
| <a id="unknown_var">`unknown_var`</a>             | The template variable is not known, and might result in an error or a broken notification. | Remove the variable or use the equivalent, known variable. |


