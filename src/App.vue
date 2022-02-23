<template>
  <div style="padding-left:100px; padding-right:100px; margin: auto;"><img alt="Vue logo" src="./assets/DDM-Logo.png" style="width: 300px; height: 300px;"/></div>
  <div>
  <button v-on:click="value1=false; value0=true; readBinData();">Teams</button>
  <button v-on:click="value1=true; value0=false; readBinData();">Items</button>
  <button>Story</button>
  <button>Notifications</button>
  <button>Ballots</button>
  <button>Labels</button>
  </div>

  <h1>DIGIDAMARA STATIC DATA</h1>
  <div>
    <div style="height: 200px"> </div>
    <h2>Loading...</h2>
    <div style="height: 200px"> </div>
  </div>
  <div>
    <div class="myform" v-if="value0">
      <json-forms
        :data="data"
        :renderers="renderers"
        :schema="schema"
        :uischema="uischema"
        @change="onChange"
      />
    </div>
    <div class="myform" v-if="value1">
      <json-forms
        :data="data"
        :renderers="renderers"
        :schema="schema"
        :uischema="uischema"
        @change="onChange"
      />
    </div>
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
const uischema = uiJson;
const apiKey = "$2b$10$5kL8aZjmWy4Epqa0b8W3HeUUL00fyq7UhvSmLDvIBKEA.iRMy7Khi";
const jsonbinAccessUrls = {
  "teamsUrl":"62152dc91b38ee4b33c90568",
  "itemsUrl":"62158d528152db698ceeb0a3",
  "storyUrl":"62158fc71b38ee4b33c9e788",
  "notificationsUrl":"62158ff31b38ee4b33c9e807",
  "ballotsUrl":"621590ea1b38ee4b33c9eab7",
  "labelsUrl":"621591751b38ee4b33c9ec2e"
}

let categoryIndex = 0;



export default defineComponent({
  name: "App",
  components: {
    JsonForms,
  },
  data() {
     const teamsSchema = schema.properties.teams;
     const itemsSchema = schema.properties.items;
     const teamsUi = uischema.teams;
     const itemsUi = uischema.items;
    console.log("set items ui schema" + itemsUi);

    return {
      // freeze renderers for performance gains
      renderers: Object.freeze(renderers),
      data: dataJson,
      teamsSchema,
      teamsUi,
      value0: true,
      value1: false
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
    
    /* goToCategory(value: number)
    {
      categoryIndex = Math.min(Math.max(value, 0), 5);
    },
    pressCategoryButton(toggleIndex: number)
    {
      const list = [this.value0, this.value1];
      for(let i=0; i<list.length; i++)
      {
        list[i] = false;
      }
      this.goToCategory(toggleIndex);
    }, */

    async sendBinData() {
      const req = new XMLHttpRequest();

      categoryIndex = 0;
      let path = "";
      switch(categoryIndex)
      {
        case 0:
        path = jsonbinAccessUrls.teamsUrl;
        break;
        case 1:
        path = jsonbinAccessUrls.itemsUrl;
        break;
        case 2:
        path = jsonbinAccessUrls.storyUrl;
        break;
        case 3:
        path = jsonbinAccessUrls.notificationsUrl;
        break;
        case 4:
        path = jsonbinAccessUrls.ballotsUrl;
        break;
        case 5:
        path = jsonbinAccessUrls.labelsUrl;
        break;
      }

      req.onreadystatechange = () => {
      if (req.readyState == XMLHttpRequest.DONE) {
        console.log(req.responseText);        
        }
      };

      req.open("PUT", "https://api.jsonbin.io/v3/b/"+path, true);
      req.setRequestHeader("Content-Type", "application/json");
      req.setRequestHeader("X-Master-Key", apiKey);
      req.send(JSON.stringify(this.data));
    },

    async readBinData()
    {
      let path = "";
      switch(categoryIndex)
      {
        case 0:
        path = jsonbinAccessUrls.teamsUrl;
        break;
        case 1:
        path = jsonbinAccessUrls.itemsUrl;
        break;
        case 2:
        path = jsonbinAccessUrls.storyUrl;
        break;
        case 3:
        path = jsonbinAccessUrls.notificationsUrl;
        break;
        case 4:
        path = jsonbinAccessUrls.ballotsUrl;
        break;
        case 5:
        path = jsonbinAccessUrls.labelsUrl;
        break;
      }

      const req = new XMLHttpRequest();

      req.onreadystatechange = () => {
        if (req.readyState == XMLHttpRequest.DONE) {
          console.log(req.response);
          console.log(req.responseText);

          this.data = JSON.parse(req.responseText).record;
          
        }
      };

      req.open("GET", "https://api.jsonbin.io/v3/b/"+ path + "/latest", true);
      req.setRequestHeader("X-Master-Key", apiKey);
      req.send();
    }
  },
  created()
  {
    //this.pressCategoryButton(1);
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

.invisible{
  visibility: hidden;
}
</style>


