<template>
  <div>
    <div class="progress-bar" role="progressbar" aria-valuenow="70" aria-valuemin="0" aria-valuemax="100" :style="progressWidth">progress...</div>
    <form v-on:submit.prevent>
      <div >
        <label>Promo </label><br>
        <input type="text" v-model="promo" ><br>
      </div>
      <div v-for="(item, index) in personInfo" :key="index">
        <div>
          <label>{{ item.name }}</label>
          <span class="fa" 
                v-if="controls[index].activated"
                :class="controls[index].error ? 'fa-exclamation-circle text-danger' : 
                                                'fa-check-circle text-success'"
                >
          </span>
          <br>
          <input type="text"
                  v-bind:value="item.value"
                  @input="onInput(index, $event.target.value)"
          >
          <br>
        </div>
      </div>
      <label for="guest">Guests</label>&nbsp;
      <button class="add guest" @click="addGuest()">+</button><br>

      <div v-for="(guest, index) in guests">
        <label for="guest" @dblclick="removeGuest($event, index)"> Guest {{ index + 1 }} </label><br>
        <input type="text"  name="guest" 
        v-model="guests[index]"
        @mouseenter="changeColor($event, index)"
        @mouseleave="$event.target.parentNode.style.color = 'black'"
        ><br>
      </div>
      <p> Your sale is {{ sale }} % </p>
      <input type="submit" value="Send Data" @click.once="toggleResult()" :disabled="done < controls.length">
    </form> 

    <br><br>
    <!--  -->
    <div v-if="showResult">
      <h2>All Done!</h2>
      <div class="table1">
        <table class="table1">
          <tr v-for="item in personInfo" :key="item.name">  
              <td>{{ item.name }}</td>
              <td>{{ item.value }}</td>
          </tr>
          <tr>
            <td>Guests</td>
            <td v-for="(guest, index) in guests" :key="index">
                {{ guest }}
            </td>
          </tr>
          
        </table>
        <!-- v-if удаляет элемент/компонент -->
        <!-- v-show ставит style:display='none' на элемент/компонент -->
        <p v-show="showResult"> Your sale was {{ sale }} % </p>
      </div>
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
        pattern: /.+/
      },
      {
        name: 'firstname',
        value: '',
        pattern: /^[a-zA-Z]+$/ //regular expression
      },
      {
        name: 'lastname',
        value: '',
        pattern: /^[a-zA-Z]+$/
      },
      {
        name: 'telephone',
        value: '',
        pattern: /^[0-9]+$/
      },
    ],
  }),
  beforeMount(){ //HOOK
    for(let i=0; i<this.personInfo.length;i++){
      this.controls.push({
        error: true,
        activated: false
      })
    }
  },
  methods: {
    toggleResult(){
      this.showResult = !this.showResult
    },
    addGuest(){
      this.guests.push('')
    },
    removeGuest(e, index){
      // console.log(e.target.parentNode)s
      this.guests.splice(index, 1)
    },
    onInput(index, value){
      let data = this.personInfo[index]
      let control = this.controls[index]
      data.value = value
      control.error = !data.pattern.test(value)
      control.activated = true
    }
    
  },
  computed: { // свойства которые высчитываются на основе других
  // computed нельзя использовать при выполнении асинхронных операций т.к. computed высчитывается
  // сразу как только зависимая переменная меняет значение (т.е. данные с сервера не успеют прийти)

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
  watch: { // один из примеров - использование с асинхронным кодом
    promo(){
      console.log('promo watcher triggered')
      getSale(this.promo, (sale) => {  //стрелочная ф-ия сохраняет контекст Vue экземпляра
        this.sale = sale
      })
    }
  }

}



// Можно объявлять ф-ии вне области видимости экземпляра Vue и вызывать их в рамках экземпляра
function getSale(code, callback){
  let codes = {
    newYear: 10,
    some: 20
  }
  // console.log(codes['newYear'])
  // console.log(codes[code])
  let sale = (codes[code] !== undefined) ? codes[code] : 0
  // console.log(sale)
  setTimeout(() => {
    callback(sale)
  }, 500)
}
</script>




<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
