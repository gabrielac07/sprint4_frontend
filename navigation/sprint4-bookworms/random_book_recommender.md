---
layout: page 
title: Book Roulette
permalink: /random_book_recommender/
---
<style>
    .container {
        max-width: 600px;
        margin: 50px auto;
        padding: 20px;
        background-color: #E8C5A4;
        border-radius: 8px;
    }

    h1 {
        background: #a57e5a/*#6e8a60*/;
        padding: 50px;
        font-size: 2em;
        text-align: center;
    }

    h2 {
        margin: 20px;
        font-size: 1.5em:
    }

    /*input {
        padding: 15px;
    }*/

    ul {
        list-style-position: inside;
        gap: 16px;
        font-size: 18px;
        color: #E5E7EB;
    }

    select, button {
        padding: 10px 15px;
        font-size: 25px;
        margin: 10px 5px;
        border: 1px solid;
        color: white;
        background-color: #a57e5a;
        /*border-color: white;*/
        border-radius: 4px;
        cursor: pointer;
    }

    input, textarea, {
        width: 100%;
        padding: 10px;
        margin-bottom: 20px;
        border: 1px solid #ccc;
        border-radius: 4px;
        font-size: 16px;
    }

    select:focus, button:hover {
        background-color: #500A0A/*#72db8e*/;
        transition: 0.3s;   
    }

    .book_details {
        margin-top: 20px;
        text-align: center;
        display: flex;
        align-items: center;
    }

    .book_cover {
        max-width: 200px;
        margin: 10px auto;
        display: flex;
        align-items: center;
        border-width: 8px;
        border-color: #eda579;
    }

    .description {
        font-size: 0.9em;
        margin: 10px 0;
    }

    .start_over {
        margin-top: 20px;
        /*background-color:*/
        color: white;
        border: none;
        padding: 10px 15px;
        cursor: pointer;
        border-radius: 4px;
    }

    .start_over:hover {
        /*background-color:*/
    }
</style>
<html>
    <div class="container">
        <h1>Welcome to your Random Book Recommender!</h1>
        <div id="genre_selection">
            <h2>What genre do you want to read?</h2>
            <select id="genre">
                <option value="nonfiction">Nonfiction</option>
                <option value="historical_fiction">Historical Fiction</option>
                <option value="suspense_thriller">Suspense/Thriller</option>
                <option value="fantasy">Fantasy</option>
                <option value="romance">Romance</option>
                <option value="dystopian">Dystopian</option>
                <option value="classic">Classics</option>
                <option value="mystery">Mystery</option>
            </select>
            <button onclick="getRandomBook()">Get a Book!</button>
        </div>
        <div id="book_display" class="book_details" style="display: none;">
            <div class="book_cover"><img id="book_cover" alt="Book Cover"></div>
            <h2 id="book_title"></h2>
            <h3 id="book_author"></h3>
            <p id="book_description" class="description"></p>
            <button class="start_over" onclick="startOver()">Get a Different Book</button>
        </div>
        <!--This section is the display for adding a bookrec-->
        <div id="add_bookrec">
            <button onclick="inputBookRec()">Add a Book Recommendation!</button>
        </div>
        <div id="input_bookrec" class="bookrec_table" style="display: none;">
            <form id="bookRecForm">
                <p><label>
                    Book Title:
                    <input type="text" name="title" id="title" required>
                </label></p>
                <p><label>
                    Author:
                    <input type="text" name="author" id="author" required>
                </label></p>
                <p><label>
                    Genre:
                    <!--<input type="text" name="genre" id="genre" required>-->
                    <select name="genre" id="genre" required>
                        <option value="nonfiction">Nonfiction</option>
                        <option value="historical_fiction">Historical Fiction</option>
                        <option value="suspense_thriller">Suspense/Thriller</option>
                        <option value="fantasy">Fantasy</option>
                        <option value="romance">Romance</option>
                        <option value="dystopian">Dystopian</option>
                        <option value="classic">Classics</option>
                        <option value="mystery">Mystery</option>
                    </select>
                </label></p>
                <p><label>
                    Description:
                    <textarea type="text" name="description" rows="5" id="description" required></textarea>
                </label></p>
                <p><label>
                    Book Cover Image URL:
                    <input type="url" name="cover_url" id="cover_url" required>
                </label></p>
                <p>
                    <button type="button" onclick="addBookRec()">Done</button>
                    <button type="button" onclick="deleteBookRec()">Delete</button>
                </p>
            </form>
        </div>
    </div>
