---
title: Response Validation
category: clients
caption: Response Validation
feature:
  artifact: io.ktor:ktor-client-core:$ktor_version
  class: io.ktor.client.features.HttpCallValidator
ktor_version_review: 1.2.0
---

This feature allows to validate HTTP response and handle transformation exceptions from engine and pipelines.

{% include feature.html %}

## Configuration

To configure response validation feature use `validateResponse` and `handleResponseException` methods:

```kotlin
HttpResponseValidator {
    validateResponse { response: HttpResponse ->
        // ...
    }

    handleResponseException { cause: Throwable ->
        // ...
    }
}
```

This feature could be configured multiple times; all validators and handlers are saved and called in order of install.

## Expect success

The `ExpectSuccess` feature implemented using response validation:

```kotlin
HttpResponseValidator {
    validateResponse { response ->
        val statusCode = response.status.value
        when (statusCode) {
            in 300..399 -> throw RedirectResponseException(response)
            in 400..499 -> throw ClientRequestException(response)
            in 500..599 -> throw ServerResponseException(response)
        }

        if (statusCode >= 600) {
            throw ResponseException(response)
        }
    }
}
```

The feature is installed by default, but could be disabled in the client configuration:

```kotlin
val client = HttpClient() {
    expectSuccess = false
}
```
