<template>
  <div class="container w-50">
    <div class="row g-3 justify-content-md-center">
      <app-input
        placeholder="Chicken friedrice"
        v-model="newRecipe.recipeTitle"
      >
        <label class="form-label">Recipe Title</label>
      </app-input>
      <app-input
        placeholder="www.imgelink.com/fried-rice.jpg"
        v-model="newRecipe.recipeImage"
      >
        <label class="form-label">Recipe Image</label>
      </app-input>
      <app-text-area v-model="newRecipe.description">
        <label class="form-label">Recipe Description</label>
      </app-text-area>
      <div>
        <div class="col-12">
          <h2>Ingredients</h2>
        </div>
        <div
          class="row g-1 justify-content-md-center"
          style="margin-top: 5px"
          v-for="item in ingredientCount"
          :key="item"
        >
          <app-input
            colStyle="col-8"
            placeholder="2 large eggs"
            v-model="newRecipe.ingredients[item - 1]"
          ></app-input>

          <app-button
            :buttonType="item !== ingredientCount ? 'delete' : 'add'"
            @click="addIngredient(item)"
            >{{ item !== ingredientCount ? "Delete" : "Add" }}</app-button
          >
        </div>
      </div>
      <div>
        <div class="col-12">
          <h2>Directions</h2>
        </div>
        <div
          class="row g-1 justify-content-md-center"
          style="margin-top: 5px"
          v-for="item in directionCount"
          :key="item"
        >
          <app-input
            colStyle="col-8"
            :placeholder="`Step ${item}`"
            v-model="newRecipe.directions[item - 1]"
          ></app-input>
          <app-button
            :buttonType="item !== directionCount ? 'delete' : 'add'"
            @click="addDirection(item)"
            >{{ item !== directionCount ? "Delete" : "Add" }}</app-button
          >
        </div>
      </div>
      <app-button @click="addRecipe">
        {{ recipes ? "Update" : "Submit" }}
      </app-button>
    </div>
  </div>
</template>

<script>
import Input from "../newRecipe/Input.vue";
import Button from "../newRecipe/Button.vue";
import TextArea from "../newRecipe/TextArea.vue";

export default {
  props: {
    recipes: {
      type: Object,
      required: false,
    },
  },
  data() {
    return {
      newRecipe: this.recipes
        ? { ...this.recipes }
        : {
            recipeImage: "",
            recipeTitle: "",
            ingredients: [],
            directions: [],
            description: "",
          },
      ingredientCount: 1,
      directionCount: 1,
    };
  },
  components: {
    "app-input": Input,
    "app-button": Button,
    "app-text-area": TextArea,
  },
  mounted() {
    if (this.recipes) {
      this.ingredientCount = this.recipes.ingredients.length;
      this.directionCount = this.recipes.directions.length;
    }
  },
  methods: {
    addRecipe() {
      if (!this.recipes) {
        console.log("Tambah")
        this.$store.dispatch("addRecipe", this.newRecipe).then(() => {
          this.$router.push("/");
        });
      } else {
        console.log("edit")
        let { id: _, ...newRecipe } = this.newRecipe;
        this.$store
          .dispatch("updateRecipe", { newRecipe, id: this.newRecipe.id })
          .then(() => {
            console.log("yes")
            this.$router.push("/user");
          });
      }
    },
    addIngredient(item) {
      if (item === this.ingredientCount) {
        this.ingredientCount += 1;
      } else {
        this.newRecipe.ingredients.splice(item - 1, 1);
        this.ingredientCount -= 1;
      }
    },
    addDirection(item) {
      if (item === this.directionCount) {
        this.directionCount += 1;
      } else {
        this.newRecipe.directions.splice(item - 1, 1);
        this.directionCount -= 1;
      }
    },
  },
};
</script>