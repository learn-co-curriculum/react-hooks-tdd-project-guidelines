# React Test-Driven Development Project Guidelines

## Introduction

Now that you've learned some React fundamentals as well as how to write tests
for React applications, it's time for your first project! Your goal for this
project is to **build a React application based on a wireframe and a set of
deliverables, following Test-Driven Development principles.**

## Requirements

Your project should demonstrate your ability to do the following:

- Create a component hierarchy to represent a UI.
- Manage state in a React application.
- Interact with an API from a React application.
- Create client-side routes using React Router and display different components
  based on URL navigation.
- Follow a test-driven development process to writing React code, including unit
  tests for individual components as well as integration tests for component
  hierarchies.

A rubric is provided at the bottom of this document that outlines the criteria
by which your assignment will be evaluated.

## Project Brief

For this project, you'll be creating a React frontend application that uses the
GitHub API to search and view data about users and their repositories. You must
build features that complete the following **user stories**:

As a user, I can…

- Search for GitHub users by username and see a list of users that match
- See details about a user including a paginated list of their repositories (see
  below) and profile information:
  - username
  - avatar image
  - HTML URL
  - company
  - location
  - email
  - bio
- View a list of repositories, including:
  - name
  - description
  - HTML URL
- **Bonus** Sort the repositories by created date, updated date, and name
- **Bonus** Paginate the search results, and view the "previous" and "next"
  results by clicking a button
- **Bonus** Add API error handling

### Wireframes

As a rough guide for what the finished project should look like, here are some
wireframes:

