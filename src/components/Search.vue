<template>
  <div class="mini-search">
    <ais-instant-search
      :search-client="searchClient"
      index-name="wp_prime_searchable_posts"
      label="Prime"
    >
      <ais-configure :hitsPerPage="10" :restrictSearchableAttributes="['post_title']"/>
      <!--[BEGIN-ADD][Bojana 5/8/2019] Add searchBox -->
      <ais-search-box autofocus placeholder="Search . . ." />
      <!--[BEGIN-ADD] [Bojana 5/8/2019] Add searchBox-->
      <ais-autocomplete :indices="[{ value:'wp_history_posts_post', label: 'History' }]">
        <div slot-scope="{ currentRefinement, indices, refine }">
          <input
            class="form-control input-lg"
            type="search"
            :value="currentRefinement"
            placeholder="Search..."
            @input="refine($event.currentTarget.value)"
            autofocus
            show-loading-indicator
          >
          <div v-if="currentRefinement" class="resultHits" >
            
            
            <ul v-for="(index,x) in indices" :key="x" class="search-results">
              <span v-if="index.hits.length" >
                <p class="resultTitle">
                  Results from {{index.label}}
                </p>
                <hr class="resultTitleHR"> 
 
                <h3 class="subTitle">{{index.label}}</h3>
                <li class="result-items">
                  <ul>
                    <li
                      v-for="(hit,i) in index.hits"
                      :key="i"
                      @click="hitRedirect(hit)"
                      class="result-item row"
                    >
                      <!--
                      TODO
                      This would be a good place to put 'sub-components' for each type of result - e.g. post, faculty, spotlight, etc.
                      -->

                      <div v-if="hit.images.thumbnail && hit.images.thumbnail.url" class="photo" >
                        <img :src="hit.images.thumbnail.url" alt class="profileImg">
                      </div>
                      <div v-if="!hit.images.thumbnail || !hit.images.thumbnail.url" class="photo" >
                        <img src="https://www.deedsalone.com/wp-content/uploads/2019/03/empty-face-athlete.svg" alt class="profileImg empty">
                      </div>                     
                      <div class="content">
                        <h4>
                          <ais-highlight attribute="post_title" :hit="hit"/>
                          <br>
                          <small>{{hit.post_date_formatted}}</small>
                          <br>
                        </h4>
                        <h6>{{hit.post_type_label}}</h6>
                        <p v-html="hit.post_excerpt"></p>
                      </div>
                    </li>
                    <hr class="itemHR">
                  </ul>
                </li>
              </span>
              <span v-if="!index.hits.length">
                  <p class="resultTitle">
                    No Results Found
                  </p>
                  <hr class="resultTitleHR">
                  <div class="content noresult" > 
                    <h4>
                      <small>No results were found for your search. Would you lkie to retry your search across all of CSSH?</small>
                      <br>
                    </h4>
                    <button>
                      Search All of CSSH
                    </button>
                  </div>
                      
              </span>
              
            </ul>
            
              
        
          </div>
        </div>
      </ais-autocomplete>
    </ais-instant-search>
  </div>
</template>

<script>
import algoliasearch from "algoliasearch/lite";
import { VueAutosuggest } from "vue-autosuggest";
export default {
  components: { VueAutosuggest },
  data() {
    return {
      searchClient: algoliasearch(
        "XAJ79GTSZV",
        "f62cf6274acbcdf43e219996a7966f06"
      ),
      query: "",
      FoundResult: "0",
    };
  },
  methods: {
    hitRedirect(selected) {
      if (selected) {
        window.open(selected.permalink);
      }
    },
    indicesToSuggestions(indices) {
      return indices.map(({ hits }) => ({ data: hits }));
    },
    isEmpty: function(obj) {
      for (const key in obj) {
        if (obj.hasOwnProperty(key)) {
          return false;
        }
      }
      return true;
    }
  }
};
</script>
<style lang="scss">
.search-results {
  list-style: none;
  margin: 0;
  padding: 0;
}
.result-items {
  ul {
    list-style: none;
  }
}
.result-item {
  cursor: pointer;
  margin-bottom: 1em;
}
</style>
