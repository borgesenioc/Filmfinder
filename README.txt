
# Film Finder

Film Finder is a simple web application that allows users to discover random movies based on selected genres. The project utilizes the TMDB (The Movie Database) API to fetch movie data.

## Table of Contents

- [Installation](#installation)
- [Usage](#usage)
- [Files](#files)
- [API Key](#api-key)
- [License](#license)

## Installation

1. **Clone the repository**:
   ```sh
   git clone https://github.com/yourusername/film-finder.git
   cd film-finder
   ```

2. **Open the project directory**:
   Open the project directory in your preferred code editor.

## Usage

1. **Open the `index.html` file**:
   Open `index.html` in your web browser. This will load the Film Finder application.

2. **Select a genre**:
   Use the dropdown menu to select a genre.

3. **Fetch a random movie**:
   Click the "Let's Play!" button to fetch and display a random movie from the selected genre.

4. **Like or dislike a movie**:
   Use the like or dislike buttons to fetch another random movie.

## Files

- `index.html`: The main HTML file that structures the Film Finder web page.
- `style.css`: The CSS file that styles the Film Finder web page.
- `script.js`: The main JavaScript file that handles API requests and application logic.
- `helpers.js`: A JavaScript file containing helper functions used by `script.js`.

## API Key

The project uses the TMDB (The Movie Database) API to fetch movie data. To use the application, you need a valid TMDB API key.

1. **Get a TMDB API Key**:
   - Sign up at [The Movie Database](https://www.themoviedb.org/) if you don't have an account.
   - Go to your account settings and navigate to the API section to generate an API key.

2. **Set the API Key**:
   - Open `script.js`.
   - Replace the placeholder API key with your own:
     ```javascript
     const tmdbKey = 'YOUR_API_KEY';
     ```

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

## Detailed Explanation of Files

### `index.html`
This is the main HTML file for the Film Finder application. It includes references to the styles and scripts used in the project.

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>Film Finder</title>
  <link rel="stylesheet" href="style.css">
  <script defer src="helpers.js"></script>
  <script defer src="script.js"></script>
</head>
<body>
  <header>
    <h1 id="appTitle">🍿Film Finder🍿</h1>
  </header>
  <form id="genreForm">
    <label>Choose a genre:</label>
    <select name="genres" id="genres"></select>
  </form>
  <button id="playBtn">Let's Play!</button>
  <div id="movieInfo">
    <div id="moviePoster"></div>
    <div id="movieText"></div>
  </div>
  <div id="likeOrDislikeBtns" hidden>
    <button id="likeBtn"><i class="fa-solid fa-thumbs-up"></i></button>
    <button id="dislikeBtn"><i class="fa-solid fa-thumbs-down"></i></button>
  </div>
</body>
</html>
```

### `style.css`
This file contains the CSS styles for the Film Finder application.

### `script.js`
This file contains the main JavaScript code for fetching genres and movies from the TMDB API and displaying them on the page.

```javascript
const tmdbKey = '861323a96197f76151c253f6c744a6c0';
const tmdbBaseUrl = 'https://api.themoviedb.org/3';
const playBtn = document.getElementById('playBtn');

const getGenres = async () => {
  const genreRequestEndpoint = 'genre/movie/list';
  const requestParams = `?api_key=${tmdbKey}`;
  const urlToFetch = `${tmdbBaseUrl}/${genreRequestEndpoint}${requestParams}`;

  try {
    const response = await fetch(urlToFetch);
    if (response.ok) {
      const jsonResponse = await response.json();
      console.log(jsonResponse);
      const genres = jsonResponse.genres;
      return genres;
    }
  } catch (error) {
    console.error(error);
  }
};

const getMovies = async () => {
  const selectedGenre = getSelectedGenre();
  const discoverMovieEndpoint = 'discover/movie';
  const requestParams = `?api_key=${tmdbKey}&with_genres=${selectedGenre}`;
  const urlToFetch = `${tmdbBaseUrl}/${discoverMovieEndpoint}${requestParams}`;

  try {
    const response = await fetch(urlToFetch);
    if (response.ok) {
      const jsonResponse = await response.json();
      const movies = jsonResponse.results;
      return movies;
    }
  } catch (error) {
    console.error(error);
  }
};

const getMovieInfo = async (movie) => {
  const movieId = movie.id;
  const movieEndpoint = `movie/${movieId}`;
  const requestParams = `?api_key=${tmdbKey}`;
  const urlToFetch = `${tmdbBaseUrl}/${movieEndpoint}${requestParams}`;

  try {
    const response = await fetch(urlToFetch);
    if (response.ok) {
      const movieInfo = await response.json();
      return movieInfo;
    }
  } catch (error) {
    console.error(error);
  }
};

const showRandomMovie = async () => {
  const movieInfo = document.getElementById('movieInfo');
  if (movieInfo.childNodes.length > 0) {
    clearCurrentMovie();
  }
  const movies = await getMovies();
  const randomMovie = getRandomMovie(movies);
  const info = await getMovieInfo(randomMovie);
  displayMovie(info);
};

getGenres().then(populateGenreDropdown);
playBtn.onclick = showRandomMovie;
```

### `helpers.js`
This file contains helper functions used in the `script.js` file.

```javascript
const populateGenreDropdown = (genres) => {
  const select = document.getElementById('genres');
  for (const genre of genres) {
    let option = document.createElement('option');
    option.value = genre.id;
    option.text = genre.name;
    select.appendChild(option);
  }
};

const getSelectedGenre = () => {
  const selectedGenre = document.getElementById('genres').value;
  return selectedGenre;
};

const showBtns = () => {
  const btnDiv = document.getElementById('likeOrDislikeBtns');
  btnDiv.removeAttribute('hidden');
};

const clearCurrentMovie = () => {
  const moviePosterDiv = document.getElementById('moviePoster');
  const movieTextDiv = document.getElementById('movieText');
  moviePosterDiv.innerHTML = '';
  movieTextDiv.innerHTML = '';
};

const likeMovie = () => {
  clearCurrentMovie();
  showRandomMovie();
};

const dislikeMovie = () => {
  clearCurrentMovie();
  showRandomMovie();
};

const createMoviePoster = (posterPath) => {
  const moviePosterUrl = `https://image.tmdb.org/t/p/original/${posterPath}`;
  const posterImg = document.createElement('img');
  posterImg.setAttribute('src', moviePosterUrl);
  posterImg.setAttribute('id', 'moviePoster');
  return posterImg;
};

const createMovieTitle = (title) => {
  const titleHeader = document.createElement('h1');
  titleHeader.setAttribute('id', 'movieTitle');
  titleHeader.innerHTML = title;
  return titleHeader;
};

const createMovieOverview = (overview) => {
  const overviewParagraph = document.createElement('p');
  overviewParagraph.setAttribute('id', 'movieOverview');
  overviewParagraph.innerHTML = overview;
  return overviewParagraph;
};

const getRandomMovie = (movies) => {
  const randomIndex = Math.floor(Math.random() * movies.length);
  const randomMovie = movies[randomIndex];
  return randomMovie;
};

const displayMovie = (movieInfo) => {
  const moviePosterDiv = document.getElementById('moviePoster');
  const movieTextDiv = document.getElementById('movieText');
  const likeBtn = document.getElementById('likeBtn');
  const dislikeBtn = document.getElementById('dislikeBtn');
  const moviePoster = createMoviePoster(movieInfo.poster_path);
  const titleHeader = createMovieTitle(movieInfo.title);
  const overviewText = createMovieOverview(movieInfo.overview);
  moviePosterDiv.appendChild(moviePoster);
  movieTextDiv.appendChild(titleHeader);
  movieTextDiv.appendChild(overviewText);
  showBtns();
  likeBtn.onclick = likeMovie;
  dislikeBtn.onclick = dislikeMovie;
};
```

This README file provides users with the necessary information to set up and use the Film Finder application. Make sure to replace any placeholder text, such as the GitHub repository URL, with actual values relevant to your project.
