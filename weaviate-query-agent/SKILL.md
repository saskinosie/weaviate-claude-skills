---
name: weaviate-query-agent
description: Search and retrieve data from Weaviate using semantic search, filters, RAG, and hybrid queries
version: 1.0.0
author: Scott Askinosie
dependencies:
  - weaviate-connection
  - weaviate-collection-manager
---

# Weaviate Query Agent Skill

This skill helps you search and retrieve data from Weaviate collections using semantic vector search, keyword search, filters, and RAG capabilities.

## Purpose

Query your Weaviate collections intelligently to find relevant information, perform Q&A, and analyze your data.

## When to Use This Skill

- User wants to search for information in a collection
- User asks questions that need semantic search
- User needs to filter results by specific criteria
- User wants to use RAG (Retrieval Augmented Generation) for Q&A
- User asks about finding similar items
- User needs to combine vector search with filters

## Prerequisites

- Active Weaviate connection (use **weaviate-connection** skill first)
- Collection with data (use **weaviate-data-ingestion** skill to add data)
- Python weaviate-client library installed

## Query Types

### 1. Semantic Search (Vector Search)

Find objects semantically similar to your query:

```python
import weaviate

# Assuming client is already connected
collection = client.collections.get("Articles")

# Search by meaning
response = collection.query.near_text(
    query="artificial intelligence and machine learning",
    limit=5
)

# Display results
for obj in response.objects:
    print(f"Title: {obj.properties['title']}")
    print(f"Content: {obj.properties['content'][:200]}...")
    print(f"Score: {obj.metadata.score}\n")
```

### 2. Search with Specific Properties

Return only the fields you need:

```python
response = collection.query.near_text(
    query="vector databases",
    limit=5,
    return_properties=["title", "author", "publishDate"]
)

for obj in response.objects:
    print(f"{obj.properties['title']} by {obj.properties['author']}")
```

### 3. Keyword Search (BM25)

Traditional keyword-based search:

```python
from weaviate.classes.query import QueryReference

response = collection.query.bm25(
    query="vector search",
    limit=5
)

for obj in response.objects:
    print(f"Title: {obj.properties['title']}")
```

### 4. Hybrid Search (Best of Both Worlds)

Combine semantic and keyword search:

```python
response = collection.query.hybrid(
    query="machine learning applications",
    limit=5,
    alpha=0.5  # 0 = pure BM25, 1 = pure vector, 0.5 = balanced
)

for obj in response.objects:
    print(f"Title: {obj.properties['title']}")
    print(f"Score: {obj.metadata.score}\n")
```

### 5. Filter Results

Search with conditions:

```python
from weaviate.classes.query import Filter

# Search with author filter
response = collection.query.near_text(
    query="AI advancements",
    limit=5,
    filters=Filter.by_property("author").equal("Jane Smith")
)

# Multiple filters
response = collection.query.near_text(
    query="technology trends",
    limit=10,
    filters=(
        Filter.by_property("author").equal("Jane Smith") &
        Filter.by_property("publishDate").greater_than("2024-01-01T00:00:00Z")
    )
)

# Filter by array contains
response = collection.query.near_text(
    query="programming",
    filters=Filter.by_property("tags").contains_any(["python", "javascript"])
)
```

### 6. Filter Operators

```python
from weaviate.classes.query import Filter

# Equality
Filter.by_property("status").equal("published")

# Comparison
Filter.by_property("price").greater_than(100)
Filter.by_property("price").less_than(500)
Filter.by_property("price").greater_or_equal(100)
Filter.by_property("price").less_or_equal(500)

# String matching
Filter.by_property("title").like("*vector*")  # Contains "vector"

# Array operations
Filter.by_property("tags").contains_any(["ai", "ml"])
Filter.by_property("tags").contains_all(["python", "tutorial"])

# Combine filters
(Filter.by_property("price").greater_than(100) &
 Filter.by_property("category").equal("Electronics"))

# OR conditions
(Filter.by_property("author").equal("John") |
 Filter.by_property("author").equal("Jane"))
```

### 7. Search by Image (Multi-modal)

For collections with CLIP or multi2vec:

