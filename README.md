## Introduction
We will continue from hw2, the previous website. 

## Goals
This task's main goals are: 
1. Adding users to our website. Only logged in users can create new notes, but guests can view without adding/editing/deleting notes. Follow https://fullstackopen.com/en/part4/. For simplicity, the name, username, password, and email cannot be changed after user creation, and we don't support user deletion.
2. End to end testing: you have to submit at least 5 Playwright tests, and this is an opportunity to practice test driven design (see tip at the bottom of this file).
3. Minimize front end waiting time:
    1. Next.Js static side generation (https://nextjs.org/docs/pages/building-your-application/rendering/static-site-generation): the page HTML is generated when you run next build.
    2. React prefetch: react will get pages in the background, and make them ready in a local cache.


## Submission
1. Submission is in pairs, but starting alone is better for practice.
2. Coding: 70%, Questions: 30%.
3. Your submitted git repo should be *private*, please make barashd@post.bgu.ac.il and nitzanlevy (github username) collaborators.
4. Deadline: Official deadline is the 19.7.24, but because of the exam period, we'll give an automatic extension to whoever needs it for exam preparation.
5. Additionally, solve the [theoretical questions](https://forms.gle/PVFGhibZzrb44N4n6).
6. The ex3 forum is open for questions in Moodle.
7. Git repository content:
    1. Aim for a minimal repository size that can be cloned and installed:  most of the files in github should be code and package dependencies (package.json).
    2. Don't submit node_modules dir, .next project dir, JSON creation script, .env file, or JSON files.
8. If a specific use case is not described here, you can code it as you see fit.
9. As before, You will submit HW3 will be submitted via a Github repository, using a tag called a3. [git tagging](https://git-scm.com/book/en/v2/Git-Basics-Tagging)


## AI
Recommendation about using an AI assistant: You can ask questions and read the answers but never copy them. Understand the details, but write the code yourself. If two people copy the same AI code, it will be considered plagiarism.

## Plagiarism
1. We use a plagiarism detector.
2. The person who copies and the person who was copied from are both responsible. 
3. Set your repository private, and don't share your code.

## Prerequisites
### Recommended reading:
1. Read about user administration and authentication at https://fullstackopen.com/en/part4/.
2. Read about bcrypt format: https://en.wikipedia.org/wiki/Bcrypt.
3. Read about JSON Web Token: https://jwt.io/introduction.
4. Read about testing using Playwright at: https://fullstackopen.com/en/part5/end_to_end_testing_playwright#playwright

### Suggestions:
1. Many times, errors are lost between try/catch blocks, frontend/backend communication, async functions do not show to main screen.
    1. breakpoint don't work in some code parts, such as useeffect.
    2. use the backend middleware logger, console logs, and possibly more loggers, to see into what's happening. It is sometimes printed to the vs.code console, and sometimes to Chrome's console.
2. Like before, progress in small steps, commit when the code is stable with meaningful commit messages. Yyou can choose to commit onle some files that have changed.
3. Implementation steps:
    1. Move the frontent to a next.js structure.
    2. Add Playwright testing library.(tests are under the tests library, see the example Playwright adds to your project)
    3. Write a single test: for example, log in a user/ verify that a logged out user can't add new notes, this test should fail.
    4. Add the nessescary code/schemas/mongo data to pass this test.
    5. add another test that fails, 



## Mongo
1. Like before, the destination Mongodb will always contain at least one note.

```
{
 id: number;
 title: string;
 author: {
 name: string;
 email: string;
 } | null;
 content: string;
};
```
2. Add a collection name called `users` with schema:
```
{
  name: string,
  email: string,
  username: string,
  passwordHash: string
}
```

We need to store the password hashes, we'll use mongodb to be aligned with fullstackopen examples. (https://fullstackopen.com/en/part4/.)

## Supporting next.js folder structure
In order for next.js "build" to work correctly, the folder structure should be updated. (https://nextjs.org/docs/getting-started/project-structure)
Notice: this is only the frontend. The backend which uses express stays the same. (It could be added into the next.js project, but here we use it externally.)

One option:
```
src
├── components
├── consts.tsx
├── contexts
├── css
├── pages 
│   └── index.tsx
└── utils
```
index.tsx is a "page" in next.js terms, and contains the top most compononet called App: don't put other compononets under pages directory, move them to components instead.
To check that it works in dev mode, use `npm run dev`.
To check that it works in test mode, use `npm run build` (checks structure, typescript syntax, and prepares SSR), and then `npm run start`. We won't test for typescript syntax errors in `npm run build`.

## Static server generation (SSG)
Read: https://nextjs.org/docs/pages/building-your-application/data-fetching/get-static-props
Update App component to get props during build time:
```
const App = ({ prop1,prop2... }) => {}
```

 It gets props at build time:

```
export async function getStaticProps() { 

  return {
    props: {
      prop1,prop2...
    }
  };
}
```

### behaviour
1. Each note's `Edit`/`Delete` buttons, are shown only if a user is logged in, and his name is the same as the name of the note's author: assumed unique.
2. `add new note` is only shown if a user is logged in, and the appropriate user fields are used in the new note. 

### pre render, caching
1. The static generation will pre-render the first page.
2. In react, implement the following caching mechanism:
    1. We'll have 5 (=pagination size) pre-fetched pages.
    2. The pages we will store are the pages which are visible in the current page pagination bar (for example, current page is 5, and the cached pages are 3,4,5,6,7).
    3. When moving to a new page:
        1. If the current page is in the cache, it will be loaded from there.
        2. in the background, using async function, we will update the cache as needed: for example, when moving from page 5 to 6, only one page should be fetched.
            1. It's ok to have 5 fetches of 10 notes each, instead of one fetch of 50 notes.

## Playwright requirements- hw3
1. Submit 5 Playwright e2e tests under the "test" folder (parallel to "src" folder). They should pass with `npx playwright test`.
https://nextjs.org/docs/pages/building-your-application/testing/playwright

## Frontend test requirements- hw3
### naming
1. Create User form: The homepage will contain a create user form with html name attribute **"create_user_form"**:
    1. field text: `Name`,  html name attribute **"create_user_form_name"**.
    2. field text: `Email`,  html name attribute **"create_user_form_email"**. Email doesn't need to be verified.
    3. field text: `Username`,  html name attribute **"create_user_form_username"**.
    4. field text: `Password`,  html name attribute **"create_user_form_password"**.
    5. button text: `Create User`,  html name attribute **"create_user_form_create_user"**.
    6. button text: `Cancel`,  html name attribute **"create_user_form_cancel"**.
2. Login form: The homepage will contain a login form with html name attribute **"login_form"**:
    1. field text: `Username`,  html name attribute **"login_form_username"**.
    2. field text: `Password`,  html name attribute **"login_form_password"**.
    3. Like in Full Stack Open, part 4, the accepted token will be saved in a react state, and sent as a 'Authorization' header on following api requests.
        1. comment: it's considered less secure to have the javascript access the token. It's good enough for this exercise.
3. Logout button: The homepage will contain a logout button with html name attribute **"logout"**. The logout will delete the token from the state.
    1. Comment: it should also be added to a black list on the server, so it won't be used later. In this exercise we won't use blacklist. 
4. Add new note for a logged in user, should have the correct name and the email in the note.


## Backend test requirements- hw3
Like in part4, implement:
1. Post request to '/users', to create a new user with the required fields from mongo (assuming username and name is unique, you don't need to verify).
2. Post request to '/login', to login using a username and a password, return a signed token, uses a SECRET env. variable from `.env`. returns `{token,name,email}`.

## Implementation - backend error handling - hw3:
Read and use the following error codes: [HTTP response status codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
In addition to hw2 codes (see "backend error handling - reminder from hw2"):
1. 401: Unauthorized: user is unknown, and needs to authenticate to get a response, or incorrect credentials.
2. 403: Forbidden: user is known, and does not have access rights to the content.


### The tester will:
1. `git clone <your_submitted_github_repo>`
2. `cd <cloned_dir>`
3. `git checkout a3`
4. `npm install` from the `frontend` dir (package.json should exist)
5. `npm run build && npm run start` from the `frontend` dir  (configured to default port 3000). The build/start enable static server side generation.
6. Copy a `.env` file into the `backend` and 'frontend' dir.
7. `npm install` from the `backend` dir (package.json should exist)
8. `node index.js` from the `backend` dir (configured to default port 3001)
9. run your tests: `npx playwright test`, check they all pass.
10. Run tests based on 'test requirements hw3'.


### Tips - Test Driven Development 
1. This exercise is an opportunity to practice test driven design.
2. The three laws of TDD: (Taken from "Clean Code"/ Robert C. Martin, Chapter 9, unit tests)
    1. You may not write exercise code until you have written a failing test.
    2. You may not write more of a test than is sufﬁcient to fail.
    3. You may not write more exercise code than is sufﬁcient to pass the currently failing test.     

3. How to write a single test? Use the "given-when-then" convention:
    1. given that a user is in the database, and a correct auth request is made, then success is expected.
    2. given an existing note id, a delete request is sent, then the post should no longer be in the database.

## Good luck!

## reminder from previous excercises:

## Implementation - backend error handling - reminder from hw2:
1. Unknown route/note number: 404
2. Generic error response, cannot delete/update note: 500
4. Missing fields in the request: 400.
5. Success codes:
    1. Saved note: 201.
    2. Deleted note: 204.
    3. Other: 200.

### Front End test requirements- reminder from hw1
1. Each note should be of [class name](https://www.w3schools.com/html/html_classes.asp) **"note"**. (And not post)
2. A note must get the unique ID from the database and use it as the [html id attribute](https://www.w3schools.com/html/html_id.asp).
3. Pagination buttons:
    1. Navigation buttons should be with [html name attribute](https://www.w3schools.com/tags/att_name.asp) **"first"**, **"previous"**, **"next"**, **"last"**.
    2. Page buttons should be with the [html name attribute](https://www.w3schools.com/tags/att_name.asp) **"page-<target_page_number>"**

### Front End test requirements- reminder from hw2
1. Each note should have an `Edit`/`Delete` button.
2. Edit button: we will only test the body of the edited note, (content field).
    1. name attribute: **"edit-<note_id>"**  [html name attributes](https://www.w3schools.com/tags/att_name.asp). For example, "edit-1".
    2. When clicked, a text input should be rendered:
        1. with the same input as the note.
        2. It should be editable
        3. with name **"text_input-<note_id>"**
        4. with "save" button **"text_input_save-<note_id>"**
        5. with "cancel" button **"text_input_cancel-<note_id>"**

3. Delete button:
    1. name **"delete-<note_id>"** [html name attributes](https://www.w3schools.com/tags/att_name.asp). For example, "delete-1".

4. `Add new note` button: we will only test the body of the new note, (content field).
    1. button name **"add_new_note"**. [html name attributes](https://www.w3schools.com/tags/att_name.asp).
    2. When clicked, React should render a text input:
        1. It should be editable
        2. with name **"text_input_new_note"**
        3. with "save" button **"text_input_save_new_note"**
        4. with "cancel" button **"text_input_cancel_new_note"**
5. There should be a global "theme" button that changes between two styles: dark and light.
    1. name attribute: **"change_theme"**.
    2. You're free to implement any style change, as long as it appears visually on the UI.
6. Data transfer: 10 notes at a time, (frontend-backend, backend-atlas, see Mongoose pagination API, similarly to hw2).