<script>
    //The genreMap object maps the dropdown values (nonfiction, historical_fiction, etc.) to terms recognized by the bookdb API (e.g., "Nonfiction", "Suspense/Thriller").
    const genreMap = {
        nonfiction: "Nonfiction",
        historical_fiction: "Historical Fiction",
        suspense_thriller: "Suspense/Thriller",
        fantasy: "Fantasy",
        romance: "Romance",
        dystopian: "Dystopian",
        classic: "Classics",
        mystery: "Mystery"
    }
    //
    function getRandomBook() {
    //Get the selected genre from the dropdown
    //const genre = document.getElementById("genre").value;
    const genreKey = document.getElementById("genre").value;
    const query = genreMap[genreKey] || "fiction"; // Fallback to "fiction" if genre not mapped
    //
    //Build the API URL with the selected genre as a query parameter
    //const apiUrl = `${pythonURI}/api/random_book?genre=${encodeURIComponent(query)}`;
    const apiUrl = `http://127.0.0.1:8504/api/random_bookrec?genre=${encodeURIComponent(query)}`;
    //Fetch data from the backend API
    fetch(apiUrl) // Flask server endpoint
        .then((response) => {
                if (!response.ok) {
                    throw new Error('No books found for the selected genre.');
                }
                return response.json();
        })
        .then((book) => {
            displayBook(book); // Display the book details on the page
        })
        .catch((error) => { // Catch -> handles any error during execution
            console.error("Error fetching data:", error);
            alert("An error occurred while fetching the book. Please try again.");
        });
    }
    function displayBook(book) {
        const { title, author, description, image_cover } = book;
        // Update the DOM (Document Object Model) with book details
        document.getElementById("book_title").innerText = title;
        document.getElementById("book_author").innerText = `By: ${author}`;
        document.getElementById("book_description").innerText = description;
        // Book cover display
        document.getElementById("book_cover").src = image_cover;
        document.getElementById("book_cover").style.display = image_cover ? "block" : "none";      
        // Hide the genre selection and show the book details
        document.getElementById("genre_selection").style.display = "none";
        document.getElementById("book_display").style.display = "block";
    }
    function startOver() {
        // Reset to the initial view
        document.getElementById("genre_selection").style.display = "block";
        document.getElementById("book_display").style.display = "none";
    }
    //
    let lastAddedBookId = null; // Store the ID of the last added book
    // Section for displaying form to add a book rec
    function inputBookRec() {
        document.getElementById("input_bookrec").style.display = "block";
        document.getElementById("add_bookrec").style.display = "none";
    }
    //
    // Section for adding book recs
    function addBookRec() {
        const title = document.getElementById('title').value;
        const author = document.getElementById('author').value;
        const genre = document.getElementById('genre').value;
        const description = document.getElementById('description').value;
        const coverUrl = document.getElementById('cover_url').value;
    //
        const bookRec = { 
            title: title,
            author: author,
            genre: genre,
            description: description,
            cover_url: coverUrl 
        };
    //
        fetch('http://127.0.0.1:8504/api/add_bookrec', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify(bookRec)
        })
        .then(response => response.json())
        .then(data => {
            if (data.success) {
                alert('Book recommendation added successfully!');
                lastAddedBookId = data.id; // Store the ID of the added book
            } else {
                alert('Failed to add book recommendation.'); //This is suppsoed to be "Failed to add book recommendation"
            }
        })
        .catch(error => {
            console.error('Error:', error);
            alert('An error occurred while adding the book recommendation.');
        });
    }
    // Section for deleting book recs
    function deleteBookRec() {
        if (lastAddedBookId === null) {
            alert('No book recommendation to delete.');
            return;
        }
        //
        fetch(`http://127.0.0.1:8504/api/delete_bookrec/${lastAddedBookId}`, {
            method: 'DELETE'
        })
        .then(response => response.json())
        .then(data => {
            if (data.message === 'Book deleted successfully') {
                alert('Book recommendation deleted successfully!');
                clearForm();
            } else {
                alert('Failed to delete book recommendation.');
            }
        })
        .catch(error => {
            console.error('Error:', error);
            alert('An error occurred while deleting the book recommendation.');
        });
    }
    // Function to clear the form
    function clearForm() {
        document.getElementById('title').value = '';
        document.getElementById('author').value = '';
        document.getElementById('genre').value = '';
        document.getElementById('description').value = '';
        document.getElementById('cover_url').value = '';
        document.getElementById("input_bookrec").style.display = "none";
        document.getElementById("add_bookrec").style.display = "block";
        lastAddedBookId = null;
    }
</script>
</html>