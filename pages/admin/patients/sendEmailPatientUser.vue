<template>
  <div>
    <admin-nav-bar/>
    <b-container>
        <h2>Enviar uma mensagem para um Paciente</h2>
        <br>
        <form @submit.prevent="send">
        <b-form-group
            id="username"
            description="The username is required"
            label-for="username"
            :invalid-feedback="invalidUsernameFeedback"
            :state="isUsernameValid"
        >
          <b-input v-model.trim="username" :state="isUsernameValid" required placeholder="Escreva o username do Paciente" />
        </b-form-group>
        <b-form-group
            id="subject"
            label-for="subject"
        >
          <b-input v-model.trim="subject" placeholder="Escreva o Cabeçalho" />
        </b-form-group>
        <b-form-group
            id="message"
            label-for="message"
        >
          <textarea v-model="message" class="form-control" name="message" placeholder="Escreva a sua mensagem"></textarea>
        </b-form-group>
        &nbsp;
        <button class="btn btn-primary btn-lg btn-block" @click.prevent="send" :disabled="!isFormValid">Enviar</button>
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
      username: null,
      subject: null,
      message: null
    }
  },
  computed: {
    invalidUsernameFeedback () {
      if (!this.username) {
        return null
      }
      let usernameLen = this.username.length
      if (usernameLen < 3 || usernameLen > 30) {
        return 'The username must be between [3, 30] characters.'
      }
      return ''
    },

    isUsernameValid () {
      if (!this.invalidUsernameFeedback === null) {
        return null
      }
      return this.invalidUsernameFeedback === ''
    },

    isFormValid () {
      if (! this.isUsernameValid) {
        return false
      }
      return true
    }
  },
  methods: {
    send() {
      this.$axios.$post(`/api/patientusers/${this.username}/email/send`, {
        subject: this.subject,
        message: this.message
      })
      .then(msg => {
        this.$toast.success(msg).goAway(1500)
      })
      .catch(error => {
        this.$toast.error('error sending the e-mail').goAway(3000)
      })
    },
    methods: {
      signOut(){
        this.$auth.logout()
        this.$router.push('/')
      }
    }
  }
}
</script>