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
  <button v-on:click="onClick"></button>
  <div class="data">
    <p>
      {{data}}
    </p>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import schemaJson from "./assets/schemaJson.json";
import uiJson from "./assets/uiJson.json";
import dataJson from "./assets/data.json";
import { JsonForms, JsonFormsChangeEvent } from "@jsonforms/vue";

import {
  defaultStyles,
  mergeStyles,
  vanillaRenderers,
} from "@jsonforms/vue-vanilla";

// mergeStyles combines all classes from both styles definitions into one
const myStyles = mergeStyles(defaultStyles, { control: { label: "mylabel" } });

const renderers = [
  ...vanillaRenderers,
  // here you can add custom renderers
];

const schema = schemaJson;
const uischema = uiJson;

export default defineComponent({
  name: "App",
  components: {
    JsonForms,
  },
  data() {
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
