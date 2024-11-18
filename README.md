1. Set Up the Project
First, set up the React project using Create React App to streamline the development process.
npx create-react-app movie-search-app
cd movie-search-app
npm start
This will create a basic React project structure and run the development server.
2. Install Necessary Packages
We’ll need the following:
axios to fetch data from the OMDB API.
react-modal to display the movie details in a modal.
Install them via npm:
npm install axios react-modal
3. Create the Main Components
We'll need a few main components:

App – The root component that holds everything.
SearchBar – A component for the search input.
MovieList – A component to display the list of movies.
MovieModal – A modal to display movie details when clicked.
4. Fetching Movie Data
You can use the OMDB API to fetch movie data. You'll need an API key for it, which can be obtained by registering on their website. For example, your URL for fetching movies might look like:
http://www.omdbapi.com/?apikey=YOUR_API_KEY&s=movieTitle
5. Building the Components
Let’s start with the basic structure of the app.
App Component (App.js)
The App.js will manage the state and coordinate the different components
import React, { useState, useEffect } from "react";
import axios from "axios";
import SearchBar from "./components/SearchBar";
import MovieList from "./components/MovieList";
import MovieModal from "./components/MovieModal";
import Modal from "react-modal";

const App = () => {
  const [query, setQuery] = useState(""); // Search query
  const [movies, setMovies] = useState([]); // Movies list
  const [selectedMovie, setSelectedMovie] = useState(null); // Selected movie for modal
  const [isModalOpen, setIsModalOpen] = useState(false); // Modal visibility

  useEffect(() => {
    // Fetch popular movies when the app loads
    fetchMovies("popular");
  }, []);

  const fetchMovies = async (query) => {
    const apiKey = "YOUR_API_KEY";
    const url = `http://www.omdbapi.com/?apikey=${apiKey}&s=${query}`;

    try {
      const response = await axios.get(url);
      setMovies(response.data.Search || []);
    } catch (error) {
      console.error("Error fetching movies:", error);
    }
  };

  const handleSearch = (e) => {
    e.preventDefault();
    fetchMovies(query);
  };

  const openModal = async (movieId) => {
    const apiKey = "YOUR_API_KEY";
    const url = `http://www.omdbapi.com/?apikey=${apiKey}&i=${movieId}`;

    try {
      const response = await axios.get(url);
      setSelectedMovie(response.data);
      setIsModalOpen(true);
    } catch (error) {
      console.error("Error fetching movie details:", error);
    }
  };

  const closeModal = () => {
    setIsModalOpen(false);
    setSelectedMovie(null);
  };

  return (
    <div className="app">
      <h1>Movie Search App</h1>
      <SearchBar query={query} setQuery={setQuery} onSearch={handleSearch} />
      <MovieList movies={movies} onMovieClick={openModal} />

      {selectedMovie && (
        <Modal isOpen={isModalOpen} onRequestClose={closeModal}>
          <MovieModal movie={selectedMovie} />
        </Modal>
      )}
    </div>
  );
};
export default App;
MovieList Component (MovieList.js)
The MovieList component will render the list of movies fetched from the API
import React from "react";

const MovieList = ({ movies, onMovieClick }) => {
  return (
    <div className="movie-list">
      {movies.length === 0 ? (
        <p>No movies found</p>
      ) : (
        movies.map((movie) => (
          <div key={movie.imdbID} className="movie-item" onClick={() => onMovieClick(movie.imdbID)}>
            <img src={movie.Poster} alt={movie.Title} />
            <h3>{movie.Title}</h3>
            <p>{movie.Year}</p>
          </div>
        ))
      )}
    </div>
  );
};
export default MovieList;
MovieModal Component (MovieModal.js)
The MovieModal component will display the additional details of the selected movie.
import React from "react";

const MovieModal = ({ movie }) => {
  return (
    <div className="movie-modal">
      <h2>{movie.Title}</h2>
      <p><strong>Genre:</strong> {movie.Genre}</p>
      <p><strong>Plot:</strong> {movie.Plot}</p>
      <p><strong>Rating:</strong> {movie.imdbRating}</p>
      <img src={movie.Poster} alt={movie.Title} />
    </div>
  );
};
export default MovieModal;
6. CSS Styling (App.css)
You can add basic CSS to style the app.
.app {
  font-family: Arial, sans-serif;
  text-align: center;
}

.movie-list {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

.movie-item {
  margin: 10px;
  cursor: pointer;
  text-align: center;
}

.movie-item img {
  max-width: 200px;
}

.movie-modal {
  padding: 20px;
  text-align: center;
}

.movie-modal img {
  max-width: 300px;
}
7. Handling Modal Accessibility
To make sure the modal works properly, ensure react-modal is correctly initialized in your app. You can do this in the index.js file:
import Modal from "react-modal";

Modal.setAppElement("#root");
8. Testing and Running Locally
Once the app is ready, test it locally by running:
npm start



