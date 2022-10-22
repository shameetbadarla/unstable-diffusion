<template>
  <div data-app style="margin-top: 10%;" class="container">

    <v-dialog
        v-model="dialog"
        max-width="290"
        persistent
    >
      <v-card dark color="#262424">
        <v-card-title class="text-h5">
          Backend Key
        </v-card-title>
        <v-text-field v-model="backendApi" style="margin-left: 7px; margin-right: 7px" label="key"></v-text-field>
        <v-card-text style="color: red">{{this.errorMes}}</v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
              color="green darken-1"
              text
              @click="submitBackend"
              small

          >
            Submit
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-row>
      <div style="font-size: 42px;color: #ED635E;margin: 0 auto;margin-bottom: 4%;background-color: #1C1B1B;padding-left: 20px;padding-right: 20px;box-shadow: 5px 8px #670F0B;">Unstable Diffusion</div></v-row>
    <v-row>
      <v-text-field
          v-model="promptStr"
          placeholder="Write a text ex: Astronaut riding horse on mars"
          dark
          append-icon="mdi-send"
          @click:append="generateImg"
          loading
          solo
          @keyup.enter="generateImg"

      >
        <template v-slot:progress>
          <v-progress-linear
              :value="progress"
              color="#EA4D47"
              absolute
              height="5"
              indeterminate
              v-if="loading"
          ></v-progress-linear>
        </template>
      </v-text-field>
    </v-row>
    <v-row>
      <v-col>
        <img v-if="downloadBtn" width="300" height="300" id="generatedImage1">
      </v-col>
      <v-col>
        <img v-if="downloadBtn" width="300" height="300" id="generatedImage2">
      </v-col>
      <v-col>
        <img v-if="downloadBtn" width="300" height="300" id="generatedImage3">
      </v-col>
    </v-row>
    <v-row>
      <v-col>
        <v-btn style="margin-top: 2%" dark class="rounded-0" color="#8A130F" v-if="downloadBtn" @click="download(1)"><v-icon>mdi-download</v-icon></v-btn>
      </v-col>
      <v-col>
        <v-btn  style="margin-top: 2%" dark class="rounded-0" color="#8A130F" v-if="downloadBtn" @click="download(2)"><v-icon>mdi-download</v-icon></v-btn>
      </v-col>
      <v-col>
        <v-btn style="margin-top: 2%" dark class="rounded-0" color="#8A130F" v-if="downloadBtn" @click="download(3)"><v-icon>mdi-download</v-icon></v-btn>
      </v-col>
    </v-row>
    <v-row style="margin-top: 10%;">
      <v-col>
        <a style="color: #635F5F" href="https://colab.research.google.com/">Powered by Google Colab</a>
      </v-col>
      <v-col>
        <a style="color: #635F5F" href="https://www.heroku.com/">Hosted on Heroku</a>
      </v-col>
      <v-col>
        <a style="color: #635F5F" href="https://stability.ai/">Model from Stability.Ai</a>
      </v-col>
      <v-col>
        <a style="color: #635F5F" href="https://keras.io/">Implemented in Keras</a>
      </v-col>
    </v-row>
    <v-row>
      <span style="margin:0 auto;margin-top: 4%;color: #635F5F;font-size: 18px ">Usage under <a style="color:#635F5F" href="https://mit-license.org/">MIT License</a></span>
    </v-row>
    <v-snackbar
        v-model="snackbar"
    >
      This might take upto a minute

      <template v-slot:action="{ attrs }">
        <v-btn
            color="#EA4D47"
            text
            v-bind="attrs"
            @click="snackbar = false"
        >
          Close
        </v-btn>
      </template>
    </v-snackbar>

  </div>
</template>

<script>

import axios from 'axios'
export default {
  name: 'test',
  data () {
    return {
      promptStr:'',
      downloadBtn:false,
      loading:false,
      snackbar: false,
      dialog: true,
      backendApi:null,
      errorMes:''
    }
  },
  mounted () {

  },
  methods: {

    generateImg(){
      this.loading=true
      this.snackbar=true
      this.downloadBtn=true
      axios.post(`${this.backendApi}/process`, {
        prompt: `${this.promptStr}`
      })
          .then(function (response) {
            console.log(response);
            const byteCharacters = atob(response.data.image0);
            const byteNumbers = new Array(byteCharacters.length);
            for (let i = 0; i < byteCharacters.length; i++) {
              byteNumbers[i] = byteCharacters.charCodeAt(i);
            }
            const byteArray = new Uint8Array(byteNumbers);
            document.getElementById("generatedImage1").src = URL.createObjectURL(new Blob([byteArray], { type: 'image/png' }));
            const byteCharacters1 = atob(response.data.image1);
            const byteNumbers1 = new Array(byteCharacters1.length);
            for (let i = 0; i < byteCharacters1.length; i++) {
              byteNumbers1[i] = byteCharacters1.charCodeAt(i);
            }
            const byteArray1 = new Uint8Array(byteNumbers1);
            document.getElementById("generatedImage2").src = URL.createObjectURL(new Blob([byteArray1], { type: 'image/png' }));

            const byteCharacters2 = atob(response.data.image2);
            const byteNumbers2 = new Array(byteCharacters2.length);
            for (let i = 0; i < byteCharacters2.length; i++) {
              byteNumbers2[i] = byteCharacters2.charCodeAt(i);
            }
            const byteArray2 = new Uint8Array(byteNumbers2);
            document.getElementById("generatedImage3").src = URL.createObjectURL(new Blob([byteArray2], { type: 'image/png' }));




          })
          .then(()=>{

            this.loading=false
          })

    },

    download(imgNum) {
      var a = document.createElement("a");
      document.body.appendChild(a);
      a.style = "display: none";
      a.href = document.getElementById(`generatedImage${imgNum}`).src;
      a.download = `unstablediff${imgNum}`;
      a.click();
      window.URL.revokeObjectURL(a.href);

    },
    submitBackend(){
      console.log(this.backendApi)
      if(this.backendApi===null){
        this.errorMes ='field empty'
      }
      if(this.backendApi!==null){
        if(this.backendApi.includes('ngrok')){
          this.dialog=false
        }


      }

    }

  }
}
</script>

<style scoped>


</style>

