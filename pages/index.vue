<template>
  <div class="container">
    <div class="row justify-content-center">
      <div class="col-12">
        <h1>Recipe List</h1>
        <ul class="recipe-list">
          <li v-for="recipe in recipes" :key="recipe.uuid">
            <img
              :src="`http://localhost:3001/${recipe.images.small}`"
              :alt="recipe.title"
            />
            <div class="info">
              <h2>
                <nuxt-link :to="`/recipes/${recipe.uuid}`">
                  {{ recipe.title }}
                </nuxt-link>
              </h2>
              <p>{{ recipe.description }}</p>
              <p>
                Serves {{ recipe.servings }}<br />
                {{ recipe.prepTime + recipe.cookTime }} Minutes
              </p>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      recipes: []
    }
  },
  created() {
    axios.get('http://localhost:3001/recipes').then((response) => {
      this.recipes = response.data
    })
  }
}
</script>

<style>
.recipe-list {
  padding-left: 0;
  list-style-type: none;
}
.recipe-list li {
  margin-bottom: 10px;
  display: flex;
  align-items: center;
}
.recipe-list li img {
  margin-right: 12px;
}
</style>
