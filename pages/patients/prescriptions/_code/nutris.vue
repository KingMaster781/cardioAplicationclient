<template>
  <div>
    <patient-nav-bar />
    <br />
    <b-container>
      <h4>Detalhes da Prescrição:</h4>
      <p>Codigo: {{ prescription.code }}</p>
      <p>Duração: {{ prescription.duracao }} meses</p>
      <p>Data de Inserção: {{ prescription.insertionDate }}</p>
      <p>Estado: {{ prescription.vigor }}</p>
      <p>Código do Programa: {{ prescription.programCode }}</p>
      <h5>Exercicios associados ao Programa:</h5>
      <b-table
        v-if="exercises.length"
        striped
        over
        :items="exercises"
        :fields="fieldExercises"
      />
      <p align="center"><a class="primary" @click="$router.go(-1)">Voltar a Trás</a></p>
    </b-container>
  </div>
</template>

<script>
export default {
  data() {
    return {
      exercises: [],
      prescription: {},
      fieldPrescription: [
        "code",
        "duracao",
        "insertionDate",
        "vigor",
        "programCode",
        "actions",
      ],
      fieldExercises: ["code", "name", "descExercise"],
    };
  },
  computed: {
    code() {
      return this.$route.params.code;
    },
  },
  created() {
    this.$axios.$get("/api/prescription-nutris/" + this.code).then((prescriptions) => {
      this.prescription = prescriptions;
      this.showExercises();
    });
  },
  methods: {
    showExercises() {
      this.$axios
        .$get("/api/program/" + this.prescription.programCode + "/exercises")
        .then((exer) => (this.exercises = exer || {}));
    },

    signOut() {
      this.$auth.logout();
      this.$router.push("/");
    },
  },
};
</script>
