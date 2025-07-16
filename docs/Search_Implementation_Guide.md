# Search Implementation Guide

This document outlines the steps and considerations for integrating Meilisearch with the TechNexus Blog to provide robust search functionality.

## 1. Meilisearch Setup

Meilisearch is a fast, open-source search engine that provides a great developer experience.

### 1.1 Local Setup (Development)

For local development, you can run Meilisearch using Docker:

1. **Install Docker:** Ensure Docker is installed and running on your system.
2. **Run Meilisearch Container:**

    ```bash
    docker run -p 7700:7700 \
      -e MEILI_MASTER_KEY='YOUR_MASTER_KEY' \
      getmeili/meilisearch:latest
    ```

    Replace `'YOUR_MASTER_KEY'` with a strong, unique key. This key will be used for authentication.

### 1.2 Cloud Setup (Production)

For production, consider using a managed Meilisearch service (e.g., Meilisearch Cloud) for scalability and reliability.

## 2. Indexing Article Content from Supabase

To index article content, we will leverage Supabase Edge Functions to listen for changes in the `articles` table and push relevant data to Meilisearch.

### 2.1 Data Schema for Meilisearch

The Meilisearch index will store the following fields for each article:

* `id`: Unique identifier for the article (from Supabase).
* `title`: The title of the article.
* `excerpt`: A short summary of the article.
* `content`: The full content of the article (indexed for comprehensive search).
* `slug`: The URL-friendly slug for the article.
* `publishedDate`: The date the article was published.
* `tags`: An array of tags associated with the article.
* `expertiseLevel`: The expertise level required for the article.
* `format`: The content format (e.g., ARTICLE, TUTORIAL).

### 2.2 Supabase Edge Function for Indexing

We will create a Supabase Edge Function that triggers on database changes (e.g., `INSERT`, `UPDATE`, `DELETE` on the `articles` table).

**Example Logic (Conceptual):**

```typescript
// backend/src/functions/meilisearch-sync.ts (example path)

import { serve } from 'https://deno.land/std@0.177.0/http/server.ts';
import { MeiliSearch } from 'https://esm.sh/meilisearch@0.37.1'; // Use appropriate version

const MEILI_HOST = Deno.env.get('MEILI_HOST') || 'http://localhost:7700';
const MEILI_MASTER_KEY = Deno.env.get('MEILI_MASTER_KEY') || 'YOUR_MASTER_KEY'; // Use environment variables!
const MEILI_INDEX_NAME = 'articles';

const client = new MeiliSearch({
  host: MEILI_HOST,
  apiKey: MEILI_MASTER_KEY,
});

serve(async (req) => {
  // This function would be triggered by Supabase Realtime or DB triggers
  // For simplicity, let's assume we receive data in the request body
  const payload = await req.json();

  // Process Supabase event (e.g., INSERT, UPDATE, DELETE)
  // For INSERT/UPDATE:
  if (payload.event === 'INSERT' || payload.event === 'UPDATE') {
    const articleData = payload.record; // Assuming Supabase event structure

    const document = {
      id: articleData.id,
      title: articleData.title,
      excerpt: articleData.excerpt,
      content: articleData.content, // Full content is now indexed
      slug: articleData.slug,
      publishedDate: articleData.publishedDate,
      tags: articleData.tags,
      expertiseLevel: articleData.expertiseLevel,
      format: articleData.format,
    };

    try {
      await client.index(MEILI_INDEX_NAME).addDocuments([document]);
      return new Response(JSON.stringify({ message: 'Document indexed successfully' }), {
        headers: { 'Content-Type': 'application/json' },
      });
    } catch (error) {
      console.error('Error indexing document:', error);
      return new Response(JSON.stringify({ error: 'Failed to index document' }), {
        status: 500,
        headers: { 'Content-Type': 'application/json' },
      });
    }
  }

  // For DELETE:
  if (payload.event === 'DELETE') {
    const articleId = payload.old_record.id; // Assuming Supabase event structure
    try {
      await client.index(MEILI_INDEX_NAME).deleteDocument(articleId);
      return new Response(JSON.JSON.stringify({ message: 'Document deleted successfully' }), {
        headers: { 'Content-Type': 'application/json' },
      });
    } catch (error) {
      console.error('Error deleting document:', error);
      return new Response(JSON.stringify({ error: 'Failed to delete document' }), {
        status: 500,
        headers: { 'Content-Type': 'application/json' },
      });
    }
  }

  return new Response(JSON.stringify({ message: 'No action taken' }), {
    headers: { 'Content-Type': 'application/json' },
  });
});

// Initial indexing script (run once)
async function initialIndex() {
  // Fetch all articles from Supabase
  // For example, using Supabase JS client or a direct SQL query
  // const { data: articles, error } = await supabase.from('articles').select('*');
  // if (error) throw error;

  // const documents = articles.map(article => ({
  //   id: article.id,
  //   title: article.title,
  //   excerpt: article.excerpt,
  //   slug: article.slug,
  //   publishedDate: article.publishedDate,
  //   tags: article.tags,
  //   expertiseLevel: article.expertiseLevel,
  //   format: article.format,
  // }));

  // await client.index(MEILI_INDEX_NAME).addDocuments(documents);
  // console.log('Initial indexing complete.');
}

// Call initialIndex() if needed, or set up a separate script for it.
```

**Note:** The actual implementation of Supabase Edge Functions and data syncing will require specific Supabase client setup and environment variable management.

## 3. Backend Search API Endpoint

We will create a Supabase Edge Function that exposes an API endpoint to query Meilisearch.

**Example Endpoint (`GET /api/search`):**