```python
import base64

# Encode query image
with open("query_image.jpg", "rb") as f:
    query_image = base64.b64encode(f.read()).decode("utf-8")

collection = client.collections.get("ProductCatalog")

# Find similar images
response = collection.query.near_image(
    near_image=query_image,
    limit=5,
    return_properties=["name", "description", "price"]
)

for obj in response.objects:
    print(f"Product: {obj.properties['name']} - ${obj.properties['price']}")
```

### 8. Search by Vector

If you have a pre-computed embedding:

```python
# Your custom embedding
query_vector = [0.1, 0.2, 0.3, ...]  # 1536 dimensions for OpenAI

response = collection.query.near_vector(
    near_vector=query_vector,
    limit=5
)
```

### 9. Get Object by ID

Retrieve specific object:

```python
# Get by UUID
obj = collection.query.fetch_object_by_id("uuid-here")

print(f"Title: {obj.properties['title']}")
print(f"Content: {obj.properties['content']}")
```

### 10. Fetch Multiple Objects

Get all objects or filter by property:

```python
from weaviate.classes.query import Filter

# Get all objects (paginated)
response = collection.query.fetch_objects(limit=100)

# Get objects matching filter
response = collection.query.fetch_objects(
    filters=Filter.by_property("section").equal("Introduction"),
    limit=50
)

for obj in response.objects:
    print(obj.properties['title'])
```

## RAG (Retrieval Augmented Generation)

Use Weaviate's generative module for Q&A:

### Single Prompt RAG

```python
# Collection must have generative module configured
collection = client.collections.get("TechnicalDocuments")

response = collection.generate.near_text(
    query="How do I configure HVAC systems?",
    single_prompt="Answer this question based on the context: {question}. Context: {content}",
    limit=3
)

# Get generated answer
print(f"Answer: {response.generated}")

# See source documents
for obj in response.objects:
    print(f"\nSource: {obj.properties['title']}")
    print(f"Content: {obj.properties['content'][:200]}...")
```

### Grouped Task RAG

Generate one response using all results:

```python
response = collection.generate.near_text(
    query="What are the best practices for fan selection?",
    grouped_task="Summarize the key recommendations from these documents about fan selection",
    limit=5
)

print(response.generated)
```

### Custom RAG Implementation

If collection doesn't have generative module:

```python
from openai import OpenAI

# Search Weaviate
weaviate_collection = weaviate_client.collections.get("TechnicalDocuments")
search_results = weaviate_collection.query.near_text(
    query="What is the friction loss for round elbows?",
    limit=5,
    return_properties=["content", "section", "page"]
)

# Build context
context = "\n\n".join([
    f"[{obj.properties['section']} - Page {obj.properties['page']}]\n{obj.properties['content']}"
    for obj in search_results.objects
])

# Call LLM
openai_client = OpenAI()
response = openai_client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {
            "role": "system",
            "content": "You are a technical assistant. Answer questions based on the provided context."
        },
        {
            "role": "user",
            "content": f"Question: What is the friction loss for round elbows?\n\nContext:\n{context}"
        }
    ]
)

answer = response.choices[0].message.content
print(answer)
```

## Advanced Query Features

### 1. Limit and Offset (Pagination)

```python
# Get results 10-20
response = collection.query.near_text(
    query="AI trends",
    limit=10,
    offset=10
)
```

### 2. Distance Threshold

Only return results within certain similarity:

```python
response = collection.query.near_text(
    query="machine learning",
    distance=0.3,  # Only results with distance < 0.3
    limit=10
)
```

### 3. Include Vector in Response

```python
response = collection.query.near_text(
    query="vector search",
    limit=5,
    return_metadata=["vector"]
)

for obj in response.objects:
    print(f"Vector: {obj.metadata.vector[:5]}...")  # First 5 dimensions
```

### 4. Aggregate Queries

Get statistics about your collection:

```python
from weaviate.classes.aggregate import GroupByAggregate

# Count objects
result = collection.aggregate.over_all(
    total_count=True
)
print(f"Total objects: {result.total_count}")

# Group by property
result = collection.aggregate.over_all(
    group_by=GroupByAggregate(prop="author")
)

for group in result.groups:
    print(f"Author: {group.grouped_by.value} - Count: {group.total_count}")
```

