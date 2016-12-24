# Express CRUD with File Storage -- Books API

Write an Express app, that uses a file for persistent storage.

## Expectations

* Use ES6 wherever possible (e.g. `let`, `const`, destructuring and fat arrow functions)
* Use the [GitHub Workflow](https://guides.github.com/introduction/flow/)
* Make regular commits
  * Make a commit every time you complete a feature
  * Give each commit a meaningful commit message
  * Don't expect the directions in this README to tell you when to commit
* All file operations must be asynchronous
* Your code must be perfectly indented.  If we can't read it, we aren't going to grade it.
* Work in your project directory at all times
* All access to the data (both in the file and in memory) can only be done through your `data_store` module
  * This stresses the [Single responsibility principle](https://en.wikipedia.org/wiki/Single_responsibility_principle)

## Part 1: Set up your npm/Express project

1. Fork and clone this repo
1. `touch app.js`
1. `npm init -y`
1. `npm install --save express`
1. `echo node_modules > .gitignore`
1. `git add .`
1. `git commit -m "Initial project setup"`

## Part 2: Set up nodemon and your npm scripts

1. `npm install --save-dev nodemon`
1. Open `package.json`
1. Inside `scripts`
  * add ``"watch": "`npm bin`/nodemon"``
  * make sure that you have the trailing commas in the right places!
1. `npm run watch`
1. Leave nodemon running.  Open up a new terminal window/tab, and continue working from this new terminal.

## Part 3: Create an Express server

1. Open `app.js`
1. Require Express
1. Initialize an `app`
1. Determine the port to use
  * The port to use could be passed in as the second command line argument
  * If not provided, default to 8000
  * Store the port in a `const`
1. Use `app.listen` to bind and listen for connections on the above port
1. Check that there are no errors in the terminal where nodemon is running
1. Go to `http://localhost:8000`.  If everything is working, you should see `Cannot GET /`
1. Git add, commit, push

## Part 4: Set up your database file

1. `mkdir db`
  * `db` is short for "database"
1. `cd db`
1. `touch seed.json`
  * this is where our initial data (i.e. "seed data") is stored
  * this file will be used to reset our database file whenever we want to start over
1. open `seed.json` and paste the following inside

    ```js
    [
      {
        "id": 1,
        "author": "Marijn Haverbeke",
        "title": "Eloquent JavaScript"
      },
      {
        "id": 2,
        "author": "Nick Morgan",
        "title": "JavaScript for Kids"
      },
      {
        "id": 3,
        "author": "Kyle Simpson",
        "title": "You Don't Know JS"
      }
    ]
    ```

1. Go back to your project directory
1. Open `package.json`
1. Inside `scripts`
  * add `"reset": "cp db/seed.json db/data.json"`
  * make sure that you have the trailing commas in the right places!
1. `npm run reset`
1. `ls db`
  * notice that there is now a `data.json` in the `db` directory
  * `db/data.json` is the file that we are going to modify
  * if you make changes to `db/data.json` that you don't like and you want to start again,
    `npm run reset` will reset `db/data.json` to be the same as `db/seed.json`
1. `echo db/data.json >> .gitignore`
  * database files are typically not checked into source control, because they
    can get large, have nothing to do with development, and might hold sensitive
    data

## Part 5: Create a module called data_store

1. Create a new file called `data_store.js`
1. Open `data_store.js`
1. Create an empty module in here.  We will fill it in in the following steps.
1. Open `app.js`
1. Require your data_store module

## Part 6: Implement a function that reads the contents of the file into memory

1. Open `data_store.js`
1. Create a global variable, called `global_store`.  Set it to `null`
1. Do NOT export this global variable
  * *Why not?* Single responsibility principle.  Only the `data_store` should handle all access to/from the data.
1. Write a function called `load_from_file` that reads all the contents of the `db/data.json` file into memory
  * *TIP:* "into memory" means save it in a variable (let's use the above-created global variable)
  * File read/write is slow.  So we will work from memory as much as
    possible and only update the file when we have to.
1. Export this function.
1. Open `app.js`
1. Call `load_from_file` before you call `app.listen`

## Part 7: Implement a function that returns all the books

1. Open `data_store.js`
1. Write a function called `get_all_books` that returns an array of all the books that are in memory
1. Export this function.

## Part 8: Implement GET /api/books

1. Define a GET route at /api/books
1. The route should send a json response with an array of all the books
  * use your data store's `get_all_books` function to achieve this
1. If your data store is `null` (and so `get_all_books` returns null), respond with 404 Not Found

## Part 9: Implement finding a book by its ID

1. Open `data_store.js`
1. Write a function called `get_book_by_id(id)`
  * returns the book with that ID
  * if no book is found, return `undefined`
1. Export this function.

## Part 10: Implement GET /api/books/:id

1. Define a GET route at /api/books/:id
1. The route should send a json response with the book that has the given ID
  * use your data store's `get_book_by_id(id)` function to achieve this
1. If there is no book with the given ID, respond with `404 Not Found`

## Part 11: Implement a function that updates the file with the current information

1. Open `data_store.js`
1. Write a function called `write_to_file` that writes all the books that are in memory, back into the file
  * if there is an error, throw it (i.e. `throw err`)
  * if it is successful, log a success message to the console
1. Make sure that you can read the data back out of the file by using `load_from_file`
1. Do NOT export this function.
  * This function is going to be a "private" or "secret" function that only your module can use
  * The management of the data store should be invisible to all outside libraries that use the module
  * So we restrict access to only the functions that outside libraries need to do their tasks,
    by only exporting functions and variables that we think they need to use

## Part 12: Implement adding a new book (with a unique id) to the data store

1. Open `data_store.js`
1. Create a new global variable called `LAST_ID`
1. When you call `load_from_file`, update `LAST_ID` to be the largest ID that was loaded from the file
1. Write a function called `add_book` that:
  * takes an object as a parameter
  * gives it a unique ID
    * *TIP:* just add 1 to `LAST_ID` and use that
  * adds it to books that are already in memory
  * calls `write_to_file` to update the file
  * returns the added book (with its unique ID)
1. Export this function

## Part 13: Implement POST /api/books

1. Install and use `body-parser`
1. Get the body of the request and pass it to `add_book`
1. The route should send a json response with the newly-created book
1. The status of the request should be `201 Created`
1. Check `db/data.json` to be sure that your new book is in there with a unique ID

## Part 14: Implement updating a book in the data store

1. Open `data_store.js`
1. Write a function called `update_book` that:
  * takes an ID as a parameter
  * takes an object as a parameter
  * finds the book with that ID
  * if it is not found, return `undefined`
  * updates that book to have the information in the object
  * do NOT update the ID
  * calls `write_to_file` to update the file
  * returns the updated book
1. Export this function

## Part 15: Implement PUT /api/books/:id

1. Get the body of the request and pass it to `update_book`
1. The route should send a json response with the newly-updated book
1. If there is no book with the given ID, respond with `404 Not Found`
1. Check `db/data.json` to be sure that your book was updated in the file as expected

## Part 16: Implement deleting a book from the data store

1. Open `data_store.js`
1. Write a function called `delete_book` that:
  * takes an ID as a parameter
  * finds the book with that ID
  * if it is not found, return `undefined`
  * removes that book from the global variable
  * calls `write_to_file` to update the file
  * returns the removed book
1. Export this function

## Part 17: Implement DELETE /api/books/:id

1. Define a DELETE route at /api/books/:id
1. The route should send a json response with the book that was deleted
  * use your data store's `delete_book` function to achieve this
1. If there is no book with the given ID, respond with `404 Not Found`
1. Check `db/data.json` to be sure that target book gone from the file

## Submit your work

* Make sure you add, commit and push all your changes to your fork of this repo and pull request

## STRETCH

Implement the data store using a [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)
