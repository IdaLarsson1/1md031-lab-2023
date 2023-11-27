<template>
  <div>
    <h3>{{ burger.name }}</h3>
    <img v-bind:src=burger.url style="height: 200px;">
    <ul>
      <li>{{ burger.kCal }} kCal</li>
      <li v-if="burger.lactose">Innehåller <span class="allergi">laktos</span></li>
      <li v-if="burger.gluten">Innehåller <span class="allergi">gluten</span></li>
      <li v-if="burger.vegan">Vegansk</li>
    </ul>

    <button v-on:click="amountOrdered--">Ta bort en</button>
    {{ amountOrdered }}
    <button v-on:click="amountOrdered++">Lägg till en</button><br>
    
    <button v-on:click="addBurger">Lägg till i beställning</button>
  </div>

</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },
  data: function () {
    return {
      amountOrdered: 0,
      
    }
  },
  methods: {
    addBurger: function () {   
      this.$emit('orderedBurger', { name:   this.burger.name, 
                                  amount: this.amountOrdered 
                                  }
                );
      this.amountOrdered = 0;
    }
  }
}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.allergi {
  font-weight: bold;
}


/*
#hamburgers {
  color: white;
  background-color: black;
  border: dotted white;
}

.wrapper {
    display: grid;
    grid-gap: 10px;
    grid-template-columns: 33% 33% 33%;
}

.box {
    border-radius: 5px;
    padding: 20px;
}

*/
</style>
