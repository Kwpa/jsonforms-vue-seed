<template>
  <img alt="Vue logo" src="./assets/logo.png" />
  
  <h1>JSON Forms Vue 3</h1>
  <div class="myform">
    <json-forms
      :data="data"
      :renderers="renderers"
      :schema="schema"
      :uischema="uischema"
      @change="onChange"
    />
  </div>
  <button v-on:click="sendBinData">Send To Server</button>
  <button v-on:click="readBinData">Read From Server</button>
  <div class="data">
    <p>
      {{data}}
    </p>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import schemaJson from "./assets/schemaJson.json";
import teamSchemaJson from "./assets/teamSchema.json";
import uiJson from "./assets/uiJson.json";
import dataJson from "./assets/data.json";
import { JsonForms, JsonFormsChangeEvent } from "@jsonforms/vue";

import {
  defaultStyles,
  mergeStyles,
  vanillaRenderers,
} from "@jsonforms/vue-vanilla";
import axios from 'axios';

// mergeStyles combines all classes from both styles definitions into one
const myStyles = mergeStyles(defaultStyles, { control: { label: "mylabel" } });

const renderers = [
  ...vanillaRenderers,
  // here you can add custom renderers
];

const schema = schemaJson;
const teamSchema = teamSchemaJson;
const uischema = uiJson;
const teamId = "t_001";

export default defineComponent({
  name: "App",
  components: {
    JsonForms,
  },
  data() {
    //dataJson.teams[0]
    return {
      // freeze renderers for performance gains
      renderers: Object.freeze(renderers),
      data: dataJson,
      schema,
      uischema
    };
  },
  methods: {
    onChange(event: JsonFormsChangeEvent) {
      console.log(event);
      this.data = event.data;
    },
    onClick()
    {
      console.log("click");
      const XHR = new XMLHttpRequest(),
        FD  = new FormData();

      // Push our data into our FormData object
        FD.append( "data", JSON.stringify(this.data));

      // Define what happens on successful data submission
      XHR.addEventListener( 'load', function( event ) {
        alert( 'Yeah! Data sent and response loaded.' );
      } );

      // Define what happens in case of error
      XHR.addEventListener(' error', function( event ) {
        alert( 'Oops! Something went wrong.' );
      } );

      // Set up our request
      XHR.open( 'POST', 'https://example.com/cors.php' );

      // Send our FormData object; HTTP headers are set automatically
      XHR.send( FD );
    }
    ,
    async getData() {
      try {
        const response = await axios.get(
          "http://jsonplaceholder.typicode.com/posts"
        );
        // JSON responses are automatically parsed.
        this.data = response.data;
      } catch (error) {
        console.log(error);
      }
    },
    
    async sendBinData() {
      const req = new XMLHttpRequest();

      req.onreadystatechange = () => {
      if (req.readyState == XMLHttpRequest.DONE) {
        console.log(req.responseText);        
        }
      };

      req.open("PUT", "https://api.jsonbin.io/v3/b/62152dc91b38ee4b33c90568", true);
      req.setRequestHeader("Content-Type", "application/json");
      req.setRequestHeader("X-Master-Key", "$2b$10$5kL8aZjmWy4Epqa0b8W3HeUUL00fyq7UhvSmLDvIBKEA.iRMy7Khi");
      req.send(JSON.stringify(this.data));
    },

    async readBinData()
    {
      const req = new XMLHttpRequest();

      req.onreadystatechange = () => {
        if (req.readyState == XMLHttpRequest.DONE) {
          console.log(req.response);
          console.log(req.responseText);

          this.data = JSON.parse(req.responseText).record;
          
        }
      };

      req.open("GET", "https://api.jsonbin.io/v3/b/62152dc91b38ee4b33c90568/latest", true);
      req.setRequestHeader("X-Master-Key", "$2b$10$5kL8aZjmWy4Epqa0b8W3HeUUL00fyq7UhvSmLDvIBKEA.iRMy7Khi");
      req.send();
    }
  },
  created()
  {
    //this.readBinData();
  },
  provide() {
    return {
      styles: myStyles,
    };
  },
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  margin-left: 120px;
  margin-right: 120px;
}

.mylabel {
  color: darkslategrey;
}

.vertical-layout {
  margin-left: 10px;
  margin-right: 10px;
}

.myform {
  width: 640px;
  margin: 0 auto;
}

.text-area {
  min-height: 80px;
}

.data{
  max-width: 400px;
  margin: auto;
}
</style>
