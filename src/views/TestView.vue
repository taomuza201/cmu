<template>
  <v-container fluid>
    <v-row>
      <v-col>
        <center>
          <h1>ระบบจองห้องค้นคว้ากลุ่มสำนักหอสมุด</h1>
        </center>
      </v-col>
    </v-row>
    <v-row align="center" justify="center">
      <v-col cols="6">
        <v-select :items="rooms" v-model="input_rooms" label="เลือกห้องที่ต้องการ" item-text="name"
          @change="thisrooms()" item-value="room_id"></v-select>
      </v-col>
    </v-row>


    <v-row>
      <v-col cols="12" md="6">
        <div class="mx-auto">
          <v-date-picker v-model="picker" elevation="15" @change="thisday()"></v-date-picker>
        </div>

      </v-col>

      <v-col cols="12" md="6">

        <v-card class="mx-auto mt-3" v-for="item  in slot" :key="item.slot_id" @click="select(item.slot_id)">
          <v-card-text>

            <p class="text-h4 text--primary text-center">
              {{ item.time_start }} - {{ item.time_end }}


            </p>
            <center>

              <p v-if="check_slot.filter(d => d.slot_id === item.slot_id).length >=1"> <span class="text-red">Not
                  Available </span> </p>
              <p v-else> <span class="text-green">
                  Available </span> </p>


            </center>
          </v-card-text>

        </v-card>
      </v-col>


    </v-row>


  </v-container>
</template>


<script>
  import axios from 'axios';
  axios.defaults.headers.common = {
    'Authorization': 'Bearer ' + 'EWET'
  }

  export default {
    data() {
      return {
        rooms: [],
        slot: [],
        input_rooms: null,
        check_slot: null,
        input_slot: null,
        picker: null,
        available: null,

      }
    },
    created() {
      axios
        .get('https://cloud-3001.lib.cmu.ac.th/exam/room')
        .then((response) => {
          console.log(response.data);
          this.rooms = response.data;
        })
        .catch(error => {
          console.log(error);
        });

    },
    methods: {

      thisrooms() {
        console.log(this.input_rooms);
        axios
          .get('https://cloud-3001.lib.cmu.ac.th/exam/slot')
          .then((response) => {


            console.log(response.data.filter(d => d.room_id === this.input_rooms));


            this.slot = response.data.filter(d => d.room_id === this.input_rooms);
          })
          .catch(error => {
            console.log(error);
          })
      },
      thisday() {

        axios
          .get('https://cloud-3001.lib.cmu.ac.th/exam/reserve/' + this.input_rooms + '/' + this.picker)
          .then((response) => {

            this.check_slot = response.data;
            console.log(response.data);
          })
          .catch(error => {
            console.log(error);
          })



      },
      select(slot_id) {



        if (this.check_slot.filter(d => d.slot_id === slot_id).length >= 1) {

          if (confirm("Confiram to cancel")) {
            axios
              .delete('https://cloud-3001.lib.cmu.ac.th/exam/reserve/' + slot_id + '/' + this.picker).then(response => {
                console.log(response.data);
              }).catch(error => {
                console.log(error);
              })

          }


        } else {

          if (confirm("Confiram to reserve this room")) {
            axios
              .put('https://cloud-3001.lib.cmu.ac.th/exam/reserve/' + slot_id + '/' + this.picker).then(response => {
                console.log(response.data);
              }).catch(error => {
                console.log(error);
              })


          }


        }


        this.thisday();




      },

    }
  }
</script>
<style scoped>
  .text-red {
    color: red;
    ;
  }

  .text-green {
    color: green;
    ;
  }
</style>