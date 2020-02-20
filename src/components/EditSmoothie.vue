<template>
  <div v-if="smoothie"
       class="edit-smoothie container">
    <h2>Edit {{smoothie.title}} Smoothie</h2>
    <form novalidate
          @submit.prevent="updateSmoothie">
      <div class="field title">
        <label for="title">Smoothie Title:</label>
        <input id="title"
               type="text"
               name="title"
               v-model="smoothie.title">
      </div>
      <div v-for="(ingredient, index) in smoothie.ingredients"
           :key="index"
           class="field">
        <label for="ingredient">Ingredient {{index + 1}}:</label>
        <input type="text"
               name="ingredient"
               id="ingredient"
               v-model="smoothie.ingredients[index]">
        <i class="material-icons delete"
           @click="deleteIngredient(ingredient)">delete</i>
      </div>
      <div class="field add-ingredient">
        <label for="add-ingredient">Add an ingredient (tab to add):</label>
        <input id="add-ingredient"
               name="add-ingredient"
               type="text"
               @keydown.tab.prevent="addIngredient"
               v-model="newIngredient">
      </div>
      <div class="field center-align">
        <p v-if="feedback"
           class="red-text">{{feedback}}</p>
        <button class="btn pink">Update Smoothie</button>
      </div>
    </form>
  </div>
</template>

<script>
  import db from '@/firebase/init';
  import slugify from 'slugify';

  export default {
    name: 'EditSmoothie',
    data() {
      return {
        smoothie: null,
        newIngredient: null,
        feedback: null
      }
    },
    created() {
      let ref = db.collection('smoothies')
        .where('slug', '==', this.$route.params.slug);
      ref.get()
        .then(snapshot => snapshot.forEach(doc => {
          this.smoothie = {
            id: doc.id,
            ...doc.data()
          }
        }))
        .catch(error => console.error(error));
    },
    methods: {
      updateSmoothie() {
        if (this.smoothie.title) {
          this.feedback = null;
          db.collection('smoothies')
            .doc(this.smoothie.id)
            .update({
              title: this.smoothie.title,
              ingredients: this.smoothie.ingredients,
              slug: slugify(this.smoothie.title, {
                replacement: '-',
                remove: /[$*_+~.()'"!\-:@]/g,
                lower: true
              })
            })
            .then(() => {
              this.$router.push({
                name: 'Index'
              });
            })
            .catch(error => console.error(error));
        } else {
          this.feedback = 'You must enter a smoothie title';
        }
      },
      addIngredient() {
        if (this.newIngredient) {
          this.smoothie.ingredients.push(this.newIngredient);
          this.feedback = null;
          this.newIngredient = null;
        } else {
          this.feedback = 'You must enter a value to add an ingredient';
        }
      },
      deleteIngredient(ingredient) {
        this.smoothie.ingredients = this.smoothie.ingredients.filter(ing => ing !== ingredient);
      }
    }
  }
</script>

<style>
  .edit-smoothie {
    margin-top: 60px;
    padding: 20px;
    max-width: 500px;
  }

  .edit-smoothie h2 {
    font-size: 2em;
    margin: 20px auto;
  }

  .edit-smoothie .field {
    margin: 20px auto;
    position: relative;
  }

  .edit-smoothie .delete {
    cursor: pointer;
    position: absolute;
    right: 0;
    bottom: 16px;
    color: #aaa;
    font-size: 1.4em;
  }
</style>
