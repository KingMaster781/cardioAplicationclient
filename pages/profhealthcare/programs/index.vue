<template>
  <div>
    <prof-health-nav-bar />
    <b-container>
      <h2>Consultar todos os Programas</h2>
      <br />
      <b-button
        variant="success"
        style="float: right"
        :to="`/profhealthcare/programs/createProgram`"
        >Criar novo registo
        <fa :icon="['fas', 'plus']" />
      </b-button>
      <b-table
        v-if="programs.length"
        striped
        over
        :items="programs"
        :fields="fields"
      >
        <template v-slot:cell(actions)="row">
          <b-button
            variant="info"
            :to="`/profhealthcare/programs/${row.item.code}`"
          >
            <fa :icon="['fas', 'info-circle']" />
          </b-button>
          <b-button
            variant="primary"
            :to="`/profhealthcare/programs/${row.item.code}/edit`"
          >
            <fa :icon="['fas', 'pen']" />
          </b-button>
          <b-button variant="danger">
            <fa :icon="['fas', 'trash']" @click="remove(row.item.code)" />
          </b-button>
        </template>
      </b-table>
      <h5 v-else>Não existem atualmente programas inseridos na plantaforma</h5>
      <br />
      <p align="center">
        <a class="primary" @click="$router.go(-1)">Voltar a Trás</a>
      </p>
    </b-container>
  </div>
</template>

<script>
export default {
  data() {
    return {
      fields: ["code", "name", "descProgram", "actions"],
      programs: [],
    };
  },
  created() {
    this.$axios.$get("api/program/").then((program) => {
      this.programs = program;
    });
  },
  methods: {
    signOut() {
      this.$auth.logout();
      this.$router.push("/");
    },
    remove(item) {
      this.$axios
        .$delete("/api/program/" + item)
        .then(() => {
          this.$router.push("/profhealthcare/programs");
        })
        .catch((error) => {
          this.errorMsg = error.response.data;
        });
    },
  },
};
</script>
