<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Login Relawan</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content>
      <img src="../image/blood.jpg" alt="" />
      <Form @submit="onSubmit">
        <ion-grid>
          <ion-row>
            <p>Email</p>
          </ion-row>
          <ion-row class="input-login">
            <Field
              name="email"
              as="input"
              placeholder="email"
              :rules="isEmail"
              autocomplete="off"
            >
            </Field>
            <ion-icon :icon="person" />
          </ion-row>
          <ion-row class="error-message">
            <error-message name="email"></error-message>
          </ion-row>

          <ion-row>
            <p>Password</p>
          </ion-row>
          <ion-row class="input-login">
            <Field
              name="password"
              as="input"
              placeholder="password"
              :rules="isRequired"
              autocomplete="off"
              type="password"
            >
            </Field>
            <ion-icon :icon="key" />
          </ion-row>
          <ion-row class="error-message">
            <error-message name="password"></error-message>
          </ion-row>
          <p class="error-message">{{ loginMessage }}</p>

          <ion-row>
            <ion-button type="submit" color="danger" class="btn-login"
              >Login</ion-button
            >
          </ion-row>
          <ion-row>
            <ion-button
              color="primary"
              class="btn-login"
              href="/tabs/register-volunteer"
              >Registrasi</ion-button
            >
          </ion-row>
        </ion-grid>
      </Form>
    </ion-content>
  </ion-page>
</template>

<script>
import {
  IonPage,
  IonHeader,
  IonTitle,
  IonToolbar,
  IonContent,
  IonGrid,
  IonRow,
  IonIcon,
  IonButton,
} from "@ionic/vue";
import { Field, ErrorMessage, Form } from "vee-validate";
import { person, key } from "ionicons/icons";
import { Storage } from "@ionic/storage";
import { Http } from "@capacitor-community/http";
export default {
  name: "loginPage",
  components: {
    IonPage,
    IonHeader,
    IonTitle,
    IonToolbar,
    IonContent,
    IonGrid,
    IonRow,
    IonIcon,
    IonButton,
    Form,
    Field,
    ErrorMessage,
  },
  data() {
    return {
      person,
      key,
      loginMessage: "",
    };
  },
  methods: {
    async onSubmit(values) {
      const API = process.env.VUE_APP_API;
      try {
        const options = {
          url: `${API}/v1/auth/volunteer/login`,
          headers: {
            "Content-Type": "application/json",
            Accept: "application/json",
          },
          data: { email: values.email, password: values.password },
        };

        const response = await Http.request({ ...options, method: "POST" });

        if (response.status == 401) {
          this.loginMessage = "Password atau Email anda salah";
          setTimeout(() => {
            this.loginMessage = "";
          }, 5000);
        } else {
          const result = response.data;
          console.log(result);
          const store = new Storage();
          await store.create();
          await store.set("accessVolunteer", result.token);

          this.$router.push("/tabs/profile-volunteer");
        }
      } catch (error) {
        console.log(error);
      }
    },
    isRequired(value) {
      if (!value) {
        return "Input Tidak Boleh Kosong";
      }
      return true;
    },
    isEmail(value) {
      if (!value) {
        return "Email Tidak Boleh Kosong";
      }
      if (
        !/^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$/.test(
          value
        )
      ) {
        return "Email Tidak Valid";
      }

      return true;
    },
  },
};
</script>

<style scoped>
p {
  font-size: 12px !important;
  margin-left: 26px;
}
.input-login {
  border-bottom: 1px solid rgb(109, 106, 106);
  margin: auto !important;
  width: 90% !important;
}
.btn-login {
  width: 90% !important;
  margin: 8px auto;
}
img {
  height: 40%;
  margin: auto;
  display: block;
}
.input-login {
  border-bottom: 1px solid rgb(109, 106, 106);
  margin: auto !important;
  width: 90% !important;
}
input {
  border: none;
  outline: none;
  width: 90%;
  padding: 8px !important;
}

input:focus {
  border: none;
  outline: none;
}

.error-message {
  font-size: 12px !important;
  margin-left: 22px;
  color: red !important;
  font-style: italic;
  margin-top: 2px !important;
}

ion-item {
  margin: auto;
  color: #3880ff;
}
</style>
