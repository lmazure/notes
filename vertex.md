
```sh
# Extract from service-account.json
SA_EMAIL=$(jq -r .client_email service-account.json)
PRIVATE_KEY=$(jq -r .private_key service-account.json | sed 's/\\n/\n/g') # unescape private key newlines
PROJECT_ID=$(jq -r .project_id service-account.json)

IAT=$(date +%s)
EXP=$((IAT + 3600)) # will not work if duration is larger than 1 hour

# Create payload
claim=$(cat <<EOF | jq -c .
{
  "iss": "$SA_EMAIL",
  "scope": "https://www.googleapis.com/auth/cloud-platform",
  "aud": "https://oauth2.googleapis.com/token",
  "iat": $IAT,
  "exp": $EXP
}
EOF
)

# Base64 encode (URL-safe)
header_b64=$(echo -n '{"alg":"RS256","typ":"JWT"}' | base64 -w0 | tr '/+' '_-' | tr -d '=')
claim_b64=$(echo -n "$claim" | base64 -w0 | tr '/+' '_-' | tr -d '=')

# Sign with openssl
to_sign="$header_b64.$claim_b64"
signature=$(echo -n "$to_sign" | openssl dgst -sha256 -sign <(echo "$PRIVATE_KEY") -binary | base64 -w0 | tr '/+' '_-' | tr -d '=')
JWT="$to_sign.$signature"

# Get access token
ACCESS_TOKEN=$(curl -s -X POST \
  -d "grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer" \
  -d "assertion=$JWT" \
  https://oauth2.googleapis.com/token | jq -r .access_token)

echo "Token: $ACCESS_TOKEN"

# Test

export PROJECT_ID="your-gcp-project-id"
export LOCATION="us-central1"        # or europe-west1, etc.
export MODEL_ID="gemini-2.0-flash-001"  # example model

curl -X POST \
  -H "Authorization: Bearer $ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  "https://${LOCATION}-aiplatform.googleapis.com/v1beta1/projects/${PROJECT_ID}/locations/${LOCATION}/endpoints/openapi/chat/completions" \
  -d "{
    \"model\": \"google/${MODEL_ID}\",
    \"messages\": [
      {\"role\": \"user\", \"content\": \"Write a short story about a magic backpack.\"}
    ]
  }"
```

in SquashTM

URL = `https://${LOCATION}-aiplatform.googleapis.com/v1beta1/projects/${PROJECT_ID}/locations/${LOCATION}/endpoints/openapi/chat/completions`  
for me: `https://us-central1-aiplatform.googleapis.com/v1beta1/projects/vertex-ai-laurents-project/locations/us-central1/endpoints/openapi/chat/completions`

API key: the access token generated above

HTTP header name 1: `Authorization`  
HTTP header value template 1: `Bearer {{apiKey}}`

payload template:
```
{
    "model": "google/gemini-2.0-flash-001",
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
