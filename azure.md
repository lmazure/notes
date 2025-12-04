The URL is:
```
<AZURE_OPENAI_ENDPOINT>/openai/deployments/<AZURE_OPENAI_DEPLOYMENT>/chat/completions?api-version=<AZURE_OPENAI_API_VERSION>
```
which gives for me
```
https://squashtm-laurent-france.openai.azure.com/openai/deployments/gpt-5-nano/chat/completions?api-version=2025-01-01-preview
```

Set the API key.

HTTP header name 1: `api-key`  
HTTP header value template 1: `{{apiKey}}`

payload template:
```
{
    "messages": [
        {{#each messages}}{
            "role": "{{#if (isSystem role)}}system{{/if}}{{#if (isUser role)}}user{{/if}}{{#if (isModel role)}}assistant{{/if}}",
            "content": {{convertToJsonString content}}
        }{{#unless @last}},
        {{/unless}}{{/each}}
    ]
}
```

JSON path: `choices[0].message.content`

Et voilà !