### 5. Multi-target Vector Search

For collections with multiple vector spaces:

```python
from weaviate.classes.query import TargetVectors

# Search specific vector space
response = collection.query.near_text(
    query="machine learning",
    target_vector="content_vector",  # Specify which vector
    limit=5
)
```

### 6. Reranking

Improve search quality with reranking:

```python
# Collection must have reranker configured
response = collection.query.near_text(
    query="best practices for database design",
    limit=20,
    rerank={
        "property": "content",
        "query": "database design best practices"
    }
)
```

## Complete Search Examples

### Example 1: Advanced Document Search

```python
from weaviate.classes.query import Filter, MetadataQuery

collection = client.collections.get("TechnicalDocuments")

# Complex search with filters and specific properties
response = collection.query.near_text(
    query="seismic zone requirements",
    limit=10,
    filters=(
        Filter.by_property("section").like("*Building*") &
        Filter.by_property("page").greater_than(50)
    ),
    return_properties=["title", "content", "section", "page"],
    return_metadata=MetadataQuery(distance=True, certainty=True)
)

# Process results
for obj in response.objects:
    print(f"\n{'='*60}")
    print(f"Section: {obj.properties['section']}")
    print(f"Page: {obj.properties['page']}")
    print(f"Certainty: {obj.metadata.certainty:.2%}")
    print(f"\nContent:\n{obj.properties['content'][:300]}...")
```

### Example 2: Multi-Modal Product Search

```python
import base64

collection = client.collections.get("ProductCatalog")

# Search by text and image
with open("reference_product.jpg", "rb") as f:
    ref_image = base64.b64encode(f.read()).decode("utf-8")

# Hybrid approach: text + image similarity
response = collection.query.near_image(
    near_image=ref_image,
    limit=10,
    filters=Filter.by_property("price").less_than(500),
    return_properties=["name", "description", "price", "category"]
)

print("Similar products under $500:\n")
for obj in response.objects:
    print(f"• {obj.properties['name']}")
    print(f"  Price: ${obj.properties['price']}")
    print(f"  Category: {obj.properties['category']}\n")
```

### Example 3: Interactive Q&A System

```python
def ask_question(question: str, collection_name: str = "TechnicalDocuments"):
    """Interactive Q&A using RAG"""
    collection = client.collections.get(collection_name)

    # Search with RAG
    response = collection.generate.near_text(
        query=question,
        single_prompt=f"""Based on the following context, answer this question: {question}

Context: {{content}}

Provide a clear, concise answer. If you cannot answer based on the context, say so.""",
        limit=5
    )

    print(f"\n🤖 Answer:\n{response.generated}\n")
    print(f"📚 Sources:")
    for i, obj in enumerate(response.objects, 1):
        print(f"{i}. {obj.properties.get('title', 'Untitled')} (Page {obj.properties.get('page', 'N/A')})")

# Use it
ask_question("What are the wind zone requirements for Missouri?")
ask_question("How do I calculate motor efficiency?")
```

## Query Performance Tips

1. **Use Filters**: Pre-filter before vector search for better performance
2. **Limit Results**: Only retrieve what you need (smaller `limit` is faster)
3. **Select Properties**: Don't return all properties if you only need some
4. **Batch Queries**: Query once and process locally rather than many small queries
5. **Hybrid Search**: Use `alpha` parameter to balance speed vs accuracy
6. **Index Optimization**: Ensure properties used in filters are indexed

## Error Handling

```python
from weaviate.exceptions import WeaviateQueryException

try:
    response = collection.query.near_text(
        query="machine learning",
        limit=10
    )

    if not response.objects:
        print("No results found")
    else:
        for obj in response.objects:
            print(obj.properties['title'])

except WeaviateQueryException as e:
    print(f"Query error: {str(e)}")
except Exception as e:
    print(f"Unexpected error: {str(e)}")
```

## Troubleshooting

