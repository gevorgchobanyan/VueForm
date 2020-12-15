<template>
  <div>
    <div class="sample">

      <div class="alert" :class="divClassName" :style="[properties, minHeight]">Some text</div>
      <br>
      <div>
        <label for="input1">Color</label>
        <input type="text" id="input1" v-model="properties['color']">

        <label for="input2">FontStyle</label>
        <input type="text" id="input2" v-model="properties['fontStyle']">

        <label for="input3">backgroundColor</label>
        <input type="text" id="input3" v-model="properties['backgroundColor']">

        <button class="btn btn-primary" @click="height += 20">+ height</button>
        {{height}}
      </div>

      <div class="form-check">
        <input class="form-check-input" 
                type="radio" 
                name="exampleRadios" 
                id="exampleRadios1"
                value = "info"
                v-model="divClass"
                >
        <label class="form-check-label" for="exampleRadios1">
          Info
        </label>
      </div>
      <div class="form-check">
        <input class="form-check-input" 
                type="radio" 
                name="exampleRadios" 
                id="exampleRadios1"
                value = "warning"
                v-model="divClass"
                >
        <label class="form-check-label" for="exampleRadios1">
          Warning
        </label>
      </div>
      <br>


      <table class="table" :class="flags">
        <tr>
          <td>title</td>
          <td>some</td>
        </tr>
        <tr>
          <td>title</td>
          <td>some</td>
        </tr>
        <tr>
          <td>title</td>
          <td>some</td>
        </tr>
      </table>

      <input type="checkbox" v-model="flags['table-bordered']"><label>table-bordered</label><br>
      <input type="checkbox" v-model="flags['table-background-color']"><label>table-background-color</label><br>
      <br>
  
    </div>
    <br><br>
  </div>
</template>

<script>
export default {
    data: () => ({ 
    divClass: '',
    properties: {
      //vue распарсит. При использовании в стилях можно писать так
      color: '',
      fontStyle: '',
      backgroundColor: ''
    },
    height: 0,
    flags: {
      'table-bordered' : false,
      'table-background-color': false,
    },
  }),
  methods: {
    changeColor(e, i){
      if(i%2 === 0){
        e.target.parentNode.style.color = "green"
        e.target.style.color = "green"
      }else{
        e.target.parentNode.style.color = "blue" 
        e.target.style.color = "blue" 
      }
    }
  },
  computed: {
  // computed нельзя использовать при выполнении асинхронных операций т.к. computed высчитывается
  // сразу как только зависимая переменная меняет значение (т.е. данные с сервера не успеют прийти)
    divClassName(){
      //используем массив чтобы задать несколько классов элементу
      return [
        'alert-' + this.divClass,
        'my-' + this.divClass 
      ]
    },
    minHeight(){
      return {
        minHeight: this.height + 'px'
      }
    },
  },
  watch: { // один из примеров - использование с асинхронным кодом
  }
   
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.my-info {
  font-style: italic;
}
.my-warning {
  font-size: 20px;
}
.table-background-color { 
  background-color: blue;
}
</style>
