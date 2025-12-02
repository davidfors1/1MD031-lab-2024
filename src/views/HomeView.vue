<template>
    <div>    
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

        <section>
            <p id="stext"> Klicka gärna i vart du vill ha din burgare levererad:</p>
            <div id= "mapWrapper">
                <div id = map v-on:click="setLocation">
                    <div id="target">
                        <div v-bind:style="{ left: location.x + 'px', top: location.y + 'px'}">
                            T
                        </div> 
                    </div>
                </div>
            </div>
         </section>

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

const socket = io("localhost:3000");

/* function MenuItem(namn, url, kCal, glu, lak) {
  this.namn = namn;
  this.URL = url;
  this.kCal = kCal;
  this.gluten = glu;
  this.laktos = lak;
} 
*/

/* const burgers = [
  new MenuItem("Burger au caillot de sang.", "/img/smasmabug.jpg", 1000 ,true, true),
  new MenuItem("Le Burger.", "/img/leburger.jpg", 300,  true, true),
  new MenuItem("    "name": "Burger désagréablement.", "/img/laskigburgare.jpg", 600, false, true)] */

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
            betalningsmetod:"",
            kön:"",
        }
    }   
    },

  methods: {

  updateOrder(event) {
    this.orderedBurgers[event.name] = event.amount;
  },

  order: function() {
    socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: this.location.x,
                                           y: this.location.y,
                                           Namn: this.form.namn,
                                           Email: this.form.email,
                                           Betalning: this.form.betalningsmetod,
                                           Kön: this.form. kön,
                                         },
                                orderItems: this.orderedBurgers
                              }
                            );
    },

    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },

    setLocation: function (event){
        var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
            this.location.x = event.clientX - offset.x;
            this.location.y = event.clientY - offset.y;
        }          
    },
}

</script>
<style>

#mapWrapper {
    margin: 10px 20px 20px 20px;       
    height: 400px;       
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
        font-size: large;
  } 

}

</style>