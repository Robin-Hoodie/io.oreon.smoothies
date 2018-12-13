<template>
  <div class="add-smoothie container">
    <h2 class="center-align indigo-text">Add new smoothie recipe</h2>
    <form novalidate
          @submit.prevent="addSmoothie">
      <div class="field title">
        <label for="title">Smoothie Title:</label>
        <input id="title"
               type="text"
               name="title"
               v-model="title">
      </div>
      <div v-for="(ingredient, index) in ingredients"
           :key="index"
           class="field">
        <label for="ingredient">Ingredient {{index + 1}}:</label>
        <input type="text"
               name="ingredient"
               id="ingredient"
               v-model="ingredients[index]">
        <i class="material-icons delete"
           @click="deleteIngredient(ingredient)">delete</i>
      </div>
      <div class="field add-ingredient">
        <label for="add-ingredient">Add an ingredient:</label>
        <input id="add-ingredient"
               name="add-ingredient"
               type="text"
               @keydown.tab.prevent="addIngredient"
               v-model="newIngredient">
      </div>
      <div class="field center-align">
        <p v-if="feedback"
           class="red-text">{{feedback}}</p>
        <button class="btn pink">Add Smoothie</button>
      </div>
    </form>
  </div>
</template>

<script>
  import db from '@/firebase/init';
  import slugify from 'slugify';

  export default {
    name: 'AddSmoothie',
    data() {
      return {
        title: null,
        newIngredient: null,
        ingredients: [],
        feedback: null
      }
    },
    methods: {
      addSmoothie() {
        if (this.title) {
          this.feedback = null;
          db.collection('smoothies')
            .add({
              title: this.title,
              ingredients: this.ingredients,
              slug: slugify(this.title, {
                replacement: '-',
                remove: /[$*_+~.()'"!\-:@]/g,
                lower: true
              })
            })
            .then(() => {
              this.$router.push({
                name: 'Index'
              })
            })
            .catch(error => console.error(error));
        } else {
          this.feedback = 'You must enter a smoothie title';
        }
      },
      addIngredient() {
        if (this.newIngredient) {
          this.ingredients.push(this.newIngredient);
          this.newIngredient = null;
          this.feedback = null;
        } else {
          this.feedback = 'You must enter a value to add an ingredient';
        }
      },
      deleteIngredient(ingredient) {
        this.ingredients = this.ingredients.filter(ing => ing !== ingredient);
      }
    }
  }
</script>

<style>
  .add-smoothie {
    margin-top: 60px;
    padding: 20px;
    max-width: 500px;
  }

  .add-smoothie h2 {
    font-size: 2em;
    margin: 20px auto;
  }

  .add-smoothie .field {
    margin: 20px auto;
    position: relative;
  }

  .add-smoothie .delete {
    cursor: pointer;
    position: absolute;
    right: 0;
    bottom: 16px;
    color: #aaa;
    font-size: 1.4em;
  }
</style>
