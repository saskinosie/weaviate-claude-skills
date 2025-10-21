---
name: weaviate-connection
description: Connect to Weaviate vector database (cloud or local) and verify connection health
version: 1.0.0
author: Scott Askinosie
---

# Weaviate Connection Skill

This skill helps you connect to a Weaviate database instance and verify the connection is healthy.

## Purpose

Establish and test connections to Weaviate vector databases, supporting both Weaviate Cloud and self-hosted instances.

## When to Use This Skill

- User wants to connect to their Weaviate database
- User needs to verify their Weaviate connection is working
- User asks to check Weaviate cluster health or status
- User mentions a Weaviate URL or API key

## Requirements

- Python 3.8+
- weaviate-client library
- Weaviate cluster URL
- Weaviate API key (for cloud instances)

## Connection Instructions

### Step 1: Gather Connection Details

Ask the user for:
1. **Cluster URL** (e.g., `your-cluster.weaviate.cloud` or `http://localhost:8080`)
2. **API Key** (for Weaviate Cloud instances)
3. **Additional API keys** if using embedded models:
   - OpenAI API key (if using OpenAI vectorizer)
   - Cohere API key (if using Cohere vectorizer)

### Step 2: Install Dependencies

```bash
pip install weaviate-client
```

### Step 3: Create Connection Code

```python
import weaviate
import os

# For Weaviate Cloud
client = weaviate.connect_to_weaviate_cloud(
    cluster_url="YOUR_CLUSTER_URL",  # e.g., "abcd1234.weaviate.cloud"
    auth_credentials=weaviate.auth.Auth.api_key("YOUR_WEAVIATE_API_KEY"),
    headers={
        "X-OpenAI-Api-Key": "YOUR_OPENAI_KEY",  # Optional
        "X-Cohere-Api-Key": "YOUR_COHERE_KEY"   # Optional
    }
)

# For Local Weaviate
# client = weaviate.connect_to_local(
#     host="localhost",
#     port=8080
# )

# Test the connection
try:
    # Check if client is ready
    if client.is_ready():
        print("✅ Connected to Weaviate successfully!")

        # Get cluster metadata
        meta = client.get_meta()
        print(f"Weaviate version: {meta.get('version', 'unknown')}")

        # List collections
        collections = client.collections.list_all()
        print(f"\nFound {len(collections)} collections:")
        for name, config in collections.items():
            print(f"  - {name}")
    else:
        print("❌ Connection failed - cluster not ready")

except Exception as e:
    print(f"❌ Error connecting to Weaviate: {str(e)}")

finally:
    # Always close the connection
    client.close()
```

### Step 4: Verify Connection Health

After connecting, check:
- ✅ Client is ready (`client.is_ready()`)
- ✅ Can retrieve metadata (`client.get_meta()`)
- ✅ Can list collections (`client.collections.list_all()`)

## Best Practices

1. **Secure Credentials**: Never hardcode API keys - use environment variables
2. **Close Connections**: Always close the client when done to prevent memory leaks
3. **Error Handling**: Wrap connection code in try/except blocks
4. **Connection Reuse**: Keep one client instance per session, don't create multiple

## Common Issues

### Issue: "Connection refused"
- **Solution**: Check cluster URL is correct (no `https://` prefix for cloud URLs)
- **Solution**: Verify cluster is running (for local instances)

### Issue: "Invalid API key"
- **Solution**: Double-check API key is correct
- **Solution**: Ensure API key has proper permissions

### Issue: "Timeout"
- **Solution**: Check network connectivity
- **Solution**: Verify firewall isn't blocking connection

## Environment Variables Template

```bash
# .env file
WEAVIATE_URL=your-cluster.weaviate.cloud
WEAVIATE_API_KEY=your-weaviate-api-key
OPENAI_API_KEY=your-openai-key  # Optional
COHERE_KEY=your-cohere-key      # Optional
```

Then load with:
```python
from dotenv import load_dotenv
import os

load_dotenv()

client = weaviate.connect_to_weaviate_cloud(
    cluster_url=os.getenv("WEAVIATE_URL"),
    auth_credentials=weaviate.auth.Auth.api_key(os.getenv("WEAVIATE_API_KEY")),
    headers={
        "X-OpenAI-Api-Key": os.getenv("OPENAI_API_KEY"),
        "X-Cohere-Api-Key": os.getenv("COHERE_KEY")
    }
)
```

## Next Steps

After establishing connection:
- Use **weaviate-collection-manager** skill to manage collections
- Use **weaviate-data-ingestion** skill to add data
- Use **weaviate-query-agent** skill to search and retrieve data

## Additional Resources

- [Weaviate Python Client Docs](https://weaviate.io/developers/weaviate/client-libraries/python)
- [Weaviate Cloud Setup](https://console.weaviate.cloud/)