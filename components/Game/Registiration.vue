<script>
import { mapGetters } from 'vuex'

  export default {
    data () {
      return {
        header: 'Welcome to the Space Wars',
        description: 'Please enter your username and name. If you never created your account before, you can create your username and name typing here!',
        username: '',
        name: '',
        point: 0,
        storeUsername: '',
        dialog: true,
        isLoading: false,
        rules: [
        value => !!value || 'Required.',
        value => (value && value.length >= 3) || 'Min 3 characters',
      ],
    }
},
    computed: {
      isDisabled() {
        if (this.username.length >= 3) {
            return false
        } 
        return true
      },
    },
    mounted() {
        this.storeUsername = this.$store.state.game.username
        this.checkStoreUsername()
    },
     methods: {
        checkStoreUsername() {
            if (
                !this.storeUsername 
                || this.storeUsername.length === 0 
                || this.storeUsername === undefined
                || this.storeUsername === null
            ) {
                this.dialog = true
                this.username = ''
                return
            }
            this.dialog = false
            this.username = this.storeUsername
            return
        },
        updateStoreUserInfo() {
            this.$store.dispatch('game/onUpdateUserInfo', { username: this.username, name: this.name, rankPoints: this.point })
        },
        checkUserLoginInfo() {
            const username = this.username.toLowerCase();
            const name = this.name.toLowerCase();
            
            const users = this.$store.state.game.users;
            
            const exists = users.some(user => user.username === username);
            const userInfo = users.find(user => user.username === username)
            if (exists) {
                this.username = userInfo.username
                this.name = userInfo.name
                this.point = userInfo.point
                return true; 
            } else {
                return false; 
            }
        },
        async createUserFirestore() {
            await this.$fire.firestore.collection('leaderboard').doc(this.username.toLowerCase()).set({
                name: this.name.toLowerCase(),
                username: this.username.toLowerCase(),
                point: this.point
            });
        },
        async updateUsersState() {
            const documents = await this.$fire.firestore.collection('leaderboard').get()
            this.$store.dispatch('game/onUpdateUsers', documents.docs.map(doc => doc.data()))
        },
        async submit() {
            this.isLoading = true
            const isCreated = this.checkUserLoginInfo()
            if (!isCreated) {
              await this.createUserFirestore()
            }
            this.updateStoreUserInfo()
            this.isLoading = false
            this.dialog = false
        }
    }
}
</script>
<template>
    <v-row justify="center">
    <v-dialog
      v-model="dialog"
      persistent
      max-width="350"
    >
      <v-card>
        <v-card-title class="text-h5">
          {{ header }}
        </v-card-title>
        <v-card-text>{{ description }}</v-card-text>
        <v-text-field
            class="ml-2"
            label="Name"
            :rules="rules"
            hide-details="auto"
            v-model.trim="name"
        ></v-text-field>
        <v-text-field
            class="ml-2"
            label="Username"
            :rules="rules"
            hide-details="auto"
            v-model.trim="username"
        ></v-text-field>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="info"
            text
            block
            :disabled="isDisabled"
            :loading="isLoading"
            @click="submit"
          >
            Login or Register
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-row>
</template>