<template>
  <div>
    <prof-health-nav-bar/>
    <b-container>
      <form @submit.prevent="create">
        <b-form-group
          id="code"
          label-for="code"
        >
          <b-input v-model.trim="code" type="number" placeholder="Insere o código dos dados" />
        </b-form-group>
        <b-form-group
          id="descriData"
          label-for="descriData"
        >
          <b-input v-model.trim="descriData" placeholder="Insere uma pequena descrição dos dados" />
        </b-form-group>
        <b-form-group
          id="value"
          label-for="value"
        >
          <b-input
            v-model.trim="value"
            type="number"
            placeholder="Valor dos dados"
          />
        </b-form-group>
        <b-form-group
          id="group-type"
          label-for="group-type"
        >
          <b-form-select id="dataType" v-model="type" :options="dataType"
            value-field="code"
            text-field="unidadeValorQuantitativo"
            > 
          </b-form-select>
        </b-form-group>
        <p v-show="errorMsg" class="text-danger">
          {{ errorMsg }}
        </p>
        <button class="btn btn-primary" @click.prevent="create">CREATE</button>
        <br />
        <p align="center"><a class="primary" @click="$router.go(-1)">Voltar a Trás</a></p>
      </form>
    </b-container>
  </div>
</template>

<script>
export default {
  data() {
    return {
      code: null,
      insertionDate: null,
      descriData: null,
      value: null,
      type: null,
      errorMsg: false,
      dataType: [{}],
    };
  },
  created() {
      this.$axios.$get("/api/typeofdata/").then((dataTypes) => {
        this.dataType = dataTypes;
      });
  },
  methods: {
    signOut() {
      this.$auth.logout();
      this.$router.push("/");
    },
    dataTypes(){
        return this.dataType;
    },
    create() {
        const today = new Date();
    this.$axios
      .$post("/api/data/" + this.$route.params.username + "/" + this.type, {
          code: this.code,
        insertionDate: today.getFullYear()+'-'+(today.getMonth()+1)+'-'+today.getDate(),
        descriData: this.descriData,
        value: this.value,
        type: this.type,
      })
      .then(() => {
        this.$router.push('/profhealthcare/patients/' + this.$route.params.username + '/dados')
      })
      .catch((error) => {
        this.errorMsg = error.response.data;
      });
  },
  },
};
</script>
