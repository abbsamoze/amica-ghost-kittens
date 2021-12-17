<template>
  <!-- <q-page class="flex flex-center"> -->
    <!-- <div class="flex flex-center">
      <h1 style="font-size:30px; color: white;">Hur pigg känner du dig idag?</h1>
      <q-btn-toggle
        style=""
        v-model="model"
        color="purple"
        text-color="white"
        toggle-color="orange"
        toggle-text-color="black"
        rounded
        unelevated
        glossy
        :options="[
          {label: '1', value: 'one', id:1},
          {label: '2', value: 'two', id:2},
          {label: '3', value: 'three', id:3},
          {label: '4', value: 'four', id:4},
          {label: '5', value: 'five', id:5}
        ]"
      />
      <q-btn color="secondary" icon-right="mail" label="Submit Answer" v-on:click="sumbit()" style="margin-top:20vw;"/>
    </div> -->
    
  <!-- </q-page> -->

 <div class="q-pa-md">
    <q-form @submit="onSubmit" class="q-gutter-md">
      <div class="q-mt-xl">
        <h1 id="title">Hur pigg känner du dig idag 1-10?</h1>
        <q-slider
          name="Pigghet"
          v-model="Pigghet"
          label-always
          :min="0"
          :max="10"
          :step="1"
        />

        <h1 id="title">Hur många timmar har du sovit idag?</h1>
        <q-radio dark name="Sömntid" v-model="Sömntid" val="0" label="1" id="radio" @click="submitTrue()"/>
        <q-radio dark name="Sömntid" v-model="Sömntid" val="1-3" label="2-3" id="radio" @click="submitTrue()"/>
        <q-radio dark name="Sömntid" v-model="Sömntid" val="4-6" label="4-6" id="radio" @click="submitTrue()"/>
        <q-radio dark name="Sömntid" v-model="Sömntid" val="7-9" label="7-9" id="radio" @click="submitTrue()"/>
        <q-radio dark name="Sömntid" v-model="Sömntid" val="10+" label="10+" id="radio" @click="submitTrue()"/>
      </div>

      <div>
        <q-btn label="Submit" type="submit" color="primary"/>
      </div>
    </q-form>

    <q-card v-if="submitResult.length> 0" flat bordered class="q-mt-md bg-grey-2">
      <q-card-section>Tack för ditt svar:</q-card-section>
      <q-separator />
      <q-card-section class="row q-gutter-sm items-center">
        <div
          v-for="(item, index) in submitResult"
          :key="index"
          class="q-px-sm q-py-xs bg-grey-8 text-white rounded-borders text-center text-no-wrap"
        >{{ item.name }} = {{ item.value }}</div>
      </q-card-section>
    </q-card>
  </div>

</template>

<script>
import { defineComponent } from 'vue';
import { ref } from 'vue'

export default {
  setup () {
    const submitResult = ref([])

    return {
      Pigghet: ref(5),
      Sömntid: ref('0'),
      submitResult,

      onSubmit (evt) {
        if(s) {
          const formData = new FormData(evt.target)
          const data = []

          for (const [ name, value ] of formData.entries()) {
            data.push({
              name,
              value
            })
          }

          let intervals = {
            "0": 0,
            "1-3": 1,
            "4-6": 2,
            "7-9": 3,
            "10+": 4
          }

          submitResult.value = data

          fetch("https://api.simsva.se/amicadb/answer", {
            method: "POST",
            body: `sleep=${data[0]['value']}&sleep_time=${intervals[data[1]['value']]}`,
            headers: {
              "Content-Type": "application/x-www-form-urlencoded",
            },
          })
        }
      }
    }
  },
  methods: {
    submitTrue() {
      console.log(s);
      s = true;
      console.log(s);
    }
  }
}
</script>

<style>
body{
  background-image: url("https://www.healthysleeptexas.com/wp-content/themes/hst/i/bg-body.jpg");
  background-repeat: no-repeat;
  background-size: cover;
}

#title {
  font-size: 20px;
  color: white;
}

#radio {
  color: white;
  font-weight: 500;
}
</style>
