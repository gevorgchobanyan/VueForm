<template>
  <div>
    
    <div class="progress" style="width: 40%;">
      <div class="progress-bar" 
           role="progressbar" 
           :style="progressWidth" 
           aria-valuenow="25" 
           aria-valuemin="0" 
           aria-valuemax="100"
           >
           {{ done/controls.length*100 }} %
      </div>
    </div>

    <form class="form-group" style="width: 25%;" v-on:submit.prevent>
      <div >
        <label >Promo </label><small class="form-text text-muted">Promo code: newYear</small><br>
        <input class="form-control form-control-sm" type="text" v-model="promo" ><br>
      </div>
      <div v-for="(item, index) in personInfo" :key="index">
        <div>
          <label>{{ capitalize(item.name) }}</label>
          <span class="fa" 
                v-if="controls[index].activated"
                :class="controls[index].error ? 'fa-exclamation-circle text-danger' : 
                                                'fa-check-circle text-success'"
                >
          </span>
          <br>
          <input class="form-control form-control-sm"
                 type="text"
                 v-bind:value="item.value"
                 @input="onInput(index, $event.target.value)"
          >
          <br>
        </div>
      </div>
      <label for="guest">Guests</label>&nbsp;
      <button class="add guest" @click="addGuest()">+</button><br>

      <div v-for="(guest, index) in guests" :key="'A' + index">
        <label for="guest"> 
          Guest {{ index + 1 }} 
          <i style="color:red" class="fa fa-window-close"  @click="removeGuest($event, index)" aria-hidden="true"></i>
        </label><br>
        
        <input class="form-control form-control-sm" type="text"  name="guest" 
        v-model="guests[index]"
        @mouseenter="changeColor($event, index)"
        @mouseleave="$event.target.parentNode.style.color = 'black'"
        ><br>
      </div>
      <p> Your sale is {{ sale }} % </p>
      <input type="submit" value="Send Data" @click.once="toggleResult()" :disabled="done < controls.length">
    </form> 



    <br><br>
    <div v-if="showResult">
      <h2>All Done! Your sale is {{ sale }} % </h2>
      <table class="table table-striped">
        <tbody>
          <tr v-for="(item, index) in personInfo" :key="index"
          >
            <td>{{capitalize(item.name)}}</td>
            <td>{{ item.value }}</td>
          </tr>
          <tr>
            <td>Guests</td>
            <td>{{guests.join()}}</td>
          </tr>
        </tbody>
      </table>
    </div>


  </div>
</template>

<script>
export default {
  data: () => ({ 
    promo: '',
    sale: 0,
    guests: [],
    controls: [],
    showResult: false,
    personInfo: [
      {
        name: 'email',
        value: '',
        pattern: /^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$/
      },
      {
        name: 'firstname',
        value: '',
        pattern: /^[a-zA-Z]+$/ 
      },
      {
        name: 'lastname',
        value: '',
        pattern: /^[a-zA-Z]+$/
      },
      {
        name: 'telephone',
        value: '',
        pattern: /^\d{10}$/
      },
    ],
  }),
  beforeMount(){ 
    for(let i=0; i<this.personInfo.length;i++){
      this.controls.push({
        error: true,
        activated: false
      })
    }
  },
  methods: {
    changeColor(e, i){
      if(i%2 === 0){
        e.target.parentNode.style.color = "green"
        e.target.style.color = "green"
      }else{
        e.target.parentNode.style.color = "blue" 
        e.target.style.color = "blue" 
      }
    },
    toggleResult(){
      this.showResult = !this.showResult
    },
    addGuest(){
      this.guests.push('')
    },
    removeGuest(e, index){
      this.guests.splice(index, 1)
    },
    onInput(index, value){
      let data = this.personInfo[index]
      let control = this.controls[index]
      data.value = value
      control.error = !data.pattern.test(value)
      control.activated = true
    },
    capitalize(str){
      return str.capitalize()
    },
  },
  computed: { 
    
    done(){
      let done = 0
      for(let i=0; i<this.controls.length;i++){
        if(!this.controls[i].error){
          done++
        }
      }
      return done
    },
    progressWidth(){
      return {
        width: (this.done / this.controls.length * 100) + '%'
      }
    }
  },
  watch: { 
    promo(){
      console.log('promo watcher triggered')
      getSale(this.promo, (sale) => { 
        this.sale = sale
      })
    }
  }

}

//

function getSale(code, callback){
  let codes = {
    newYear: 10,
    some: 20
  }
  let sale = (codes[code] !== undefined) ? codes[code] : 0
  setTimeout(() => {
    callback(sale)
  }, 500)
}

String.prototype.capitalize = function() {
    return this.charAt(0).toUpperCase() + this.slice(1);
}



</script>




<style scoped>
label i:hover {
  background-color: rgb(228, 106, 106);
}
</style>
