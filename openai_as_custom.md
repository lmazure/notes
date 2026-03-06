in SquashTM

- API provider: `Custom`
- URL: `https://api.openai.com/v1/chat/completions`
- API key (use an OpenAI key)
- HTTP header name 1: `Authorization`
- HTTP header value template 1: `Bearer {{apiKey}}` 
- Payload template:
    ```
    {
        "model": "gpt-5.1",
        "messages": [
            {{#each messages}}{
                "role": "{{#if (isSystem role)}}system{{/if}}{{#if (isUser role)}}user{{/if}}{{#if (isModel role)}}assistant{{/if}}",
                "content": {{convertToJsonString content}}
            }{{#unless @last}},
            {{/unless}}{{/each}}
        ],
        "temperature": 0.7,
        "seed": 42
    }
    ```
- JSON path of generated text `choices[0].message.content`
