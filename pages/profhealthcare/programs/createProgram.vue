<template>
    <div>
      <prof-health-nav-bar/>
      <b-container>
            <h2>Criar um Programa</h2>
            <br>
            <form @submit.prevent="create" :disabled="!isFormValid">
                <b-form-group
                    id="code"
                    description="O código é necessário"
                    label-for="code"
                    :invalid-feedback="invalidCodeFeedback"
                    :state="isCodeValid"
                >
                <b-input v-model.trim="code" :state="isCodeValid" required placeholder="Insira o código do programa" />
                </b-form-group>
                    <b-form-group
                        id="name"
                        description="O nome é necessário"
                        label-for="name"
                        :invalid-feedback="invalidNameFeedback"
                        :state="isNameValid"
                    >
                    <b-input v-model.trim="name" :state="isNameValid" required placeholder="Insira o nome do programa" />
                </b-form-group>
                    <b-form-group
                        id="desc"
                        description="A descrição do programa é necessária"
                        label-for="desc"
                        :invalid-feedback="invalidDescFeedback"
                        :state="isDescValid"
                    >
                    <b-input v-model.trim="desc" :state="isDescValid" placeholder="Insira a descrição do programa" />
                </b-form-group>
                <p v-show="errorMsg" class="text-danger">
                    {{ errorMsg }}
                </p>
                <h5>Selecionar Exercicios:</h5>
                    <b-form-group>
                        <b-form-checkbox-group
                            v-model="selectExercises"
                            :options="exercises"
                            class="mb-3"
                            value-field="code"
                            text-field="name"
                            disabled-field="notEnabled"
                            stacked
                        ></b-form-checkbox-group>
                    </b-form-group>
                <br>
                <button class="btn btn-primary btn-lg btn-block" @click.prevent="create" :disabled="!isFormValid">Criar</button>
                <br>
                <p align="center"><a class="primary" @click="$router.go(-1)">Voltar a Trás</a></p>
            </form>
        </b-container>
    </div>
 </template>

<script>
  export default {
    data(){
        return {
            code: null,
            name: null,
            desc: null,
            exercises: [],
            fields: ['code', 'name'],
            selectExercises: [],
            errorMsg: null
        }
    },

    computed: {
        invalidCodeFeedback () {
            if (!this.code) {
                return null
            }
            let codeLen = this.code.length
            if (codeLen < 1 || codeLen > 30) {
                return 'The code must be between [1, 30] number characters.'
            }
            return ''
        },

        isCodeValid () {
            if (!this.invalidCodeFeedback === null) {
                return null
            }
            return this.invalidCodeFeedback === ''
        },

        invalidNameFeedback () {
            if (!this.name) {
                return null
            }
            let nameLen = this.name.length
            if (nameLen < 3 || nameLen > 25) {
                return false
            }
            return ''
        },

        isNameValid () {
            if (!this.invalidNameFeedback === null) {
                return null
            }
            return this.invalidNameFeedback === ''
        },

        invalidDescFeedback () {
            if (!this.desc) {
                return null
            }
            let descLen = this.desc.length
            if (descLen < 3 || descLen > 255) {
                return false
            }
            return ''
        },

        isDescValid () {
            if (!this.invalidDescFeedback === null) {
                return null
            }
            return this.invalidDescFeedback === ''
        },
	
        isFormValid () {
            if (! this.isCodeValid) {
                return false
            }
            if (! this.isNameValid) {
                return false
            }
            if (! this.isDescValid) {
                return false
            }
            if (this.selectExercises.length === 0){
                return false
            }
            return true
        }
    },
    created(){
        this.$axios.$get('api/exercise/')
            .then((exercise) => {
            this.exercises = exercise
            })
    },

    methods: {
        create() {
            this.$axios.$post('/api/program', {
                code: this.code,
                name: this.name,
                descProgram: this.desc,
            })
            .then(() => {
                this.selectExercises.forEach(exercise=>{
                    this.$axios.$post('/api/program/' + this.code + '/exercises/',{code: exercise})
                    .then(() => {
                        console.log("success")
                    })
                    .catch((e)=>{
                        this.errorMsg = e.response.data
                    })
                })
                if(this.errorMsg == null)
                {
                    this.$router.push('/profhealthcare/programs')
                }
            })
            .catch((error) => {
                this.errorMsg = error.response.data
            })
            
        },

        signOut(){
            this.$auth.logout()
            this.$router.push('/')
        }
    }
  }
</script>