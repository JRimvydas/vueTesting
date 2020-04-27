<template>
  <div class="form">
    <h1 class="title">Užsisakyti treniruotę</h1>
    <b-notification
      :active.sync="success"
      aria-close-label="Close notification"
    >
      Sėkmingai užsakyta treniruotė
    </b-notification>

    <form @submit.prevent="add">
      <div class="columns">
        <div class="column">
          <b-field label="Vardas">
            <b-input v-model="name"></b-input>
          </b-field>
        </div>
        <div class="column">
          <b-field label="Pavardė">
            <b-input v-model="surname"></b-input>
          </b-field>
        </div>
      </div>

      <b-field label="Email">
        <b-input v-model="email" type="email"></b-input>
      </b-field>
      <div class="field-body">
        <div class="field is-expanded">
          <label class="label">Telefono numeris</label>
          <div class="field has-addons">
            <p class="control">
              <a class="button is-static">
                +370
              </a>
            </p>
            <p class="control is-expanded">
              <input
                class="input"
                type="number"
                placeholder="Telefono numeris"
                v-model="phone"
                required
              />
            </p>
          </div>

          <b-field label="Data">
            <b-datepicker
              v-model="date"
              placeholder="Pasirinkite tinkamą dieną"
              :min-date="new Date()"
              @blur="check"
            >
            </b-datepicker>
          </b-field>

          <div class="times">
            <label class="label">Pasirinkite laiką:</label>
            <div class="buttons">
              <b-button
                v-for="time in availableTimes"
                :key="time.time"
                type="is-small"
                @click="btnbg($event, time.time)"
                >{{ time.visibleTime }}
              </b-button>
            </div>
          </div>

          <div class="block">
            <label class="label">Treniruotės tipas:</label>
            <div class="field">
              <b-radio v-model="type" name="type" native-value="internetu">
                Internetu (€15)
              </b-radio>
            </div>
            <div class="field">
              <b-radio v-model="type" name="type" native-value="Namuose/biure">
                Namuose/Biure (€30)
              </b-radio>
            </div>
            <div class="field">
              <b-radio v-model="type" name="type" native-value="PC EUROPA">
                PC Europa (€30)
              </b-radio>
            </div>
          </div>
        </div>
      </div>
      <!-- <b-button
        type="is-primary"
        class="button is-primary"
        v-bind:loading="isloading"
        native-type="submit"
      >
        Apmokėti
      </b-button> -->
      <b-button
        type="is-primary"
        @click="isCardModalActive = !isCardModalActive"
      >
        Tęsti apmokėjimą
      </b-button>

      <b-modal :active.sync="isCardModalActive" :width="640" scroll="keep">
        <div class="card">
          <div class="card-content">
            <div class="content">
              <h2 class="subtitle">Apmokėjimas</h2>
              <card
                class="stripe-card"
                :class="{ complete }"
                stripe="pk_test_FZ4E7gckshlJ6REa83tS6xxD004B0QtCse"
                :options="stripeOptions"
                @change="complete = $event.complete"
              />
              <b-button
                type="is-primary"
                native-type="submit"
                v-bind:loading="isloading"
                >Apmokėti</b-button
              >
            </div>
          </div>
        </div>
      </b-modal>
    </form>

    <!-- <b-modal :active.sync="isComponentModalActive" has-modal-card trap-focus :destroy-on-hide="false" aria-role="dialog" aria-modal> -->
  </div>
</template>

<script>
import firebase from "firebase/app";
import "firebase/firestore";
import { Card, createToken } from "vue-stripe-elements-plus";
export default {
  name: "Form",
  components: { Card },
  data() {
    return {
      name: "",
      surname: "",
      email: "",
      phone: "",
      type: "",
      date: null,
      availableTimes: [],
      selectedTime: 0,
      isloading: false,
      success: false,
      isCardModalActive: false,
      stripeOptions: {},
      complete: false,
    };
  },
  methods: {
    add() {
      this.isloading = true;
      createToken().then((card) => {
        firebase
          .firestore()
          .collection("orders")
          .add({
            name: this.name,
            surname: this.surname,
            phone: this.phone,
            date: this.date,
            email: this.email,
            type: this.type,
            time: this.selectedTime,
            card: card,
          })
          .then(() => {
            this.isloading = false;
            this.success = true;
          });
      });
    },
    btnbg(e, time) {
      e.target.classList.toggle("is-primary");
      this.selectedTime = time;
    },
    check() {
      const busyTimes = [];
      firebase
        .firestore()
        .collection("orders")
        .where("date", "==", this.date)
        .get()
        .then((response) =>
          response.docs.forEach((doc) => {
            busyTimes.unshift(doc.data().time);
          })
        )
        .then(() => {
          const startTime = 480;
          const endTime = 1200;
          const sessionTime = 30;
          this.availableTimes = [];
          for (let i = startTime; i < endTime; i += sessionTime) {
            if (!busyTimes.includes(i)) {
              const h = Math.floor(i / 60);
              let m = (i % 60).toPrecision(2);
              if (m.toString().includes(".")) {
                m = m.toString().replace(".", "");
              }
              this.availableTimes.push({
                time: i,
                visibleTime: `${h}:${m}`,
              });
            }
          }
        });
    },
  },
};
</script>

<style scoped>
.form {
  padding: 1em;
}
.stripe-card {
  margin-bottom: 1em;
}
</style>
