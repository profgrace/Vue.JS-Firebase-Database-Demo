<template>
  <div id="app" class="container">
    <div class="page-header">
      <h1>Book Database App - Using Firebase</h1>
    </div>

    <div class="panel panel-default">
      <div class="panel-heading">
        <h3>Add New Book</h3>
      </div>
      <div class="panel-body">
        <form id="form" class="form-inline" @submit.prevent="addBook">
          <div class="form-group">
            <label for="bookTitle">Title:</label>
            <input type="text" id="bookTitle" class="form-control" v-model="newBook.title">
          </div>
          <div class="form-group">
            <label for="bookAuthor">Author:</label>
            <input type="text" id="bookAuthor" class="form-control" v-model="newBook.author">
          </div>
          <div class="form-group">
            <label for="bookUrl">URL:</label>
            <input type="text" id="bookUrl" class="form-control" v-model="newBook.url">
          </div>
          <input type="submit" class="btn btn-primary" value="Add Book">
        </form>
      </div>
    </div>

    <div class="panel panel-default">
      <div class="panel-heading">
        <h3>Book Lists</h3>
      </div>
      <div class="panel-body">
        <table class="table table-striped">
          <thead>
            <tr>
              <th>Title</th>
              <th>Author</th>
              <th>Delete</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="book in books" :key="book.title">
              <td>
                <a v-bind:href="book.url" target="_blank">{{book.title}}</a>
              </td>
              <td>{{book.author}}</td>
              <td>
                <span @click="removeBook(book)"><i class="fas fa-trash-alt"></i></span>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
    <router-view/>
  </div>
</template>

<script>
import Firebase from "firebase";
import Toastr from "toastr";

let config = {
  apiKey: "AIzaSyDwl8v4nLfJZC5h4Cja-o4OGZqFO9R_J2Y",
  authDomain: "vuejs-firebase-demo-97699.firebaseapp.com",
  databaseURL: "https://vuejs-firebase-demo-97699.firebaseio.com",
  projectId: "vuejs-firebase-demo-97699",
  storageBucket: "vuejs-firebase-demo-97699.appspot.com",
  messagingSenderId: "600367553731"
};

let app = Firebase.initializeApp(config);
let db = app.database();

let booksRef = db.ref("books");

// ******** Confirmation Dialog Stuff **********
let message = "Are you sure?";
let options = {
  html: false, // set to true if your message contains HTML tags. eg: "Delete <b>Foo</b> ?"
  reverse: false, // switch the button positions (left to right, and vise versa)
  okText: "Continue",
  cancelText: "Cancel",
  backdropClose: true // set to true to close the dialog when clicking outside of the dialog window, i.e. click landing on the mask
};

export default {
  name: "App",
  firebase: {
    books: booksRef
  },
  data() {
    return {
      newBook: {
        title: "",
        author: "",
        url: ""
      }
    };
  },
  methods: {
    addBook: function() {
      booksRef.push(this.newBook);
      this.newBook.title = "";
      this.newBook.author = "";
      this.newBook.url = "";
      Toastr.success("Great! New Book has been added.");
    },
    removeBook: function(book) {
      this.$dialog
        .confirm(message, options)
        .then(function() {
          booksRef.child(book[".key"]).remove();
          Toastr.success("Book has been removed.");
        })
        .catch(function() {
          Toastr.success("Whoops! Let's be careful next time.");
        });
    }
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
td span {
  cursor: pointer;
}
</style>
