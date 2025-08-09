# Movie Finder App

A React web application that allows users to search and discover movies using The Movie Database (TMDB) API, enhanced with search tracking and trending movies features powered by Appwrite database.


---
<img width="1919" height="906" alt="image" src="https://github.com/user-attachments/assets/0856693d-5f32-464c-b2fb-57588c102d24" />
<img width="1919" height="974" alt="image" src="https://github.com/user-attachments/assets/7e619820-7e87-4bdb-b9e7-c40424cf6c6e" />
---

## Features

- Search movies by keywords with debounced input to reduce unnecessary API calls
- Browse popular movies by default
- View trending movies based on search counts tracked in Appwrite
- Display movie cards with poster images
- Loading spinner and error messages for better UX
- Store and update search count data in Appwrite database

---

## Technology Stack

- **Frontend:** React, React Hooks (`useState`, `useEffect`), Tailwind CSS (optional, based on classNames)
- **API:** TMDB REST API
- **Backend:** Appwrite for document database and serverless functions
- **Utilities:** `react-use` for debouncing input

---

## Environment Variables

Create a `.env` file in your project root and add:

```env
VITE_TMDB_API_KEY=your_tmdb_api_key
VITE_APPWRITE_ENDPOINT=your_appwrite_endpoint
VITE_APPWRITE_PROJECT_ID=your_appwrite_project_id
VITE_APPWRITE_DATABASE_ID=your_appwrite_database_id
VITE_APPWRITE_COLLECTION_ID=your_appwrite_collection_id
```

## Installation & Setup
1. Clone the repo
2. Install dependencies
```
npm install
```
Run development server
```

npm run dev
```

## How It Works
- The app fetches popular or searched movies from TMDB using your API key.
- Input is debounced (500ms delay) to prevent excessive API requests during typing.
- Each successful search updates a search count record in Appwrite database.
- Trending movies are determined by the highest search counts and displayed at the top.
- Movie cards show movie posters and titles for easy browsing.

## Key Code Snippets
- Fetch Movies with Debounced Search
```javascript
const debouncedSearchTerm = useDebounce(searchTerm, 500);

useEffect(() => {
  fetchMovies(debouncedSearchTerm);
}, [debouncedSearchTerm]);
```

- Update Search Count in Appwrite
```javascript
export const updateSearchCount = async (searchTerm, movie) => {
  const result = await database.listDocuments(DATABASE_ID, COLLECTION_ID, {
    queries: [Query.equal('searchTerm', searchTerm)],
  });

  if (result.documents.length > 0) {
    const doc = result.documents[0];
    await database.updateDocument(DATABASE_ID, COLLECTION_ID, doc.$id, {
      count: doc.count + 1,
    });
  } else {
    await database.createDocument(DATABASE_ID, COLLECTION_ID, ID.unique(), {
      searchTerm,
      count: 1,
      movie_id: movie.id,
      poster_url: `https://image.tmdb.org/t/p/w500${movie.poster_path}`,
    });
  }
};
```
## Future Enhancements
- Add user authentication with Appwrite for personalized watchlists
- Implement pagination or infinite scroll for movie lists
- Include detailed movie pages with ratings, trailers, and reviews
- Improve UI/UX design and responsiveness
