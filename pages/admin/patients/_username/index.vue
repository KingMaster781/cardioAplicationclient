<template>
  <div>
    <admin-nav-bar />
    <b-container>
      <h4>Paciente: {{ patient.username}}</h4>
      <br>
      <p>Username: {{ patient.username }}</p>
      <p>Name: {{ patient.name }}</p>
      <p>Email: {{ patient.email }}</p>
      <br />
      <h5>Profissionais de Saude que trabalharam com o paciente</h5>
      <b-table
        striped
        over
        :items="profHealthcares"
        :fields="fieldProfHealthcare"
        ><template v-slot:cell(actions)="row">
          <b-button variant="info" :to="`/admin/healthcares/${row.item.username}`">
            <fa :icon="['fas', 'info-circle']" />
          </b-button>
          <b-button
            variant="primary"
            :to="`/admin/healthcares/${row.item.username}/edit`"
          >
            <fa :icon="['fas', 'pen']" />
          </b-button>
        </template>
      </b-table>
      <br />
      <p align="center"><a class="primary" @click="$router.go(-1)">Voltar a Trás</a></p>
    </b-container>
  </div>
</template>

<script>
export default {
  data() {
    return {
      patient: {},
      data: {},
      exam: {},
      fieldProfHealthcare: ["username", "name", "email", "actions"],
      fieldData: [
        "code",
        "insertionDate",
        "descriData",
        "value",
        "typeOfDataCode",
        "actions",
      ],
      fieldExam: ["code", "date", "dateResult", "done", "actions"],
      fieldPrescription: [
        "code",
        "duracao",
        "insertionDate",
        "vigor",
        "programCode",
        "actions",
      ],
    };
  },
  computed: {
    username() {
      return this.$route.params.username;
    },
    profHealthcares() {
      return this.patient.profHealthcareDTOList || [];
    },
  },
  created() {
    this.$axios.$get(`/api/patientusers/${this.username}`).then((patient) => {
      this.patient = patient || {};
      this.$axios
        .$get("/api/data/patient/" + this.patient.username)
        .then((datas) => {
          this.data = datas;
        });
    });
  },
  methods: {
    signOut() {
      this.$auth.logout();
      this.$router.push("/");
    },
    removeData(code) {
      this.$axios.$delete("api/data/" + code).then(
        this.$axios.$get("api/data/patient/" + this.patient.username).then((datas) => {
        this.data = datas;
      })
      );
    }
  },
};
</script>
