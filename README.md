# alx-project-0x14

## API Overview

The MoviesDatabase API is a comprehensive RESTful web service that provides detailed information about movies, TV shows, actors, and related entertainment content. This API offers access to an extensive database containing over 9 million titles including movies, series, and episodes, along with information about 11 million actors, crew members, and cast. Key features include:

- Detailed movie and TV show information (titles, ratings, release dates, genres, runtime)
- Actor and crew biographies with complete filmographies
- YouTube trailer URLs and promotional content
- Awards and nominations data
- Box office statistics and revenue information
- High-quality poster and backdrop images
- Search functionality across multiple categories
- Real-time updates to ensure data accuracy

The API is designed to be developer-friendly with consistent response formats and comprehensive documentation, making it ideal for building movie websites, mobile applications, recommendation systems, and entertainment-focused projects.

## Version

**Current Version:** v1

The MoviesDatabase API uses a versioning system to ensure backward compatibility. The current stable version is v1, which is accessible through RapidAPI's infrastructure. All endpoints follow RESTful conventions and return JSON-formatted responses.

## Available Endpoints

### Title Endpoints
- **GET /titles** - Retrieve a list of titles with filtering and pagination options
- **GET /titles/{id}** - Get detailed information about a specific title by its ID
- **GET /titles/search/title/{query}** - Search for titles by name
- **GET /titles/random** - Retrieve random title suggestions

### Series Endpoints
- **GET /titles/series/{id}** - Get detailed information about a TV series
- **GET /titles/season/{seriesId}** - Retrieve season information for a specific series
- **GET /titles/episode/{episodeId}** - Get details about a specific episode

### Name (Actor/Crew) Endpoints
- **GET /names** - Retrieve a list of actors and crew members
- **GET /names/{id}** - Get detailed information about a specific person
- **GET /names/search/name/{query}** - Search for people by name

### Awards Endpoints
- **GET /titles/awards/{id}** - Retrieve awards and nominations for a specific title
- **GET /names/awards/{id}** - Get awards information for a specific person

### Media Endpoints
- **GET /titles/images/{id}** - Fetch images (posters, backdrops) for a title
- **GET /titles/videos/{id}** - Retrieve video content including trailers

### Metadata Endpoints
- **GET /titles/genres** - Get a list of available genres
- **GET /titles/countries** - Retrieve list of production countries
- **GET /titles/languages** - Get available language options

## Request and Response Format

### Request Structure

All API requests must include proper authentication headers and follow this general structure:

**Base URL:** `https://moviesdatabase.p.rapidapi.com`

**Required Headers:**
```
X-RapidAPI-Key: your_api_key_here
X-RapidAPI-Host: moviesdatabase.p.rapidapi.com
```

**Example Request (GET Title by ID):**
```http
GET /titles/tt0111161 HTTP/1.1
Host: moviesdatabase.p.rapidapi.com
X-RapidAPI-Key: your_api_key_here
X-RapidAPI-Host: moviesdatabase.p.rapidapi.com
```

**Query Parameters (where applicable):**
- `page` - Page number for pagination (default: 1)
- `limit` - Number of results per page (default: 10, max: 50)
- `year` - Filter by release year
- `genre` - Filter by genre
- `sort` - Sort results (e.g., "year.desc", "rating.desc")

### Response Structure

All successful responses return JSON-formatted data with a consistent structure:

**Successful Response (200 OK):**



### Example Authenticated Request (TypeScript/JavaScript)

```typescript
const options = {
  method: 'GET',
  headers: {
    'X-RapidAPI-Key': process.env.RAPIDAPI_KEY,
    'X-RapidAPI-Host': 'moviesdatabase.p.rapidapi.com'
  }
};

fetch('https://moviesdatabase.p.rapidapi.com/titles/tt0111161', options)
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('Error:', error));
```

### Security Best Practices

- **Never expose your API key** in client-side code or public repositories
- Store API keys in environment variables (`.env` files)
- Use backend proxies for production applications
- Rotate API keys regularly
- Monitor API key usage in RapidAPI dashboard

## Error Handling

The MoviesDatabase API uses standard HTTP status codes to indicate the success or failure of requests. Understanding these error codes is crucial for building robust applications.

### Common HTTP Status Codes

**Success Codes:**
- `200 OK` - Request succeeded, data returned
- `201 Created` - Resource successfully created
- `204 No Content` - Request succeeded, no data to return

**Client Error Codes:**
- `400 Bad Request` - Invalid request parameters or malformed syntax
- `401 Unauthorized` - Missing or invalid API key
- `403 Forbidden` - Valid API key but insufficient permissions or quota exceeded
- `404 Not Found` - Requested resource doesn't exist
- `429 Too Many Requests` - Rate limit exceeded

**Server Error Codes:**
- `500 Internal Server Error` - Server-side error occurred
- `503 Service Unavailable` - API temporarily unavailable

### Error Response Format

