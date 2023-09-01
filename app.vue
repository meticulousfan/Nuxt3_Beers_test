<template>
  <div>
    <h1>Beers List</h1>
    <ul>
      <li v-for="beer in beers" :key="beer.id">
        <h2>{{ beer.name }}</h2>
        <p>Tagline: {{ beer.tagline }}</p>
        <p>Description: {{ beer.description }}</p>
        <img :src="beer.image_url" alt="Beer Image">
        <p>ABV: {{ beer.abv }}</p>
        <p>IBU: {{ beer.ibu }}</p>
        <p v-if="containsLactose(beer)">Contains Lactose</p>
        <p v-if="isDryHopped(beer)">Dry Hopped</p>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      beers: [],
    };
  },
  async mounted() {
    try {
      const response = await fetch('https://api.punkapi.com/v2/beers?brewed_after=11-2012');
      const data = await response.json();
      this.beers = data
        .filter((beer) => !beer.ingredients.hops.some((hops) => hops.name === 'Centennial'))
        .sort((a, b) => a.abv - b.abv);
    } catch (error) {
      console.error(error);
    }
  },
  methods: {
    containsLactose(beer) {
      return beer.ingredients.malt.some((malt) => malt.name.toLowerCase().includes('lactose'));
    },
    isDryHopped(beer) {
      return beer.method.twist && beer.method.twist.toLowerCase().includes('dry hopped');
    },
  },
};
</script>
