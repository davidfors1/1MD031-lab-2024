<template>
<div>
    
    <!--<div>
    <h1>Burgers</h1>
    <Burger v-for="burger in burgers" 
             v-bind:burger="burger" 
             v-bind:key="burger.name" />
    </div>
    <div id = map v-on:click="addOrder">
    </div>-->
    
    <html lang="en">
   <head>
    <link rel="stylesheet" type="text/css" href="/public/css/style.css">
       <meta charset="utf-8"/>
   </head>
   
   <body>
    <header id="head">
        <img src="https://cdn.pixabay.com/photo/2016/06/13/18/19/france-1454908_1280.jpg" id="flag">
        <h1 id="htext">
            Bienvenue à McDooo!
        </h1>
    </header>
    
    <main>
        <section id="burgare">
            <h2 id="stext">
                Välj en burgare!
            </h2>
            <p id="stext">
                Här kan du se lite vackra bilder på våra underbart fantastiska burgare.
            </p>

            <div class="wrapper">
                <Burger v-for="burger in burgers" 
             v-bind:burger="burger" 
             v-bind:key="burger.name"
             v-on:orderedBurger="updateOrder($event)"    
              />
            </div>    
        

        </section>
        <section>
            <div id= "mapWrapper">
                <div id = map v-on:click="addOrder">
                    <div id="target">
                        <div v-bind:style="{ left: location.x + 'px', top: location.y + 'px'}">
                            T
                        </div> 
                    </div>
                </div>
            </div>
         </section>
        
        <section id="info">
            <h2 id="stext">
                Information om dig!
            </h2>
            <p id="stext">
                Vi vill veta lite granna om dig, så vi inte skickar den underbara burgaren till fel person.
            </p>

            <form id="stext">
                <p>
                    <label for="Namn">Ditt namn</label><br>
                    <input type="text" id="namn" v-model="form.namn" required placeholder="Namn">
                </p>
                <p>
                    <label for="E-postadress">Din e-postadress</label><br>
                    <input type="text" id="email" v-model="form.email" placeholder="E-postadress">
                </p>
                <!--<p>
                    <label for="gata">Namnet på din gata</label><br>
                    <input type="text" id="gata" v-model="form.gata" required placeholder="Gatunamn">
                </p>
                <p>
                    <label for="Husnummer">Ditt husnummer</label><br>
                    <input type="number" id="nummer" v-model="form.nummer" required placeholder="Husnummer">
                </p>-->

                <p>
                    <label for="betalningsmetod">Betalningsmetod</label><br>
                    <select type="option" id="betalningsmetod" v-model="form.betalningsmetod">
                    <option>Cash</option>
                    <option>Kort</option>
                    <option>Klarna </option>
                    <option selected>Kramar</option>
                    </select>
                </p>

                <p>
                    <label for="Ditt kön">Kön</label>
                    <div>
                        <input type="radio" id="Man" v-model="form.kön" value="Man"  />
                        <label for="Man">Man</label>
                    </div>

                    <div>
                        <input type="radio" id="Kvinna" v-model="form.kön" value="Kvinna" checked  />
                        <label for="Kvinna">Kvinna</label>
                    </div>

                    <div>
                        <input type="radio" id="Vill ej ange" v-model="form.kön" value="Vill ej ange" />
                        <label for="Vill ej ange">Vill ej ange</label>
                    </div>
                </p>
            </form>


        </section>

        <button v-on:click="order">
            <img src="/img/OrderNow.png"style="width: 50px">
            Beställ Nu!
        </button>    
    </main>
    
    <hr> 
    <footer>
         &copy; 2025 David
    </footer>

   </body>

</html>
</div>  
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'
import OneBurger from '../components/OneBurger.vue';

let burgers= [
  { name: "Burger au caillot de sang.", kCal: 10000, url: "/img/smasmabug.jpg", lactose: true, gluten: true },
  { name: "Le Burger.", kCal: 300, url: "/img/leburger.jpg", lactose: true, gluten: true },
  { name: "Burger désagréablement.", kCal: 600, url: "/img/laskigburgare.jpg", lactose: false, gluten: true }
]

const socket = io("localhost:3000");

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: menu,
      orderedBurgers: {
      },
      location: { x: 0,
            y: 0
          },
      form: {
        namn:"",
        email:"",
        /*gata:"",
        nummer:"",*/
        betalningsmetod:"",
        kön:"",
      }
    }
  },

  methods: {

  updateOrder(event) {
    this.orderedBurgers[event.name] = event.amount;
    console.log("Beställning hittills:", this.orderedBurgers);
  },


  order() {
    console.log("Beställning mottagen!");
    console.log("Namn:", this.form.namn);
    console.log("Email:", this.form.email);
   /* console.log("Gata:", this.form.gata);
    console.log("Nummer:", this.form.nummer);*/
    console.log("Betalning:", this.form.betalningsmetod);
    console.log("Kön:", this.form.kön);
    console.log("Beställda burgare:", this.orderedBurgers)
  },

    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    addOrder: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
        this.location.x = event.clientX - offset.x;
        this.location.y = event.clientY - offset.y;
        console.log("Klickad plats:", this.location);
      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: event.clientX - 10 - offset.x,
                                           y: event.clientY - 10 - offset.y },
                                orderItems: ["Beans", "Curry"]
                              }
                 );
    }

}
}

</script>

<style>

#mapWrapper {
    margin: 25px 5px 10px 10px;       
    height: 500px;        
    overflow: scroll;    
    border: 5px dashed #000091;
}

  #map {
    background: url("/img/polacks.jpg");
    width: 1920px;
    height: 1078px;
  }

  body {
   font-family:monospace; color:#E1000F;
    .ingredient {
        color: ff5500;
    }
    #allergen {
        font-weight: bold;
    }

    #stext{
       margin: 5px 0px 0px 10px;
       padding: 5px 5px 5px; 
    }

    #head{
        margin: 20px 5px 0px 10px;
        height: 100px;
        overflow: hidden;
    }

    #htext{
        position: absolute;
        padding: 20px 80px 0px;
        margin-top: -825px;
    }

    #flag{
        opacity: 0.9;
        width: 100%;
        height: auto;
    }

    
    #burgare {
        border: 5px dashed white;
        margin: 25px 5px 10px 10px;
        background-color: black;
        color: white;
    }

      #info {
        border: 5px dashed #E1000F;
        margin: 25px 5px 10px 10px;
    }

    #knapp{
        margin: 15px 5px 10px 10px;
        padding: 15px 5px 5px;
    }

    button:hover {
        background-color: #000091;
        cursor:grabbing

    }

    .wrapper {
     display: grid;
     grid-template-columns: repeat(auto-fill, 10em);
     gap: 1em;
     padding: 0;    
    }

    #target {
    position: relative;
    margin: 0;
    padding: 0;
    background-repeat: no-repeat;
    width:1920px;
    height: 1078px;
    cursor: crosshair;
  }

    #target div {
    position: absolute;
    background: black;
    color: white;
    border-radius: 10px;
    width:20px;
    height:20px;
    text-align: center;
  } 

}




</style>