<template>
    <div id="app">
        
        
        <div class="container">
            <div class="row">
            <div class="column first">
                <h1>CORONA STATUS USA</h1>
        
            <div class="card" style="width: 18rem;">
  <div class="card-body">
    <h3 class="card-title">Confirmed Cases</h3>
    <h4 class="card-subtitle mb-2 text-muted">{{totalCases}} cases </h4>
    
  </div>

    <div class="card" style="width: 18rem;">
  <div class="card-body">
    <h4 class="card-title">Recovered Cases</h4>
    <h5 class="card-subtitle mb-2 text-muted">{{data.recovered}} patients</h5>
    <h6 class="card-text">{{recoverPercent}} %</h6>
    
    
  </div>
</div>

<div class="card" style="width: 18rem;">
  <div class="card-body">
    <h4 class="card-title">Deaths</h4>
    <h5 class="card-subtitle mb-2 text-muted">{{data.dead}} people</h5>
    <h6 class="card-text">{{deadPercent}} %</h6>
  </div>
</div>

</div>
</div>
        
    <!--Second Column-->
        
         <div class="column two">
                
        
            <div class="card" style="width: 18rem;">
        <div class="card-body">
    <h3 class="card-title">Save the current stat</h3>
    <span>Click the button to record the data at </span><hr>
    <button class="btn btn-primary" @click="save">Save</button>
    
  </div>

    <div class="card" style="width: 18rem;">
  <div class="card-body">
    <h4 class="card-title">Current Time</h4>
    <h5 class="card-subtitle mb-2 text-muted">{{data.time}}</h5>
    
    
    
  </div>
</div></div></div>


<!--This is the third column-->
<div class="column">
                
        
            <div class="card" style="width: 18rem;">
        <div class="card-body">
    <h3 class="card-title">View the saved stats</h3>
    <span>Click the button to see the record</span><hr>
    <button class="btn btn-primary" @click="retrive">Retrive</button>
    
  </div>

    <div v-for="data of retrived.arr" :key="data">
    <div class="card" style="width: 18rem;">
      
  <div class="card-body">
    
    <h4 class="card-title">At {{data.time}}</h4>
    <h5 class="card-subtitle mb-2 text-muted">Confirmed Cases : {{data.confirmed}}</h5>
    <h5 class="card-subtitle mb-2 text-muted">Deaths : {{data.dead}}</h5>
    <h5 class="card-subtitle mb-2 text-muted">Recovered Cases : {{data.recovered}}</h5>
    </div>
    
    
    
  </div>
</div></div></div>
        

</div>
</div>
</div>
        
</template>

<script>
import axios from 'axios';
    
    export default{
        
   
    data(){
        return{
            patient:[],
            deadPercent:null,
            recoverPercent:null,
            confirmed:null,
            dead:null,
            recovered:null,
            totalCases:null,
            
            data:{
              time:null,
              confirmed:null,
              dead:null,
              recovered:null
            },
            retrived:{
              arr:[]
            }
        }
    },
    created(){
        setInterval(()=>{
          axios({
    "method":"GET",
    "url":"https://coronavirus-map.p.rapidapi.com/v1/summary/latest",
    "headers":{
    "content-type":"application/octet-stream",
    "x-rapidapi-host":"coronavirus-map.p.rapidapi.com",
    "x-rapidapi-key":"b0433bc771msh581ee1857b31568p1894e7jsnda44c40b2881"
    }
    })
    .then((response)=>{
     
      const data = response.data;
     
     const region = data.data.regions;
     console.log(region);

     for(let key in region){
         this.patient.push(region[key]);
     }
     
    const totalCases = this.patient[0].total_cases; 
    const confirmed = this.patient[0].tested;
    const dead = this.patient[0].deaths;
    const recovered = this.patient[0].recovered;
    
    

    
    this.deadPercent =dead/totalCases*100;
    this.recoverPercent = recovered/totalCases*100;

    this.data.confirmed = confirmed;
    this.data.dead = dead;
    this.data.recovered = recovered;
    this.totalCases = totalCases; 
   
      
      

       
      
    
       
      
    })
    .catch((error)=>{
      console.log(error)
    })
        },9000);

    setInterval(()=>{
      const today = new Date();
    const time = today.getHours() + ' : ' + today.getMinutes() + ' : ' + today.getSeconds();
    this.data.time = time;
    })
    

    },
    methods:{
      save(){
        
        axios.post('http://localhost:3000/corona',{time: this.data.time, confirmed:this.data.confirmed, dead:this.data.dead, recovered:this.data.recovered}).then(response=>{console.log(response)})
    },
    retrive(){
      axios.get('http://localhost:3000/corona').then(response=>{
        const data = response.data;
        const arr = [];
        for(let key in data){
          const send = data[key];
          send.id = key;
          this.retrived.arr.push(send);
          
        }
        
        
        
      })
    }}
    }

   

</script>

<style  scoped>

.card{
    margin: 30px;
}

.two{
    margin-left:5px;
}

.first{
  margin-left:-30px;
}
</style>