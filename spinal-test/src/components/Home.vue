<template>
  <div class="container">
    <h1 class="text-center"> Statistique d'occupation : </h1>


    <section v-if="loading">Chargement en cours...</section>
    <section v-else class="flex-center">

      <article class="overflow list-floor">
        <h1>Etages :</h1>
        <div v-for="floor in floors" :key="floor.dynamicId" @click="selectFloor(floor.dynamicId)">
          <p class="rounded hover"
            :class="{ 'bg-selected': isSelected == floor.dynamicId, 'bg-floor': isSelected !== floor.dynamicId }">
            {{ floor.name }}
          </p>
        </div>
      </article>

      <article class=" list-room ">
        <h1>Detail pieces : </h1>
        <div class="bg-grey overflow rounded" v-if="selectedFloor && selectedFloor.length > 0">
          <aside class="border-styled hover" @click="showDetail(room.dynamicId)"
          :class="{ 'border-styled-selected' : isDetail == room.dynamicId && room.endpoint[0]}"
            v-for="room in selectedFloor" :key="room.dynamicId">
            <div class="flex-center item-center">
              <p>{{ room.name }}</p>

              <p class="bg-true rounded status-width text-center hover"
                v-if="room.endpoint[0]?.endpoints[4].currentValue == true">true</p>
              <p class="bg-false rounded status-width text-center hover"
                v-if="room.endpoint[0]?.endpoints[4].currentValue == false">false</p>
              <p class="bg-null rounded status-width text-center hover"
                v-if="room.endpoint[0]?.endpoints[4].currentValue == undefined || room.endpoint[0]?.endpoints[4].currentValue === undefined">
                undefined</p>
            </div>

            <div v-if="isDetail == room.dynamicId && room.endpoint[0]">
              <hr />
              <div v-for="detail in room.endpoint[0]?.endpoints" :key="detail.dynamicId">
                <p>{{ detail.name }} : {{ detail.currentValue }}</p>
              </div>
            </div>

          </aside>
        </div>
        <div v-else>
          Chargement .....
        </div>
      </article>

    </section>

  </div>
</template>

<script>

export default {
  name: 'HomeComponent',
  components: {
  },

  data() {
    return {
      loading: true,
      floors: [],
      selectedFloor: null,
      isSelected: null,
      isDetail: false,
      option: { method: "GET" }
    };
  },

  mounted() {

    fetch("https://api-developers.spinalcom.com/api/v1/floor/list", this.option)
      .then(res => res.json())
      .then(res => {
        this.floors = res;
        this.selectFloor(res[0].dynamicId);
        this.isSelected = res[0].dynamicId;
        this.loading = false;
      })
      .catch(error => {
        console.error('Une erreur s\'est produite lors de la récupération des données :', error);
        this.loading = false;
      });
  },

  methods: {

    selectFloor(floor) {

      this.isSelected = floor;

      fetch(`https://api-developers.spinalcom.com/api/v1/floor/${floor}/room_list`, this.option)
        .then(res => res.json())
        .then(rooms => {
          const promises = rooms.map(room =>
            fetch(`https://api-developers.spinalcom.com/api/v1/room/${room.dynamicId}/control_endpoint_list`, this.option)
              .then(endpointRes => endpointRes.json())
              .then(endpoint => {
                room.endpoint = endpoint;
                return room;
              })
          );

          return Promise.all(promises);
        })
        .then(updatedRooms => {
          this.selectedFloor = updatedRooms;
        })
        .catch(error => console.error(error));
    },

    showDetail(id) {
      this.isDetail = id;
    }

  },

}
</script>


<style>
.container {
  width: 80%;
  margin: auto;
}

.flex-center {
  display: flex;
  justify-content: space-between;
}

.item-center {
  align-items: center;
}

.text-center {
  text-align: center;
}

.overflow {
  max-height: 75vh;
  overflow-y: auto;
}

.list-floor {
  width: 35%;
}

.list-room {
  width: 50%;
}

.bg-grey{
  background-color: #C5C6C6;
}

.bg-true {
  background-color: #88C7BC;
}

.bg-false {
  background-color: #FE4B4B;
}

.bg-null {
  background-color: #e5e7e6;
}

.status-width {
  width: 15%;
}

.rounded {
  border-radius: 1rem;
  padding: 1rem;
}

.bg-floor {
  background-color: #FDF0E7;
  border: solid;
  border-color: #f8d5abef;
}

.bg-floor:hover {
  background-color: #f2e3d7;
  border: solid;
  border-color: #f8d5abef;
}

.bg-selected {
  background-color: #fca944;
  border: solid;
  border-color: #fca944;

}

.bg-selected:hover {
  background-color: #f39422;
}

.border-styled {
  /* border-radius: 10px 100px / 120px; */
  border-radius: 10px 50px / 50px;
  background-color: #ffffff;
  padding-right: 1em;
  padding-left: 1em;
  margin-bottom: 15px;
  padding-bottom: 1em;
}

.border-styled-selected {
  /* border-radius: 10px 100px / 120px; */
  border-radius: 10px 50px / 50px;
  background-color: #f1f3f2;
  padding-right: 1em;
  padding-left: 1em;
  margin-bottom: 15px;
  padding-bottom: 1em;
}

.hover {
  cursor: pointer;
}

aside {
  width: 95%;
  margin: auto;
  padding-left: 10%;
}

aside:hover {
  background-color: #f1f3f2;
}

.underline {
  border-bottom: 2px solid black
}
</style>
