<template>
  <div>
    <Header />
    <b-container class="container">
      <h1 class="title">Family recipes</h1>

      <div v-if="isEmpty === true">
        you don't have family recipes yet
      </div>
      <div v-else>
        <b-row>
          <b-col class="row" v-for="r in family_recipes" :key="r.id">
            <FamilyPreview class="recipePreview" :recipe="r" />
          </b-col>
        </b-row>
      </div>
    </b-container>
  </div>
</template>

<script>
//familyPrev
import FamilyPreview from "../components/FamilyPreview";
import Header from "@/components/Header";

export default {
  components: {
    FamilyPreview,
    Header,
  },
  data() {
    return {
      family_recipes: [],
      isEmpty: false,
    };
  },

  async created() {
    try {
      if (!this.$cookies.get("session")) {
        this.$root.store.logout();
        this.$router.push({ name: "main" });
      } else {
        if (localStorage.family_recipes) {
          // get from local storage if has
          this.family_recipes = JSON.parse(localStorage.family_recipes);
        } else {
          let response;
          response = await this.axios.get(
            "http://localhost:3000/profile/getAllFamilyRecipesSummary",
            //"https://assignment-3-2-avital.herokuapp.com/recipe/search/id/" +
            {
              withCredentials: true,
            }
          );

          var family_dict = response.data;
          this.family_recipes = [];
          for (var recipe_id in family_dict) {
            var currRecipe = family_dict[recipe_id];
            currRecipe.id = recipe_id;
            this.family_recipes.push(currRecipe);
          }
        }
        if (
          !Array.isArray(this.family_recipes) ||
          !this.family_recipes.length
        ) {
          // recipes is empty
          this.isEmpty = true;
        } else {
          this.isEmpty = false;
          localStorage.family_recipes = JSON.stringify(this.family_recipes); // if not empty, save in local storage
        }
      }
    } catch (error) {
      console.log(error);
    }
  },
};
</script>

<style></style>
