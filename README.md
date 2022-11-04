# Open Api Generator Bug

Sample code to allow reproducing the following bug: https://github.com/OpenAPITools/openapi-generator/issues/13917
with the [Open Api Generator](https://github.com/OpenAPITools/openapi-generator).

# How to reproduce the bug

```
./mvnw clean install
```

Output:

```
[ERROR] COMPILATION ERROR :
[INFO] -------------------------------------------------------------
[ERROR] /Users/luciano/Downloads/openapi-generator-bug/target/generated-sources/openapi/src/main/java/org/openapitools/codegen/api/model/PatchRequestInner.java:[25,1] @Builder is only supported on classes, records, constructors, and methods.
[ERROR] /Users/luciano/Downloads/openapi-generator-bug/target/generated-sources/openapi/src/main/java/org/openapitools/codegen/api/model/PatchRequestInner.java:[25,17] NoArgsConstructor is only supported on a class or an enum.
[ERROR] /Users/luciano/Downloads/openapi-generator-bug/target/generated-sources/openapi/src/main/java/org/openapitools/codegen/api/model/PatchRequestInner.java:[25,43] AllArgsConstructor is only supported on a class or an enum.
[INFO] 3 errors
```


# Bug description

Given the following components:

```
components:
  schemas:
    PatchRequest:
          type: array
          items:
            oneOf:
              - $ref: '#/components/schemas/JSONPatchRequestAddReplaceTest'
              - $ref: '#/components/schemas/JSONPatchRequestRemove'
              - $ref: '#/components/schemas/JSONPatchRequestMoveCopy'


    JSONPatchRequestAddReplaceTest:
      type: object
      additionalProperties: false
      ...
    JSONPatchRequestRemove:
      type: object
      ...   
    JSONPatchRequestMoveCopy:
      type: object
      additionalProperties: false
```

And given the usage of the following configuration setting in the Maven plugin:

```
<additionalModelTypeAnnotations>@lombok.Builder @lombok.NoArgsConstructor @lombok.AllArgsConstructor</additionalModelTypeAnnotations>
```

The plugin generates an Java Interface annotated with Lombok annotations:


```
lombok.Builder @lombok.NoArgsConstructor @lombok.AllArgsConstructor

@Generated(value = "org.openapitools.codegen.languages.SpringCodegen", date = "2022-11-04T13:47:46.119673+01:00[Europe/Berlin]")
public interface PatchRequestInner extends Serializable {
}
```

This makes the compilation to fail.
