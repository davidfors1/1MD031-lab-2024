<template>
  <div>
    <div style="display: inline-block" id="burg" class="box a">
      <h3>
        {{ burger.name }}
      </h3>
      
      <img v-bind:src="burger.url" style="width: 400px;">

      <ul>
        <li>Kalorier: {{burger.kCal}} kCal</li>  
        <li v-if="burger.lactose">Innehåller laktos</li>
        <li v-else>Laktosfri</li>  
        <li v-if="burger.gluten">Innehåller gluten</li>
        <li v-else>Glutenfri</li>
      </ul>
      <button v-on:click="reduceAmount()">-</button>
        <span>{{ amountOrdered }}</span>
      <button v-on:click="increaseAmount()">+</button>
            
    </div>
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
    increaseAmount: function() {
        this.amountOrdered++
        this.$emit('orderedBurger', { name: this.burger.name, amount: this.amountOrdered 
        }
        );
      },
      reduceAmount: function() {
        if (this.amountOrdered>0) {
          this.amountOrdered--
        }
        this.$emit('orderedBurger', { name: this.burger.name, amount: this.amountOrdered 
        }
        );
      }
    }, 

}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#burg{
        margin: 5px 20px;
        padding: 5px;
    }

</style>