![Wireframes](https://curriculum-content.s3.amazonaws.com/phase-2/react-tdd-project/react-tdd-project-wireframes.png)

- [Original wireframes](https://excalidraw.com/#json=X3xeHnsI3Nzk9mATEfvfV,zJtbepwBNSdeLwYhRzSNQg)

Styling isn't the focus of this project, and you're free to change the look and
feel as you like, so long as all the user stories are represented in your
application.

You should use these wireframes to determine what **routes** your application
will need, and to design your **component hierarchy** following the process from
[Thinking in React][thinking].

## Setup

You'll be coding out this project from scratch using [Create React
App][create-react-app] to build your application and provide the necessary
dependencies.

You can generate a new project like so:

```console
$ npx create-react-app github-search-project --use-npm
```

### Setup: React Router

React Router doesn't come installed with Create React App, so you'll need to
install it. Our curriculum covers [React Router v5][react router 5], which we
recommend using:

```console
$ npm install react-router-dom@5
```

> Note: make sure to include @5 at the end of the install command to install
> React Router version 5 instead of version 6. If you're curious to try out
> version 6, you can view information on the differences between v5 and v6 in
> [the docs][react router 6].

### Setup: MSW for Tests

Create React App comes with [Jest][jest] and [React Testing Library][rtl]
pre-configured, so you don't need to do any additional setup to start writing
tests for React components.

You will need to install [Mock Service Worker][msw] in order to mock API
requests for testing:

```console
$ npm install msw
```

Follow [this guide](https://mswjs.io/docs/getting-started/mocks/rest-api) for
configuring mocks for a REST API.

### Setup: GitHub Repository

In order to submit your project, you'll need to create a new GitHub repository
for your code following the guide below. Since you'll be using the existing code
from your local repository, **don't** check any of the boxes next to "Add a
README", "Add a .gitignore", or "Choose a license".

- [Create a repo](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-new-repository)

Once your repository is created, follow the instructions to push an existing
repository from the command line:

```console
$ git remote add origin git@github.com:your-name/your-repo-name.git
$ git branch -M main
$ git push -u origin main
```

It's recommended that you create a GitHub repository for your project and push
up your code often.

Once your work is done, you'll submit a link to your GitHub repository in Canvas
for review.

## GitHub API

This project involves using the GitHub API to access data. The [GitHub API
docs][gh api] are an awesome resource for everything you'll need to build out
the user stories, but they can also be overwhelming to get started. Here's a
quick example of what you'll need to do to interact with the GitHub API.

One of the features you'll be building out is the ability to **search for
users** using the GitHub API. The documentation for this feature of the GitHub
API can be found here:

- https://docs.github.com/en/rest/reference/search#search-users

Take a look at the API documentation. What URL do we use to make the search? How
do we tell the GitHub API what user we're looking for?

The base URL for all API requests is `https://api.github.com`, and the endpoint
for searching users is `/search/users`. To provide a search term, we use a query
parameter `q`. All together, to search a user whose username includes "octo",
we'd make a request to the following URL:

- https://api.github.com/search/users?q=octo

In order to perform this search from your application, you'll need to make a GET
request from a React component, parse the response data, and use that data for
your component's state. Something like this:

```jsx
// search term is a dynamic prop
function SearchResults({ term }) {
  // state to hold the search results
  const [users, setUsers] = useState([]);

  useEffect(() => {
    // initiate a request when the component mounts or the term changes
    fetch(`https://api.github.com/search/users?q=${term}`)
      .then((r) => r.json())
      .then((result) => setUsers(result.items));
  }, [term]);

  // render the results as JSX
}
```

### Endpoints

Here are all the important endpoints you'll need in order to build out the
remaining user stories:

- [`/search/users`](https://docs.github.com/en/rest/reference/search#search-users):
  search users using their GitHub username
- [`/users/{username}`](https://docs.github.com/en/rest/reference/users#get-a-user):
  get a user's profile information
- [`/users/{username}/repositories`](https://docs.github.com/en/rest/reference/repos#list-repositories-for-a-user):
  list repositories for a user

### API Limitations

The GitHub API is free to use, and works without needing any authentication
(such as an API key). However, unauthenticated clients are limited to **60
requests per hour**. It's pretty easy to go over that limit as you're building
your application, so you may want to create an access token that you can use
with the API:

- https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token

**Important**: make sure not to include your API key in your project's source
code! To keep your API key safe, create a `.env.development.local` file and
store your key there while you are developing the application. The `.local`
environment files are intended not to be checked into source control, and are
included in the `.gitignore` file that comes with Create React App. Refer to
[this guide][create-react-app env] for information on working with API keys in
Create React App.

Note that even keys that aren't included in your source control may still be
included in the client-side code when you publish your project.
[This guide](https://www.freecodecamp.org/news/how-to-access-secret-api-keys-using-netlify-functions-in-a-react-app/)
has information on using Netlify Functions to hide API keys.

## Rubric

Your assignment will be evaluated on a 1-4 scale for each of the following
criteria:

- **Create a component hierarchy to represent a UI**

1. Did not manage to render everything required by the user stories.
2. Renders everything that is user stories, but doesn't have features cleanly
   separated into separate components (i.e. no strong separation of concerns
   between components).
3. Renders everything that is asked for in the deliverables. Components are
   abstract/reusable. No unnecessary components were created.
4. Additional components created to complete one or more bonus deliverables.

- **Manage state in a React application**

1. Does not have all the necessary state to achieve the functionality required
   by the user stories.
2. Has all the necessary state required by the user stories, but is not managing
   state following best practices (i.e. state is duplicated between components;
   state is held higher in the component tree than it should be; state not being
   updated following user events).
3. Has all the necessary state required by the user stories. State is not
   duplicated and is held at the appropriate components in the component
   hierarchy. State is successfully updated in response to user events.
4. Uses state successfully to complete one or more bonus deliverables.

- **Interact with an API from a React application**

1. Does not use the API successfully to achieve the functionality required by
   the user stories.
2. Is able to interact with the API successfully, but is not following best
   practices (i.e. incorrect use of `useEffect` and the dependencies array;
   incorrect syntax for `fetch`/network request code; not handling loading state
   when waiting for API response).
3. Uses the API successfully to achieve the functionality required by the user
   stories, following best practices.
4. Uses additional features of the API to successfully complete one or more
   bonus deliverables.

- **Create client-side routes using React Router and display different
  components based on URL navigation**

1. Does not use React Router to achieve the functionality required by the user
   stories.
2. Is able to use React Router, but is missing some components (i.e. not able to
   use a `<Link>` component with a `<Route>` component; not able to use
   `useParams` to get data from URL params).
3. Uses React Router successfully to achieve the functionality required by the
   user stories, and demonstrates the ability to use the `<BrowserRouter>`,
   `<Link>`, `<Route>`, and `<Switch>` components and the `useParams` hook.
4. Uses React Router to successfully complete one or more bonus deliverables.

- **Follow a test-driven development process to writing React code, including
  unit tests for individual components as well as integration tests for
  component hierarchies**.

1. Few or no tests present in the completed application.
2. Has tests present, but tests don't cover all of the functionality required by
   the user stories. Not able to perform advanced tests, such as working with an
   API or testing components that use React Router.
3. Has tests for all the functionality required by the user stories. Tests are
   present for both individual components as well as larger component
   hierarchies. Performs tests that make requests to an API, and tests
   components that use React Router.
4. Has tests coverage for one or more bonus deliverables.

## Resources

- [Create React App][create-react-app]
- [React Router 5][react router 5]
- [React Testing Library][rtl]
- [Jest][jest]
- [Mock Service Worker][msw]
- [GitHub API][gh api]

[thinking]: https://reactjs.org/docs/thinking-in-react.html
[create-react-app]: https://create-react-app.dev/
[create-react-app env]:
  https://create-react-app.dev/docs/adding-custom-environment-variables/
[msw]: https://mswjs.io/
[react router 5]: https://v5.reactrouter.com/
[react router 6]: https://reactrouter.com/docs/en/v6/upgrading/v5
[gh api]:
  https://docs.github.com/en/rest/guides/getting-started-with-the-rest-api
[rtl]: https://testing-library.com/docs/react-testing-library/intro
[jest]: https://jestjs.io/
