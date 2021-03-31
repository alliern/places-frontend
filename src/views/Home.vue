<template>
  <div class="home">
    <h1>Add a Place!</h1>
    Name:
    <input type="text" v-model="newPlaceName" />
    Address:
    <input type="text" v-model="newPlaceAddress" />
    <button v-on:click="placesCreate">Add</button>
    <div v-for="place in places" v-bind:key="place.id">
      <h3>{{ place.name }}</h3>
      <button v-on:click="placesShow(place)">More Info</button>
    </div>
    <dialog id="place-info">
      <form method="dialog">
        <h1>Place Info</h1>
        <p>
          Name
          <input type="text" v-model="currentPlace.name" />
        </p>
        <p>
          Address
          <input type="text" v-model="currentPlace.address" />
        </p>
        <button v-on:click="placesUpdate(currentPlace)">Update</button>
        <button v-on:click="placesDestroy(currentPlace)">Delete</button>
        <button>Close</button>
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
      newPlaceName: "",
      newPlaceAddress: "",
      currentPlace: {},
    };
  },
  created: function () {
    this.placesIndex();
  },
  methods: {
    placesIndex: function () {
      axios.get("/api/places").then((response) => {
        console.log(response.data);
        this.places = response.data;
      });
    },
    placesShow: function (place) {
      this.currentPlaces = place;
      document.querySelector("#place-info").showModal();
    },
    placesCreate: function () {
      var params = {
        name: this.newPlaceName,
        address: this.newPlaceAddress,
      };
      axios
        .post("/api/places", params)
        .then((response) => {
          console.log(response.data);
          this.places.push(response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    placesUpdate: function (place) {
      var params = {
        name: place.name,
        address: place.address,
      };
      axios
        .patch("/api/places/" + place.id, params)
        .then((response) => {
          console.log("success!", response.data);
          this.places.push(response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    placesDestroy: function (place) {
      axios.delete("/api/places/" + place.id).then((response) => {
        console.log("success!", response.data);
        var index = this.places.indexOf(place);
        this.place.splice(index, 1);
      });
    },
  },
};
</script>
