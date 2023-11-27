<template>
  <div> 
    <div>
      <header>        
        <h1>Välkommen till Foodora 2.0</h1>
        <img src="https://thatsup.se/content/img/place/t/u/tugg-burgers-uppsala-0.jpg">
      </header>
      <section id="hamburgers">
        <div class="wrapper">
          <Burger v-for="burger in burgers"
                  v-bind:burger="burger" 
                  v-bind:key="burger.name"
                  v-on:orderedBurger="addToOrder($event)"/>
        </div>
      </section>
    </div>
  </div>

  <section id="currentOrder">
    <h3>Din beställning: </h3>
    <div v-for="(number, name) in orderedBurger">
      {{ name }}: {{ number }} st.
    </div>
  </section>

  <section id="kundInformation">
    <form>
      <h3>Kundinformation</h3>
      Skriv in din information så att beställningen kommer rätt!

      <h4>Leveransinformation</h4>
      <p>
        <label for="Namn">Namn</label><br>
        <input type="text" id="namn" v-model="namn" required="required" placeholder="För- och efternamn">
      </p>
      <p>
        <label for="Email">E-mail</label><br>
        <input type="email" id="mail" v-model="mail" required="required" placeholder="E-mail adress">
      </p>
<!--  <p>
        <label for="Adress">Adress</label><br>
        <input type="text" id="adress" v-model="adress" required="required" placeholder="Gata">
      </p>
      <p>
        <label for="Husnummer">Hus</label><br>
        <input type="number" id="husnummer" v-model="husnummer" required="required" placeholder="Husnummer">
      </p>  -->
      <p>
        <label for="payment">Betalningsalternativ</label><br>
        <select id="payment" v-model="payment">
          <option>Swish</option>
          <option>Kort</option>
          <option>Klarna</option>
          <option>PayPal</option>
        </select>
      </p>

      Vart vill du få beställningen?
      <div class="mapWrap">
        <label>Klicka på kartan där du bor.</label>
        <div id="map" v-on:click="setLocation">
          <div v-bind:style="{left: this.location.x + 'px', 
                              top: this.location.y + 'px'}">
            <span>T</span>
          </div>
        </div>
      </div>
      <p>
        <label for="Gender">Kön</label><br>
        <input type="radio" id="man" value="man" v-model="gender" required="required">
        <label for="Man">Man</label><br>
        <input type="radio" id="kvinna" value="kvinna" v-model="gender" required="required">
        <label for="Kvinna">Kvinna</label><br>
        <input type="radio" id="Vill ej ange" value="vill ej ange" v-model="gender" required="required" checked="checked">
        <label for="Vill ej ange">Annat/Vill ej ange</label><br>
      </p> 
    </form>             
  </section>
  
  <section id="beställ">
    <button type="submit" v-on:click="beställ">
      <img src="https://cdn-icons-png.flaticon.com/512/3075/3075977.png" style="width: 20px;">
      Skicka beställning
    </button>
  </section>

  <footer>
    <hr>
    &copy; 2023 Ida Larsson
  </footer>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

console.log(menu); 
const socket = io();

/*function MenuItem(name, url, kCal, l, g, v) {
  this.name = name;
  this.url = url;
  this.kCal = kCal;
  this.lactose = l;
  this.gluten = g;
  this.vegan = v;
} 

const minHamburgare = [
  new MenuItem("Den nyttiga", "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQyojvzAQkWU2Z03zMI5nAIY-5xH0wn0nvXLQ&usqp=CAU", "300", false, true, true),
  new MenuItem("Den höga", "https://www.shakentogetherlife.com/wp-content/uploads/2022/10/hamburger-baked-in-oven.jpg", "500", true, false, false),
  new MenuItem("Smashburgaren", "https://media-cdn.tripadvisor.com/media/photo-s/25/18/0e/8a/original-smash-burger.jpg", "1000", true, true, false)
]; */
/* console.log(minHamburgare); */

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: menu,
      namn:'',
      mail:'',
      payment:'',
      gender:'',
      orderedBurger: {},
      location: {x:0, y:0}
    }
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
  /*  addOrder: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      this.location = {x: event.clientX - 10 - offset.x,
                       y: event.clientY - 10 - offset.y},
       socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: event.clientX - 10 - offset.x,
                                           y: event.clientY - 10 - offset.y },
                                orderItems: ["Beans", "Curry"]
                              }
                 ); 
    }, */
    setLocation: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      this.location = {x: event.clientX - 10 - offset.x,
                       y: event.clientY - 10 - offset.y}
    },

    addToOrder: function (event) {
      if (this.orderedBurger[event.name] == null) {
        this.orderedBurger[event.name] = event.amount;
      }  
      else {
        this.orderedBurger[event.name] += event.amount;
      }
    },
    beställ: function (event) {
      this.kundInformation = {name: this.namn, mail: this.mail, payment: this.payment, gender: this.gender},
      console.log(this.kundInformation),
      console.log(this.orderedBurger),
      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: this.location.x ,
                                           y: this.location.y },
                                orderItems: this.orderedBurger,
                                customer: this.kundInformation                             
                              }
                 );
    }
  }
}
</script>

<style>
@import 'https://fonts.googleapis.com/css?family=Pacifico|Dosis';

body {
  font-family: Kanit, sans-serif;
}
header {
  margin: 10px;
  height: 250px;
  overflow: hidden;
}
header > img {
  opacity: 0.6;
  width: 100%;
  height: auto;
}
header > h1 {
  position: absolute;
  padding: 40px 40px;
}
section {
  margin: 10px;
  padding: 10px;
}
button {
  margin: 10px;
  background-color: rgb(198, 141, 225);
  border: 1px dotted gainsboro;
  border-radius: 20px;
}
button:hover {
  background-color: plum;
  cursor: pointer;
}
.allergi {
  font-weight: bold;
}
#hamburgers {
  padding: 20px;
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
#beställ > button {
  width: 270px;
  height: 50px;
  font-size: x-large;
  font-weight: bold;
}
#currentOrder > h3 {
  margin-bottom: 10px;
}
#currentOrder {
  padding: 20px;
  border: dotted black;
  background-color: rgb(198, 141, 225);
}
#kundInformation {
  padding: 20px;
  border: dotted black;
  /*padding-top: 0px;*/
  padding-bottom: 0px;
}
#map {
  width: 1920px;
  height: 1078px;
  background: url("../../public/img/polacks.jpg");
  cursor: crosshair;
}
#map div {
  position: relative;
  background: black;
  color: white;
  border-radius: 10px;
  width:20px;
  height:20px;
  text-align: center;
}
.mapWrap {
  width: 500px;
  height: 400px;
  overflow: scroll;
}
</style>