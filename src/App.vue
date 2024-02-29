<script>
  export default {
    data(){
        return {
            buttons:[
                {label:'Del',type:'delete',color:"special"},{label:'С',type:'clear',color:"special"},{label:'.',type:'dot',color:"special"},{label:'*',type:'sign',color:"special"},
                {label:'7',type:'digit'},{label:'8',type:'digit'},{label:'9',type:'digit'},{label:'/',type:'sign',color:"special"},
                {label:'4',type:'digit'},{label:'5',type:'digit'},{label:'6',type:'digit'},{label:'+',type:'sign',color:"special"},
                {label:'1',type:'digit'},{label:'2',type:'digit'},{label:'3',type:'digit'},{label:'-',type:'sign',color:"special"},
                {label:'(',type:'bracket',opened:true},{label:'0',type:'digit'},{label:')',type:'bracket',closed:true},{label:'=',type:'result',color:"special"},
            ],
             output:'',
             outputObj:[],
             result:0,
             currentNum:'',
             sign:'',
             finish:false,
             brackets:0,

        }
    },
    methods:{
       click(btn){
           this.showEqual = false
           if(btn.type === 'delete') this.delete();
           else if(btn.type === 'result') this.printResult();
           else if(btn.type === 'dot') this.printDot(btn);
           else if(btn.type === 'digit')this.printDigit(btn);
           else if(btn.type === 'sign')this.printSign(btn);
           else if(btn.type === 'bracket')this.printBracket(btn);
           else if(btn.type === 'clear')this.clear();
       },
      delete(){
         if(this.output.length > 0){
           this.output = this.output.slice(0, -1)
           this.outputObj = this.outputObj.slice(0, -1)
           if(this.currentNum.length > 0)this.currentNum = this.currentNum.slice(0, -1)
         }
         else {
           this.result = 0;
         }
      },
      clear(){
        this.result = 0 ;
        this.output = ""
        this.outputObj = [];
      },
      printResult(){
         if(this.brackets === 0 && this.output !== ''){

           this.result = Math.floor( eval(this.output) * 100000 ) / 100000 ;
           this.output = this.result.toString()
           this.outputObj = [];
         }

      },
      printDot(btn){
         if(!this.currentNum.includes('.') && this.currentNum.length > 0){
            this.output += btn.label
            this.outputObj.push(btn)
            this.currentNum += btn.label
         }
      },
      printDigit(btn){
          if(this.outputObj.length > 0) {
             let prevSymbolCurrentNum = this.currentNum.slice(-1);
             if(prevSymbolCurrentNum === '0'){
               this.currentNum = this.currentNum.slice(0, -1)
               this.output = this.output.slice(0, -1)
               this.outputObj = this.outputObj.slice(0, -1)
             }
          }

          this.currentNum += btn.label
          this.output += btn.label
          this.outputObj.push(btn)
      },
      printSign(btn){
        let access = false;

        if(this.outputObj.length > 0){
          let prevSymbol = this.outputObj.slice(-1)[0];

          if(prevSymbol.type === 'digit' || (prevSymbol.type === 'bracket' && prevSymbol.closed )){
            access = true;
          }

          if(btn.label === '-' && (prevSymbol.type === 'bracket' && prevSymbol.opened ) ){
            access = true;
          }
        }

        if(btn.label === '-' && this.outputObj.length === 0 ){
          access = true;
        }

        if(access){
          this.currentNum  = '';
          this.output += btn.label
          this.outputObj.push(btn)
        }

      },
      printBracket(btn){
          let access = false;
          if(this.outputObj.length > 0){
            let prevSymbol = this.outputObj.slice(-1)[0];
            //если скобка открывающая только  если она первая  или перед ней знак
            if(btn.opened) {
                let previousIsOpenedBracket = prevSymbol.type === 'bracket' && prevSymbol.opened;
                let previousIsSign = prevSymbol.type === 'sign';

                if(previousIsOpenedBracket || previousIsSign){
                    this.brackets += 1
                    access = true;
                }
            }
            else {
                let previousIsClosedBracket = prevSymbol.type === 'bracket' && prevSymbol.closed;
                let previousIsDigit = prevSymbol.type === 'digit';
                if(previousIsClosedBracket || previousIsDigit){
                  if(this.brackets > 0){
                      this.brackets -= 1
                      access = true;
                  }

                }
            }
          }


          if(this.outputObj.length === 0 &&  btn.opened){
            this.brackets += 1
            access = true;
          }

          if(access){
            this.output += btn.label
            this.outputObj.push(btn)
          }



      },

    }
  }
</script>

<template>
  <div class="main-container">

    <div class="calc-container" >
      <div class="header">
        <p>{{output}}</p>
      </div>
<!--      <div class="result">-->
<!--          <p>{{result}}</p>-->
<!--      </div>-->
      <div class="btn-container">
          <div v-for="btn in buttons" class="btn-wrapper">
            <div v-on:click="click(btn)" :class="btn.color === 'special' ? 'btn-orange' : ''" class="btn">{{btn.label}}</div>
          </div>
      </div>
    </div>
  </div>
</template>

<style scoped>

</style>
