<template>
  <v-dialog max-width="600px" v-model="dialog">
    <v-btn flat slot="activator" class="success black--text">Add new proj</v-btn>
    <v-card>
        <v-card-title>
          <h1>Add</h1>
        </v-card-title>
        <v-card-text>
          <v-form class="px-3" ref="form">
            <v-text-field label="Title" v-model="title" prepend-icon="folder" :rules="inputRules"></v-text-field>
            <v-textarea label="Information" v-model="content" prepend-icon="edit" :rules="inputRules"></v-textarea>
            <v-menu>
              <v-text-field :value="formattedDate" slot="activator" label="Due date" prepend-icon="date_range" :rules="inputRules"></v-text-field>
              <v-date-picker v-model="due">
              </v-date-picker>
            </v-menu>

            <v-spacer></v-spacer>
            <v-btn flat class="success mx-0 mt-1 black--text" @click="submit" :loading="loading">Add project</v-btn>
          </v-form>
        </v-card-text>
    </v-card>
  </v-dialog>
</template>

<script>
import format from 'date-fns/format'
import parseISO from 'date-fns/parseISO'
import db from '@/fb'

export default {
  data() {
    return {
      title: '',
      content: '',
      due: null,
      inputRules: [
        v => v.length >= 3 || 'Minimum length is 3 caracters'
      ],
      loading: false,
      dialog: false
    }
  } ,
  methods: {
    submit() {
      if(this.$refs.form.validate()) {
        this.loading = true

        console.log(this.title, this.content)
        const project = {
          title: this.title,
          content: this.content,
          due: format(parseISO(this.due), 'do MMM yyyy'),
          person: 'Hedy Lamarr',
          status: 'ongoing'
        }
        db.collection('projects').add(project)
          .then(() => {
            console.log('added to firebase');
            this.loading = false
            this.dialog = false
            this.$emit('projectAdded')
          })
      }
      
    }
  },
  computed: {
    formattedDate() {      
      return this.due ? format(parseISO(this.due), 'do MMM yyyy') : ''
    }
  }
}
</script>