```json
{
  "message": "You are not subscribed to this API.",
  "statusCode": 403,
  "error": "Forbidden"
}
```

### Error Handling Implementation (TypeScript)

```typescript
interface ApiError {
  message: string;
  statusCode: number;
  error: string;
}

async function fetchMovieData(titleId: string): Promise<any> {
  try {
    const response = await fetch(
      `https://moviesdatabase.p.rapidapi.com/titles/${titleId}`,
      {
        headers: {
          'X-RapidAPI-Key': process.env.RAPIDAPI_KEY!,
          'X-RapidAPI-Host': 'moviesdatabase.p.rapidapi.com'
        }
      }
    );

    if (!response.ok) {
      const errorData: ApiError = await response.json();
      
      switch (response.status) {
        case 400:
          throw new Error(`Bad Request: ${errorData.message}`);
        case 401:
          throw new Error('Unauthorized: Check your API key');
        case 403:
          throw new Error('Forbidden: API quota exceeded or insufficient permissions');
        case 404:
          throw new Error(`Title not found: ${titleId}`);
        case 429:
          throw new Error('Rate limit exceeded. Please wait before retrying.');
        case 500:
          throw new Error('Server error. Please try again later.');
        default:
          throw new Error(`API Error: ${errorData.message}`);
      }
    }

    return await response.json();
  } catch (error) {
    console.error('Error fetching movie data:', error);
    throw error;
  }
}
```

### Best Practices for Error Handling

1. **Implement Retry Logic:** For 429 and 5xx errors, implement exponential backoff
2. **Graceful Degradation:** Show cached data or fallback content when API fails
3. **User-Friendly Messages:** Convert technical errors to user-understandable messages
4. **Logging:** Log errors for debugging and monitoring purposes
5. **Timeout Handling:** Set appropriate timeouts to prevent hanging requests

## Usage Limits and Best Practices

### Rate Limits by Subscription Tier

**Free Tier:**
- 500 requests per month
- 10 requests per minute
- Best for: Testing and small personal projects

**Basic Tier:**
- 10,000 requests per month
- 20 requests per minute
- Best for: Small applications and prototypes

**Pro Tier:**
- 50,000 requests per month
- 50 requests per minute
- Best for: Production applications

**Ultra Tier:**
- 500,000 requests per month
- 100 requests per minute
- Best for: High-traffic applications

### Best Practices

#### 1. Implement Caching
```typescript
// Cache responses to minimize API calls
const cache = new Map<string, { data: any; timestamp: number }>();
const CACHE_DURATION = 3600000; // 1 hour

async function getCachedMovie(titleId: string) {
  const cached = cache.get(titleId);
  
  if (cached && Date.now() - cached.timestamp < CACHE_DURATION) {
    return cached.data;
  }
  
  const data = await fetchMovieData(titleId);
  cache.set(titleId, { data, timestamp: Date.now() });
  return data;
}
```

#### 2. Use Pagination Efficiently
- Request only the data you need with appropriate `limit` parameters
- Don't fetch all pages at once; implement lazy loading

#### 3. Optimize Query Parameters
- Use specific filters to reduce response size
- Request only necessary fields if the API supports field selection

#### 4. Implement Request Throttling
- I

#### 5. Handle Errors Gracefully
- Implement retry logic with exponential backoff
- Provide fallback content when API is unavailable

#### 6. Monitor Your Usage
- Track API calls in your application
- Set up alerts for approaching rate limits
- Review usage statistics in RapidAPI dashboard

#### 7. Optimize for Production
- Use environment variables for API keys
- Implement server-side proxies to hide API keys
- Consider using a CDN for frequently accessed data
- Batch requests when possible

#### 8. Data Management
- Store frequently accessed data in your database
- Update cached data periodically rather than on every request
- Implement background jobs for bulk data synchronization

### Performance Tips

1. **Compress Responses:** Enable GZIP compression if supported
2. **Parallel Requests:** Use `Promise.all()` for independent requests
3. **Request Prioritization:** Fetch critical data first
4. **Lazy Loading:** Load data only when needed
5. **Connection Pooling:** Reuse HTTP connections when possible

### Cost Optimization

- Start with the Free tier for development
- Upgrade only when necessary
- Monitor usage patterns to choose the right tier
- Implement efficient caching to reduce API calls
- Use webhooks (if available) instead of polling

---

## Getting Started

To start using the MoviesDatabase API in your project:

1. Clone this repository
2. Install dependencies: `npm install` or `yarn install`
3. Create a `.env` file and add your RapidAPI key
4. Review the documentation above
5. Start building your application!

## Support and Resources

- **API Documentation:** [RapidAPI MoviesDatabase](https://rapidapi.com/SAdrian/api/moviesdatabase)
- **RapidAPI Support:** [RapidAPI Help Center](https://docs.rapidapi.com/)
- **Community:** Join RapidAPI forums for discussions and support

---

**License:** This project documentation is provided for educational purposes. The MoviesDatabase API is subject to RapidAPI's terms of service.    