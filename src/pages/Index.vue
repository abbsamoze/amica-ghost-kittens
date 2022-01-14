<template>
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
        <q-radio dark name="Sömntid" v-model="Sömntid" val="0" label="0" id="radio" />
        <q-radio dark name="Sömntid" v-model="Sömntid" val="1-3" label="1-3" id="radio" />
        <q-radio dark name="Sömntid" v-model="Sömntid" val="4-6" label="4-6" id="radio" />
        <q-radio dark name="Sömntid" v-model="Sömntid" val="7-9" label="7-9" id="radio" />
        <q-radio dark name="Sömntid" v-model="Sömntid" val="10+" label="10+" id="radio" />
      </div>

      <div>
        <q-btn label="Submit" type="submit" color="primary" />
      </div>
    </q-form>

    <!-- <q-card v-if="notanswered" flat bordered class="q-mt-md bg-grey-2">
      <q-card-section>Tack för ditt svar:</q-card-section>
      <q-separator />
      <q-card-section class="row q-gutter-sm items-center">
        <div
          v-for="(item, index) in submitResult"
          :key="index"
          class="q-px-sm q-py-xs bg-grey-8 text-white rounded-borders text-center text-no-wrap"
        >{{ item.name }} = {{ item.value }}</div>
      </q-card-section>
    </q-card> -->

  </div>
</template>

<script>

import { defineComponent } from 'vue';
import { ref } from 'vue'
import { Cookies } from 'quasar'

export default {
  setup () {
    const submitResult = ref([])

    return {
      Pigghet: ref(5),
      Sömntid: ref('0'),
      submitResult,
      answtext: 'hej',
      api_url: "https://api.simsva.se/amicadb",
      alreadyanswered: false,
      notanswered: false,

      onSubmit (evt) {
          let temp = this;
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
          let temptext=""
          fetch(`https://api.simsva.se/amicadb/answer`, {
            method: "POST",
            body: `sleep=${data[0]['value']}&sleep_time=${intervals[data[1]['value']]}&token=${Cookies.get("token")}`,
            headers: {
              "Content-Type": "application/x-www-form-urlencoded",
            },
          }).then(res => {
            let self = this;
            switch(res.status) {
              case 200: // Success
                alert('Thanks for submitting')
                break;

              case 401: // Already answered
                alert('Already submitted today')
                break;

              case 404: // Invalid token
                alert('Invalid token')
                break;
            }
          })
      }
    }
  },
  methods: {
    myFunction () {
      var value = Cookies.get('token');
      if (value == null) {
          fetch(`${this.api_url}/token`) 
            .then(res => res.text())
            .then(data => Cookies.set("token", data))
      }
    },
  },
  created() {
    this.myFunction()
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