### Issue: "No results found"
- **Solution**: Try broader query terms
- **Solution**: Check if collection has data
- **Solution**: Verify vectorizer is working

### Issue: "Filter not working"
- **Solution**: Check property name matches schema exactly (camelCase)
- **Solution**: Ensure property type matches filter operation
- **Solution**: Verify property is indexed

### Issue: "Slow queries"
- **Solution**: Reduce `limit` value
- **Solution**: Use filters to narrow search space
- **Solution**: Return fewer properties
- **Solution**: Check collection size and indexing

### Issue: "Generative search fails"
- **Solution**: Verify collection has generative module configured
- **Solution**: Check API keys for OpenAI/Cohere are set
- **Solution**: Ensure sufficient API quota

## Complete Query Pipeline Example

```python
import weaviate
from weaviate.classes.query import Filter, MetadataQuery
from openai import OpenAI
import os

def semantic_search_with_rag(
    question: str,
    collection_name: str = "TechnicalDocuments",
    limit: int = 5,
    filters: Filter | None = None
):
    """Complete search pipeline with custom RAG"""

    # Connect to Weaviate
    weaviate_client = weaviate.connect_to_weaviate_cloud(
        cluster_url=os.getenv("WEAVIATE_URL"),
        auth_credentials=weaviate.auth.Auth.api_key(os.getenv("WEAVIATE_API_KEY"))
    )

    try:
        # Step 1: Vector search
        print(f"🔍 Searching for: {question}")
        collection = weaviate_client.collections.get(collection_name)

        response = collection.query.near_text(
            query=question,
            limit=limit,
            filters=filters,
            return_properties=["content", "section", "page", "title"],
            return_metadata=MetadataQuery(distance=True)
        )

        if not response.objects:
            return "No relevant information found."

        # Step 2: Build context
        context_parts = []
        sources = []

        for obj in response.objects:
            props = obj.properties
            context_parts.append(
                f"[{props.get('section', 'Unknown')} - Page {props.get('page', 'N/A')}]\n{props['content']}"
            )
            sources.append({
                "title": props.get('title', 'Untitled'),
                "section": props.get('section', 'Unknown'),
                "page": props.get('page', 'N/A'),
                "distance": obj.metadata.distance
            })

        context = "\n\n---\n\n".join(context_parts)

        # Step 3: Generate answer
        print("🤖 Generating answer...")
        openai_client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))

        chat_response = openai_client.chat.completions.create(
            model="gpt-4o",
            messages=[
                {
                    "role": "system",
                    "content": "You are a technical assistant. Provide accurate answers based on the context. Cite specific page numbers when possible."
                },
                {
                    "role": "user",
                    "content": f"Question: {question}\n\nContext:\n{context}\n\nProvide a comprehensive answer."
                }
            ],
            max_tokens=1000
        )

        answer = chat_response.choices[0].message.content

        # Step 4: Format response
        result = f"\n{'='*70}\n"
        result += f"🤖 ANSWER:\n\n{answer}\n"
        result += f"\n{'='*70}\n"
        result += f"📚 SOURCES:\n\n"

        for i, source in enumerate(sources, 1):
            result += f"{i}. {source['title']} - {source['section']} (Page {source['page']})\n"
            result += f"   Similarity: {1 - source['distance']:.2%}\n"

        return result

    finally:
        weaviate_client.close()

# Example usage
if __name__ == "__main__":
    question = "What is the friction loss for round elbows?"

    # Optional: Add filters
    filters = Filter.by_property("section").like("*Ductwork*")

    result = semantic_search_with_rag(
        question=question,
        collection_name="Cook_Engineering_Manual",
        limit=5,
        filters=filters
    )

    print(result)
```

## Next Steps

After mastering queries:
- Combine with **weaviate-data-ingestion** for continuous data updates
- Monitor query performance and adjust schemas
- Experiment with different query types for your use case
- Build custom applications on top of Weaviate

## Additional Resources

- [Weaviate Query Docs](https://weaviate.io/developers/weaviate/search)
- [RAG Tutorial](https://weaviate.io/developers/weaviate/tutorials/rag)
- [Filter Reference](https://weaviate.io/developers/weaviate/search/filters)