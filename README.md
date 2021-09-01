# In Class Activity (IA01) - New York Times Best Seller List

This in a live coding in class activity. The goal is to learn to create React Components, style web pages, and access a RESTful web service.

You will also be learning how to use git alongside this activity for your future projects and assignments.

## Today's Challenge

You've been asked by the NY Times to design a simple web page to see the current best selling books of the week.

---

**Check point #1 - Developer API key**

You will need to create your own NYTimes developer API key. This is similar to what you will do for the Movie DB API for your assignment!. [Here are step by step instructions on how to setup NY Times Developer API Key](Signup_NYTimes_API.md) to figure out how to create your own API key. You will need to turn on the Books API for this assignment.

Now that you have created your API key, you will create an environment file to store your key. Under the main folder, create a file name `.env.local`
Inside this file, add the following:
```
REACT_APP_NY_TIMES_API_KEY = your_api_key
```
***NB: DO NOT ADD THIS FILE TO YOUR GIT REPOSITORY***

---

**Check point #2 - Install Libraries & Code Clean Up**

Next, let's install the libraries that we will use to build out site. The first is Bootstrap. This is a library that allows us to build responsive websites.

There are 2 ways to use [Bootstrap](https://getbootstrap.com/):
1. Add the CSS and JS files directly to you index.html file. This is how it is most often used in non-React applications. Here you use normal HTML and the CSS classes included.
1. Use the react component version, [installed with npm](https://react-bootstrap.github.io/). This uses JSX.

We will be using the second. This does not stop use from using the CSS classes directly in the tags. To get started, open a terminal window and navigate to your project folder (you can usually open a terminal directly in your IDE). Next using the node package manager we will install the needed libraries:
```
npm i bootstrap react-bootstrap --save
```

After the installation is complete we will update our files.

***index.js***
- Remove the index.css import
- Remove the reportWebVitals import and function call
- Add the Bootstrap CSS import `import 'bootstrap/dist/css/bootstrap.min.css';`

***App.js***
- Remove the logo import
- Remove the contents of the return and replace is with `<> </>`

Youe file should look like the following:
```js
import './App.css'

function App(){

	return (
		<></>
	);
	
}

export default App;
```


***App.css***

Replace all the contents with the following:
```css
.card img{
  height: 10.625rem;
  max-width: 8.25rem;
  padding-top:1em;
}
.header-title{
  font-family: nyt-cheltenham,georgia,'times new roman',times,serif;
}
```

To be able to make calls to the API we will be using a library named Axios. Axios is a lightweight HTTP client based on the `$http` service within Angular.js v1.x and is similar to the native JavaScript [Fetch API](https://www.digitalocean.com/community/tutorials/js-fetch-api).

Axios is promise-based, which gives you the ability to take advantage of JavaScript's `async` and `await` for more readable asynchronous code.

You can also intercept and cancel requests, and there's built-in client-side protection against cross-site request forgery. 

To install Axios, in the terminal run the following command:
```
npm i axios --save
```

---

**Check point #3 - Use git to commit changes**

Great! Before we move forward to updating the app let's ensure that we save the changes we've made so far.

After navigating to where you downloaded the project, do a `git status` to see what changes you've made so far.

You can also do a `git diff` to see the exact line changes of what changes you've done. You should always check to see what changes you've done so you don't accidentally commit something you didn't want to.

Awesome! Let's commit those changes to make sure we don’t lose them! You should always try to commit your changes while you code, as it'll help you keep track of what you've done so far. This becomes especially important as you work on more complex assignments, as oftentimes we introduce bugs while we code, and having a record of working states to go back to is vitale to ensure less hair pulling. 

Check out this [resource provided by GitHub](https://guides.github.com/introduction/git-handbook/) for learning more about git commands.

You can add the file you changed by doing `git add` followed by the filename or you can add all files in the system by doing `git add .`. Now, you can create a commit with a message `git commit -m "<your message here>"`. Your message should be descriptive of the changes that happened.

---

**Check point #4 - Adding elements to Layout**

Now that we have prepared our project files, lets starting creating the layout.

***App.js***

In Bootstrap, pages are usually wrapped in a container, so let's start by adding the Container tag. At the top, import the Container component from react-bootstrap
```
import {Container} from 'react-bootstrap';
```
Then add the Container tag between the empty tags:
```
<Container fluid className="d-flex flex-column m-2"> </Container>
```
We are adding a few Bootstrap styling options.

Bootstrap uses a grid layout that is based on tables (rows and columns). Each row can be divided into as many as 12 columns. If you set a column width to 12, that means you want the column to take up the entire width of the screen.
We will now add a title to the page. The title will be inside the Container, nested inside of a Row and Col (column). Add Row and Col to the import list at the top. 

```jsx
<Row className="my-2">
	<Col md={12}>
		<h1 className="display-5 header-title">The New York Times Best Sellers</h1>
		<hr/>
	</Col>
</Row>
```

Below this title, we would like to display our list of best sellers. To do this we will create our own custom component. Under the `src` folder create a new folder named `components`. In the components folder, create a file named `BestSellerList.js`. 
Add the BestSellerList component to your imports:
```
import BestSellerList from "./components/BestSellerList";
```
Below the Row tag, add our BestSellerList
```
<BestSellerList />
```

Inside your BestSellerList.js add the following:
```js
import React, {Component} from 'react';


class BestSellerList extends Component {
render() {
        return (
            <>
				Best Seller List goes here.
            </>
        );
    }
}

export default BestSellerList;
```

Now let's run our code and see where we are. *(Don't forget to make commits to your repository)*

![No Content NY Time Best Seller List](/img/bsl_no_content.PNG)

---

**Check point #5 - Create Best Seller List**

Now let's look at creating our first component. React State is built-in option for managing data that will change within a component. When application state changes, the UI is rerendered to reflect the change. Think of live news feed where new news items automatically appears as you are viewing it. State can be expressed in React components with a single JavaScript object. The state object is scoped to the component and uses the `this` keyword. The state object is initialized as part of the class constructor.

We are going to use a state variable to save the list of books. So let's start by creating our constructor and adding the state value.

```js
constructor(props){
	super(props);
	this.state = { books: [] };
}
```

Now we have somewhere to store the books. Next we need to use the API to retrieve the list of books. But when do we want it to be called? Because there is latency is many different areas of JavaScript, we must explore the Component Lifecycle of React. The component lifecycle consists of methods that are invoked in series when a component is mounted or updated. These methods are invoked either before or after the component renders the UI. The `render` methods is in fact part of the lifecycle. For this example we will use `componentDidMount`. It is a good place to initialize third-party JavaScript that requires a DOM such as our API call. So let's go ahead and add that method to our class.

To be able to make our API call we will use the Axios library we installed earlier. Add the import statement to top of the page.

Now we are ready to make our API call. Let's define a variable to holds the URL and API Key.

```js
const BEST_SELLER_URL = `https://api.nytimes.com/svc/books/v3/lists/current/hardcover-fiction.json?api-key=${process.env.REACT_APP_NY_TIMES_API_KEY}`;
```

Next, we want to make an asynchronous call to the API.

```js
const getBooks = async () => {

	await axios.get(BEST_SELLER_URL)
		.then(response =>{
			this.setState({books: response.data.results.books});
		}).catch((err) =>{
			console.log("Fetch Error: " + err)
		});
}
getBooks();
```

Let's update our output to show the names of the books. We will use the higher-order function, map, to iterate over the books. Replace the placeholder text with the following code:
```jsx
{
	this.state.books.map((book) => {
		return <p>{book.title}</p>
	})
}
```

Now let's run our code and see where we are. *(Don't forget to make commits to your repository)*

![Title List NY Time Best Seller List](/img/bsl_title_list.PNG)

---

**Check point #6 - Create Book Layout**

Now that we have access to the book data, let's create a layout to display the book image, title, rank, author, and description. We will also add an Amazon buy button.

As we know everything in Bootstrap is orgranized in rows and columns, so lets import those into our component. Let's wrap our output in a row. Replace the empty tags with a Row tag.

The add vantage of Bootstrap is it's responsive behaviour, the ability to adjust it's layout based on size of the screen. We are going to take advantage of this and create several columns per row to display the books. We will also use the Card component. Add this to your import list. Copy the template into your code replacing the current return of the paragragh tag.

```jsx
<Col xxl={2} lg={3} md={4} sm={6} className="d-flex align-items-stretch">
	<Card className="m-2">
		<Card.Img variant="top" style={{width:"50%"}} src="" alt="" className="mx-auto" />
		<Card.Body>
			<Card.Title></Card.Title>
			<Card.Subtitle className="text-muted"></Card.Subtitle>
			<Card.Text></Card.Text>
		</Card.Body>
		<a target="_blank" rel="noreferrer" href="#" className="btn btn-secondary">BUY ON AMAZON</a>
	</Card>
</Col>
```

Next lets add the data values to the template. We'll start with the book image. The link to the iamge is stored in the property book_image. Remove the quotation marks and replace it with the variable in curly braces.

```jsx
<Card.Img variant="top" style={{width:"50%"}} src={book["book_image"]} alt="" className="mx-auto" />
```

Update the rest of the layout:
- Add the alt text of the image as the title of book. 
- Add the book title to the Card.Title.
- Add the rank to next to the title in parenthesis 
- Add the author to the Card.Subtitle
- Add the description to the Card.Text
- Add the amazon_product_url to anchor tag href

You may additional styling to make the title bolded and the author italicized.

Now let's run our code and see where we are. *(Don't forget to make commits to your repository)*

![NY Time Best Seller List](/img/bsl_no_badge.gif)


You may also update your layout and make your rank into Badge. Add Badge to your bootstrap imports and replace the rank with the following code:

```jsx
<Badge className="position-absolute top-0 start-0 rounded-pill translate-middle bg-primary">{book.rank}</Badge>
```

Also let's add more detail the image alt text. Set the alt text to show the name of the book and the contributor. Declare a variable and use it in the template.
```js
const altText = book.title + " " + book.contributor;
```

---

**Check point #7 - Make Book Layout it's own component**

Imagine you weren't only going to load the best seller list but other books lists as well. Each time you created a new list component, you would need to duplicate the layout you just created. However, components allow us to reuse code in different places, so let's create a component for our book layout so it can be used in different places.

In the components folder add a new file `Book.js`. Given that this component is used to display data that we already have and will not change, we can create a stateless functional component.

***Book.js***

```js
import React from 'react';

function Book() {
	return (
		<>
		</>
	);
}

export default Book;
```

Next cut and paste the template code we just created in App.js and place it in the return. You are going to note that we are using `book` which doesn't exist in this component. We will pass this value as a property to the component. In the parenthesis following the function name add `{book}`. Be sure to also move over your bootstrap imports.

Great! Now we just need to use our Book component inside our BestSellerList component. Where we removed the template code we will add the Book tag. To pass the information you will add a tag attribute named book and assign it the value book (don't forget the curly braces). When using map with a component, you also need to pass a key value that uniquely identifies each element in the list. We will use the primary_isbn10.
```jsx
<Book book={book} key={book.primary_isbn10} />
``` 

Now let's run our code and see where we are. *(Don't forget to make commits to your repository)*

![Complete NY Time Best Seller List](/img/bsl_complete.gif)

--- 

**Bonus Check point: Add all buy options**

The NY Times API includes several options for the user to purchase the book. Update the layout to be a dropdown with all the link options.

## What Your Files Should Look Like

[Solution](Solution.md)

## License
[License](LICENSE)
