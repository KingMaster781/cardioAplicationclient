<template>
  <div>
    <prof-health-nav-bar />
    <br /><br />
    <b-container>
      <h2>Criar uma prescrição de Exercicios</h2>
      <br />
      <form @submit.prevent="create" :disabled="!isFormValid">
        <b-form-group
          id="code"
          description="O código é necessário"
          label-for="code"
          :invalid-feedback="invalidCodeFeedback"
          :state="isCodeValid"
        >
          <b-input
            v-model.trim="code"
            :state="isCodeValid"
            required
            placeholder="Insira o código da prescrição"
          />
        </b-form-group>
        <b-form-group
          id="duracao"
          description="A duração é necessária"
          label-for="duracao"
          :invalid-feedback="invalidCodeFeedback"
          :state="isCodeValid"
        >
          <b-input
            v-model.trim="duracao"
            :state="isDuracaoValid"
            required
            placeholder="Insira a duração da prescrição em dias"
          />
        </b-form-group>
        <b-form-group
          id="patient"
          description="O paciente é necessário"
          label-for="patient"
          :invalid-feedback="invalidPatientFeedback"
          :state="isPatientValid"
        >
          <b-select
            v-model="patient"
            :options="patients"
            :state="isPatientValid"
            required
            value-field="username"
            text-field="name"
          >
            <template v-slot:first>
              <option :value="null" disabled>
                -- Por favor, selecione o paciente --
              </option>
            </template>
          </b-select>
        </b-form-group>
        <b-form-group
          id="program"
          description="O programa é necessário"
          label-for="program"
          :invalid-feedback="invalidProgramFeedback"
          :state="isProgramValid"
        >
          <b-select
            v-model="program"
            :options="programs"
            :state="isProgramValid"
            required
            value-field="code"
            text-field="name"
          >
            <template v-slot:first>
              <option :value="null" disabled>
                -- Por favor, selecione o Programa RPC --
              </option>
            </template>
          </b-select>
        </b-form-group>
        <p v-show="errorMsg" class="text-danger">
          {{ errorMsg }}
        </p>
        <button
          class="btn btn-primary btn-lg btn-block"
          @click.prevent="create"
          :disabled="!isFormValid"
        >
          Criar
        </button>
        <br />
        <p align="center">
          <a class="primary" @click="$router.go(-1)">Voltar a Trás</a>
        </p>
      </form>
    </b-container>
  </div>
</template>

<script>
export default {
  data() {
    return {
      code: null,
      patient: null,
      program: null,
      duracao: null,
      patients: [],
      programs: [],
      errorMsg: false,
    };
  },
  computed: {
    invalidCodeFeedback() {
      if (!this.code) {
        return null;
      }
      let codeLen = this.code.length;
      if (codeLen < 1 || codeLen > 30) {
        return "The code must be between [1, 30] number characters.";
      }
      return "";
    },

    isCodeValid() {
      if (!this.invalidCodeFeedback === null) {
        return null;
      }
      return this.invalidCodeFeedback === "";
    },

    invalidDuracaoFeedback() {
      if (!this.duracao) {
        return null;
      }
      if (this.duracao < 0) {
        return "A duração tem que ser superior ou igual a 0";
      }
      return "";
    },

    isDuracaoValid() {
      if (!this.invalidDuracaoFeedback === null) {
        return null;
      }
      return this.invalidDuracaoFeedback === "";
    },

    invalidPatientFeedback() {
      if (!this.patient) {
        return null;
      }
      if (!this.patients.some((patient) => this.patient === patient.username)) {
        return null;
      }
      return "";
    },

    isPatientValid() {
      if (!this.invalidPatientFeedback === null) {
        return null;
      }
      return this.invalidPatientFeedback === "";
    },

    invalidProgramFeedback() {
      if (!this.program) {
        return null;
      }
      if (!this.programs.some((program) => this.program === program.code)) {
        return null;
      }
      return "";
    },

    isProgramValid() {
      if (!this.invalidPatientFeedback === null) {
        return null;
      }
      return this.invalidPatientFeedback === "";
    },

    isFormValid() {
      if (!this.isCodeValid) {
        return false;
      }
      if (!this.isDuracaoValid) {
        return false;
      }
      if (!this.isPatientValid) {
        return false;
      }
      if (!this.isProgramValid) {
        return false;
      }
      return true;
    },
  },

  created() {
    this.$axios
      .$get(`/api/profhealthcares/${this.$auth.user.sub}`)
      .then((healthcare) => {
        this.patients = healthcare.patients || [];
      });
    this.$axios.$get("api/program/").then((program) => {
      this.programs = program;
    });
  },

  methods: {
    create() {
      let dates = new Date();
      let month = dates.getUTCMonth() + 1;
      let day = dates.getUTCDate();
      let year = dates.getUTCFullYear();
      let currentDate = day + "/" + month + "/" + year;
      this.$axios
        .$post("/api/prescription-exercises", {
          code: this.code,
          duracao: this.duracao,
          insertionDate: currentDate,
          programCode: this.program,
          patientUser_username: this.patient,
        })
        .then(() => {
          this.$router.push(
            "/profhealthcare/patients/" +
              this.$route.params.username
          );
        })
        .catch((error) => {
          this.errorMsg = error.response.data;
        });
    },
    signOut() {
      this.$auth.logout();
      this.$router.push("/");
    },
  },
};
</script>
