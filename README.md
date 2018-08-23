# Testing-Mocha-Chai


#### Mocha 

- [Mocha](https://mochajs.org/) is a feature-rich JavaScript test framework running on Node.js and in the browser. It makes asychronous testing easy, and allows us to choose our own assertion library.

- [Chai](http://www.chaijs.com/) - Chai is a BDD / TDD assertion library for node and the browser that can be paired with any javascript testing framework (ie Mocha).


### Dependencies

```
 npm install chai chai-http mocha --save-dev
```

- We want to save our testing dependencies as dev dependencies because this code isn't relavent to our deployed production application
- Check out all the files before you begin and the package.json

#### Why Unit Test

- There are many reasons to unit test, some of the reasons are the following 

1.  It improves the Quality of your code, and allows for more agile development, so when you refactor your code you can do it more safely, because you have tested components of your code. In this way you can verify individual units of code when testing the whole application and make it easier to debug.  

2.  Unit testing helps you design your code, because you have to think about all the relative inputs and outputs of your code

3.  Helps in debugging code, when tests fail, you then realize this test fails because of recent changes to your code

4.  Reduces the Cost of software development, it is much easier to fix bugs early in development then in late stages that then may cause huge refactoring which can be very costly.

- There are many other reasons and you could read further at some of the following articles

1.  [8 benefits of Unit Testing](https://dzone.com/articles/top-8-benefits-of-unit-testing)
2.  [Is Unit Testing Worth It](https://stackoverflow.com/questions/67299/is-unit-testing-worth-the-effort)
3.  [Top Benefits of Unit Testing](https://apiumhub.com/tech-blog-barcelona/top-benefits-of-unit-testing/)



#### Lets get to it 

- We will be writing our tests in the ```test``` folder 
- Notice the initial setup 

```
process.env.NODE_ENV = 'test';


const mongoose = require('mongoose');
const Book     = require('../models/book');


const chai = require('chai');
const chaiHttp = require('chai-http');
const server = require('../app');

// This is the type of assertion we are using
// we could also use assert or expect, its your choice, or your bosses
const should = chai.should();

// allow us to make http requests to our server
chai.use(chaiHttp);

```

- Here at the top of file we are setting our environment to our testing environment which will then use our test database which is defined in the config folder.  

-  We are also requiring our models and mongoose so we can test our database queries. 

-  Then we require chai our assertion library, that will allow us to write our tests and make http requests to our server
-  The environment we are the tests we are running this in is mocha (which allows us to test asychrounous code)

-  The only thing we have to do to set up mocha is make sure we are using it in our package.json to run our tests like the following 


```
  "scripts": {
    "start": "NODE_ENV=dev && nodemon ./bin/www",
    "test": "mocha"
  }
```

- So when we run the command ```npm test``` we will run our tests that are in our test folder.  



### On to the tests 

- First thing we do is write our describe block.  A describe block is a function that describes what we are testing, we can name it anything we want, it should be relevant to what we are doing so we will name it ```Books API```


```
describe('Books API', () => {

  // were emptying the database before each test
  beforeEach((done) => {
    Book.remove({}, (err) => {
      done();
    });
  });


})// end Of Books APi

```


- So this describe block will contain all the tests for our crudable API 
- Before each test we want to empty the database so we know the exact expected results each time 
- and you'll notice that after each test we have to call ```done()```  to let our tests know the test has finished. 

#### The first API test

```

describe('Books API', () => {

  // were emptying the database before each test
  beforeEach((done) => {
    Book.remove({}, (err) => {
      done();
    });
  });

describe('/Get Book', () => {

    it('it should GET all the books', (done) => {
      chai.request(server)
      .get('/book')
      .end((err, res) => {
        res.should.have.status(200);
        res.body.should.be.a('array');
        res.body.length.should.be.eql(0);

        // when your done testing the important things
        done();
      });
    });



  });// end of get

});// end Of Books APi

```

- So here we are using "it" (from mocha, "describe" is as well), to describe what the following test should do.  So once again we can name it anything we want and in our case it should 'it should GET all the books`.

-  Inside the test we are using ```chai``` to make an http request 'get' to our server to retrieve all the books
-  In our response we are using ```chai``` to test the important responses from the server. Then we call ```done()``` when we are finished like always

#### We can do the same for POST requests also 


```
describe('/POST Book', () => {

    it('it should not Post a book without complete Parameters', (done) => {

      // This is the object we are sending to the server
      const book = {
        title: 'Cats Cradle',
        author: 'Kurty',
        year: 1950
      };

      chai.request(server)
      .post('/book')
      .send(book)
      .end((err, res) => {
        res.should.have.status(200);
        res.body.should.be.a('object');
        res.body.should.have.property('errors');
        res.body.errors.should.have.property('pages');
        res.body.errors.pages.should.have.property('kind').eql('required');

        done();
      })
    })// end of fail

  it('it should POST A BOOK', (done) => {

    const book = {
      title: "Cats Cradle",
      author: 'Kurty',
      year: 1950,
      pages: 350
    };

    chai.request(server)
    .post('/book')
    .send(book)
    .end((err, res) => {

      res.should.have.status(200);
      res.body.should.be.a('object');
      res.body.should.have.property('message').eql('Book successfully added');
      res.body.book.should.have.property('title');
      res.body.book.should.have.property('author');
      res.body.book.should.have.property('year');
      res.body.book.should.have.property('pages');

      done();
    })



  })


  })// end of Post Book

```

- Note this is inside the describe('Books API') block 

- Here we are testing two different situations, one where we send an incomplete form request, 
- the second a correct post request

##### You can go ahead and finish the rest of these testing routes 


#### Further Readings / Resources

### Resources
- [Sandi Metz's Magic Tricks of Testing](https://www.youtube.com/watch?v=URSWYvyc42M)
- [Jest docs](https://facebook.github.io/jest/docs/en/getting-started.html) (React Testing)
- [Test the components themselves with Enzyme.js](https://git.generalassemb.ly/ga-wdi-lessons/testing-in-react-with-jest-enzyme) (React Testing)








