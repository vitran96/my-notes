Site: https://openapi-generator.tech/docs/installation/

# CLI

%% TODO: get sample command from TSS project %%
Check out generator list to see what options you have: https://openapi-generator.tech/docs/generators

Notes:
- The tool support relative path.

```shell
npx @openapitools/openapi-generator-cli generate -i petstore.yaml -g ruby -o /tmp/test/
openapi-generator-cli generate -i petstore.yaml -g ruby -o /tmp/test/
```

# [[Maven]] plugin

https://openapi-generator.tech/docs/plugins

If you just need to generate code, there is a plugin to generate the client.
This approach is not as useful as generate the whole source code to use as an independent package.
And this approach require dev to resolve