```typescript
// backend/src/functions/search-api.ts (example path)

import { serve } from 'https://deno.land/std@0.177.0/http/server.ts';
import { MeiliSearch } from 'https://esm.sh/meilisearch@0.37.1'; // Use appropriate version

const MEILI_HOST = Deno.env.get('MEILI_HOST') || 'http://localhost:7700';
const MEILI_API_KEY = Deno.env.get('MEILI_API_KEY'); // Use a search-only API key for frontend
const MEILI_INDEX_NAME = 'articles';

const client = new MeiliSearch({
  host: MEILI_HOST,
  apiKey: MEILI_API_KEY, // Use a dedicated search key from environment variables
});

serve(async (req) => {
  const url = new URL(req.url);
  const query = url.searchParams.get('q');

  if (!query) {
    return new Response(JSON.stringify({ error: 'Query parameter "q" is required' }), {
      status: 400,
      headers: { 'Content-Type': 'application/json' },
    });
  }

  try {
    const searchResults = await client.index(MEILI_INDEX_NAME).search(query, {
      // Optional: configure search parameters like limit, attributesToHighlight, etc.
      limit: 10,
      attributesToHighlight: ['title', 'excerpt'],
    });

    // Map Meilisearch results to a format suitable for the frontend
    const formattedResults = searchResults.hits.map(hit => ({
      id: hit.id,
      title: hit.title,
      excerpt: hit.excerpt,
      slug: hit.slug,
      // Add other relevant fields
    }));

    return new Response(JSON.stringify({ results: formattedResults, totalHits: searchResults.estimatedTotalHits }), {
      headers: { 'Content-Type': 'application/json' },
    });
  } catch (error) {
    console.error('Error performing search:', error);
    return new Response(JSON.stringify({ error: 'Search failed' }), {
      status: 500,
      headers: { 'Content-Type': 'application/json' },
    });
  }
});
```

**Note:** For production, it's crucial to use a Meilisearch API key with limited permissions (e.g., search-only) for the frontend API.

## 4. Frontend Integration

### 4.1 Search Bar Component (`src/components/ui/SearchBar.tsx`)

This component will capture user input and trigger the search.

```tsx
// src/components/ui/SearchBar.tsx
import React, { useState, FormEvent } from 'react';
import { useRouter } from 'next/navigation'; // Assuming Next.js

interface SearchBarProps {
  // Add any other props if needed, e.g., initialQuery
}

const SearchBar: React.FC<SearchBarProps> = () => {
  const [query, setQuery] = useState<string>('');
  const router = useRouter();

  const handleSearch = (e: FormEvent) => {
    e.preventDefault();
    if (query.trim()) {
      router.push(`/search?q=${encodeURIComponent(query)}`);
    }
  };

  return (
    <form onSubmit={handleSearch} className="flex items-center">
      <input
        type="text"
        placeholder="Search articles..."
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        className="p-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
      />
      <button type="submit" className="ml-2 p-2 bg-blue-500 text-white rounded-md hover:bg-blue-600">
        Search
      </button>
    </form>
  );
};

export default SearchBar;
```

### 4.2 Search Results Page (`src/app/search/page.tsx`)

This page will fetch search results from the backend API and display them.

```tsx
// src/app/search/page.tsx
'use client';

import React, { useEffect, useState } from 'react';
import { useSearchParams } from 'next/navigation';
import ArticleCard from '@/components/articles/ArticleCard'; // Assuming ArticleCard component exists

interface SearchResult {
  id: string;
  title: string;
  excerpt: string;
  slug: string;
  // Add other fields as returned by the API
}

const SearchPage: React.FC = () => {
  const searchParams = useSearchParams();
  const query = searchParams.get('q');
  const [results, setResults] = useState<SearchResult[]>([]);
  const [loading, setLoading] = useState<boolean>(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    if (!query) {
      setLoading(false);
      return;
    }

    const fetchResults = async () => {
      setLoading(true);
      setError(null);
      try {
        // Replace with your actual backend API endpoint
        const response = await fetch(`/api/search?q=${encodeURIComponent(query)}`);
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        const data = await response.json();
        setResults(data.results || []);
      } catch (err: any) {
        setError(err.message);
        setResults([]);
      } finally {
        setLoading(false);
      }
    };

    fetchResults();
  }, [query]);

  return (
    <div className="container mx-auto p-4">
      <h1 className="text-3xl font-bold mb-6">Search Results for: "{query}"</h1>

      {loading && <p>Loading results...</p>}
      {error && <p className="text-red-500">Error: {error}</p>}
      {!loading && !error && results.length === 0 && query && (
        <p>No articles found for your search query.</p>
      )}
      {!loading && !error && results.length > 0 && (
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
          {results.map((article) => (
            <ArticleCard
              key={article.id}
              title={article.title}
              excerpt={article.excerpt}
              slug={article.slug}
              // Pass other necessary props to ArticleCard
              // e.g., author, publishedDate, etc. if available in search results
            />
          ))}
        </div>
      )}
    </div>
  );
};

export default SearchPage;
```

## 5. Next Steps

* **Tunde:**
  * Implement the Supabase Edge Function for Meilisearch data synchronization.
  * Implement the Supabase Edge Function for the `/api/search` endpoint.
  * Configure Meilisearch index settings (e.g., `searchableAttributes`, `filterableAttributes`).
* **Ada:**
  * Refine the `SearchBar` and `SearchPage` components based on UI mockups and actual API response structure.
  * Implement error handling and loading states more robustly.
  * Add pagination to the search results page if needed.
  * Ensure `ArticleCard` component can display the data from search results.

This document provides a foundational guide. Specific implementation details for Supabase Edge Functions and Meilisearch configuration will be detailed in subsequent steps or within the respective code files.
