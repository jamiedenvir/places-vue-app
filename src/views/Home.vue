<template>
  <div class="home">
    <div>
      <h1>Some Nice Places :)</h1>
      <h2>Make New Place</h2>
      <ul>
        <li class="error" v-for="error in errors" v-bind:key="error">{{ error }}</li>
      </ul>
      <input type="text" v-model="newPlaceParams.name" placeholder="Name" />
      <br />
      <input type="text" v-model="newPlaceParams.address" placeholder="Address" />
      <br />
      <button v-on:click="createPlace()">Add. That. Place!</button>
    </div>
    <!-- indexPlaces -->
    <div v-for="place in places" :key="place.id">
      <h3>Name: {{ place.name }}</h3>
      <p>Address: {{ place.address }}</p>
      <button v-on:click="showPlace(place)">More Info</button>
    </div>

    <dialog id="place-details">
      <form method="dialog">
        <h1>Place Info</h1>
        Name:
        <input type="text" v-model="currentPlace.name" />
        Address:
        <input type="text" v-model="currentPlace.address" />
        <br />
        <button v-on:click="updatePlace()">Update</button>
        <button>Close</button>
        <button v-on:click="destroyPlace()">Delete</button>
      </form>
    </dialog>
  </div>
</template>

<style></style>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      places: [],
      newPlaceParams: {},
      currentPlace: {},
      errors: [],
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("http://localhost:3000/places").then((response) => {
        console.log("Places array", response.data);
        this.places = response.data;
      });
    },
    createPlace: function () {
      axios
        .post("http://localhost:3000/places", this.newPlaceParams)
        .then((response) => {
          console.log(response.data);
          this.places.push(response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
    },
    showPlace: function (place) {
      this.currentPlace = place;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function () {
      axios
        .patch(`http://localhost:3000/places/${this.currentPlace.id}`, this.currentPlace)
        .then((response) => {
          console.log(response.data);
          this.currentPlace = {};
        })
        .catch((error) => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
    },
    destroyPlace: function () {
      axios.delete(`http://localhost:3000/places/${this.currentPlace.id}`).then((response) => {
        var index = this.places.indexOf(this.currentPlace);
        this.places.splice(index, 1);
        console.log(response.data);
      });
    },
  },
};
</script>
