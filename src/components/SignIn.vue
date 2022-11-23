/* eslint-disable no-unused-vars */
<template>
  <v-app
    id="inspire"
    style="
      background: rgb(131, 58, 180);
      background: linear-gradient(36deg, rgba(131, 58, 180, 1) 19%, rgba(43, 29, 253, 1) 91%);">
    <v-content style="padding:0;">
      <v-container class="fill-height" fluid>
        <v-row align="center" justify="center">
          <div class="col-md-4">
            <v-card class="elevation-12" style="padding: 3.5rem 4rem">
              <v-alert
                type="error"
                v-model="loginLabelFailed"
                dismissible
                close-text="Close Alert"
              >
                Usuario o contraseña incorrectos
              </v-alert>
              <div>
                <h3>Bienvenido a TropSmart</h3>
                <h5>Inicia Sesión para continuar</h5>
              </div>
              <v-card-text>
                <v-form ref="form" v-model="valid" lazy-validation>
                  <v-text-field
                    v-model="signInInput.email"
                    :rules="[(v) => !!v || 'Ingrese un email']"
                    label="Correo"
                    name="login"
                    type="text"
                    outlined
                  ></v-text-field>

                  <v-text-field
                    id="password"
                    v-model="signInInput.password"
                    :rules="[(v) => !!v || 'Ingrese una contraseña']"
                    label="Contraseña"
                    name="password"
                    type="password"
                    outlined
                  ></v-text-field>
                </v-form>
              </v-card-text>
              <div class="pa-2" style="display: flex; justify-content: center">
                <v-btn
                  dark
                  large
                  color="skyblue"
                  @click="validate"
                  :disabled="loading"
                >
                  <span
                    v-show="loading"
                    class="spinner-border spinner-border-sm"
                  ></span>
                  Iniciar Sesión</v-btn
                >
              </div>
            </v-card>
          </div>
        </v-row>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
import TsDataService from "../services/TsDataService";

export default {
  data: () => ({
    valid: false,
    signInInput: {
      email: "",
      password: "",
    },
    loginLabelFailed: false,
    loading: false,
  }),
  props: {
    source: String,
  },
  mounted() {},
  methods: {
    validate() {
      if (this.$refs.form.validate()) {
        TsDataService.login(this.signInInput).then((response) => {
          if (response.success == true) {
            this.$store.dispatch("auth/login", response.resource).then(() => {
              this.$router.push("/profile").catch(() => {}),
                (error) => {
                  this.loading = false;
                  this.message =
                    (error.response && error.response.data) ||
                    error.message ||
                    error.toString();
                };
            });
          } else {
            this.loginLabelFailed = true;
          }
        });

        //auth/login  From auth.module.js
        /*this.$store.dispatch('auth/login', this.signInInput)
                .then( response =>{
                  console.log("signIn response : ", response);
                  if(response.success == true)
                  {
                    this.$router.push("/profile")
                    .catch(()=>{}) ,
                    error => {
                    this.loading = false;
                    this.message = (error.response && error.response.data)
                            || error.message || error.toString();

                    }
                  }
                  else
                  {
                    this.loginLabelFailed = true;
                  }
              });*/

        /*TsDataService.login(this.signInInput)
          .then(response => {
            if(response.data.success==true)
            {
              console.log("auth.user :",response.data.resource)

            }
            else
              alert("Credenciales no validas")
          })*/
      }
    },
    reload() {
      var location = this.$route.fullPath;

      this.$router.replace("");

      this.$nextTick(() => this.$router.replace(location));
    },
  },
};
</script>
