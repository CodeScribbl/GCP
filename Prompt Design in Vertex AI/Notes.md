## Notes - 01/05/2025
`git clone https://github.com/GoogleCloudPlatform/generative-ai.git`


the blog post](https://cloud.google.com/blog/products/ai-machine-learning/how-to-use-grounding-for-your-llms-with-text-embeddings)

The pricing of the Cloud services we will use in this tutorial are available in the following pages:

- [Vertex AI Embeddings for Text](https://cloud.google.com/vertex-ai/pricing#generative_ai_models)
- [Vertex AI Vector Search](https://cloud.google.com/vertex-ai/pricing#matchingengine)
- [BigQuery](https://cloud.google.com/bigquery/pricing)
- [Cloud Storage](https://cloud.google.com/storage/pricing)
- [Vertex AI Workbench](https://cloud.google.com/vertex-ai/pricing#notebooks) if you use one

You can use the [Pricing Calculator](https://cloud.google.com/products/calculator) to generate a cost estimate based on your projected usage. The following is an example of rough cost estimation with the calculator, assuming you will go through this tutorial a couple of time.


With the [Vertex AI Embeddings for Text](https://cloud.google.com/vertex-ai/docs/generative-ai/embeddings/get-text-embeddings), you can easily create a text embedding with LLM. The product is also available on [Vertex AI Model Garden](https://cloud.google.com/model-garden)

![](https://storage.googleapis.com/github-repo/img/embeddings/textemb-vs-notebook/7.png)

This API is designed to extract embeddings from texts. It can take text input up to 2048 input tokens, and outputs 768 dimensional text embeddings.

### Explore Vector Search and try the demo

In this task, you will explore the Vector search notebook and try the public demo. Since index creation and deployment takes ~30 minutes, you can try the public demo and explore the notebook while you wait.

#### While you wait: Try the Stack Overflow semantic search demo

1. The Vector Search public demo is available as a [public live demo](https://ai-demos.dev/). Select "STACKOVERFLOW" and enter any coding question as a query, so it runs a text search on **8 million** questions posted on [Stack Overflow](https://stackoverflow.com/). Try the text semantic search with some queries like 'How to shuffle rows in SQL?' or arbitrary programming questions.

#### While you wait: Explore the Vector Search notebook

1. In the notebook, navigate to the **Bringing Gen AI and LLMs to production services** section at the top and read through the vector search use cases and explanations.
