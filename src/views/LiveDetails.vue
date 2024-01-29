<template>
  <div class="card mt-5">
    <div
      v-if="fixture"
      class="card-header bg-dark text-white pulse"
      v-text="fixture.name"
    />
    <div v-if="selections.length > 0" class="card-body">
      <div class="card-text">
        <div class="container">
          <div v-for="market in markets" :key="market.id" class="mt-4">
            <h4 v-text="market.name" />
            <div
              v-if="getSelectionsByMarketId(market.id).length < 4"
              class="row mt-2"
            >
              <div
                v-for="selection in getSelectionsByMarketId(market.id)"
                :key="selection.id"
                class="col"
                style="cursor: pointer"
              >
                <div class="card bg-primary text-white h-100">
                  <div class="card-body">
                    <p
                      class="card-text"
                      v-text="`${selection.name} : ${selection.currentOdd}`"
                    />
                  </div>
                </div>
              </div>
            </div>
            <template v-else>
              <div
                v-for="selection in getSelectionsByMarketId(market.id)"
                :key="selection.id"
                class="row mt-2"
              >
                <div class="col" style="cursor: pointer">
                  <div class="card bg-primary text-white h-100">
                    <div class="card-body">
                      <p
                        class="card-text"
                        v-text="`${selection.name} : ${selection.currentOdd}`"
                      />
                    </div>
                  </div>
                </div>
              </div>
            </template>
          </div>
        </div>
      </div>
    </div>
    <div class="card mt-5" v-else>
      <h5>Aucune sélection disponible</h5>
    </div>
    <router-link to="/lives" class="btn btn-light mt-4"
      >Retour aux lives</router-link
    >
  </div>
</template>

<script>
import axios from "@/services/axios.js";

export default {
  name: "liveDetails",
  created() {
    this.fetchSelections();
    this.getFixture();
  },
  data() {
    return {
      selections: [],
      fixture: null,
    };
  },
  methods: {
    fetchSelections() {
      axios.get("/selections.json").then((res) => {
        this.selections = res.data.filter(
          (elt) => elt.market.event.id.toString() === this.$route.params.id
        );
      });
    },
    getFixture() {
      axios.get("/lives.json").then((res) => {
        this.fixture = res.data.find(
          (elt) => elt.id.toString() === this.$route.params.id
        );
      });
    },
    getSelectionsByMarketId(marketId) {
      const selections = this.selections.filter(
        (elt) => elt.market.id === marketId
      );

      return selections.length < 4
        ? selections
        : selections.sort((a, b) => a.currentOdd - b.currentOdd);
    },
  },
  computed: {
    markets() {
      const markets = this.selections.map((elt) => elt.market);

      return markets.reduce((array, item) => {
        return array.find((elt) => elt.id === item.id)
          ? array
          : [...array, item];
      }, []);
    },
  },
};
</script>
