<template>
  <v-container>
    <v-row class="text-center">
      <v-col class="" cols="12">
        <h1 class="title">Qiita記事Export</h1>
        <v-card class="mt-10">
          <v-card-text>
            <p>QiitaのユーザID</p>
            <v-form
              ref="form"
              v-model="valid"
              lazy-validation
            >
              <v-text-field
                v-model="name"
                :counter="20"
                :rules="nameRules"
                label="Name"
                required
              ></v-text-field>
              <v-btn
                :disabled="!valid"
                color="primary"
                @click="search"
              >
                search
              </v-btn>
            </v-form>
          </v-card-text>
        </v-card>

        <v-card v-if="user" class="mt-10">
          <v-card-text>
            <img :src="user.profile_image_url" width=100 />
            <p class="subheading">{{ user.id }}</p>
            <p class="caption">{{ user.description }}</p>

            <div v-for="item in items">
              <v-card flat>
                <v-card-text>
                  <v-row>
                    <v-col cols="10">
                      <p class="subheading text-left">{{ item.title }} / like: {{ item.likes_count }}</p>
                    </v-col>
                    <v-col cols="2">
                      <v-btn small @click="download(item.id, item.title, item.body)">download</v-btn>
                    </v-col>
                  </v-row>
                </v-card-text>
              </v-card>
            </div>
          </v-card-text>
        </v-card>

        <v-card class="mt-10">
          <v-card-title class="text-center">
            <p class="caption">LinuxとかMacで黒い画面使える人はこういうことしましょう</p>
          </v-card-title>
          <v-card-text>
            <p>$ http --follow 'qiita.com/api/v2/users/ユーザID/items' | jq '.[] | .url' | xargs -I{} echo {} | sed 's/$/\.md/g' | xargs wget</p>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script lang="ts">
  import Vue from 'vue';
  import axios from 'axios';

  export default Vue.extend({
    data: () => ({
      valid: true,
      name: '',
      nameRules: [
        (v: string) => !!v || 'Name is required',
        (v: string) => !new RegExp(/[!"#$%&'()\*\+,\/:;<=>?@\[\\\]^`{|}~]/g).test(v) ? true : false || 'Special characters are invalid',
        (v: string) => (v && v.length <= 20) || 'Name must be less than 20 characters',
      ],
      user: '',
      items: [],
    }),

    methods: {
      search() {
        axios.get(`https://qiita.com/api/v2/users/${this.name}`)
          .then(
            res => {
              this.user = res.data;
            }
          )
          .catch(
            err => {
              console.log(err);
              this.user = '';
            }
          );
        axios.get(`https://qiita.com/api/v2/users/${this.name}/items`)
          .then(
            res => {
              this.items = res.data;
            }
          )
          .catch(
            err => {
              console.log(err);
              this.items = [];
            }
          );
      },
      download(id: string, title: string, text: string) {
        const blob = new Blob([ '#', title, '\n\n', text], { "type" : "text/plain" });
        const link = document.createElement('a')
        link.href = window.URL.createObjectURL(blob)
        link.download = id + '.md'
        link.click()
      }
    }
  });
</script>
