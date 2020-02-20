<template>
  <div class="index container">
    <div class="row">
      <div class="col s4"
           v-for="smoothie in smoothies"
           :key="smoothie.id">
        <div class="card">
          <div class="card-content">
            <i class="material-icons delete"
               @click="deleteSmoothie(smoothie.id)">delete</i>
            <h2 class="indigo-text">{{smoothie.title}}</h2>
            <ul class="ingredients">
              <li v-for="(ingredient, index) in smoothie.ingredients"
                  :key="index">
            <span class="chip">
              {{ingredient}}
            </span>
              </li>
            </ul>
          </div>
          <span class="btn-floating btn-large halfway-fab pink">
          <router-link :to="{name: 'EditSmoothie', params: {slug: smoothie.slug}}">
            <i class="material-icons edit">edit</i>
          </router-link>
        </span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import db from '@/firebase/init';

  export default {
    name: 'Index',
    data() {
      return {
        smoothies: []
      }
    },
    methods: {
      deleteSmoothie(id) {
        db.collection('smoothies')
          .doc(id)
          .delete()
          .then(() =>
            this.smoothies = this.smoothies.filter(smoothie => smoothie.id !== id))
          .catch(error => console.error(error));
      }
    },
    created() {
      db.collection('smoothies')
        .get()
        .then(snapshot => snapshot.forEach(document => {
          this.smoothies.push({
            id: document.id,
            ...document.data()
          })
        }));
    }
  }
</script>

<style>
  .index {
    margin-top: 60px;
  }

  .index .card {
    margin: 20px 0;
  }

  .index h2 {
    font-size: 1.8em;
    text-align: center;
    margin-top: 0;
  }

  .index .ingredients {
    margin: 30px auto;
  }

  .index .ingredients li {
    display: inline-block;
  }

  .index .delete {
    position: absolute;
    top: 4px;
    right: 4px;
    cursor: pointer;
    color: #aaa;
    font-size: 1.4em;
  }
</style>
