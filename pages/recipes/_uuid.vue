<template>
  <div class="container">
    <div class="row">
      <div class="col-12">
        <h1>{{ recipe.title }}</h1>
        <p>{{ recipe.description }}</p>
        <ul class="specs">
          <li>Serves: {{ recipe.servings }}</li>
          <li>Prep Time: {{ recipe.prepTime }}</li>
          <li>Cook Time: {{ recipe.cookTime }}</li>
        </ul>
      </div>
    </div>
    <div class="row">
      <div class="col-8">
        <img
          v-if="recipe.images"
          :src="`http://localhost:3001/${recipe.images.full}`"
          :alt="recipe.title"
          class="img-fluid detail-image"
        />
        <h3>Directions</h3>
        <ol class="directions">
          <li v-for="(direction, index) in recipe.directions" :key="index">
            <em v-if="direction.optional">Optional:</em>
            {{ direction.instructions }}
          </li>
        </ol>
      </div>
      <div class="col-4 ingredient-column">
        <h3>Ingredients</h3>
        <ul class="ingredients">
          <li v-for="ingredient in recipe.ingredients" :key="ingredient.uuid">
            <strong>
              {{ ingredient.amount }} {{ ingredient.measurement }}
            </strong>
            {{ ingredient.name }}
            <div v-if="isOnSpecial(ingredient.uuid)" class="alert alert-light">
              <p>
                <strong>
                  {{ isOnSpecial(ingredient.uuid).title }}
                  <small>
                    {{ isOnSpecial(ingredient.uuid).type }}
                  </small>
                </strong>
              </p>
              <p
                v-if="
                  isOnSpecial(ingredient.uuid).type === 'local' ||
                  isOnSpecial(ingredient.uuid).type === 'event'
                "
              >
                <a
                  :href="`http://maps.google.com/maps?q=${
                    isOnSpecial(ingredient.uuid).geo
                  }`"
                  >{{ isOnSpecial(ingredient.uuid).text }}</a
                >
              </p>
              <p v-else>
                {{ isOnSpecial(ingredient.uuid).text }}
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
      recipe: {},
      specials: [],
      current: {}
    }
  },
  created() {
    axios.get(`http://localhost:3001/recipes/${this.$route.params.uuid}`).then((response) => {
      this.recipe = response.data
    })
    axios.get('http://localhost:3001/specials').then((response) => {
      this.specials = response.data
    })
  },
  methods: {
    isOnSpecial(currentItemId) {
      return this.specials.find((special) => {
        return special.ingredientId === currentItemId
      })
    }
  }
}
</script>

<style>
img.detail-image {
  margin-bottom: 40px;
}
ul.specs {
  list-style-type: none;
  padding-left: 0;
  display: flex;
}
ul.specs li {
  margin-right: 5px;
  padding: 2px 15px;
  background: rgba(100, 100, 100, 0.2);
  border-radius: 50px;
}
div.ingredient-column {
  background: rgba(100, 100, 100, 0.2);
  padding-top: 20px;
  padding-bottom: 20px;
  border-radius: 10px;
}
h3 {
  border-bottom: 1px solid black;
}
ul.ingredients {
  list-style-type: none;
  padding-left: 0;
}
div.alert {
  margin-top: 10px;
}
ol.directions {
  padding-left: 0;
  font-size: 21px;
  list-style-type: none;
  counter-reset: directions-counter;
}
ol.directions li {
  counter-increment: directions-counter;
  margin-bottom: 10px;
  padding-left: 40px;
  text-indent: -40px;
}
ol.directions li::before {
  text-indent: 0;
  text-align: center;
  margin-right: 5px;
  content: counter(directions-counter);
  background-color: rgba(100, 100, 100, 0.4);
  display: inline-block;
  width: 33px;
  height: 33px;
  border-radius: 50px;
  color: white;
}
</style>
