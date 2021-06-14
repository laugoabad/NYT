<template>
  <div class="container">
    <div class="card">
      <div class="heading">
        <h4 class="title">NYTimes Books</h4>
      </div>
      <div class="content">
        <h5>Please Select a Category</h5>
        <!-- When a category is selected the option is sent to list all the books in that category and unhides the title input-->
        <select
          class="custom-select"
          @change="changeCategory($event); hideInput=true; searchQuery=null"
        >
          <!-- Lists the 10 categories found ON MOUNTED  -->
          <option disabled selected>Categories</option>
          <option v-for="(category, index) in categories" :key="index">{{ category.list_name }}</option>
        </select>
        <!-- input of the title to search -->
        <div class="search" v-if="hideInput">
          <input class="form-control" v-model="searchQuery" type="text" placeholder="Enter Title" />
        </div>
        <!-- starts listing all the books of the category and then filters as the user inputs the words-->
        <ul class="list">
          <li v-for="book of resultQuery" :key="book.rank" >
            <a :href="'https://www.google.com/search?q='+book.title+book.author" target="_blank" rel="noopener noreferrer">
              <h6 class="title">{{book.title}}</h6>
              <p class="description">{{book.description}}</p>
              <p class="author">{{book.author}}</p>
            </a>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "Books",

  data() {
    return {
      categories: null,
      opt: null,
      books: null,
      searchQuery: null,
      hideInput: false
    };
  },

  //SEARCHS THE FIRST 10 CATEGORYS
  async mounted() {
    const response = await axios.get(
      "https://api.nytimes.com/svc/books/v3/lists/names.json?api-key=wMrIxYjKdpTQq76wy7ngPAG1OD0VJy8j"
    );
    this.categories = response.data.results.slice(0, 10);
  },

  //FILTERS THE SEARCH BY TITLE AND RETURNS resultQuery as this a computed property
  computed: {
    resultQuery() {
      if (this.searchQuery) {
        return this.books.filter(item => {
          return this.searchQuery
            .toLowerCase()
            .split(" ")
            .every(v => item.title.toLowerCase().includes(v));
        });
      } else {
        return this.books;
      }
    }
  },

  //QUERY THE LIST OF BOOKS THAT BELONGS TO THAT CATEGORY
  methods: {
    changeCategory(event) {
      this.opt = event.target.value;
      axios
        .get(
          "https://api.nytimes.com/svc/books/v3/lists/current/" +
            this.opt +
            ".json?api-key=wMrIxYjKdpTQq76wy7ngPAG1OD0VJy8j"
        )
        .then(response => (this.books = response.data.results.books));
    }
  }
};
</script>

<style scoped lang="scss">
.container {
  z-index: 1;
  margin: 36px auto;
  max-width: 826px;
  background-color: white;
}

.card {
  box-shadow: 0 2px 3px rgba(10, 10, 10, 0.1), 0 0 0 1px rgba(10, 10, 10, 0.1);
  padding: 24px;
}


.list {
  > li {
    &:not(:last-child) {
      margin-bottom: 18px;
    }
    > a {
      color: #0a5b8c;
      display: block;
      margin-bottom: 6px;
      text-decoration: none;
    }
    > span {
      color: rgba(#3b4242, 0.7);
      font-size: 12px;
    }
    .title {
      font-size: 16px;
      font-weight: 500;
      margin-bottom: 10px;
      font-style: italic;
    }

    .description {
      font-size: 14px;
      margin-bottom: 10px;
    }
    .author {
      font-size: 16px;
      font-weight: 600;
    }
  }

}

.btn {
  color: #fff;
  cursor: pointer;
  background-color: #117a8b;
  border: 1px solid transparent;
  padding: 6px 12px;
  border-radius: 6px;
  transition: all 0.1s ease-in;
  &:hover {
    background-color: #138496;
    border-color: #117a8b;
  }
}

.heading {
  margin-bottom: 30px;

  .title {
    font-size: 18px;
    font-weight: 600;
  }
}
.search {
  margin: 20px 0px 40px;
  input.form-control {
    font-size: 18px;
    border: none;
    border-bottom: 2px solid #ced4da;
    outline: 0;
    padding: 6px 0;
    width: 100%;
  }
  ::placeholder {
    font-size: 18px;
    font-weight: 600;
    color: orange;
  }
}

.custom-select {
  position: relative;
  font-family: Arial;
  font-weight: 600;
  padding: 8px;
  background-color: orange;
  margin: 14px 0px;
}

.custom-select select {
  display: none;
}
</style>
