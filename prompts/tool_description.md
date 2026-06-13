# Pinecone Vector Store Tool – Configuration

## Tool Name

```
codex_catalog_search
```

## Tool Description

```
Searches the Codex d.o.o. sales catalog (CODEX_2010_katalog_NET_links) for
information about bearings, chains, belts, housings, seals, retaining rings,
adhesives, and related industrial mechanical parts, as well as company
information, product specifications, technical definitions, and the list of
distributors and sales points by country.

Use this tool whenever the user asks about:
- specific products, product categories, or technical specifications
  (e.g. bearing types, dimensions, materials)
- the Codex sales programme or product catalog structure
- definitions of technical terms used in the catalog
- distributors, dealers, or sale points in a specific country or region
- company contact information (address, phone, email)

The input should be a concise search query describing what information is
needed (e.g. "extreme bearings specifications" or "distributor Slovakia").
The tool returns the most relevant excerpts from the catalog.

The catalog content is available in Slovenian, English, German, Croatian, and
Serbian - search results may contain text in multiple languages regardless of
the query language.

Do not use this tool for general questions unrelated to Codex products, the
catalog, or its distributors.
```

## Index configuration

- **Operation Mode**: Retrieve Documents (As Tool for AI Agent)
- **Pinecone Index**: `codexchatbot`
- **Embeddings model**: `text-embedding-3-small` (must match the embeddings
  model used during indexing, otherwise vector similarity search will not work
  correctly)
