# App.js

```jsx
import {Container, Row, Col} from 'react-bootstrap'
import BestSellerList from "./components/BestSellerList";
import './App.css';

function App() {
  return (
      <>
          <Container fluid className="d-flex flex-column m-2">
              <Row className="my-2">
                  <Col md={12}>
                      <h1 className="display-5  header-title">The New York Times Best Sellers</h1>
                      <hr/>
                  </Col>
              </Row>
              <BestSellerList />
          </Container>

      </>
  );
}

export default App;

```

# BestSellerList.js

```jsx
import React, {Component} from 'react';
import {Row} from 'react-bootstrap'
import axios from "axios";
import Book from "./Book";

class BestSellerList extends Component {
    constructor(props) {
        super(props);
        this.state = {
            books: []
        }
    }
    componentDidMount() {
        const BEST_SELLER_URL = `https://api.nytimes.com/svc/books/v3/lists/current/hardcover-fiction.json?api-key=${process.env.REACT_APP_NY_TIMES_API_KEY}`;

        const getBooks = async () => {

            await axios.get(BEST_SELLER_URL)
                .then(response =>{
                    this.setState({books: response.data.results.books});
                }).catch((err) =>{
                    console.log("Fetch Error: " + err)
                });
        }
        getBooks();
    }
    render() {
        return (
            <Row>
                {
                    this.state.books.map(book => <Book book={book} key={book.primary_isbn10} /> )
                }
            </Row>
        );
    }
}

export default BestSellerList;
```


# Book.js

```jsx
import React from 'react';
import {Card, Col, Badge} from 'react-bootstrap'

function Book({book}) {

    const altText = book.title + " " + book.contributor;

    return (
        <Col xxl={2} lg={3} md={4} sm={6} className="d-flex align-items-stretch">
            <Card className="m-2">
                <Card.Img variant="top" style={{width:"50%"}} src={book["book_image"]} alt={altText} className="mx-auto" />
                <Card.Body>
                    <Card.Title><strong>{book.title}</strong> <Badge className="position-absolute top-0 start-0 rounded-pill translate-middle bg-primary">{book.rank}</Badge></Card.Title>
                    <Card.Subtitle className="text-muted"><i>{book.author}</i></Card.Subtitle>
                    <Card.Text>{book.description}</Card.Text>
                    <a target="_blank" rel="noreferrer" href={book["amazon_product_url"]} className="btn btn-secondary">BUY ON AMAZON</a>
                </Card.Body>
            </Card>
        </Col>

    );
}

export default Book;
```
